pipeline{
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS = '24f40dbb-1e8f-4826-98c4-d79d97bba191'
        DOCKER_IMAGE_NAME = 'kirtighugtyal006/mvn-hello-world'
        DOCKER_IMAGE_TAG = 'latest'
        DOCKER_HOME = tool 'docker'
        PATH = "${DOCKER_HOME}/bin:${env.PATH}"
    }
    stages
       {
            stage("Build")
            {
                steps{
                    script {
                        withMaven(
                            maven: 'maven3', 
                            goals: 'clean install'
                        ){}
                    }
                }
            }
            stage("Test")
            {
                steps{
                    script {
                        withMaven(
                            maven: 'maven3', 
                            goals: 'test'
                        ){}
                    }
                }
            }
             stage("Integration Tests")
            {
                steps{
                    script {
                        withMaven(
                            maven: 'maven3', 
                            goals: 'integration-test'
                        ){}
                    }
                }
            }
        stage('Build and Push Docker Image') {
            steps {
                script {
                    sh 'docker --version'
                    }
                }
            }
        }
        // stage('Push to Docker Hub') {
        //     steps {
        //         script {
        //             withCredentials([string(credentialsId: "${DOCKER_HUB_CREDENTIALS}", variable: 'DOCKER_HUB_CREDS')]) {
        //                 docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_HUB_CREDS}") {
        //                     docker.image("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}").push()
        //                 }
        //             }
        //         }
        //     }
        // }
             // stage("Deploying"){
             //        steps{
             //            withKubeConfig([credentialsId: 'kube-config']){
             //                sh 'pwd && ls'
             //                sh 'kubectl apply -f kubernetes/mongodb/mongodb.yml'
             //                sh 'kubectl apply -f kubernetes/app/deployment.yml'
             //                sh 'kubectl apply -f kubernetes/app/nodeport.yml'
             //                sh 'kubectl get all'
             //            }
             //        }
             //    }
        }
        // post {
        //     success {
        //         setBuildStatus("Build succeeded", "SUCCESS");
        //     }
        //     failure {
        //         setBuildStatus("Build failed", "FAILURE");
        //     }
        //   }
}
// void setBuildStatus(String message, String state) {
//   step([
//       $class: "GitHubCommitStatusSetter",
//       reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/KirtiGhugtyal6/FINAL_CAPSTONE"],
//       contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
//       errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
//       statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
//   ]);
// }
