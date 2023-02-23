node {
  try {
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
      echo "Stage: Build"
      // if(env.BRANCH_NAME == 'master') {
        withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
          withEnv(["DOCKER_IMAGE_NAME=docker-pipeline", "DOCKER_IMAGE_TAG=latest"]) {
            sh 'docker build -t $DOCKERHUB_USERNAME/$DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG --no-cache .'
            sh 'docker images'
            sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD'
            sh 'docker push $DOCKERHUB_USERNAME/$DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG'
          }
        }
      // }
    }
    stage('Deploy') {
      //  if(env.BRANCH_NAME == 'master') {
        withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
          echo "Stage: Deploy"
           withEnv(["DOCKER_IMAGE_NAME=docker-pipeline", "DOCKER_IMAGE_TAG=latest"]) {
              sh 'docker pull $DOCKERHUB_USERNAME/$DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG'
              sh 'docker run -d -p 8090:8090 --name app $DOCKERHUB_USERNAME/$DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG'
              sh 'docker rmi -f $DOCKERHUB_USERNAME/$DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG'
          }
        }
      // }
    }
  }
  catch (err) {
    throw err
  }
}
