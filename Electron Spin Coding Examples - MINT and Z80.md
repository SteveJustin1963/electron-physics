# Electron Spin Coding Examples for MINT and Z80 Assembly

This file collects practical coding examples based on the electron-spin README.

The core idea is that spin-\(\frac{1}{2}\) can be treated as a tiny two-state system:

```text
spin state = [A, B]

A = amplitude/probability weight for UP
B = amplitude/probability weight for DOWN
```

For a TEC-1, MINT, or Z80 assembly demo, this is a very good fit because the basic model can start with one bit and grow into a real fixed-point spinor simulator.

---

## 1. Simplest Spin State: UP / DOWN Only

The simplest model is:

```text
0 = spin down
1 = spin up
```

Useful operations:

```text
SHOW     print current spin
FLIP     change up to down, down to up
MEASURE  show UP or DOWN
```

This models the Pauli-X matrix:

```text
sigma_x flips |up>   -> |down>
sigma_x flips |down> -> |up>
```

In Z80 terms, this is just toggling one bit.

```asm
; SPIN = 0 or 1
; XOR 1 flips it

FLIP:
    LD A,(SPIN)
    XOR 01h
    LD (SPIN),A
    RET

SPIN:
    DB 01h
```

This is the first “quantum gate” you can run on a TEC-1.

---

## 2. Two-Byte Probability Model

Use two bytes:

```text
UPAMP = amplitude/probability weight for up
DNAMP = amplitude/probability weight for down
```

For a simple probability-only model, use 0–100:

```text
UPAMP = 100
DNAMP = 0
```

That means pure up.

```text
UPAMP = 50
DNAMP = 50
```

That means a 50/50 superposition.

Define states:

```text
Z_UP   = [100, 0]
Z_DOWN = [0, 100]
X_UP   = [50, 50]
X_DOWN = [50, 50] with hidden sign ignored
```

The hidden sign matters in real quantum mechanics, but for a first TEC-1 model you can ignore phase/sign and simulate probabilities only.

Memory layout:

```asm
UPAMP:
    DB 100

DNAMP:
    DB 0
```

Set \(z\)-up:

```asm
SET_Z_UP:
    LD A,100
    LD (UPAMP),A
    XOR A
    LD (DNAMP),A
    RET
```

Set \(z\)-down:

```asm
SET_Z_DOWN:
    XOR A
    LD (UPAMP),A
    LD A,100
    LD (DNAMP),A
    RET
```

Set an \(x\)-axis superposition:

```asm
SET_X_SUPER:
    LD A,50
    LD (UPAMP),A
    LD (DNAMP),A
    RET
```

---

## 3. Measurement Simulator with a Pseudo-Random Byte

Measurement is where the simulation gets interesting.

If:

```text
UPAMP = 50
DNAMP = 50
```

then generate a pseudo-random number from 0–99.

```text
if random < UPAMP:
    result = UP
else:
    result = DOWN
```

For Z80 it is easier to use a 0–255 scale instead of 0–100.

```text
0   = 0%
128 = 50%
255 = 100%
```

### Simple Random Byte Generator

This is a tiny LFSR-style pseudo-random byte generator.

```asm
; RANDOM BYTE LFSR
; Returns pseudo-random byte in A

RAND:
    LD A,(SEED)
    ADD A,A
    JR NC,NO_XOR
    XOR 1Dh

NO_XOR:
    LD (SEED),A
    RET

SEED:
    DB 5Ah
```

### Measurement Routine

```asm
; Crude version:
; random byte 0-255
; compare to threshold UPAMP scaled 0-255

MEASURE:
    CALL RAND
    LD B,A              ; B = random number

    LD A,(UPAMP)        ; UPAMP is 0-255 probability
    CP B
    JR NC,MEAS_UP

MEAS_DOWN:
    XOR A
    LD (SPIN),A
    RET

MEAS_UP:
    LD A,1
    LD (SPIN),A
    RET

SPIN:
    DB 0

UPAMP:
    DB 128              ; 128/256 = 50%
```

---

## 4. Stern–Gerlach Simulator

This would make a good TEC-1 visual demo.

Input state:

```text
Z_UP
Z_DOWN
X_UP
X_DOWN
```

Measurement axis:

```text
Z or X
```

Output:

