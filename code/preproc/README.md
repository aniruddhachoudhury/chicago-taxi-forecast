# Preprocessing module

This module reads the raw dataset from BigQuery, apply transformations using TFT and dump all artifacts on Google Cloud Storage

## Build for Kubeflow pipeline
In any doubt, check the [official documentation](https://www.kubeflow.org/docs/gke/gcp-e2e/)


### Build Image
```
export DEPLOYMENT_NAME=chicago-taxi-forecast
export PROJECT=ciandt-cognitive-sandbox
export VERSION_TAG=latest
export DOCKER_IMAGE_NAME=gcr.io/${PROJECT}/${DEPLOYMENT_NAME}/preproc:${VERSION_TAG}

docker build ./ -t ${DOCKER_IMAGE_NAME} -f ./Dockerfile
```

### Test locally

Define a local directory to read/write pipeline artifacts

```
ARTIFACTS_DIR=/home/CIT/rodrigofp/Projects/Specialization2019/demo_taxi/assets
docker run -it -v ${PWD}/src:/src -v ${ARTIFACTS_DIR}:/artifacts_dir --rm  ${DOCKER_IMAGE_NAME} bash
```

Run container
```
python3 read_metadata.py \
--tfx-artifacts-dir /artifacts_dir \
--project ciandt-cognitive-sandbox \
--start-date 2019-04-10 \
--end-date  2019-04-30 \
--split-date 2019-04-20 \
--temp-dir /tmp \
--runner DirectRunner


python3 bq2tfrecord.py \
--tfrecord-dir /artifacts_dir \
--tfx-artifacts-dir /artifacts_dir \
--project ciandt-cognitive-sandbox \
--window-size 6 \
--start-date 2019-04-10 \
--end-date  2019-04-30 \
--split-date 2019-04-20 \
--temp-dir /tmp \
--runner DirectRunner
```


### Upload container image to the GCP Conainer Registry
```
gcloud auth configure-docker --quiet

docker push ${DOCKER_IMAGE_NAME}
```
