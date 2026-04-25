# Electron Spin: Theory and Mathematics

Electron spin is an intrinsic angular momentum property of electrons and other fermions. It is **not** literal rotation like a classical spinning top. It is a purely quantum-mechanical property of the electron.

For an electron, the spin quantum number is:

\[
s = \frac{1}{2}
\]

The magnitude of the spin angular momentum is fixed:

\[
|\mathbf{S}| = \hbar \sqrt{s(s+1)}
\]

So for an electron:

\[
|\mathbf{S}| = \hbar \sqrt{\frac{1}{2}\left(\frac{1}{2}+1\right)}
= \frac{\sqrt{3}}{2}\hbar
\]

But when measured along any chosen axis, the spin projection has only two possible values:

\[
S_n = \pm \frac{\hbar}{2}
\]

These are usually called **spin up** and **spin down** along that axis.

“**Intrinsic**” means the electron has angular momentum even when treated as a point particle with no internal spinning surface.

---

## Historical Context and Discovery

### Stern–Gerlach Experiment, 1922

In the Stern–Gerlach experiment, silver atoms were passed through an inhomogeneous magnetic field.

Silver atoms are useful here because each has one unpaired outer electron. Classical physics predicted a continuous spread of deflections. Instead, the beam split into **two discrete spots**.

This showed that angular momentum projection is quantized.

The result is now understood as evidence for spin-\(\frac{1}{2}\)-type behaviour, although the full modern interpretation came later.

### Pauli and Dirac

Pauli introduced the idea of spin in 1925 to explain atomic spectra and the exclusion principle.

Dirac’s relativistic electron equation, published in 1928, made spin appear naturally. In that sense, electron spin is deeply connected to relativity.

The Dirac equation is:

\[
\left(i\hbar \gamma^\mu \partial_\mu - mc\right)\psi = 0
\]

It uses four-component spinors. In the non-relativistic limit, this reduces to the two-component spinor description used for ordinary electron spin.

---

## Quantum Theory of Spin

Spin is described in a **two-dimensional Hilbert space**, also called spinor space.

This spin space is separate from ordinary position or orbital motion.

For an electron in an atom, total angular momentum is:

\[
\mathbf{J} = \mathbf{L} + \mathbf{S}
\]

where:

- \(\mathbf{L}\) is orbital angular momentum
- \(\mathbf{S}\) is spin angular momentum
- \(\mathbf{J}\) is total angular momentum

For hydrogen fine structure, the allowed eigenvalues of \(\mathbf{J}^2\) help explain energy splittings.

Electrons obey the **Pauli exclusion principle**:

> No two identical fermions can occupy the same complete quantum state.

For an atom, that means no two electrons can have exactly the same set of quantum numbers:

\[
n,\ l,\ m_l,\ m_s
\]

This is one of the key reasons the periodic table has its structure.

---

## Magnetic Moment

An electron’s spin gives it a magnetic moment.

The magnetic moment is:

\[
\boldsymbol{\mu} =
-g\frac{e}{2m_e}\mathbf{S}
\]

where:

- \(g \approx 2\) for the electron
- \(e\) is the positive elementary charge
- \(m_e\) is the electron mass
- the minus sign appears because the electron has negative charge

This magnetic moment interacts with magnetic fields and causes effects such as the **Zeeman effect**.

---

## Mathematical Formalism

The spin operators are:

\[
\mathbf{S} = \frac{\hbar}{2}\boldsymbol{\sigma}
\]

where:

\[
\boldsymbol{\sigma} = (\sigma_x,\sigma_y,\sigma_z)
\]

The Pauli matrices are:

\[
\sigma_x =
\begin{pmatrix}
0 & 1 \\
1 & 0
\end{pmatrix}
\]

\[
\sigma_y =
\begin{pmatrix}
0 & -i \\
i & 0
\end{pmatrix}
\]

\[
\sigma_z =
\begin{pmatrix}
1 & 0 \\
0 & -1
\end{pmatrix}
\]

---

## Properties of the Pauli Matrices

The Pauli matrices are Hermitian:

\[
\sigma_i^\dagger = \sigma_i
\]

They square to the identity matrix:

