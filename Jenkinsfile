pipeline{

    agent any 
    environment {

        VERSION = "${env.BUILD_ID}"
    }

    stages{

        stage('sonar quality check'){
              
            agent{

                docker {
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
                     docker build -t 3.83.66.55:8083/springapp:${VERSION} .

                     docker login -u admin -p $nexus_creds 3.83.66.55:8083

                     docker push 3.83.66.55:8083/springapp:${VERSION}

                     docker rmi  3.83.66.55:8083/springapp:${VERSION}

                    '''
                   }
                }
            }
        }
        stage('Identifying misconfigs using datree in helm charts'){

            steps{
                script{
                    dir('kubernetes/myapp/') {
                         withEnv(['DATREE_TOKEN=b73821a0-0227-4d1e-8e00-e328668442a1']) {
                        sh 'helm datree test .'
                        }
                    }
                }
            }
        }
        stage('Pushing the helm charts to nexus repo'){

            steps{
                script{
                    withCredentials([string(credentialsId: 'nexus_passwd', variable: 'nexus_creds')]) {
                        dir('kubernetes/') {
                         sh '''
                         helmversion=$(helm show chart myapp | grep version | cut -d: -f 2 | tr -d ' ')
                         tar -czvf myapp-${helmversion}.tgz myapp/
                         curl -u admin:$nexus_creds http://3.83.66.55:8081/repository/helm-repo/ --upload-file myapp-${helmversion}.tgz -v 
                         '''
                }
              }
             }
            }
        }
    }
    post {
		always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "vikash.mrdevops@gmail.com";  
		}
	}
}