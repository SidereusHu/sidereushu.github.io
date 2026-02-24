+++
title = "Notes | What is Quantum Computing? Implications for RSA & ECC"
date = 2025-05-13T16:57:25+08:00
draft = false
math = true
+++

<br>

### ❓What is Quantum Computing?

Quantum computing harnesses the quantum states of microscopic particles—such as photons, electrons, or atoms—to process information. It is fundamentally based on several key principles: **superposition**, **entanglement**, **coherence**, and the **no-cloning theorem**.

As early as **1982**, **Professor Richard Feynman** famously stated: 

> _"Nature isn’t classical, damn it, and if you want to make a simulation of nature, you’d better make it quantum mechanical."_


![Feynman](/images/screenshot-Feynman.png)

This insight laid the conceptual foundation for building computational tools using quantum systems themselves, rather than relying on classical approximations of quantum behavior.

<br>

### Why Classical Computers Are Not Enough

<span style="color:grey;">Many fundamental systems in nature do not follow classical physics but are governed by quantum mechanics.</span> **Therefore, if we want to “simulate nature,” classical computation is not sufficient — we must use quantum systems themselves to build computational tools.**

<span style="color:grey;">More specifically, simulating quantum systems with classical computers faces exponential difficulty. A quantum system composed of $n$ particles requires $2^n$ complex numbers to describe its state, which quickly exceeds the processing capability of classical machines.</span> Furthermore, particles in a quantum system are often not independent — they form complex correlations known as **quantum entanglement**.

Quantum computing, on the other hand, leverages the superposition of quantum states to perform parallel computation. <span style="color:grey;">For example, a 4-qubit quantum register can represent all 16 values from 0 to 15 simultaneously, and any operation on the register acts on all 16 values at once.</span>

This is why Feynman proposed a groundbreaking idea: <span style="color:red;">instead of simulating quantum systems with classical computers, we should use quantum systems themselves to compute.</span> His insight was remarkably accurate — as we now know, there are computations that can only be carried out through quantum experiments and are beyond the reach of traditional computers. 

<br>

### What Does Quantum Computing Mean for Mainstream Cryptosystems Like RSA and ECC?

In 1994, Peter Shor proposed a quantum algorithm—**Shor's Algorithm**—that can break mainstream cryptosystems such as RSA and ECC, drawing widespread attention. <span style="color:red;">The foundation of Shor's Algorithm is its ability to perform fast Fourier transforms (FFT) on state vectors of exponential size, making it easier to identify the period of a function.</span>

Quantum computing leverages the superposition effect of quantum states, allowing parallel computation across all possible states. For example, Schrödinger’s cat can be simultaneously **‘alive’** and **‘dead’**—a superposition of two states. If there are 100 cats, each in a superposition of **‘alive’** and **‘dead’** there are ($2^{100}$) possible states. Each state has its own amplitude, positive or negative, and quantum computing can manipulate this exponentially large vector of states simultaneously.

On the surface, this exponential parallelism might seem to allow brute-force solutions to any computationally hard problem. <u>However, the output of a quantum computation is itself a superposition, making it difficult to extract a definitive answer.</u>

Currently, only Shor’s Algorithm can use quantum Fourier transforms to efficiently extract the period of a function, thereby solving the integer factorization and discrete logarithm problems upon which modern public-key cryptosystems RSA and ECC are based. **For other hard problems that cannot be reduced to period finding, no polynomial-time quantum algorithms are known yet.**

<br>

### **How Close Are We to Practical Quantum Computers?**

Yes and no. To break a 1024-bit RSA key, a universal quantum computer with millions of qubits would be needed—far beyond our current manufacturing capabilities (the largest quantum computers today have only a few hundred qubits):

- In 2017, IBM introduced the world’s first 16-qubit quantum computer.
- The latest fault-tolerant universal quantum machines, such as Google’s Willow chip (105 qubits by the end of 2024), now achieve error correction threshold capabilities.
- The Oxford team has also created single-qubit operations with gate error rates as low as 0.000015%.

Developing public-key cryptographic algorithms that can resist quantum attacks (post-quantum cryptography) has become an urgent task. **To address this, the U.S. National Institute of Standards and Technology (NIST) launched the Post-Quantum Cryptography (PQC) standardization project back in 2016.** After years of evaluation, the leading quantum-resistant public-key algorithms—such as CRYSTALS-Kyber, Dilithium, and Falcon—are now essentially standardized and are set to become the new benchmarks for federal and enterprise security infrastructure.

<br>

### D-Wave’s Progress

The Canadian quantum computing company **D-Wave** has long provided **quantum annealers**, which are well-suited for solving **optimization problems**, rather than problems like **period-finding** as required by **Shor’s algorithm**.

In **2025**, D-Wave released its **Advantage2** system, featuring **over 4,400+ qubits** and a **20-node connectivity topology**. It is currently offering cloud-based services to clients such as **Mastercard**, **NTT**, and the **Jülich Supercomputing Centre**.

Meanwhile, D-Wave’s team published a paper in _Science_ claiming **“quantum advantage”** for the first time: in simulating complex magnetic systems, their machine outperformed the world’s top classical supercomputers by what they claim would be **millions of years**.

Although this result has sparked debates over classical simulators (such as **MPS** and **t-VMC**), it still suggests that **quantum annealing** is showing **practical value** in certain scientific and engineering domains.

D-Wave has built and sold several quantum computers; however, these machines **cannot run Shor’s algorithm** and therefore **do not pose a direct threat to cryptographic schemes based on factoring or discrete logarithms**.

Strictly speaking, D-Wave builds **quantum annealers**, which are machines that can **only run quantum annealing algorithms**. Their fundamental working principle differs from that of **universal quantum computers**, which rely on quantum superposition to achieve powerful computational capabilities. In contrast, quantum annealers rely on **quantum tunneling** to simulate the annealing process.

Currently, there is research underway that attempts to transform **integer factorization** into an **annealing-compatible problem** and solve it using D-Wave machines — but so far, it has only managed to factor **integers up to 90 bits**.


<br>

### Emerging Track: Topological Quantum Computing and Fault-Tolerance

- **February 2025**: **Microsoft** launched its first **Majorana-based topological quantum processor**, _Majorana 1_, featuring highly stable topological qubits and aiming for **million-qubit scalability**.
    
- **May 2025**: **Quantinuum** announced that its **H2 system** had achieved a record-breaking **quantum volume** $2^{23}=8388608$, a key metric for assessing a system’s practical computational power.
    
- **May 2025**: **Oxford**, **Google’s Willow**, and other teams continued advancing toward fault-tolerant quantum computing by optimizing **single-qubit gate fidelities** and improving **two-qubit error correction schemes**.