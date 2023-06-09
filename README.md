# An in depth look at CARLA's sensors

## Requirement

- CMake(https://cmake.org/download/)
- Git(https://git-scm.com/downloads)
- Make(https://gnuwin32.sourceforge.net/packages/make.htm) (https://dadev.tistory.com/entry/%EC%8A%A4%ED%81%AC%EB%9E%A9-Windows%EC%97%90%EC%84%9C-make-%EB%AA%85%EB%A0%B9%EC%96%B4-%EC%84%A4%EC%B9%98-%EB%B0%8F-%EC%9D%B4%EC%9A%A9%EB%B0%A9%EB%B2%95)
- 7Zip(https://www.7-zip.org/)
- Python 3.8(https://www.python.org/downloads/)
- Windows 8.1 SDK(https://developer.microsoft.com/ko-kr/windows/downloads/sdk-archive/)
- Visual Studio 2019(https://learn.microsoft.com/ko-kr/visualstudio/releases/2019/release-notes) (https://jeo96.tistory.com/entry/CARLA-%EC%84%A4%EC%B9%98-0913-Windows-10)

## Get authority fron EpicGames github
- https://www.unrealengine.com/en-US/ue-on-github

## Installation

#### Unreal Engine
```python
git clone --depth 1 -b carla
https://github.com/CarlaUnreal/UnrealEngine.git .
```

#### CARLA
```python
# cd the directory where you want to install
git clone https://github.com/carla-simulator/carla

# cd directory/carla
Update.bat
```

#### Create Environment Variables
<img width="500" alt="image" src="https://github.com/mellamonaranja/SelfDrive/assets/55770526/2885c130-a222-4581-8942-87fce3e868e2">

#### Implement PythonAPI 
```python
# cd directory/carla
make PythonAPI
```

#### Build CARLA
```python
# cd directory/carla
make launch
```

#### Implement CarlaUE4
```python
# cd directory/carla/Unreal/CarlaUE4
```

<img width="500" alt="image" src="https://github.com/mellamonaranja/SelfDrive/assets/55770526/07729eff-d86e-41dd-aced-44cfe43b446f">

## Sensors

- RGB camera

The "RGB" camera acts as a regular camera capturing images from the scene.

```python
camera_bp=bp_lib.find('sensor.camera.rgb')
camera_init_trans=carla.Transform(carla.Location(z=2))
camera = world.spawn_actor(camera_bp, camera_init_trans, attach_to=vehicle)
```

<img width="500" alt="basic_camera1" src="https://github.com/mellamonaranja/SelfDrive/assets/55770526/c576e088-59df-46e8-aaa4-37b4eaed8ff6">

Save to disk.

```python
cc = carla.ColorConverter.CityScapesPalette
camera.listen(lambda image:image.save_to_disk('output/%06d.png' % image.frame, cc))
```

<img width="500" alt="basic_camera2" src="https://github.com/mellamonaranja/SelfDrive/assets/55770526/5010f184-b075-412a-a529-cee054c53397">

# 

- Semantic segmentation camera

This camera classifies every object in sight by displaying it in a different color according to its tags (e.g., pedestrians in a different color than vehicles). When the simulation starts, every element in scene is created with a tag. So it happens when an actor is spawned. 

- Instance segmentation camera

This camera classifies every object in the field of view both by class and also by instance ID. When the simulation starts, every element in scene is created with a tag. So it happens when an actor is spawned.

- Depth camera

The camera provides a raw data of the scene codifying the distance of each pixel to the camera (also known as depth buffer or z-buffer) to create a depth map of the elements.

The image codifies depth value per pixel using 3 channels of the RGB color space, from less to more significant bytes: R -> G -> B.

- DVS(Dynamic Vision Sensor) camera

A Dynamic Vision Sensor (DVS) or Event camera is a sensor that works radically differently from a conventional camera. Instead of capturing intensity images at a fixed rate, event cameras measure changes of intensity asynchronously, in the form of a stream of events, which encode per-pixel brightness changes. Event cameras possess distinct properties when compared to standard cameras. They have a very high dynamic range (140 dB versus 60 dB), no motion blur, and high temporal resolution (in the order of microseconds). Event cameras are thus sensors that can provide high-quality visual information even in challenging high-speed scenarios and high dynamic range environments, enabling new application domains for vision-based algorithms.

- Optical Flow Camera

The Optical Flow camera captures the motion perceived from the point of view of the camera. Every pixel recorded by this sensor encodes the velocity of that point projected to the image plane. The velocity of a pixel is encoded in the range [-2,2]. To obtain the motion in pixel units, this information can be scaled with the image size to [-2 * image_size, 2 * image_size].


![Untitled video - Made with Clipchamp](https://github.com/mellamonaranja/SelfDrive/assets/55770526/83db87a6-b06f-465e-baf4-fa6f472c517b)

# 

- LIDAR sensor

This sensor simulates a rotating LIDAR implemented using ray-casting. The points are computed by adding a laser for each channel distributed in the vertical FOV. The rotation is simulated computing the horizontal angle that the Lidar rotated in a frame.

- Radar sensor

The sensor creates a conic view that is translated to a 2D point map of the elements in sight and their speed regarding the sensor. This can be used to shape elements and evaluate their movement and direction. Due to the use of polar coordinates, the points will concentrate around the center of the view.

![Untitled video - Made with Clipchamp (2)](https://github.com/mellamonaranja/SelfDrive/assets/55770526/aa1781b5-276e-48c3-bd4c-8a8bd5573cc0)

# 

- GNSS sensor

Reports current gnss position of its parent object. This is calculated by adding the metric position to an initial geo reference location defined within the OpenDRIVE map definition.

- IMU sensor

Provides measures that accelerometer, gyroscope and compass would retrieve for the parent object. The data is collected from the object's current state.

- Collision detector

This sensor registers an event each time its parent actor collides against something in the world. Each collision sensor produces one collision event per collision per frame. Multiple collision events may be produced in a single frame by collisions with multiple other actors. To ensure that collisions with any kind of object are detected, the server creates "fake" actors for elements such as buildings or bushes so the semantic tag can be retrieved to identify it.

- Lane invasion detector

Registers an event each time its parent crosses a lane marking. The sensor uses road data provided by the OpenDRIVE description of the map to determine whether the parent vehicle is invading another lane by considering the space between wheels.

- Obstacle detector

Registers an event every time the parent actor has an obstacle ahead. In order to anticipate obstacles, the sensor creates a capsular shape ahead of the parent vehicle and uses it to check for collisions. To ensure that collisions with any kind of object are detected, the server creates "fake" actors for elements such as buildings or bushes so the semantic tag can be retrieved to identify it.

![Untitled video - Made with Clipchamp (4)](https://github.com/mellamonaranja/SelfDrive/assets/55770526/6776c152-01f9-4e84-8c0a-5c509128c0c7)
# 

## Positioning

Put the car in diffrent positions.

```python
#drop the Mini from the sky
mini=world.try_spawn_actor(mini_bp[0], spawn_points[10])
mini_pos=carla.Transform(start_point.location+carla.Location(x=0,z=10),
                             carla.Rotation(yaw=start_point.rotation.yaw-0))
mini.set_transform(mini_pos)
```

![Untitled video - Made with Clipchamp (5)](https://github.com/mellamonaranja/SelfDrive/assets/55770526/4a757f8e-8def-40e2-a97f-d060446235d7)

