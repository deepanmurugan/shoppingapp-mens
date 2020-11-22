@Library('jenkins-shared-library') _

pipeline {
   agent any
   environment {
       app = 'shoppingapp'
       service = 'shoppingapp-mens'
       registry = 'deepanmurugan/shoppingapp-mens'
       registryCredential = 'dockerhub'
       dockerImage = ''
   }
   stages {
       stage('Build') {
            steps {
		script {
			def dockerregisty = registry + ":$BUILD_NUMBER"
			dockerImage = dockerbuild.build(dockerregisty)
		}
            } 
        }
       stage('Test') {
           steps {                
               sh 'echo "Testing the docker built image"'
           }
       }
       stage('Publish') {
           steps{
               script {
                     docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                   }
               }
           }
       }
       stage('Pull Playbook Repo') {
        steps {
		dir('/tmp/ansible-playbooks/') {
		script {
			gitcheckout.checkout(branch:'main',repoUrl:'https://github.com/deepanmurugan/Ansible_Playbook.git')
          	}
		}
	}
       }
       stage ('Deploy') {
           steps {
           dir('/tmp/ansible-playbooks/') {
               script{
                   def image_id = registry + ":$BUILD_NUMBER"
                   sh "ansible-playbook deploy_k8s.yml --extra-vars \"image_id=${image_id} app_name=${app} service_name=${service}\""
               }
           }
           }
       }
   }
}
