pipeline{
    agent any 
    stages{

        stage('sonar quality status'){
            agent {
                docker{
                    image 'maven' 
                }
            }
            steps{

                script{
                    withSonarQubeEnv(credentialsId: 'sonar-newtoken') { 

                        sh 'mvn clean package sonar:sonar'

                }
            }

        }
        
    }
}
}
