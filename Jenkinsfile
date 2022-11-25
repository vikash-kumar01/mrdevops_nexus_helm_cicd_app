pipeline{
    agent any 
    environment {
        VERSION = "${env.BUILD_ID}"
    }
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
    stage ('Quality Gate status'){
        steps {
            script{

             waitForQualityGate abortPipeline: false, credentialsId: 'sonar-newtoken'
            }
    }

}

     stage ('docker build & docker push to nexus repo'){
         steps{
            script{
                 withCredentials([string(credentialsId: 'nexus_password', variable: 'nexus_cred')]) {
    // some block
                
                sh '''
                docker build -t  44.192.39.247:8083/springapp:${VERSION} .

                docker login -u admin -p $nexus_cred 44.192.39.247:8083

                docker push 44.192.39.247:8083/springapp:${VERSION}

                docker rmi  44.192.39.247:8083/springapp:${VERSION}


                '''
            }
        }
        
         }
     }
    }
       
    post {
		always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "rameshpower0215@gmail.com";  
		}
	}
}
 