\[
\sigma_i^2 = I
\]

They anticommute according to:

\[
\{\sigma_i,\sigma_j\} = 2\delta_{ij}I
\]

They commute according to:

\[
[\sigma_i,\sigma_j] = 2i\epsilon_{ijk}\sigma_k
\]

Because:

\[
S_i = \frac{\hbar}{2}\sigma_i
\]

the spin operators satisfy:

\[
[S_x,S_y] = i\hbar S_z
\]

and cyclic permutations of this relation.

---

## Total Spin

The total spin operator is:

\[
\mathbf{S}^2 = S_x^2 + S_y^2 + S_z^2
\]

Substituting the Pauli matrices:

\[
\mathbf{S}^2 =
\frac{\hbar^2}{4}
(\sigma_x^2+\sigma_y^2+\sigma_z^2)
\]

Since each Pauli matrix squares to the identity:

\[
\mathbf{S}^2 =
\frac{\hbar^2}{4}(I+I+I)
= \frac{3\hbar^2}{4}I
\]

This matches the general angular momentum rule:

\[
\mathbf{S}^2 = \hbar^2 s(s+1)I
\]

For:

\[
s = \frac{1}{2}
\]

we get:

\[
\hbar^2 \frac{1}{2}\left(\frac{1}{2}+1\right)
=
\frac{3\hbar^2}{4}
\]

---

## Eigenstates in the Z-Basis

The common spin basis is the \(z\)-basis.

Spin up along \(z\):

\[
|\uparrow_z\rangle =
\begin{pmatrix}
1 \\
0
\end{pmatrix}
\]

Spin down along \(z\):

\[
|\downarrow_z\rangle =
\begin{pmatrix}
0 \\
1
\end{pmatrix}
\]

They obey:

\[
S_z|\uparrow_z\rangle =
+\frac{\hbar}{2}|\uparrow_z\rangle
\]

\[
S_z|\downarrow_z\rangle =
-\frac{\hbar}{2}|\downarrow_z\rangle
\]

---

## General Spin Projection Along an Axis

Let the measurement direction be the unit vector:

\[
\mathbf{n}
=
(\sin\theta\cos\phi,\ \sin\theta\sin\phi,\ \cos\theta)
\]

Then:

\[
\mathbf{n}\cdot\boldsymbol{\sigma}
=
\begin{pmatrix}
\cos\theta & \sin\theta e^{-i\phi} \\
\sin\theta e^{i\phi} & -\cos\theta
\end{pmatrix}
\]

The eigenvalues are:

\[
+1,\ -1
\]

So the possible spin measurements are:

\[
S_n = \pm \frac{\hbar}{2}
\]

---

## Spin in a Magnetic Field

For a magnetic field:

\[
\mathbf{B} = B\mathbf{n}
\]

the Hamiltonian is:

\[
H = -\boldsymbol{\mu}\cdot\mathbf{B}
\]

Using the electron magnetic moment:

\[
\boldsymbol{\mu} =
-g\frac{e}{2m_e}\mathbf{S}
\]

we get:

\[
H =
\frac{g e}{2m_e}\mathbf{S}\cdot\mathbf{B}
\]

Since:

\[
\mathbf{S} = \frac{\hbar}{2}\boldsymbol{\sigma}
\]

then:

\[
H =
\frac{g e \hbar}{4m_e}
B\,\mathbf{n}\cdot\boldsymbol{\sigma}
\]

For \(g \approx 2\):

\[
H \approx
\frac{e\hbar}{2m_e}
B\,\mathbf{n}\cdot\boldsymbol{\sigma}
\]

This form assumes \(e\) is the positive elementary charge and the negative charge of the electron has already been included in the magnetic moment sign.

---

## Larmor Precession

A spin in a magnetic field precesses around the field direction.

The Larmor angular frequency magnitude is:

\[
|\omega_L| =
\frac{g e B}{2m_e}
\]

For an electron with \(g \approx 2\):

\[
|\omega_L| \approx \frac{eB}{m_e}
\]

Because the electron has negative charge, its precession direction is opposite to the direction expected for a positive charge.

---

## Key Quantum Features

### Uncertainty

