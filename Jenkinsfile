pipeline {
    agent any
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven 'maven 3.6.3'
    }

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerloginid')
	} 
    
    stages {
        stage('SCM Checkout') {
            steps {
                // Get some code from a GitHub repository
                // git 'https://github.com/prasad-gamut/BankingApp1.git'
		    checkout scm
            }
		}
        stage('Maven Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
		}
       stage("Docker build"){
            steps {
				sh 'docker version'
				sh "docker build -t dprasaddevops/bankapp-eta-app:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag dprasaddevops/bankapp-eta-app:${BUILD_NUMBER} dprasaddevops/bankapp-eta-app:latest"
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Push2DockerHub') {

			steps {
				sh "docker push dprasaddevops/bankapp-eta-app:latest"
			}
		}
		stage('Deploy to Kubernets'){
             steps{
                 script{
                         kubeconfig(credentialsId: 'k8s', serverUrl: '') {
                         sh 'kubectl apply -f kubernetesdeploy.yaml'
                         } 
		 }
                     
         }
    
}
}
