# Tutorial: Generating Synthetic Data with Python, Blender, and BlenderProc

## Introduction to Blender and BlenderProc

Before we dive into the code, let's understand the tools we're using:

### What is Blender?

Blender is a free and open-source 3D creation suite. It supports the entire 3D pipeline—modeling, rigging, animation, simulation, rendering, compositing and motion tracking, video editing and 2D animation pipeline. For our purposes, we're primarily interested in its **rendering** capabilities.

<img src="assets/blender_render-1280x720.jpg" alt="Blender" width="700"/>

Dont worry, we wont be using Blender directly, but through its Python API.


### What is BlenderProc?

BlenderProc is an open-source project for creating synthetic data using Blender and Python. It's designed to make the process of generating large datasets for computer vision tasks easier and more efficient. As you will see, it simplifies many of the complex tasks involved in synthetic data generation, especially for tasks like object detection, segmentation, and pose estimation...

<img src="https://user-images.githubusercontent.com/6104887/137109535-275a2aa3-f5fd-4173-9d16-a9a9b86f66e7.gif" alt="BlenderProc" width="700"/>

https://github.com/DLR-RM/BlenderProc

## Installation

Its highly recommended to have a compeletely seperate Python environment for Synthetic data generation.
We will install blenderproc as in the blenderproc readme instructions. Make sure you have conda installed.

```
conda create -n synth python=3.10
conda activate synth
pip install blenderproc
```

The following will automatically install Blender for us:
```
blenderproc quickstart
```

## Usage

BlenderProc has to be run inside the blender python environment, as only there we can access the blender API. Therefore, instead of running your script with the usual python interpreter, the command line interface of BlenderProc has to be used.
```
blenderproc run <your_python_script>
```
For this reason you will find that you cannot simply debug through your IDE. For debugging instructions refer to the `Breakpoint-Debugging in IDEs` section in the official [BlenderProc README](https://github.com/DLR-RM/BlenderProc).

## Examples

The Blenderproc repository has many useful examples in the [examples folder](https://github.com/DLR-RM/BlenderProc/tree/main/examples). But in this repo you will find examples that are targeted for our task.

- Render and paste on backgrounds: [examples/render_and_paste](examples/render_and_paste)
- Render on HDRI maps: 





