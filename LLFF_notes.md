# LLFF_Notes

[LLFF](https://github.com/fyusion/llff)

## Starting the docker container

Everything runs in a downloadable docker container. In the instructions, it says to use the "nividia-docker" command but I had some problems getting this client tool to work. I downloaded docker and the nvidia-docker-toolkit. I used the following command in the LLFF directory.

    sudo docker run --gpus=all --runtime=nvidia -it --rm --volume /:/host --workdir /host$PWD tf_colmap

This avoids needing the nvidia-docker client to run. The gpus and runtime set manually. 

## To make poses for nerf (inside docker container)

    python imgs2poses.py <scenedir>

Images should be put into an /images directory inside of /scenedir directory. ( /scenedir/images)

An example result on an example test scene.

## Results for test_scene

    root@aaed70b8c6b3:/host/home/sharedFolder/LLFF# python imgs2poses.py data/testscene
    Don't need to run COLMAP
    Post-colmap
    ('Cameras', 5)
    ('Images #', 20)
    ('Points', (9871, 3), 'Visibility', (9871, 20))
    ('Depth stats', 13.715656761716307, 119.32504976165527, 30.383946355797367)
    Done with imgs2poses

colmap_output.txt

    Bundle adjustment report
    ------------------------
        Residuals : 86366
       Parameters : 29728
       Iterations : 4
            Time : 0.237906 [s]
     Initial cost : 0.797628 [px]

     Final cost : 0.79745 [px]
    Termination : Convergence

      => Merged observations: 0
      => Completed observations: 0
      => Filtered observations: 1
      => Changed observations: 0.000023
      => Filtered images: 0

    Elapsed time: 0.317 [minutes]

### Input to nerf

The scene directory from the previous step can be passed to the nerf model by adding the following arguments to the nerf .config file

    datadir = <scenedir>
    dataset_type = llff


## Results for root_scene0.0 (first image set)

For this dataset, roots were rotated with the background and camera stationary. The results of pose generation from that data using LLFF are show below.
    
    root@aaed70b8c6b3:/host/home/sharedFolder/LLFF# python imgs2poses.py ../Datasets/root_scene0.0
    Don't need to run COLMAP
    Post-colmap
    ('Cameras', 5)
    ('Images #', 2)
    ERROR: the correct camera poses for current points cannot be accessed
    Done with imgs2poses   

colmap_output.txt:

    Bundle adjustment report
    ------------------------
        Residuals : 360
       Parameters : 277
       Iterations : 101
             Time : 0.0698991 [s]
     Initial cost : 0.523496 [px]
       Final cost : 0.419109 [px]
      Termination : No convergence

      => Filtered observations: 0
      => Filtered images: 0

    Elapsed time: 0.003 [minutes]

There was no convergence.

I believe that this occurred because of of the image gathering method. For this dataset, the images were taken by rotating the object of interest and keeping the camera fixed. Data could be gathered again following the instructions [here](https://github.com/fyusion/llff#using-your-own-input-images-for-view-synthesis)