Unlike the render and paste approach ([examples/render_and_paste](examples/render_and_paste)), HDRI provides realistic background and lighting information to the scene.

You can think of it as an orb in 3D space that encapsulates your scene.

<img src="../../assets/HDRI_example.png" alt="render" width="300"/> 


You are provided with lots of different HDRI's in the `datashare/haven` folder.
Use the following code to apply a random HDRI to the scene:

```python
# Set a random hdri from the given haven directory as background
haven_hdri_path = bproc.loader.get_random_world_background_hdr_img_path_from_haven(args.haven_path)
bproc.world.set_world_background_hdr_img(haven_hdri_path)
```

Make sure to disable background transparency
```python
bproc.renderer.set_output_format(enable_transparency=False)
```