```text
UP beam
DOWN beam
```

Rules:

```text
state Z_UP,   measure Z -> always UP
state Z_DOWN, measure Z -> always DOWN
state Z_UP,   measure X -> 50/50
state Z_DOWN, measure X -> 50/50
state X_UP,   measure X -> always UP
state X_DOWN, measure X -> always DOWN
state X_UP,   measure Z -> 50/50
state X_DOWN, measure Z -> 50/50
```

Possible 7-segment display output:

```text
U    = up
d    = down
r    = random / unresolved
```

Possible serial output:

```text
STATE: Z_UP
AXIS : X
RESULT: DOWN
```

### Simple State Codes

```text
0 = Z_UP
1 = Z_DOWN
2 = X_UP
3 = X_DOWN
```

### Simple Axis Codes

```text
0 = Z axis
1 = X axis
```

### Stern–Gerlach Logic

```text
if state is Z_UP and axis is Z:
    result = UP

if state is Z_DOWN and axis is Z:
    result = DOWN

if state is X_UP and axis is X:
    result = UP

if state is X_DOWN and axis is X:
    result = DOWN

otherwise:
    result = random 50/50
```

### Z80-Style Skeleton

```asm
; STATE:
; 0 = Z_UP
; 1 = Z_DOWN
; 2 = X_UP
; 3 = X_DOWN
;
; AXIS:
; 0 = Z
; 1 = X
;
; RESULT:
; 0 = DOWN
; 1 = UP

SG_MEASURE:
    LD A,(AXIS)
    OR A
    JR Z,MEASURE_Z_AXIS

MEASURE_X_AXIS:
    LD A,(STATE)
    CP 02h
    JR Z,RESULT_UP
    CP 03h
    JR Z,RESULT_DOWN
    JR RANDOM_50_50

MEASURE_Z_AXIS:
    LD A,(STATE)
    CP 00h
    JR Z,RESULT_UP
    CP 01h
    JR Z,RESULT_DOWN
    JR RANDOM_50_50

RANDOM_50_50:
    CALL RAND
    AND 80h
    JR Z,RESULT_DOWN
    JR RESULT_UP

RESULT_DOWN:
    XOR A
    LD (RESULT),A
    RET

RESULT_UP:
    LD A,01h
    LD (RESULT),A
    RET

STATE:
    DB 00h

AXIS:
    DB 00h

RESULT:
    DB 00h
```

---

## 5. Pauli Gate Demo

The Pauli matrices are ideal for small gate examples.

For a first practical TEC-1/Z80 version:

| Gate | Simple Meaning | TEC-1 Version |
|---|---|---|
| \(I\) | do nothing | leave bit unchanged |
| \(X\) | spin flip | XOR 1 |
| \(Z\) | phase flip | change hidden sign |
| \(Y\) | flip + phase | advanced version |

### Pauli-X Gate

Simple bit-only model:

```asm
; Pauli-X gate
; SPIN 0 = down, 1 = up

PAULI_X:
    LD A,(SPIN)
    XOR 01h
    LD (SPIN),A
    RET
```

### Pauli-Z Gate, Phase-Aware Version

A phase-aware model needs signs:

```text
UP_MAG
UP_SIGN
DN_MAG
DN_SIGN
```

Example:

```text
|up>      = +[255, 0]
|down>    = +[0, 255]
|x_up>    = +[181, 181]
|x_down>  = +[181, -181]
```

Why 181?

```text
255 / sqrt(2) ≈ 180.3
```

So for a rough unsigned 8-bit magnitude system:

```text
181 ≈ 1/sqrt(2)
```

For proper signed amplitudes, use 16-bit signed fixed-point values.

A \(Z\) gate changes the sign of the down component:

```text
[A, B] -> [A, -B]
```

Possible memory layout:

```asm
UP_MAG:
    DB 255
UP_SIGN:
    DB 0        ; 0 = positive, 1 = negative

DN_MAG:
    DB 0
DN_SIGN:
    DB 0
```

Pauli-Z sign flip of the down amplitude:

```asm
PAULI_Z:
    LD A,(DN_SIGN)
    XOR 01h
    LD (DN_SIGN),A
    RET
```

---

## 6. Fixed-Point Spinor Model

This is the more serious version.

Use Q8.8 or Q1.15 fixed point.

