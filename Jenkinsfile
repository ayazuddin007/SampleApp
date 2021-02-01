//def tomcatServerIP = '172.31.45.105'
def ansibleServerIP = '172.31.1.143'
def dockerServerIP = '172.31.15.21'
//def ansiblePlaybook = 'ansible-playbook ansible/p1.yml -i ansible/hosts'
//def ansiblePlaybook = 'ansible-playbook ansible/p2.yml -i ansible/hosts'
def ansiblePlaybook1 = 'ansible-playbook ansible/p11.yml -i ansible/hosts'
def ansiblePlaybook2 = 'ansible-playbook ansible/p12.yml -i ansible/hosts'

pipeline {
    agent any
    options { skipDefaultCheckout() }
    environment {
        PATH = "/opt/maven/bin/:$PATH"
    }
    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/ayazuddin007/sampleapp'   
            }
        }
        stage('Build') {
            steps {
                sh "mvn clean install"  
            }
        }
        /*stage('Deploy to Tomcat') {
            steps {
                sshagent(['Pipeline-user']) {
                    sh "scp -o StrictHostKeyChecking=no target/*.war ec2-user@${tomcatServerIP}:/opt/tomcat/webapps"
                }  
            }
        }*/
        /*stage('Deploy to Ansible') {
            steps {
                sshagent(['Pipeline-user']) {
                    sh "scp -o StrictHostKeyChecking=no -r docker ansible target/*.war ec2-user@${ansibleServerIP}:/home/ec2-user"   //copy all project files
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@${ansibleServerIP} ${ansiblePlaybook}"
                    
                }  
            }
        }*/
        stage('Docker Image,Tag Image,Push DockerHub') {
            steps {
                //def dockerHubCmd = 'docker login -u ayazway -p'
                withCredentials([string(credentialsId: 'DockerHub-Credentials', variable: 'DockerHub-Variable')]) {
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@${ansibleServerIP}  docker login -u ayazway -p  ${DockerHub-Variable}"
                }
                sshagent(['Pipeline-user']) {
                    sh "scp -o StrictHostKeyChecking=no -r * ec2-user@${ansibleServerIP}:/home/ec2-user"   //copy all project files to Ansible Server
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@${ansibleServerIP} ${ansiblePlaybook1}"   //Run p11.yml on Ansible Server        
                }  
            }
        }
        stage('Docker Container') {
            steps {
                sshagent(['Pipeline-user']) {
                    //sh "scp -o StrictHostKeyChecking=no -r * ec2-user@${dockerServerIP}:/home/ec2-user"   //copy all project files to Docker Server
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@${ansibleServerIP} ${ansiblePlaybook2}" //Run p12.yml on Docker Server from Ansible Server
                }  
            }
        }
    
    }
}
