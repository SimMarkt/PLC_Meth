# PLC_Meth

The **PLC_Meth** project provides the backend software for a programmable logic controller of a two-stage chemical methanation plant.

Programming scripts in *Structured Text*, programming language for PLC defined by PLCOpen in IEC 61131-3.



---

## Table of Contents

1. [Overview](#overview)
2. [Project structure](#project-structure)
3. [Installation and Usage](#installation-and-usage)
4. [License](#license)
5. [Citing](#citing)
6. [References](#references)
7. [Acknowledgments](#acknowledgments)

---

## Overview

### Chemical methanation plant

Chemical methanation plant
 - First methanation reactor with steam generation
 - Second methanaition reactor
 - Intermediate condensation
 - Product gas cooling
 - Gas analysis
 - Water supply

 Block flow sheet

 Pictures of the plant



### Programmable logic controller  

Cyclic runs of the different scripts.
Autonomous control with discrete action ... RB Control with Temperatur in first stage
Autonmation studio from B&R -> libraries for modules and functions...

 Autonomous control - Rule-based Control based von T_Cat

 Temperature Control based on T_cat_max < T_krit and T_cat_max < T_sinter
 T_krit based on chemical equilibrium data and linear regression with non-linear basis functions

 T_sat and p_sat in Condenser_Control -> based on Magnus equation

 Visualization in Mappp View



---

## Project Structure

The project is organized into the following directories and files:

notation l_r_T_cat_max -> local variable (l), type real (r), description
g_i_status_PL -> global variable (g), type integer (i), description
FB_PFM > Function block (FB)
L_R_T_MAX_PL -> CONSTANTS capitalized (*Maximum temperature in °C inside the enclosure*)

Safety_Mechanisms:
- Definition of warnings and error messages
- Definition of actions for specific warnings and error messages

Temperature_Control:
- PID Control
- Startup rule-based control based von the maximum catalyst temperature

biomethane plant -> plant status and watchdog variable

---

## Installation and Usage

Follow these steps to install and run the project:

```bash
# Clone the repository
git clone https://github.com/SimMarkt/PLC_Meth.git

# Navigate to the project directory
cd PLC_Meth

# Copy the source code to the folder of your PLC software project and adjust the variables in the physical IO settings
#### CODE FOR POWERSHELL COPY AND PASTE

```



## License

This project is licensed under [MIT License](LICENSE).

---

## Citing

If you use PLC_Meth in your research or application, please cite it using the following BibTeX entry:
```BibTeX
@misc{PLC_Meth,
  author = {Markthaler, Simon},
  title = {PLC_Meth: Programmable logic controller for chemical methanation},
  year = {2024},
  url = {https://github.com/SimMarkt/PLC_Meth}
}
```

---

## References

[1] Markthaler S., "*Katalytische Direktmethanisierung von Biogas: Demonstration
in industrieller Umgebung und Betriebsoptimierung mittels Reinforcement
Learning*", DECHEMA Jahrestreffen der Fachsektion Energie, Chemie
und Klima (11.-12.03.), Frankfurt/Main, 2024

[2] Markthaler S., "*Optimization of Power-to-Gas operation and dispatch using Deep Reinforcement Learning*", Dissertation (PhD Thesis), Friedrich-Alexander-Universität Erlangen-Nürnberg, 2025 (not yet been published).

---

## Acknowledgments

This project was funded by the German *Federal Ministry for Economic Affairs and Climate Action* within the **Power-to-Biogas**
project (Project ID: 03KB165). 

---