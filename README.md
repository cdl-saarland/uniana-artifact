# Artifact for "An Abstract Interpretation for SIMD Divergence on Reducible Control Flow Graphs"

## Proofs

The mechanized proofs can be found [here](https://github.com/cdl-saarland/uniana).
This repository includes a README explaining how to compile the Coq proofs.
The CoqDoc of the development is generatede by the included Makefile, but can also be investigated [here](https://compilers.cs.uni-saarland.de/projects/uniana/toc.html).


## Instructions for the evaluation results

There is a docker image with various scripts to help you reproduce the runtime and analysis results of the paper.
You can reproduce our results with the Docker image in three main steps:
First, you build LLVM, the OpenCL driver, the AnyDSL compiler stack and the OpenCL benchmarks.
The OpenCL driver (pocl) and Thorin (AnyDSL) are modified to dump the compute kernel LLVM IR whenever an OpenCL application is run (or an AnyDSL application gets compiled).
In the second step, we use the compilers and OpenCL benchmarks from the first step to extract the compute kernels for later analysis.
Finally, we run the divergence analysis tool (dacomp). 
The last script invocation will end with a rendering of the evaluation tables (Figure 5 and Figure 6).

#### Get the Docker image

TODO: URL

### Launch the Docker image

The docker image needs to be attached your X-Server (required for LuxMark).
This command will also open a bash inside the Docker container.
Keep it open for the next steps.

    docker run -ti --rm -e DISPLAY=${DISPLAY} -v /tmp/.X11-unix:/tmp/.X11-unix popl21_eval


### [Optional] Copy your ISO file of SPEC ACCEL 1.3  

For licensing reasons, we cannot provide SPEC ACCEL 1.3 with the Docker image. However, there is a script to plug-in your own copy of the benchmark suite.
If you have the SPEC ACCEL 1.3 ISO file, run the following commands outside the Docker container to load and install it inside the running Docker container.
   
Query the ID of the running Docker container (from a shell that is running outside the container):
    
    docker ps

Then, also outside docker, use the following command to copy the SPEC ISO file into the container:

    docker cp <spec_accel_13.iso> <container_id>:/home/theuser/workspace/


Now, in the running container shell, run the following command to integrate SPEC ACCEL into the evaluation system:

    bash install_spec.sh

### Build Compilers and OpenCL host applications

Inside to the docker shell run

    bash build.sh

### Extract the Kernels

    bash extract_kernels.sh

### Extract the LuxMark Kernels

We provide the extracted LLVM IR for the LuxMark kernels in our image. 
To copy them to the dump folder, call:

    bash copy_luxmark_kernels.sh

(We provide instructions below on how to extract the LuxMark OpenCL IR kernels yourself)



### Transfer the dumped kernels to the analysis folder

This will copy the extracted SIMT kernels into the input folder of dacomp, the Divergence Analysis tool.

    bash transfer_kernels.sh 

### Evaluate the Divergence Analysis Configuration

Finally, run the next line to start the actual evaluation.
This will evaluate all divergence analysis configuration on the transfered kernel files.

    bash evaluate.sh

The variable `NUM_SAMPLES` is used in this script to configure the number of sample runs.
Make sure to turn off hyper threading and turbo boost to get better timing results.
When the script has finished (which may take a while for `NUM_SAMPLES=50`), it will print a result table similar to Figure 5 and 6 in the paper.
The table is stored in `~/workspace/result_tables.txt`.



### [Optional] LuxMark kernel extraction
We provide pre-extracted OpenCL kernels (as LLVM IR) for LuxMark since its build prerequesites and setup blow up the image substantially.
In case, you want to extract the kernels yourself, first make sure that you can run Docker OpenGL applications with your setup (the steps depend on your host system and gpu).
LuxMark is a GUI application and needs your input to launch the OpenCL driver.
To run the luxmark binary, launch the following script, which will already set it up with our OpenCL driver for kernel extraction:

    bash extract_luxmark_kernels.sh
   
Then, in the GUI, configure LuxMark for OpenCL over CPU and start it.
After a while all kernel modules have been exported.
You can end LuxMark as soon as it starts rendering.