For Q8.8:

```text
1.0000 = 256
0.7071 = 181
0.5000 = 128
0.0000 = 0
```

Spinor:

```text
A = amplitude for up
B = amplitude for down
```

Use 16-bit signed integers:

```asm
A_RE:
    DW 256

A_IM:
    DW 0

B_RE:
    DW 0

B_IM:
    DW 0
```

This lets you eventually model complex amplitudes:

```text
psi = [A + iB, C + iD]
```

More explicitly:

```text
psi = [
    A_RE + i A_IM,
    B_RE + i B_IM
]
```

Then you can implement:

```text
Pauli-X:
[A, B] -> [B, A]

Pauli-Z:
[A, B] -> [A, -B]

Hadamard-like x/z basis change:
[A, B] -> [(A+B)/sqrt(2), (A-B)/sqrt(2)]
```

This gives you a proper qubit/spin simulator.

---

## 7. Fixed-Point Constants

For Q8.8:

```text
ONE      = 256
HALF     = 128
INV_SQRT2 = 181
ZERO     = 0
```

Assembly constants:

```asm
ONE:
    DW 256

HALF:
    DW 128

INV_SQRT2:
    DW 181

ZERO:
    DW 0
```

Or as assembler equates:

```asm
ONE       EQU 256
HALF      EQU 128
INV_SQRT2 EQU 181
ZERO      EQU 0
```

---

## 8. Spinor Gates in Fixed-Point Form

### Pauli-X

```text
[A, B] -> [B, A]
```

Z80-style swap of two 16-bit real amplitudes:

```asm
; A_RE and B_RE are 16-bit amplitudes

PAULI_X_16:
    LD HL,(A_RE)
    LD DE,(B_RE)

    LD (A_RE),DE
    LD (B_RE),HL

    RET
```

Note: `LD (nn),DE` is available on Z80.

### Pauli-Z

```text
[A, B] -> [A, -B]
```

Two's-complement negate of 16-bit `B_RE`:

```asm
PAULI_Z_16:
    LD HL,(B_RE)

    ; HL = -HL
    LD A,L
    CPL
    LD L,A

    LD A,H
    CPL
    LD H,A

    INC HL

    LD (B_RE),HL
    RET
```

### Hadamard-Like Basis Change

This is useful for converting between \(z\)-basis and \(x\)-basis.

```text
[A, B] -> [
    (A+B)/sqrt(2),
    (A-B)/sqrt(2)
]
```

In Q8.8, dividing by \(\sqrt{2}\) means multiplying by 181 and shifting right by 8.

Approximate method:

```text
newA = ((A + B) * 181) >> 8
newB = ((A - B) * 181) >> 8
```

On a plain Z80 this needs a 16-bit multiply routine, so it is more advanced.

For a first demo, you can cheat with common states:

```text
Z_UP   -> X measurement = 50/50
Z_DOWN -> X measurement = 50/50
X_UP   -> Z measurement = 50/50
X_DOWN -> Z measurement = 50/50
```

and avoid the full matrix multiplication until later.

---

## 9. MINT-Style Pseudocode

Because MINT is stack-ish/Forth-like, a first version could look like this conceptually.

```text
: FLIP
  SPIN @ 1 XOR SPIN !
;

: ZUP
  255 UP !
  0 DN !
;

: ZDOWN
  0 UP !
  255 DN !
;

: XSTATE
  128 UP !
  128 DN !
;

: MEASURE
  RAND UP @ <
  IF
    1 SPIN !
  ELSE
    0 SPIN !
  THEN
;
```

For display:

```text
: SHOW
  SPIN @
  IF ." UP"
  ELSE ." DOWN"
  THEN
;
```

---

## 10. More MINT-Like Minimal Version

This is a compact conceptual MINT version.

Exact syntax may need adjusting to your MINT dialect.

```text
V SPIN
V UP
V DN
V SEED

: INIT
  1 SPIN !
  255 UP !
  0 DN !
  90 SEED !
;

: FLIP
  SPIN @ 1 XOR SPIN !
;

: ZUP
  1 SPIN !
  255 UP !
  0 DN !
;

: ZDN
  0 SPIN !
  0 UP !
  255 DN !
;

: XSUP
  128 UP !
  128 DN !
;

: RAND
  SEED @ 2 *
  DUP 255 >
  IF
    256 -
    29 XOR
  THEN
  DUP SEED !
;

: MEAS
  RAND
  UP @ <
  IF
    1 SPIN !
  ELSE
    0 SPIN !
  THEN
;

: SHOW
  SPIN @
  IF
    ." UP"
  ELSE
    ." DOWN"
  THEN
;
```

