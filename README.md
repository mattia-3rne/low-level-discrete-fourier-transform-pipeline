# Low-Level Discrete Fourier Transform Pipeline

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


### The Fourier Matrix as a Vandermonde Matrix

The Fourier Matrix is a specific instance of a **Vandermonde Matrix**, constructed from the sequence of $N$-th roots of unity. This creates a basis of orthogonal complex sinusoids. When we multiply $F \mathbf{x}$, we are projecting our signal against every possible frequency to see how much they correlate:

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

---

## ⚙️ Pipeline & Methodology

This repository is structured as a 3-stage modular pipeline. Below is the detailed mathematical formulation for each stage.

### 1. Signal Generation

**Theoretical Approach**
The primary objective of this stage is to generate a synthetic time-domain signal to serve as a controlled input dataset. By constructing a signal from known mathematical components—specifically, a superposition of sinusoidal waves with distinct frequencies—we establish a ground truth. This allows for rigorous verification of the DFT implementation in subsequent stages.

To better approximate real-world data acquisition scenarios, stochastic noise is injected into the deterministic signal. This ensures that the downstream analysis algorithms are robust against imperfections inherent in physical sensors.


**Mathematical Formulation**
We model the continuous-time signal $x(t)$ as the sum of a deterministic component $s(t)$ and a stochastic noise component $\eta(t)$:

$$
x(t) = s(t) + \eta(t)
$$

**The Deterministic Component**
The clean signal $s(t)$ is constructed as a linear combination of sinusoidal waves:

$$
s(t) = A_1 \sin(2\pi f_1 t) + A_2 \sin(2\pi f_2 t)
$$

**The Stochastic Component**
The noise term $\eta(t)$ simulates background interference using Additive White Gaussian Noise. Each noise sample is treated as a random variable drawn from a Normal distribution:

$$
\eta(t) \sim \mathcal{N}(\mu, \sigma^2)
$$

**Discretization**
Digital systems process discrete data, so $x(t)$ is sampled at fixed time intervals $T_s = 1/f_s$. Substituting the discrete time steps $t_n = n \cdot T_s$ yields the final discrete sequence $x[n]$:

$$
x[n] = \underbrace{A_1 \sin(2\pi f_1 n T_s) + A_2 \sin(2\pi f_2 n T_s)}_{\text{Signal}} + \underbrace{\eta[n]}_{\text{Noise}}
$$

### 2. Spectral Analysis

**Theoretical Approach**
In this stage, we transition from the time domain to the frequency domain to analyze the noisy signal. We implement the **Discrete Fourier Transform** from first principles using Linear Algebra, treating the transformation as a change of basis where the new basis vectors correspond to orthogonal complex sinusoids.

Following the transformation, we apply a **hard threshold filter** in the frequency domain to separate the significant signal components from the stochastic noise.


**Mathematical Formulation**
The DFT transforms a finite sequence of $N$ complex numbers $\mathbf{x}$ into another sequence $\mathbf{X}$. This operation is defined as a linear transformation:

$$
\mathbf{X} = F \cdot \mathbf{x}
$$

**The Fourier Matrix Element**
The element in the $j$-th row and $k$-th column of the matrix $F$ is given by the power of the fundamental root of unity:

$$
F_{j,k} = \omega_N^{j k} = e^{-i \frac{2\pi}{N} j k}
$$

**Symmetry and Nyquist Frequency**
For real-valued input signals, the resulting DFT spectrum exhibits conjugate symmetry ($X_k = X_{N-k}^*$). The **Nyquist Frequency** corresponds to the index $k = N/2$, representing the highest frequency resolvable by the sampling rate $f_s$:

$$
f_{\text{Nyquist}} = \frac{f_s}{2}
$$

### 3. Signal Reconstruction

**Theoretical Approach**
In the final stage, we transform the processed frequency-domain data back into the time domain via **Signal Reconstruction**.

The goal is to take the filtered spectral coefficients and compute the corresponding time-domain signal. Because the DFT is a bijective mapping, this operation is perfectly invertible. However, since coefficients were modified via thresholding, the reconstructed signal $\mathbf{\hat{x}}$ will be a denoised approximation of the original input.


**Mathematical Formulation**
We aim to recover the time-domain signal vector $\mathbf{x}$ from the frequency-domain vector $\mathbf{X}$. The inverse operation is defined as:

$$
\mathbf{x} = F^{-1} \cdot \mathbf{X}
$$

**The Inverse Fourier Matrix**
The forward Fourier matrix $F$ is unitary (columns are orthogonal). A fundamental property is that its inverse is proportional to its Hermitian Transpose $F^H$. The orthogonality relation is $F^H F = N I$. Therefore, the inverse matrix is:

$$
F^{-1} = \frac{1}{N} F^H
$$

**Element-wise Definition**
While the forward matrix $F$ uses negative exponents, the conjugate transpose $F^H$ utilizes complex conjugates (positive exponents). The reconstruction structure becomes:

$$
\mathbf{x}[n] = \frac{1}{N} \sum_{k=0}^{N-1} X[k] \cdot e^{i \frac{2\pi}{N} k n}
$$

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
    git clone https://github.com/mattia-3rne/low-level-discrete-fourier-transform-pipeline.git
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

* `signal_generation.ipynb`: Generating synthetic noisy signals.
* `spectral_analysis.ipynb`: Performing DFT from scratch.
* `signal_reconstruction.ipynb`: Reconstructing the signal using Inverse DFT.
* `requirements.txt`: Python dependencies.
* `README.md`: Project documentation.
