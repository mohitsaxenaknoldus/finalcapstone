pipeline{
    agent any
     environment{
        dockerhub_repo = "kirtighugtyal/final_capstone"
        dockerhub_creds = 'e9f78131-b451-409e-844d-21da4dd448ed'
        dockerImage = ''
     }
 tools {
        maven 'maven3'
    }
    stages
       {
            stage("clean")
            {
                steps{
                    script {
                        withMaven(
                            maven: 'Maven3', 
                            goals: 'clean install'
                        )
                    }
                }
            }
            stage("Build")
            {
                steps{
                    sh "mvn compile"
                }

            }
             stage("TEST")
            {
                steps{
                    sh "mvn test"

                }
            }
             stage("package")
            {
                steps{
                    sh "mvn package"
                }
            }
             stage("building docker image"){
                    steps{
                        script{
                            dockerImage = docker.build dockerhub_repo + ":$GIT_COMMIT-build-$BUILD_NUMBER"
                        }

                     }
                }
            stage("Pushing the docker image"){
                    steps{
                        script {
                            docker.withRegistry('', dockerhub_creds){
                                dockerImage.push('latest')
                            }
                        }
                    }
                }
             stage("Deploying"){
                    steps{
                        withKubeConfig([credentialsId: 'kube-config']){
                            sh 'pwd && ls'
                            sh 'kubectl apply -f kubernetes/mongodb/mongodb.yml'
                            sh 'kubectl apply -f kubernetes/app/deployment.yml'
                            sh 'kubectl apply -f kubernetes/app/nodeport.yml'
                            sh 'kubectl get all'
                        }
                    }
                }
        }
        post {
            success {
                setBuildStatus("Build succeeded", "SUCCESS");
            }
            failure {
                setBuildStatus("Build failed", "FAILURE");
            }
          }
}
void setBuildStatus(String message, String state) {
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/KirtiGhugtyal6/FINAL_CAPSTONE"],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}
