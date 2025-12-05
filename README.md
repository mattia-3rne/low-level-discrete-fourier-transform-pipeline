# Low-Level Discrete Fourier Analysis Pipeline

## 📊 Project Overview

The goal of this project is to demystify the **Discrete Fourier Transform (DFT)** by implementing it from scratch using a fundamental Linear Algebra approach, rather than relying on optimized "black box" FFT libraries.

This project implements a complete signal processing pipeline across three distinct stages: **Data Generation**, **Spectral Analysis**, and **Signal Reconstruction**. It explicitly constructs the DFT Matrices within the complex plane to demonstrate how time-domain signals are projected onto orthogonal frequency basis vectors.

---

## 🧠 Theoretical Background

### The Linear Algebra Formulation

While the Fourier Transform is often introduced as a summation formula, this project treats it as a linear mapping between two vector spaces: the Time Domain ($\mathbb{C}^N$) and the Frequency Domain ($\mathbb{C}^N$).

We define the signal as a vector $\mathbf{x}$ and the spectral coefficients as a vector $\mathbf{X}$. The transformation is a matrix-vector multiplication:

$$
\mathbf{X} = F \cdot \mathbf{x}
$$

Where $F$ is the **Fourier Matrix**.

### Roots of Unity and the Basis Vectors

The core of the Fourier Matrix relies on the $N$-th roots of unity. We define the fundamental constant $\omega_N$ as a complex exponential representing a rotation of $2\pi/N$ radians in the complex plane:

$$
\omega_N = e^{-i \frac{2\pi}{N}} = \cos\left(\frac{2\pi}{N}\right) - i\sin\left(\frac{2\pi}{N}\right)
$$

The elements of the Fourier Matrix $F$ are powers of this root. The entry at row $j$ and column $k$ is:

$$
F_{jk} = \omega_N^{j \cdot k} = e^{-i \frac{2\pi}{N} j k}
$$

### The Fourier Matrix as a Vandermonde Matrix

The Fourier Matrix ($F$) is a specific instance of a **Vandermonde Matrix**. A general Vandermonde matrix $V$ is constructed from a sequence of values $\{v_0, v_1, \dots, v_{N-1}\}$ such that the entry in the $j$-th row and $k$-th column is the geometric progression term $v_j^k$:

$$
V = 
\begin{bmatrix}
v_0^0 & v_0^1 & v_0^2 & \dots & v_0^{N-1} \\
v_1^0 & v_1^1 & v_1^2 & \dots & v_1^{N-1} \\
v_2^0 & v_2^1 & v_2^2 & \dots & v_2^{N-1} \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
v_{N-1}^0 & v_{N-1}^1 & v_{N-1}^2 & \dots & v_{N-1}^{N-1}
\end{bmatrix}
$$

For the Discrete Fourier Transform, the sequence of values is chosen to be the $N$-th roots of unity: $v_j = \omega_N^j = e^{-i \frac{2\pi}{N} j}$. Substituting this into the Vandermonde definition yields the **Fourier Matrix**, where every row corresponds to a specific frequency component testing every time sample:

$$
F = 
\begin{bmatrix}
1 & 1 & 1 & \dots & 1 \\
1 & \omega_N & \omega_N^2 & \dots & \omega_N^{N-1} \\
1 & \omega_N^2 & \omega_N^4 & \dots & \omega_N^{2(N-1)} \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
1 & \omega_N^{N-1} & \omega_N^{2(N-1)} & \dots & \omega_N^{(N-1)(N-1)}
\end{bmatrix}
$$

This creates a basis of orthogonal complex sinusoids. When we multiply $F \mathbf{x}$, we are projecting our signal against every possible frequency to see how much they correlate.

### Inverse DFT and Orthogonality

To reconstruct the signal, we need to invert the operation:

$$
\mathbf{x} = F^{-1} \mathbf{X}
$$

A distinct property of the Fourier Matrix is that its columns are orthogonal. The Inverse DFT Matrix is proportional to the Hermitian Transpose of $F$:

$$
F^{-1} = \frac{1}{N} F^H
$$

This creates a beautiful symmetry where the Inverse Matrix utilizes positive rotations ($\omega_N^{-1} = e^{+i \frac{2\pi}{N}}$) in the complex plane.

---

## ⚙️ The Pipeline

This repository is structured as a 3-stage modular pipeline.

### 1. Signal Generation
* **Math:** Superposition of sinusoids + Gaussian Noise $\mathcal{N}(\mu, \sigma^2)$.
* **Process:** Generates discrete samples over a time interval $t \in [0, T)$.
* **Output:** Time-domain dataset (`signal_data.csv`).

### 2. Spectral Analysis
* **Math:** Construction of the $N \times N$ matrix $F$ and computation of $\mathbf{X} = F\mathbf{x}$.
* **Feature:** Implementation of a Noise Threshold Filter. Coefficients with magnitude $|\mathbf{X}_k| < \epsilon$ are zeroed out.
* **Output:** Frequency-domain coefficients (`frequency_data.csv`).

### 3. Singal Reconstruction
* **Math:** Construction of the inverse matrix $\frac{1}{N}F^H$ and computation of $\mathbf{x}_{\text{recon}} = F^{-1}\mathbf{X}_{\text{filtered}}$.
* **Result:** A clean, denoised signal reconstructed solely from the fundamental frequency components.

---

## ⚠️ Limitations

| Limitation | Description |
| :--- |:---|
| **Computational Complexity** | This implementation uses Matrix Multiplication which is $O(N^2)$. It is excellent for educational purposes but significantly slower than Fast Fourier Transform, which is $O(N \log N)$. |
| **Spectral Leakage** | If the signal does not contain an integer number of cycles within the sampling window, energy "leaks" into adjacent frequency bins. |
| **Memory Usage** | Storing the full $N \times N$ matrix $F$ requires $O(N^2)$ memory. For very large $N$, this dense matrix approach becomes impractical. |

---

## 🚀 Getting Started

### Prerequisites
* Python 3.8+
* Jupyter Notebook

### Installation

1.  **Clone the repository**:
    ```bash
    git clone https://github.com/mattia-3rne/low-level-discrete-fourier-analysis-pipeline.git
    ```

2.  **Install dependencies**:
    ```bash
    pip install -r requirements.txt
    ```

3.  **Run the Pipeline**:
    Open the notebooks in order:
    1.  `signal_generation.ipynb`
    2.  `spectral_analysis.ipynb`
    3.  `signal_reconstruction.ipynb`

---

## 📂 Project Structure

* `signal_reconstruction.ipynb`: Reconstructing the signal using Inverse DFT.
* `signal_generation.ipynb`: Generating synthetic noisy signals.
* `sepctral_analysis.ipynb`: Performing DFT from scratch.
* `requirements.txt`: Python dependencies.
* `README.md`: Project documentation.