# L2L Challenge Example Submission
This is an example for how to prepare a valid submission for the MICCAI L2L Challenge

It is based on the [pretrained imagenet](https://github.com/StefanoWoerner/mimeta-pytorch/blob/master/examples/imagenet-pretrained.py)
example from [MIMeta](https://github.com/StefanoWoerner/mimeta-pytorch)
and process input images and generates predictions by fine-tuning a pretrained
network on the target task.

A real submission will likely be more complex and probably contain multiple python
modules, but this example should give you a good starting point.

Please also note that in this example we aimed for clean and simple code, not runtime
efficiency. A more efficient fine-tuning loop would allow for more steps and a larger
network, given the available evaluation time.

## How to replicate this example

### Prerequisites
- Python 3.10
- pip
- singularity

### Install dependencies

Create a new environment with python>=3.10 and install the dependencies:
```bash
pip install -r requirements.txt
```

### Download the model
In the `example_submission` directory, run `download.py` to download the model. This
allow us to put the model in the container and avoid downloading it at evaluation time.
Containers will not have access to the internet on the evaluation server.

```bash
python download.py
```

### Build the singularity container

Still in the `example_submission` directory, run the following command to build the
singularity container:
```bash
singularity build --fakeroot --force example-submission.sif singularity.def
```

### Submit

- Create an account on the [L2L challenge portal](https://portal.l2l-challenge.org/)
  and subsequently a team.
- Go to the [submission page](https://portal.l2l-challenge.org/submission) and submit
  the `example-submision.sif` file.


## Container Specifications for your own containers
- Containers will be run with the following command:
```bash
singularity run --fakeroot --net --network none --containall --no-home --bind $INPUT_PATH:$INTERNAL_INPUT_PATH:ro --bind $RESULT_PATH:$INTERNAT_RESULT_PATH $CONTAINER_PATH $INTERNAL_INPUT_PATH $INTERNAL_RESULT_PATH
```
- Containers cannot write in any directories except for ´/tmp´. Containers can also write to
  the bound output file.
- The input file and output file are bound to directories in the root folder with randomly generated names. The
  inout and output locations are passed to the run script as the first two arguments.
- The input pkl file contains a [TorchCross](https://github.com/StefanoWoerner/torchcross)
  [`Task`](https://github.com/StefanoWoerner/torchcross/blob/master/torchcross/data/task.py) which
  contains an uncollated list of data points, i.e. a list of pairs of tensors, as the support dataset.
  The query set is the same in structure but does not contain labels.
- The output is expected to be saved as a pkl file containing a tensor of predictions or a tensor of probabilities

## Authors
Stefano Woerner, Bartłomiej Baranowski
