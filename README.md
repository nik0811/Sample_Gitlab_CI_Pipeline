# Sample_Gitlab_CI_Pipeline

```
image: docker:18-git
services:
  - docker:18-dind

before_script:
  - docker info
  - 'which ssh-agent || ( apt-get update -y && apt-get install openssh-client -y )'
  - eval $(ssh-agent -s)
  - mkdir -p ~/.ssh
  - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'
  - printenv SSH_PRIVATE_KEY | ssh-add -
stages:
  - build
  - deploy

build:
  stage: build
  script:
    - docker build -t hub.gitlab.registery/devops-assignment/java-2005 .
    - docker login hub.gitlab.registery --username $docker_username --password $docker_password
    - docker images
    - docker push hub.gitlab.registery/devops-assignment/java-2005:latest
deploy:
    stage: deploy
    script:
            - ssh ubuntu@52.66.235.248 "docker login hub.gitlab.registery -u $docker_username -p $docker_password && docker stop demo-java && docker rm -f $(docker ps -q -a) && docker run -it -d -p 8080:8080 --name demo-java hub.gitlab.registery/devops-assignment/java-2005:latest"
            - echo "assignment completed !!!"
```
