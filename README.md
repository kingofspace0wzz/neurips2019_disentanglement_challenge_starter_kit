![AIcrowd-Logo](https://raw.githubusercontent.com/AIcrowd/AIcrowd/master/app/assets/images/misc/aicrowd-horizontal.png)

# NeurIPS 2019 : Disentanglement Challenge Starter Kit

[![gitter-badge](https://badges.gitter.im/AIcrowd-HQ/disentanglement_challenge.png)](http://gitter.im/AIcrowd-HQ/disentanglement_challenge)

Instructions to make submissions to the [NeurIPS 2019 : Disentanglement Challenge](https://www.aicrowd.com/challenges/neurips-2019-disentanglement-challenge).

Participants will have to submit their code, with packaging specifications, and the evaluator will automatically build a docker image and execute their code against a series of secret datasets.

![](https://i.imgur.com/zr8RUr1.gif)

### Setup

- **Anaconda** (By following instructions [here](https://www.anaconda.com/download)) At least version `4.5.11` is required to correctly populate `environment.yml`.

* **Create your new conda environment**

```sh
conda create python=3.6 --name disentanglement_challenge
conda activate disentanglement_challenge
```

* **Your code specific dependencies**

```sh
# If say you want to install PyTorch
conda install pytorch torchvision -c pytorch
```

### Clone repository

```
git clone git@github.com:AIcrowd/neurips2019_disentanglement_challenge_starter_kit.git
cd neurips2019_disentanglement_challenge_starter_kit
pip install -r requirements.txt
```

### Download and Prepare Dataset

Follow the instructions [here](https://github.com/google-research/disentanglement_lib#downloading-the-data-sets) to download the publicly available datasets, and store them inside `./scratch/datasets` folder. Please ensure that the datasets are not checked into the repository.

### Train and Test Submission locally with Tensorflow / Pytorch / Numpy

#### Setup

The first three steps apply to all frameworks.
```
# Step 1:
cd neurips2019_disentanglement_challenge_starter_kit

# Step 2: 
# Make sure that your datasets live in "./scatch/dataset". 
# If they do not, either make sure they do (recommended)
# or change train_environ.sh accordingly. Finally:
source train_environ.sh

# Step 3:
# Enter the name of your experiment
export AICROWD_EVALUATION_NAME=myvae
# Enter the name of the dataset
export AICROWD_DATASET_NAME=cars3d
```

#### PyTorch
```
# Step 4 (PyTorch): 
# Train a VAE with Pytorch
python pytorch/train_pytorch.py
```
Take a look at the python file for available commandline arguments. 

#### Tensorflow
```
# Step 4 (TensorFlow)
# Train a VAE with TF and disentanglement_lib 
python tensorflow/train_tensorflow.py
```
Note: This is just a test submission which will only train for 10 steps (to keep training time short). To attain reasonable performance, about 300'000 training steps are recommended. This setting can be changed in `tensorflow/model.gin`.

#### NumPy
```
# Step 4 (NumPy): 
# This saves a hard-coded representation function to file.
python numpy/train_numpy.py
```

#### Evaluation
Finally, it's time to run the evaluation locally. This script will detect what framework you used, provided you used the right utility functions. 
```
Step 5:
# Run the local evaluation
python local_evaluation.py
```

# How do I specify my software runtime ?

The software runtime is specified by exporting your `conda` env to the root
of your repository by doing :

```
conda env export --no-build > environment.yml

# It might make sense here to remove the requirements.txt here to avoid confusion as environment.yml takes precedence over requirements.txt
```

This `environment.yml` file will be used to recreate the `conda environment`. This repository includes an example `environment.yml`

# What should my code structure be like ?

Please follow the structure documented in the included [train.py](https://github.com/AIcrowd/neurips2019_disentanglement_challenge_starter_kit/blob/master/train.py) to adapt
your already existing code to the required structure for this round.

## Important Concepts

### Repository Structure

- `aicrowd.json`
  Each repository should have a `aicrowd.json` with the following content :

```json
{
  "challenge_id": "aicrowd-neurips-2019-disentanglement-challenge",
  "grader_id": "aicrowd-neurips-2019-disentanglement-challenge",
  "authors": ["your-aicrowd-username"],
  "description": "sample description about your awesome agent",
  "license": "MIT",
  "gpu": true
}
```

This is used to map your submission to the said challenge, so please remember to use the correct `challenge_id` and `grader_id` as specified above.

Please specify if your code will a GPU or not for the evaluation of your model. If you specify `true` for the GPU, a **NVIDIA Tesla K80 GPU** will be provided and used for the evaluation.

### Packaging of your software environment

The recommended way is to use Anaconda configuration files using **environment.yml** files.

```sh
# The included environment.yml is generated by the command below, and you do not need to run it again
# if you did not add any custom dependencies

conda env export --no-build > environment.yml

# Note the `--no-build` flag, which is important if you want your anaconda env to be replicable across all
# Or if you have a rather simple softwar runtime, then even a :
#
# pip freeze > requirements.txt
#
# could work.
```

### Code Entrypoint

The evaluator will use `/home/aicrowd/run.sh` as the entrypoint, so please remember to have a `run.sh` at the root, which can instantitate any necessary environment variables, and also start executing your actual code. This repository includes a sample `run.sh` file.
If you are using a Dockerfile to specify your software environment, please remember to create a `aicrowd` user, and place the entrypoint code at `run.sh`.

## Frequently Asked Questions (FAQs)

Please find a list of frequently asked questions [here](https://github.com/AIcrowd/neurips2019_disentanglement_challenge_starter_kit/blob/master/FAQ.md), and please feel free to send a pull request with more questions that you think can help other participants.

## Submission

To make a submission, you will have to create a private repository on [https://gitlab.aicrowd.com/](https://gitlab.aicrowd.com/).

You will have to add your SSH Keys to your GitLab account by following the instructions [here](https://docs.gitlab.com/ee/gitlab-basics/create-your-ssh-keys.html).
If you do not have SSH Keys, you will first need to [generate one](https://docs.gitlab.com/ee/ssh/README.html#generating-a-new-ssh-key-pair).

Then you can create a submission by making a _tag push_ to your repository on [https://gitlab.aicrowd.com/](https://gitlab.aicrowd.com/).
**Any tag push (where the tag name begins with "submission-") to your private repository is considered as a submission**  
Then you can add the correct git remote, and finally submit by doing :

```
cd neurips2019_disentanglement_challenge_starter_kit
# Add AIcrowd git remote endpoint
git remote add aicrowd git@gitlab.aicrowd.com:<YOUR_AICROWD_USER_NAME>/neurips2019_disentanglement_challenge_starter_kit.git
git push aicrowd master

# Create a tag for your submission and push
git tag -am "submission-v0.1" submission-v0.1
git push aicrowd master
git push aicrowd submission-v0.1

# Note : If the contents of your repository (latest commit hash) does not change,
# then pushing a new tag will **not** trigger a new evaluation.
```

You now should be able to see the details of your submission at :
[gitlab.aicrowd.com/<YOUR_AICROWD_USER_NAME>/neurips2019_disentanglement_challenge_starter_kit/issues](gitlab.aicrowd.com//<YOUR_AICROWD_USER_NAME>/neurips2019_disentanglement_challenge_starter_kit/issues)

**NOTE**: Remember to update your username in the link above :wink:

In the link above, you should start seeing something like this take shape (each of the steps can take a bit of time, so please be patient too :wink: ) :
![](https://i.imgur.com/69nf0Te.png)

and if everything works out correctly, then you should be able to see the final scores like this :
![](https://i.imgur.com/FPIqPKb.png)

**Best of Luck** :tada: :tada:

# Author

Sharada Mohanty <https://twitter.com/MeMohanty>