Spin components do not all commute.

For example:

\[
[S_x,S_y] = i\hbar S_z
\]

This means the \(x\), \(y\), and \(z\) components of spin cannot all have definite values at the same time.

This is similar in spirit to position-momentum uncertainty, but it applies to angular momentum components.

### Superposition

A general electron spin state can be written as:

\[
|\psi\rangle =
\alpha|\uparrow_z\rangle
+
\beta|\downarrow_z\rangle
\]

where:

\[
|\alpha|^2 + |\beta|^2 = 1
\]

The probabilities are:

\[
P(\uparrow_z) = |\alpha|^2
\]

\[
P(\downarrow_z) = |\beta|^2
\]

### Measurement

When spin is measured along a chosen axis, the result is always one of two values:

\[
+\frac{\hbar}{2}
\]

or:

\[
-\frac{\hbar}{2}
\]

After measurement, the state collapses into the corresponding eigenstate for that measurement axis.

---

## Example: Spin Flip / Measuring Along X

Suppose the electron starts spin-up along \(z\):

\[
|\uparrow_z\rangle
\]

The spin-up and spin-down states along \(x\) are:

\[
|\uparrow_x\rangle =
\frac{1}{\sqrt{2}}
\left(
|\uparrow_z\rangle +
|\downarrow_z\rangle
\right)
\]

\[
|\downarrow_x\rangle =
\frac{1}{\sqrt{2}}
\left(
|\uparrow_z\rangle -
|\downarrow_z\rangle
\right)
\]

The original \(z\)-up state can be rewritten as:

\[
|\uparrow_z\rangle =
\frac{1}{\sqrt{2}}
\left(
|\uparrow_x\rangle +
|\downarrow_x\rangle
\right)
\]

So if we measure \(S_x\), the result is:

\[
+\frac{\hbar}{2}
\]

with probability:

\[
\frac{1}{2}
\]

and:

\[
-\frac{\hbar}{2}
\]

with probability:

\[
\frac{1}{2}
\]

So a \(z\)-up electron measured along \(x\) gives a 50/50 result.

---

## Simple Computational Model

A spin state can be represented as a two-value state vector.

For example:

```text
|up_z>   = [1, 0]
|down_z> = [0, 1]
```

A general state is:

```text
psi = [alpha, beta]
```

where:

```text
|alpha|^2 + |beta|^2 = 1
```

The Pauli \(X\) matrix acts like a quantum spin flip:

\[
\sigma_x
\begin{pmatrix}
1 \\
0
\end{pmatrix}
=
\begin{pmatrix}
0 \\
1
\end{pmatrix}
\]

and:

\[
\sigma_x
\begin{pmatrix}
0 \\
1
\end{pmatrix}
=
\begin{pmatrix}
1 \\
0
\end{pmatrix}
\]

So:

```text
sigma_x flips up_z into down_z
sigma_x flips down_z into up_z
```

This is why spin-\(\frac{1}{2}\) systems are often used as models for qubits.

A simple TEC-1 or MINT-style model could store the spin state as two numbers:

```text
A = amplitude for up
B = amplitude for down
```

For a simple real-valued demonstration:

```text
up_z   = [1, 0]
down_z = [0, 1]
up_x   = [0.7071,  0.7071]
down_x = [0.7071, -0.7071]
```

Then measuring a \(z\)-up state along \(x\) gives:

```text
prob_up_x   = 0.7071^2 = 0.5
prob_down_x = 0.7071^2 = 0.5
```

This gives a small computable model of quantum spin without needing full complex-number quantum mechanics at the start.

---

## Connections to Other Physics

Spin connects to many deeper topics:

- **Spin-orbit coupling**

\[
H_{SO} \propto \mathbf{L}\cdot\mathbf{S}
\]

- **Fine structure**
- **Zeeman splitting**
- **Quantum information**
- **Qubits**
- **Fermions and the spin-statistics theorem**
- **Chemistry and the Pauli exclusion principle**
- **Hartree-Fock methods with spinors**

Electron spin is therefore not just a small correction to atomic physics. It is one of the central features connecting quantum mechanics, chemistry, particle physics, and quantum computing.