---

## 11. First TEC-1 Program Target

A good first complete demo would use keypad commands:

```text
A key = set Z-up
B key = set Z-down
C key = apply spin flip
D key = measure
```

Serial output:

```text
SPIN DEMO
A = Z UP
B = Z DOWN
C = FLIP
D = MEASURE

STATE: Z_UP
AFTER FLIP: Z_DOWN
MEASURE: DOWN
```

For 7-segment output:

```text
U = up
d = down
F = flipped
r = random measurement
```

---

## 12. Suggested TEC-1 Memory Map

```asm
SPIN:
    DB 01h       ; 0 = down, 1 = up

UPAMP:
    DB 255       ; probability of up, 0-255

DNAMP:
    DB 000       ; probability of down, 0-255

SEED:
    DB 05Ah      ; random seed

STATE:
    DB 00h       ; Stern-Gerlach state code

AXIS:
    DB 00h       ; 0 = Z, 1 = X

RESULT:
    DB 00h       ; 0 = down, 1 = up
```

---

## 13. Project Ladder

Build the project in stages.

```text
PHASE 1
Single bit spin:
0 = down
1 = up
XOR 1 = spin flip

PHASE 2
Probability model:
UP_PROB = 0..255
measurement uses random byte

PHASE 3
Stern-Gerlach simulator:
measure same axis = deterministic
measure different axis = 50/50

PHASE 4
Pauli gates:
X, Z, maybe Y

PHASE 5
Signed fixed-point amplitudes:
Q8.8 or Q1.15

PHASE 6
Complex spinor:
real/imag components
proper quantum phase

PHASE 7
Magnetic field precession:
rotate phase over time using lookup tables
```

---

## 14. Magnetic Field Precession Idea

The README discusses Larmor precession:

```text
spin rotates around the magnetic field direction
```

For TEC-1 purposes, you can model this as a phase that advances every timer tick.

```text
PHASE = PHASE + SPEED
```

Then use a sine/cosine lookup table to rotate the spinor.

Simple fake version:

```text
phase 0   -> mostly up
phase 64  -> 50/50
phase 128 -> mostly down
phase 192 -> 50/50
phase 255 -> mostly up again
```

Memory:

```asm
PHASE:
    DB 00h

SPEED:
    DB 01h
```

Tick routine:

```asm
TICK_PRECESS:
    LD A,(PHASE)
    LD B,A
    LD A,(SPEED)
    ADD A,B
    LD (PHASE),A
    RET
```

Later, `PHASE` can index a lookup table.

---

## 15. Serial Display Ideas

A simple serial monitor output could look like this:

```text
ELECTRON SPIN DEMO

STATE VECTOR:
UP   = 128
DOWN = 128

BASIS:
Z

MEASURE:
RAND = 203
RESULT = DOWN
```

Or:

```text
STATE: X_UP
AXIS : Z
RULE : different basis, 50/50
RAND : 074
OUT  : UP
```

---

## 16. 7-Segment Display Ideas

Possible symbols:

```text
U = spin up
d = spin down
F = flip
P = probability mode
r = random
Z = z-axis
X = x-axis
```

If your 7-segment display cannot show a clean `X`, use:

```text
H = horizontal axis / x-axis
```

So:

```text
Z.U = z-up
Z.d = z-down
H.U = x-up
H.d = x-down
```

---

## 17. What This Demonstrates

This small program can demonstrate:

```text
1. Spin is two-state when measured.
2. A spin flip is just a Pauli-X gate.
3. Measurement can be probabilistic.
4. Measuring in the same basis is deterministic.
5. Measuring in a different basis gives 50/50 results.
6. A two-byte model can grow into a real spinor simulator.
7. The TEC-1 can act as a tiny quantum concept demonstrator.
```

The strongest bridge from theory to code is this:

```text
spin has only two measurement results along an axis,
but before measurement the state can be a two-component superposition.
```

