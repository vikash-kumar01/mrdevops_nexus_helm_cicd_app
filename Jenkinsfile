pipeline{

    agent any
    environment {

        VERSION = "${env.BUILD_ID}"
    }

    stages{

        stage('sonar quality check'){
            
            agent{

                docker{
                    image 'maven'
                }
            }
            steps{

                script{

                    withSonarQubeEnv(credentialsId: 'sonar-token') {

                        sh 'mvn clean package sonar:sonar'
                  }
                }
            }
        }
        stage('Quality Gate status'){

            steps{

                script{

                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('docker build & docker push to Nexus repo'){
            
            steps{

                script{
                    withCredentials([string(credentialsId: 'nexus_passwd', variable: 'nexus_creds')]) {
                    sh '''
                    docker build -t 65.0.168.229:8083/springapp:${VERSION} .

                    docker login -u admin -p $nexus_creds 65.0.168.229:8083

                    docker push 65.0.168.229:8083/springapp:${VERSION}

                    docker rmi 65.0.168.229:8083/springapp:${VERSION}

                    '''
                    }
                }
            }
        }
    }
}