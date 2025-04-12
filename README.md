# PLC_Meth

The **PLC_Meth** project provides the source code for the full automation of a two-stage chemical methanation plant (Fig. 1). The pilot plant was designed and built at the Institute of Energy Process Engineering at Friedrich-Alexander-Universität Erlangen-Nürnberg (FAU). It was developed to investigate methane production from hydrogen and biogenic gases in industrial environments and features a programmable logic controller from B&R Industrial Automation GmbH.

Long-term tests at an industrial biomethane production facility as part of the *Power-to-Biogas* project (Project ID: 03KB165) for over a year, and at a wastewater treatment plant in the *Kläffizient* project (Project ID: 03EI5421) for several months, have demonstrated the reliability of the software and hardware architecture for plant operation [1].

Additionally, **PLC_Meth** includes routines for the fully automated operation of the plant, including startup, load changes, and cooldown. It employs *rule-based control* for *dynamic optimization* of state changes and integrates *Supervised Learning* methods for temperature control. Furthermore, it has been used for *dynamic real-time optimization* and autonomous plant operation through *deep reinforcement learning*. For more information, please refer to [2].

![Meth_Plant](img/Meth_Plant.png)

*Figure 1: Two-stage methanation plant with a control cabinet containing an industrial PLC and I/O block.*

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

**PLC_Meth** provides the core programming scripts for the software backend that automates the methanation plant. The following sections offer a detailed explanation of the process and control architecture.  

### Chemical methanation plant

Methanation refers to the conversion of hydrogen (H<sub>2</sub>) and carbon monoxide (CO) or carbon dioxide (CO<sub>2</sub>) into methane (CH<sub>4</sub>). These reactions are typically catalyzed to enhance efficiency. In chemical methanation, reactors use chemical catalysts, such as nickel (Ni), to facilitate the process, operating within a temperature range of 473–823 K (200–600 °C) and a pressure range of 0.1–10 MPa (1–100 bar) [3,4]. The following equations illustrate the reactions, where water (H<sub>2</sub>O) is produced as a byproduct:  

CO<sub>2</sub> + 3 H<sub>2</sub> <-> CH<sub>4</sub> + 2 H<sub>2</sub>O

CO + 3 H<sub>2</sub> <-> CH<sub>4</sub> + H<sub>2</sub>O

Both reaction pathways, from CO and CO<sub>2</sub>, are highly exothermic, meaning they generate significant heat. To prevent overheating and stay within material limits, the reactors are actively cooled, and the waste heat is repurposed to produce steam. Figure 2 presents a block diagram of the methanation plant.  

![Block](img/Block.png)

*Figure 2: Block diagram of the two-stage methanation plant, including steam generation, intermediate water removal, condensation, and water supply (according to ISO 10628).*  

The two-stage methanation plant consists of the following units for methane production:  

- **1<sup>st</sup> methanation stage with steam generation**:  
  The first methanation stage is a fixed-bed reactor filled with Ni catalyst particles. It converts most of the H<sub>2</sub> and CO(<sub>2</sub>) into CH<sub>4</sub>, generating the majority of the reaction heat. To manage this heat, the reactor is actively and directly cooled using specialized heat pipes. These heat pipes transfer the reaction heat from the fixed bed to a steam generator, which utilizes it to evaporate water and produce steam. The reactor temperature is controlled by adjusting the water flow to the steam generator:  
  - A high water flow increases reactor cooling, lowering the reaction temperature.  
  - A low water flow decreases cooling, raising the reaction temperature.  

- **2<sup>nd</sup> methanation stage**:  
  The second methanation stage further enhances methane production by converting residual H<sub>2</sub> and CO(<sub>2</sub>), upgrading the gas quality to meet gas grid injection requirements (CH<sub>4</sub> concentration > 95%). Since the conversion rate and heat release are lower than in the first stage, this reactor does not require heat pipe cooling. Instead, two alternative cooling methods are employed:  
  - Heat dissipation through the reactor walls, which lowers the temperature in the outer bed regions.  
  - Water content control in the inlet stream via an intermediate condenser, which regulates conversion rates. A high water concentration (a byproduct of methanation) shifts the chemical equilibrium toward the reactants, reducing conversion and heat generation.  

- **Condenser for intermediate water removal**:  
  This shell-and-tube heat exchanger regulates gas temperature and partial water condensation by adjusting the cooling water flow. Consequently, it controls the water content of the feed gas entering the second methanation stage, helping manage reaction temperatures.  

