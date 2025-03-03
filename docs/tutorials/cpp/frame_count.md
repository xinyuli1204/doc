---
sidebar_position: 6
---

# Retrieve Frame Count

In this tutorial, we learn how to get frame count from camera with ion-kit.

## Prerequisite

* OpenCV (installed with sensing-dev SDK with `-InstallOpenCV` option) 
* ion-kit (installed with sensing-dev SDK) 

## Tutorial

The process of setting up the pipeline is the exactly same as the one in the previous tutorials. We just need to set up an additional output port and buffer to obtain frame count from the BB.

### Get frame count 

While displaying image, we also want to retrieve the frame count information. The only difference from previous tutorials is that we need to bind the frame count value to a new port.

```c++
std::vector<Halide::Buffer<uint32_t>> frame_counts;
for (int i = 0; i < num_device; ++i){
    frame_counts.push_back(Halide::Buffer<uint32_t>(1));
}
n["frame_count"].bind(frame_counts);
```

### Execute the pipeline

Execute `builder.run()` to finish the pipeline.

Since frame count directory does to numpy array `frame_counts[i]` for ith device, you can print each framecount as follows:

```c++
std::cout << frame_counts[0](0) << std::endl;
```

## Complete code

Complete code used in the tutorial is [here](https://github.com/Sensing-Dev/tutorials/blob/main/cpp/src/tutorial3_getting_frame_count.cpp)

You can Use the CMakeLists.txt provided [here](https://github.com/Sensing-Dev/tutorials/blob/main/cpp/CMAKELists.txt) to compile and build the program.

:::caution why it does not work
* If you are using OpenCV that you install by yourself, please confirm that it is linked to your program appropriately.
:::