That maps beautifully onto:

```text
two bytes       -> probability model
four words      -> real/imag spinor
lookup tables   -> magnetic precession
interrupt ticks -> time evolution
```

---

## 18. Next Expansion Ideas

Possible next programs:

```text
1. Proper Q8.8 multiply routine.
2. Hadamard/basis-change gate.
3. Complex phase using real/imag amplitudes.
4. Bloch-sphere angle stored as theta/phi.
5. Larmor precession using timer interrupts.
6. Stern-Gerlach chain:
   Z measurement -> X measurement -> Z measurement.
7. Serial menu:
   A = Z_UP
   B = Z_DOWN
   C = X_UP
   D = X_DOWN
   E = measure Z
   F = measure X
   G = Pauli-X
   H = Pauli-Z
```

A very nice experiment:

```text
Start with Z_UP.
Measure X.
Then measure Z again.
```

Expected behaviour:

```text
Z_UP -> X measurement gives random UP/DOWN.
After that, measuring Z again gives random UP/DOWN.
```

This demonstrates that measurement changes the state.

---

## 19. Minimal Full Z80 Demo Sketch

This is not tied to a specific TEC-1 monitor I/O routine. Replace `PRINT_*` and `GETKEY` with your own monitor calls.

```asm
; ------------------------------------------------------------
; Electron Spin Demo
; SPIN: 0 = down, 1 = up
; UPAMP: probability of UP, 0-255
;
; Keys:
; A = set Z-up
; B = set Z-down
; C = flip
; D = measure
; ------------------------------------------------------------

        ORG 9000h

START:
        CALL PRINT_MENU

MAIN:
        CALL GETKEY

        CP 'A'
        JP Z,CMD_ZUP

        CP 'B'
        JP Z,CMD_ZDN

        CP 'C'
        JP Z,CMD_FLIP

        CP 'D'
        JP Z,CMD_MEAS

        JP MAIN

CMD_ZUP:
        CALL SET_Z_UP
        CALL PRINT_UP
        JP MAIN

CMD_ZDN:
        CALL SET_Z_DOWN
        CALL PRINT_DOWN
        JP MAIN

CMD_FLIP:
        CALL PAULI_X
        CALL PRINT_STATE
        JP MAIN

CMD_MEAS:
        CALL MEASURE
        CALL PRINT_STATE
        JP MAIN

SET_Z_UP:
        LD A,01h
        LD (SPIN),A
        LD A,255
        LD (UPAMP),A
        RET

SET_Z_DOWN:
        XOR A
        LD (SPIN),A
        LD (UPAMP),A
        RET

PAULI_X:
        LD A,(SPIN)
        XOR 01h
        LD (SPIN),A

        ; also invert probability crudely
        LD A,(UPAMP)
        CPL
        LD (UPAMP),A
        RET

MEASURE:
        CALL RAND
        LD B,A
        LD A,(UPAMP)
        CP B
        JR NC,MEAS_UP

MEAS_DOWN:
        XOR A
        LD (SPIN),A
        RET

MEAS_UP:
        LD A,01h
        LD (SPIN),A
        RET

RAND:
        LD A,(SEED)
        ADD A,A
        JR NC,RAND_NO_XOR
        XOR 1Dh

RAND_NO_XOR:
        LD (SEED),A
        RET

PRINT_STATE:
        LD A,(SPIN)
        OR A
        JP Z,PRINT_DOWN
        JP PRINT_UP

; ------------------------------------------------------------
; Replace these with TEC-1/JMON/MINT monitor routines
; ------------------------------------------------------------

PRINT_MENU:
        RET

PRINT_UP:
        RET

PRINT_DOWN:
        RET

GETKEY:
        RET

; ------------------------------------------------------------
; Data
; ------------------------------------------------------------

SPIN:
        DB 01h

UPAMP:
        DB 255

SEED:
        DB 05Ah
```

---

## 20. Final Summary

Start with the one-bit version.

Then grow it:

```text
one bit
    -> probability byte
        -> Stern-Gerlach simulator
            -> Pauli gates
                -> signed fixed-point spinor
                    -> complex amplitudes
                        -> magnetic precession
```

This is a clean path from electron-spin theory to a working retro-computer quantum concept simulator on the TEC-1.
