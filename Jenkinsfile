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
        stage('Deploy to Kubernetes Dev Environment') {
	    //agent kubernetes	
            steps {
		script {
		//sshPublisher(publishers: [sshPublisherDesc(configName: 'kubernetes', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f kubernetesdeploy.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
		   //kubernetesDeploy(configs: "kubernetesdeploy.yaml", kubeconfigId: "kubernetes")
		    //sh 'kubectl apply -f kubernetesdeploy.yaml --kubeconfig=$KUBECONFIG'
		     withCredentials([kubeconfigFile(credentialsId: 'your-kubeconfig-id', variable: 'KUBECONFIG')]) {
                         sh 'kubectl apply -f kubernetesdeploy.yaml --kubeconfig=$KUBECONFIG'
            }
		}
	      //	configs: 'kubernetesdeploy.yaml',
             //           kubeconfigId: 'kubernetes'
        
    	}
    }
}
}
