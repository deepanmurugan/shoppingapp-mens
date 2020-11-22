@Library('jenkins-shared-library') _

pipeline {
   agent any
   environment {
       app = 'shoppingapp'
       service = 'shoppingapp-mens'
       registry = 'deepanmurugan/shoppingapp-mens'
       registryCredential = 'dockerhub'
       dockerImage = ''
       imageid = "deepanmurugan/shoppingapp-mens:$BUILD_NUMBER"
   }
   stages {
       stage('Build') {
            steps {
		script {
			dockerImage = dockerbuild(imageid)
            
		}
	    } 
        }
       stage('Test') {
           steps {                
		testcase()
           }
       }
       stage('Publish') {
           steps{
               script {
			imagepush(imageid)
                   }
           }
       }
       stage('Pull Playbook Repo') {
        steps {
		dir('/tmp/ansible-playbooks/') {
			gitcheckout(
				branch: "master",
				repoUrl: "https://github.com/deepanmurugan/Ansible_Playbook.git"
			)
		}
	}
       }
       stage ('Deploy') {
           steps {
           dir('/tmp/ansible-playbooks/') {
               script{
			deploytok8s(imageid,app,service)
               }
           }
           }
       }
   }
}