- **Condenser for product gas cooling**:  
  Also designed as a shell-and-tube heat exchanger, this condenser cools and dries the final product gas of the methanation plant.  

- **Gas control**:  
  The methanation plant features a gas control system with two mass flow controllers (MFCs). Since the plant has been used for direct methanation of biogas (containing up to 55% CO<sub>2</sub>) in the *Power-to-Biogas* and *Kläffizient* projects, the system includes one MFC for hydrogen and another for biogas regulation.  

- **Water supply**:  
  The water supply system ensures tap water deionization for steam generation and condenser cooling. It comprises a storage tank and a diaphragm pump that precisely doses water from the storage tank to the steam generator. 

- **Sensors and actuators**:  
  The methanation plant is equipped with 98 sensors that monitor temperature, pressure, gas concentrations, and water levels. Additionally, it features:  
  - 49 valves for gas and water control.  
  - 11 electric trace heating elements for individual components.  

  The electric trace heating system is essential for reactor preheating during startup and preventing water from freezing when ambient temperatures drop below the freezing point.  


### Programmable logic controller 

**PLC_Meth** includes multiple programming scripts developed in *Structured Text*, a PLC programming language defined by IEC 61131-3. These scripts run at high frequencies (0.001–0.1 seconds) on the PLC.  

Note that the source code provides only the software logic for plant automation, including the definition of global types, variables, and error messages. However, it does not include the hardware-specific variable assignments for the PLC's I/O block, as these configurations may vary for different plants.  

The code consists of 11 programming scripts:  

| Script | Description |  
|-----------|------------|  
| **Condenser_Control** | Manages cooling water flow, water levels, and condensate removal. |  
| **Data_Processing** | Converts analog and digital signals between the I/O hardware block and computational values used within the scripts. |  
| **Gas_Analysis** | Controls gas measurements, as the plant can measure only one gas stream at a time (reactant gas, gas after the first stage, or product gas). |  
| **Gas_Control** | Regulates feed gas flow rates through the MFCs. |  
| **Pressure_Control** | Maintains the plant's operating pressure. |  
| **Safety_Mechanisms** | Defines safety protocols to ensure stable and secure plant operation within temperature and pressure limits. |  
| **Steam_Generator** | Controls the diaphragm pump for water supply to the steam generator and regulates cooling for the first methanation stage. |  
| **System_Status** | Defines the plant's operational states, including idle, startup (lab/field), operation (lab/field), cooldown (lab/field), error state, hot standby, and manual operation. |  
| **Temperature_Control** | Manages the electric trace heating elements used to heat the reactors and peripheral components. |  
| **Visualization** | Processes data for visualization of temperature profiles on the Human-Machine Interface (HMI). |  
| **Water_Supply** | Controls water supply valves and regulates water levels in condensate traps. |  

Most control methods for setpoint tracking (*regulatory control*) in **PLC_Meth** use proportional-integral-derivative (PID) controllers, which have been manually tuned to achieve optimal performance in the plant.  

For full automation, **PLC_Meth** also employs *rule-based control* for *dynamic optimization* of startup, load changes, and cooldown processes. This *rule-based control* determines actions based on the maximum catalyst temperature in the first methanation stage, a critical parameter for overall plant control. This temperature:  
- Reflects reactor heating progress during startup.  
- Indicates changes in the temperature profile during load adjustments.  
- Monitors reactor cooling during cooldown.  

To regulate reactor temperatures, the plant controls heat pipe cooling in the first methanation stage and water content in the second stage. The temperature thresholds are determined by two catalyst deactivation phenomena that occur during the direct methanation of biogas:  
1. **Sintering** (occurs at temperatures > 883 K): A thermally induced process that alters catalyst structure, leading to a loss of active surface area.  
2. **Carbon Formation**: A phenomenon influenced by gas composition and temperature. Carbon deposits can form if the temperature exceeds the *critical temperature for carbon formation* (T<sub>crit</sub>).  

The value of T<sub>crit</sub> is derived from chemical equilibrium data at specific gas compositions and pressures. **PLC_Meth** incorporates an empirical equation, developed using a large equilibrium dataset and *linear regression with nonlinear basis functions* (*Supervised Learning*), to estimate this threshold.  

