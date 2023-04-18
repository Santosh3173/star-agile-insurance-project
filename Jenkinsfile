pipeline {
    agent { label 'slave1' }
	

    tools {
        // Install the Maven version configured as "M3" and add it to the path.

        maven "Maven-3.8.7"
    }

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('docker-login-id')
	} 
    
    stages {
        stage('SCM Checkout') {
            steps {
                // Get some code from a GitHub repository
                    git 'https://github.com/Santosh3173/star-agile-insurance-project.git'
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
				sh 'docker --version'
				sh 'docker build -t santoshinsuranceimage:${BUILD_NUMBER} .'
				sh 'docker image list'
				sh 'docker tag santoshinsuranceimage:${BUILD_NUMBER} dskdockerid/santoshinsuranceimage:latest'
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Approve - push Image to dockerhub'){
            steps{
                
                //----------------send an approval prompt-------------
                script {
                   env.APPROVED_DEPLOY = input message: 'User input required Choose "yes" | "Abort"'
                       }
                //-----------------end approval prompt------------
            }
        }
      
		stage('Push2DockerHub') {

			steps {
				sh 'docker push dskdockerid/santoshinsuranceimage:latest'
			}
		}
	   stage(' copy files to ansible hosts') {
           steps {
	sh 'cp  /home/devopsadmin/workspace/Insurance-Project-Pipeline/*.yml  /etc/ansible'
	}
	}
	
	    stage('deploymnet from ansible playbook to k8 cluster') {
           steps {
               ansiblePlaybook installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: '/etc/ansible/insurance-ansible-playbook.yml'
           }
	    }
	    
    }
}

