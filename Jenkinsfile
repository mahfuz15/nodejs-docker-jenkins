node {
  try {
    environment {
        DOCKER_IMAGE_NAME = 'docker-pipeline'
        DOCKER_IMAGE_TAG = 'latest'
    }
    stage('Checkout') {
      checkout scm
    }
    stage('Environment') {
      sh 'git --version'
      echo "Branch: ${env.BRANCH_NAME}"
      sh 'docker -v'
      sh 'printenv'
    }
    stage('Build') {
      if(env.BRANCH_NAME == 'master') {
        withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
          sh 'docker build -t $DOCKERHUB_USERNAME/$DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG --no-cache .'
          sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD'
          sh 'docker push myusername/myapp:latest'
        }
      }
    }
    stage('Deploy') {
      if(env.BRANCH_NAME == 'master') {
        withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
          sh 'docker pull $DOCKERHUB_USERNAME/$DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG'
          sh 'docker run -d -p 8090:8090 --name app $DOCKERHUB_USERNAME/$DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG'
          sh 'docker rmi -f $DOCKERHUB_USERNAME/$DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG'
        }
      }
    }
  }
  catch (err) {
    throw err
  }
}
