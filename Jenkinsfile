pipeline{

    agent any

    stages{

        stage('sonar quailty status'){

            agent{

                docker{
                    image 'maven'
                }
            }


            steps{

                script{

                    withSonarQubeEnv(credentialsId: 'sonarapi') {

                        sh 'mvn clean package sonar:sonar'
    
                   }
                    
                }
            }
        }
    }
}
