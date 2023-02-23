node {
  try {
    environment {
        DOCKER_IMAGE_NAME = "docker-pipeline"
        DOCKER_IMAGE_TAG = "latest"
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
      echo "Stage: Build"
      //  if(env.BRANCH_NAME == 'master') {
        withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
          sh 'docker build -t $DOCKERHUB_USERNAME/docker-pipeline:latest --no-cache .'
          sh 'docker images'
          sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD'
          sh 'docker push $DOCKERHUB_USERNAME/docker-pipeline:latest'
        }
      // }
    }
    stage('Deploy') {
      // if(env.BRANCH_NAME == 'master') {
        withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
          echo "Stage: Deploy"
          sh 'docker pull $DOCKERHUB_USERNAME/docker-pipeline:latest'
          sh 'docker run -d -p 8090:8090 --name app $DOCKERHUB_USERNAME/docker-pipeline:latest'
          sh 'docker rmi -f $DOCKERHUB_USERNAME/docker-pipeline:latest'
        }
      // }
    }
  }
  catch (err) {
    throw err
  }
}
