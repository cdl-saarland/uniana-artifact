# Artifact for "An Abstract Interpretation for SIMD Divergence on Reducible Control Flow Graphs"

## Proofs


## Instructions for the evaluation results

There is a docker image with various scripts to help you reproduce the runtime and analysis results of the paper.
You can reproduce our results with the Docker image in three main steps:
First, you build LLVM, the OpenCL driver, the AnyDSL compiler stack and the OpenCL benchmarks.
The OpenCL driver (pocl) and Thorin (AnyDSL) are modified to dump the compute kernel LLVM IR whenever an OpenCL application is run (or an AnyDSL application gets compiled).
In the second step, we use the compilers and OpenCL benchmarks from the first step to extract all simd kernels.
Finally, we run the divergence analysis tool (dacomp). 
This tool will directly produce the numbers reported in the paper.

For licensing reasons, we cannot provide SPEC ACCEL 1.3 within the image. However, there is a script to plug-in your own copy of the benchmark suite.

### Getting the docker image

TODO

### How to reproduce the results

1. Start the docker image in interactive mode (`docker run -ti <image>`).
2. Build all compilers, the OpenCL driver, OpenCL benchmark applications and the evaluation tool (dacomp) (`bash build.sh`).
3. Extract all SIMT kernels from the OpenCL benchmarks, Rodent and treelogy (`bash extract_kernels.sh`). The extracted kernels will be dumped to `~/workspace/extracted_kernels/`.
4. Copy the extracted kernels to the input directory of dacomp and evaluate all divergence analysis configuration (`bash evaluate_da.sh`).
5. Done! The final results for this run are in `workspace/dacomp/results`.
