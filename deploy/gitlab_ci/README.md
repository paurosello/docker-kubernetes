stages:
  - build

environment:
  IMAGE_NAME: my_image

docker build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script: 
    - docker login -u $CI_REGISTRY_USER -p $CI_JOB_TOKEN $CI_REGISTRY
    - docker build -t $IMAGE_NAME:latest .
    - docker push $IMAGE_NAME:latest
