# StemCreator3

## Intro

This repo is to automate the creation of native instruments stem files, mounted to the container with a -v bind mount, or from files in an AWS S3 bucket.

It is an extention of the work done:
<https://github.com/facebookresearch/demucs>
<https://github.com/xserrat/docker-facebook-demucs/>
<https://www.stems-music.com/stems-is-for-developers/>
<https://github.com/axeldelafosse/stemgen> - initial project that alexixjf used, and updates to ni-stem for python3
<https://github.com/aleixjf/StemCreator>

### Use

Have an S3 bucket with an INPUT and STEMS folders already setup. Create an account with access and write down the access keys. The input files will be deleted and the output written to the STEMS folder.

`docker run --rm -e AWS_DEFAULT_REGION=yourawsdefaultregion -e AWS_ACCESS_KEY_ID=yourawsaccesskey -e AWS_SECRET_ACCESS_KEY=yourawssecretaccesskey -e BUCKET=nameofbucket stemcreator3:3.10-slim-bullseye`

If using locally, ommit -e envionrment variables, and instead bind mount a folder with the .m4a files to be processed:

`docker run --rm -v /path/to/your/input:/INPUT stemcreator3:3.10-slim-bullseye`

The output will appear in /path/to/your/input/STEMS

### Fixes

#### ni-stem

included gpac_mac not used on linux, installed gpac as system package
mac python3 at/usr/bin/python3, on linux /usr/local/bin/python3
internal.py: old bitrate argument when converting .wav to aac meant it defauled to 128k, updated to -b:a 256k 

#### stemCreator3

Added year, album artist to metadata copied to .stem.m4a
Loading and outputting files to /INPUT to make bind mount simpler
Check if stem is already in s3 or /INPUT/STEMS

### Issues

Only m4a files will be processed. There is a bug where stemCreator will only accept the first extention listed in the supported formats. All my tracks are already .m4a so this is not a problem for me. Also the output is currently hard coded to AAC. The nature of generated stems files means that they are 5x the size anyway, and each individial track won't be as good as the original, so saving as ALAC is a waste of space.

### To Do

- Build dockerfiles using Github Actions
- Terraform
  - Define S3 Bucket
  - Deploy to AWS Fargate
  - Create Cloudwatch/EventBridge event to start container on upload to bucket
