pipeline {
   agent any

   environment {
     SERVICE_NAME = "api-gateway"
     ORGANIZATION_NAME = "fleetman-ci-cd-demo"
     REGISTRY_URL="virtualpairprogrammers"
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
           sh 'docker image build -f ./src/main/docker/Dockerfile -t ${REGISTRY_URL}/${SERVICE_NAME}:${BUILD_ID} .'
           withDockerRegistry(credentialsId: 'DockerHub', url: '') {
             sh 'docker image push ${REGISTRY_URL}/${SERVICE_NAME}:${BUILD_ID}'
              sh 'echo ${WORKSPACE}'
           }
         }
      }

      stage('Deploy to Cluster') {
          steps {
                withKubeConfig(contextName: 'default', credentialsId: 'k8s', namespace: 'default', serverUrl: '${KUBERNETES_API_SERVER}') {
                    sh 'envsubst < ${WORKSPACE}/project/deploy/deploy.yaml | kubectl apply -f -'
                }
          }
      }
   }
}
