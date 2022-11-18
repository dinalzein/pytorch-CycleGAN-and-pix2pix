# Adversarial Map Generation
This repository is forked from [pytorch-CycleGAN-and-pix2pix](https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix). It aims at using CycleGAN as a basis for style transfer and modify it in two key ways (to have an Adversarial Map Generation framework):
- Handling Text: detection and masking strategy to prevent inconsistent supervisory signal.
- Alignment Supervision: exploit the natural alignment of maps/orthophotography.

## Introdcution
We consider the problem of automatically converting an aerial orthophotography into a legible map of arbitrary style. We address this task from an image-to-image translation perspective, and use both modern maps and historical maps spanning several centuries. Maps are inherently different from natural images: they rely on symbolic representations, contain text for named entities, and can be easily aligned with aerial images. We propose to exploit these unique properties to adapt the CycleGAN adversarial generative model for our problem. Our modifications significantly improve the legibility of generated maps.

<table style="width:100%; table-layout:fixed;">
	<tr>
		<td><img width="100%" src="imgs/our_approach.pdf"></td>
	</tr>
</table>


## Datasets
We are using the [Plan IGN](https://www.geoportail.gouv.fr) dataset. To get the text annotated dataset, we evaluated [TESTER](https://github.com/mlpc-ucsd/TESTR) using the [TotalText pre-trained model](https://ucsdcloud-my.sharepoint.com/:u:/g/personal/xiz102_ucsd_edu/ESwSFxppsplEiEaUphJB0TABkIKoRvIljkVIazPUNEXI7g?e=Q8zJ0Q) on the Cassini dataset. For more information on how to evaluate [TESTR](https://github.com/mlpc-ucsd/TESTR), please refer to the [README](https://github.com/mlpc-ucsd/TESTR/blob/main/README.md).

Extract text annotated Plan IGN under [datasets](./datasets) and make sure you organize it as follows:

```
- datasets
  | - PlanIGN
  |   | - testA
  |   | - testB
  |   | - trainA
  |   | - trainB
  |   | - valA
  |   | - valB
```
where ortho images placed under A and maps placed under B.

## Prerequisites
- Linux or macOS
- Python 3.7
- CUDA 11.7
- PyTorch 1.11.0
- visdom 0.1.8
- dominate 2.4.0


## Environment setup

Create and install environment for conda users:

```bash
# Create and install environment
conda env create -f environment.yml
# Activate environment
conda activate CycleGAN
```

for pip users:

```bash
# Create environment
python3 -m virtualenv .venv --python=python3.7

# Install environment
.venv/bin/pip install -r requirements.txt

# Activate environment
source .venv/bin/activate
```


## Train/Test CycleGAN
To train cycleGAN on the Plan IGN maps dataset (should be placed in [datasets](./datasets)), run:

```bash
python train.py --dataroot ./datasets/PlanIGN --name planIGN_cyclegan --model cycle_gan
```
To see more intermediate results, check out `./checkpoints/planIGN_cyclegan/web/index.html`.

To test the model, run:
```bash
python test.py --dataroot ./datasets/PlanIGN --name planIGN_cyclegan --model cycle_gan
```
The test results will be saved to a html file here: `./results/planIGN_cyclegan/latest_test/index.html`.

## Results

\begin{figure}[H]
\center
\begin{tabular}{cc}
     \begin{subfigure}{0.35\textwidth}
     \includegraphics[width=\textwidth]{our_approach_failure/478_A_real_A.png}
     \caption{Image in $\mathcal{X}$}
     \end{subfigure}
     &
     \begin{subfigure}{0.35\textwidth}
     \includegraphics[width=\textwidth]{our_approach_failure/478_A_real_B.png}
     \caption{Real map from $\mathcal{Y}$}
     \end{subfigure}
     \\
     \begin{subfigure}{0.35\textwidth}
     \includegraphics[width=\textwidth]{our_approach_failure/478_A_fake_A.png}
     \caption{Predicted image in $\mathcal{X}$}
     \end{subfigure}
    &
    \begin{subfigure}{0.35\textwidth}
     \includegraphics[width=\textwidth]{our_approach_failure/478_A_fake_B.png}
     \caption{Predicted map in $\mathcal{Y}$}
     \end{subfigure}

\end{tabular}
\caption{\textbf{Adversarial Map Generation Failure Results on Cassini XVII.} The input data image in (a) and its corresponding real map in (b), the output map $\GXY(x)$ in (d), and the output image $\GYX(y)$ in (c). Both The predicted map in (d) and the predicted image in (c) are unsatisfactorily.}

\label{our_approach_results_cassini}

\end{figure}


### Citation
To cite this work in your publications:

```bash
@inproceedings{zhu2017unpaired,
  title={Unpaired image-to-image translation using cycle-consistent adversarial networks},
  author={Zhu, Jun-Yan and Park, Taesung and Isola, Phillip and Efros, Alexei A},
  booktitle={Proceedings of the IEEE international conference on computer vision},
  pages={2223--2232},
  year={2017}
}
```