The plant's HMI consists of a panel interface located on the front of the control cabinet, which can also be accessed remotely via a web browser. The visualization, featuring dedicated pages for various control functions, has been designed using *mapp View*.  

![Panel](img/Panel.png)

*Figure 3: Human-Machine Interface of the methanation plant.*  

The plant's alarm system is implemented using *mapp AlarmX* and is based on warning and error signals generated by the `Safety_Mechanisms` script.  

---

## Project Structure

The project is organized into the following directories and files:

```plaintext
PLC_Meth/
│
├── img/
│
└── src/
    ├── Condenser_Control/
    ├── Data_Processing/
    ├── Gas_Analysis/
    ├── Gas_Control/
    ├── Pressure_Control/
    ├── Safety_Mechanisms/
    ├── Steam_Generator/
    ├── System_Status/
    ├── Temperature_Control/
    ├── Visualization/
    ├── Water_Supply/
    ├── Global.typ
    ├── Global.var
    └── LocalizableTexts.tmx

```

Each folder in `src/` corresponds to a specific programming script and contains the following files: 
- `IEC.prg`: Defines the structure and components of the program. 
- `main.ab`: The main script.  
- `Types.typ`: Defines local types.  
- `Variables.var`: Declares local variables.  

Additionally, `src/` includes two global files:  
- `Global.typ`: Defines global types.  
- `Global.var`: Declares global variables.  

The `.var` files contain not only process-related variables but also constants and special *function blocks* (`FB`) imported from external libraries. To ensure consistency and readability, variable notation follows the syntax below, with variables written in lowercase and constants in uppercase:  

`<global/local>_<type>_<description>` 

| Syntax | Description |  
|-----------|------------|  
| `global/local` | Global and local variables/constants are prefixed with `g`/`G` and `l`/`L`, respectively. |  
| `type` | Variable/constant types are indicated as `b`/`B` (boolean), `i`/`I` (integer), and `r`/`R` (real), or as special types defined in `Global.typ`. |  
| `description` | A short description or abbreviation identifying the variable/constant. |  

The alarm system, implemented using *mapp AlarmX*, requires a text message for each warning and error. These messages are stored in `LocalizableTexts.tmx`.  

---

## Installation and Usage

Follow these steps to load and use the project: 

```bash
# Clone the repository
git clone https://github.com/SimMarkt/PLC_Meth.git

# Navigate to the project directory
cd PLC_Meth

# Copy the source code to your PLC software project folder
cp -r src/* /path/to/your/plc/project/folder/
```

If you are using a PLC system other than those from B&R Industrial Automation GmbH, modifications to the source code
may be required.
Additionally, variables must be mapped to the appropriate hardware modules in the I/O block.

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

[1] S. Markthaler, "*Katalytische Direktmethanisierung von Biogas: Demonstration
in industrieller Umgebung und Betriebsoptimierung mittels Reinforcement
Learning*", DECHEMA Jahrestreffen der Fachsektion Energie, Chemie
und Klima (11.-12.03.), Frankfurt/Main, 2024

[2] S. Markthaler, "*Optimization of Power-to-Gas operation and dispatch using Deep Reinforcement Learning*", Dissertation (PhD Thesis), Friedrich-Alexander-Universität Erlangen-Nürnberg, 2025 (not yet been published).

[3] S. Rönsch, J. Schneider, S. Matthischke, M. Schlüter, M. Götz, J. Lefebvre, P. Prabhakaran, S. Bajohr, "*Review on methanation – From fundamentals to current projects*", Fuel, 166, 2016, 276–296.

[4] M. Götz, J. Lefebvre, F. Mörs, A. McDaniel Koch, F. Graf, S. Bajohr, R. Reimert, T. Kolb, "*Renewable Power-to-Gas: A technological and economic review*", Renewable Energy, 85, 2016, 1371–1390

[5] S. Markthaler, F. Grimm, J. Karl, “Direct catalytic methanation of Biogas (DMB):
From laboratory experiments to demonstration scale”, 4th Doctoral Colloquium
BIOENERGY (13.-14.09.), Karlsruhe, 2021


---

## Acknowledgments

This project was part of the *Energie Campus Nürnberg* project (EnCN<sup>2</sup> Speicher A TP SP3; funded by the Bavarian State Government) and the *Power-to-Biogas* project (Project ID: 03KB165; funded by the German Federal Ministry for Economic Affairs and Climate Action). 

---