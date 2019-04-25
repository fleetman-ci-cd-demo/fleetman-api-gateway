pipeline {
   agent any

   environment {
     SERVICE_NAME = "fleetman-api-gateway"
     ORGANIZATION_NAME = "fleetman-ci-cd-demo"
     YOUR_DOCKERHUB_USERNAME="virtualpairprogammers"
     REPOSITORY_TAG="${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
   }

   stages {
      stage('Preparation') {
         steps {
            cleanWs()
            git credentialsId: 'GitHub', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}"
         }
      }
      stage('Build') {
         steps {
            sh '''mvn clean package'''
         }
      }

      stage('Build and Push Image') {
         steps {
           sh 'docker image build -t ${REPOSITORY_TAG} .'
           sh 'echo normally here you would push to a registry - we can use the local image'
           # withDockerRegistry(credentialsId: 'DockerHub', url: '') {
           #  sh 'docker image push ${REPOSITORY_TAG}'
           # }
         }
      }

      stage('Deploy to Cluster') {
          steps {
                withKubeConfig(contextName: 'default', credentialsId: '9a91910b-c106-47bc-bc12-757dfd2ad6a2', namespace: 'default', serverUrl: '${KUBERNETES_API_SERVER}') {
                    sh 'envsubst < ${WORKSPACE}/project/deploy/deploy.yaml | kubectl apply -f -'
                }
          }
      }
   }
}
