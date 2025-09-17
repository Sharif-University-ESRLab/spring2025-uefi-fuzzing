# Code

Our work is heavily based on [FuzzUEr](https://github.com/BreakingBoot/FuzzUEr/). So instead of the code, we have the instructions to replicate the results as below.

## Preparing the Environment
First of all we need to prepare the environment. We use the docker images to have portability (You may need a proxy if running the build on a restricted network).
```
docker build -t fuzzuer-image .
```

We need to mount edk2 or any other eval sources while running the docker image.
```
docker run -it -v ./eval_source/:/input fuzzuer-image
```

## Static Analysis
Before running the harness generator and fuzzing software we need to perform an static analysis on the target codebase, namely the `edk2` library. So we will run the following script.
```
python firness.py -i /input/input.txt -s /input/edk2 -a
```

Make sure to uncomment following piece of code in `firness.py`.
```
    # if args.analyze or complete_analysis or args.generate:
    #     # compile the firmware to get compilation database
    #     log += get_compilation_database('/workspace/scripts', tmp_dir)
    #     log += run_firness(tmp_dir, output, input_file, args.smi)
```

## Perparing Inputs
First of all choose a protocol as the target of fuzzing. The available protocols are listed under `MdePkg/Include/Protocol`. Then edit the `input.txt` with the desired protcol's functions. The list of functions are available under the struct `_EFI_<PROTOCOL-NAME>_PROTOCOL`.

## Fuzzing
Then simple you can fuzz the desired protocol.
```
python firness.py -i /input/input.txt -s /input/edk2
```

## Results
After a while, the fuzzing converges and will not generate logs. Afterwards, interrupt the process and all results are under `firsness_output`. Here is the list of important files:
- `coverage.csv`: The datapoints which the number of convered edges changes with the time relative to start of fuzzing.
- `coverage.png`: The graph made of `coverage.csv`.
- `crashes.txt`: The bugs have beed found during the fuzzing.
- `stats.csv`: Statistics of the fuzzing in number fuzzed primitives.
