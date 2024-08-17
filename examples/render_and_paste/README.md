This is an augmented https://github.com/DLR-RM/BlenderProc/tree/main/examples/advanced/random_backgrounds example.

In this example we render the objects on a transparent background (in .PNG format) and then we paste a background image behind them.

<img src="../../assets/copy_paste_examples.png" alt="render" width="900"/> 

**Main code explanation:**

## Step 1: Loading and setting up the 3D model

We load the 3D model and set up its materials:

```python
obj = bproc.loader.load_obj(args.scene)[0]
obj.set_cp("category_id", 1)
# obj.hide(True)
```

**Explanation**: The above code loads a 3D model in .obj format and assigns a category ID, which is used for segmentation labeling. You can hide objects if they aren’t required in the final render.

## Step 2: Set the object materials

```python
# Randomly perturbate the material of the object
mat = obj.get_materials()[0] # needle holder metal color
mat.set_principled_shader_value("Specular", random.uniform(0, 1))
mat.set_principled_shader_value("Roughness", random.uniform(0, 1))
mat.set_principled_shader_value("Metallic", 1)
mat.set_principled_shader_value("Roughness", 0.2)
```

**Explanation**: This sets the material properties such as Specular, Roughness, and Metallic using the Principled BSDF shader.
Some object have more than one material. For example, the needle holder has one material for the metalic part (`mat = obj.get_materials()[0]`) and another material for the golden part (`mat = obj.get_materials()[1]`).

For more information on material properties, refer to the [Blender manual](https://docs.blender.org/manual/en/latest/render/shader_nodes/shader/principled.html).

## Step 3: Setting up lighting

We create a point light and position it randomly around the object:

```python
light = bproc.types.Light()
light.set_type("POINT")
light.set_location(bproc.sampler.shell(
    center=obj.get_location(),
    radius_min=1,
    radius_max=5,
    elevation_min=1,
    elevation_max=89
))
light.set_energy(random.uniform(100, 1000))
```

**Explanation**: This step adds a point light and positions it randomly, providing variation in lighting conditions. Other light types can be explored in the [Blender manual](https://docs.blender.org/manual/en/latest/render/lights/light_object.html).

## Step 4: Camera setup and positioning

We set up the camera resolution and create multiple camera poses:

```python
bproc.camera.set_resolution(640, 480)

poses = 0
tries = 0
while tries < 10000 and poses < num_images:

    # Sample random camera location and rotation
    location = bproc.sampler.shell(
        center=obj.get_location(),
        radius_min=2,
        radius_max=10,
        elevation_min=-90,
        elevation_max=90
    )
    lookat_point = obj.get_location() + np.random.uniform([-0.5, -0.5, -0.5], [0.5, 0.5, 0.5])
    rotation_matrix = bproc.camera.rotation_from_forward_vec(lookat_point - location, inplane_rot=np.random.uniform(-0.7854, 0.7854))
    cam2world_matrix = bproc.math.build_transformation_mat(location, rotation_matrix)

    # Add camera pose if object is visible
    if obj in bproc.camera.visible_objects(cam2world_matrix):
        bproc.camera.add_camera_pose(cam2world_matrix, frame=poses)
        poses += 1
    tries += 1
```

**Explanation**: This code positions the camera randomly around the object while ensuring the object is visible in each pose. The sampler.shell function mimics the Horizontal coordinate system, controlling camera distance, elevation, and azimuth.

The `sampler.shell` function  is conceptually similar to the Horizontal coordinate system. Let's break it down:
The Horizontal coordinate system uses two main coordinates:


<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/f/f7/Azimuth-Altitude_schematic.svg/1024px-Azimuth-Altitude_schematic.svg.png" alt="BlenderProc" width="255"/>


Altitude (or Elevation): The angular distance above or below the horizon.
Azimuth: The angle along the horizon from a fixed reference point (usually true north).

In this code:

Elevation is directly represented by the elevation_min and elevation_max parameters.
Azimuth is implicitly sampled across the full 360° range.
Distance (not part of the horizontal system but necessary in 3D space) is represented by radius_min and radius_max.

## Step 5: Rendering settings

We set up the rendering parameters:

```python
bproc.renderer.set_max_amount_of_samples(100)
bproc.renderer.set_output_format(enable_transparency=True)
bproc.renderer.enable_segmentation_output(map_by=["category_id", "instance", "name"])
```

**Explanation**: This configures the renderer to generate images with transparency and segmentation masks. Reducing the sample count can speed up the rendering process.

## Step 6: Rendering and saving data

Finally, we render the images and save the data in COCO format:

```python
data = bproc.renderer.render()

bproc.writer.write_coco_annotations(os.path.join(args.output_dir, 'coco_data'),
                        instance_segmaps=data["instance_segmaps"],
                        instance_attribute_maps=data["instance_attribute_maps"],
                        colors=data["colors"],
                        mask_encoding_format="polygon",
                        append_to_existing_output=True)
```

**Explanation**: This step renders the images and saves the annotations in COCO format, commonly used for object detection and segmentation.

## Step 7: Paste the background
Finally, overlay the rendered objects onto random background images:

```bash
python paste_on_random_background.py
```
**Explanation**: Use the [paste_on_random_background.py](paste_on_random_background.py) script to add background images behind the rendered objects, completing the image generation process.

