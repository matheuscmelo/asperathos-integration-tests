pipeline {
  agent any
  stages {
    stage('Integration') {
      agent any
      steps {
        sh 'docker network create --attachable network-integration_tests-$BUILD_ID'
        sh 'docker build . -t integration-tests_build-$BUILD_ID'
        sh 'docker run -t -d --privileged --network=network-integration_tests-$BUILD_ID -v /.kube:/.kube/ --name docker-integration_tests-$BUILD_ID asperathos-docker'
        sh 'docker create --network=network-integration_tests-$BUILD_ID --name integration-tests-integration_tests-$BUILD_ID -e DOCKER_HOST=tcp://$(docker ps -aqf "name=docker-integration_tests-$BUILD_ID"):2375 -e DOCKER_HOST_URL=$(docker ps -aqf "name=docker-integration_tests-$BUILD_ID") integration-tests_build-$BUILD_ID'
        sh 'docker cp . integration-tests-integration_tests-$BUILD_ID:/integration-tests/test_env/integration_tests/asperathos-integration_tests/'
        sh 'docker start -i integration-tests-integration_tests-$BUILD_ID'
      }
    }
  }
  post {
    cleanup {
      sh 'docker stop docker-integration_tests-$BUILD_ID'
      sh 'docker rm -v docker-integration_tests-$BUILD_ID'
      sh 'docker rm -v integration-tests-integration_tests-$BUILD_ID'
      sh 'docker network rm network-integration_tests-$BUILD_ID'
    }
    success {
        sh 'docker image tag integration-tests_build-$BUILD_ID integration-tests:latest'
        sh 'docker image rm integration-tests_build-$BUILD_ID'
    }
    failure {
        sh 'docker image rm integration-tests_build-$BUILD_ID'
    }
  }
}
