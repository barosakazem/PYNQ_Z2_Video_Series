# PYNQ_Z2_Video_Series
# 🎩 PYNQ-Z2 Video Processing Project Guide: Test Pattern Generator to HDMI

## 🔥 Overview

This project creates a basic video processing pipeline on the PYNQ-Z2 board, outputting a test video pattern via HDMI using Vivado.

## ✅ Prerequisites

* Xilinx Vivado (e.g., 2020.1 or 2020.2)
* PYNQ-Z2 board files installed
* Basic knowledge of Vivado block design

## 📁 Step 1: Set Up Vivado for PYNQ-Z2

### ✔️ Add Board Files

1. Download board files: [PYNQ-Z2 Board Files (GitHub)](https://github.com/xupsh/pynq-supported-board-file/tree/master)
2. Copy the `pynq-z2` folder to:

   * **Windows**: `C:\Xilinx\Vivado\<version>\data\boards\board_files\`
   * **Linux**: `/opt/Xilinx/Vivado/<version>/data/boards/board_files/`
3. Restart Vivado. You should now see `PYNQ-Z2` listed in the board selection.

> 💡 **Tip:** Ensure the folder structure is correct: `board_files/pynq-z2/1.0/board.xml`

## 🛠️ Step 2: Create New Vivado Project

1. Open Vivado
2. Select **Create New Project**
3. Set a project name and location
4. Choose **RTL Project** and enable "Do not specify sources at this time"
5. In the "Default Part" window, go to **Boards** and select **PYNQ-Z2**

## 🧱 Step 3: Block Design — Test Pattern Generator to HDMI

## Preparation: Add Digilent Vivado Libraries for RGB2DVI IP

The `rgb2dvi` IP core used in this project is part of the Digilent Vivado IP library. Before creating your Vivado project, make sure to add these IP cores to your Vivado environment.

### Steps to add Digilent Vivado Libraries:

1. Clone or download the Digilent Vivado library repository:

   ```bash
   git clone https://github.com/Digilent/vivado-library.git

### 🎛 3.1 Create Block Design

1. In the Flow Navigator, go to **IP Integrator → Create Block Design**
2. Name your design (e.g., `video_pipeline`)

### 🔌 3.2 Add Required IP Blocks

| IP Name                           | Description                                       |
| --------------------------------- | ------------------------------------------------- |
| **ZYNQ7 Processing System**       | Required for clocking and control                 |
| **Video Timing Controller (VTC)** | Manages video sync signals                        |
| **Test Pattern Generator (TPG)**  | Generates test video signal                       |
| **AXI4-Stream to Video Out**      | Converts AXI stream to video signals              |
| **RGB to DVI**                    | HDMI output interface (can be HDL or Digilent IP) |

> 💡 **Note:** PYNQ-Z2 uses **ADV7511 HDMI TX** chip; use `rgb2dvi` IP or your own HDL bridge.

### 🔗 3.3 Connect Blocks

#### ✔️ Configure Zynq Processing System

* Run **"Run Block Automation"** and select **"Presets for PYNQ-Z2"**

#### ✔️ Add Clocking

* Use `FCLK_CLK0` (typically 100 MHz) from ZYNQ PS for all IPs

#### ✔️ TPG Connections

| TPG Port       | Connect To                                 |
| -------------- | ------------------------------------------ |
| `m_axis_video` | `s_axis_video` of AXI4-Stream to Video Out |
| `fid`          | Constant 0                                 |
| `ap_ctrl`      | Constant or AXI Lite (if control needed)   |

#### ✔️ VTC Connections

| VTC Port  | Connect To                           |
| --------- | ------------------------------------ |
| `vtc_out` | `vtg_in` of AXI4-Stream to Video Out |

#### ✔️ AXI4-Stream to Video Out Connections

| Port                                     | Connect To                 |
| ---------------------------------------- | -------------------------- |
| `vid_io_out`                             | RGB signals to RGB2DVI     |
| `active_video`, `vblank`, `hblank`, etc. | HDMI bridge (RGB2DVI)      |
| `aclken`                                 | Constant 1                 |
| `aresetn`                                | System reset or constant 1 |

#### ✔️ RGB to HDMI (RGB2DVI)

* Connect RGB outputs from AXI4-Stream to HDMI pins via `rgb2dvi`
* Output goes to **HDMI OUT** on PYNQ-Z2

### 🔄 3.4 Clock & Reset

* All IPs should use `FCLK_CLK0`
* Connect reset signals to processor reset or a constant logic high for testing

### 🔀 3.5 Validate and Generate

1. Click **Validate Design**
2. Click **Generate Bitstream**

## 📄 Step 4: Export for PYNQ

1. Export hardware: `File → Export → Export Hardware (Include Bitstream)`
2. Use `.xsa` to build a PYNQ overlay later

## 💪 Test Output

* Connect **HDMI OUT** from PYNQ-Z2 to a monitor
* You should see a test pattern (color bars, checkerboard, etc.)

## 📝 Notes

* Package `.bit`, `.hwh`, and Python files to build a PYNQ overlay
* You can configure TPG through AXI Lite for dynamic pattern switching
* Add ILA probes to debug video and AXI interfaces if needed

## ✅ Summary Checklist

| Step                                | Status |
| ----------------------------------- | ------ |
| PYNQ-Z2 Board files added           | ☑️     |
| Project created with board selected | ☑️     |
| Block design completed              | ☑️     |
| Bitstream generated                 | ☑️     |
| Exported `.xsa`                     | ☑️     |


