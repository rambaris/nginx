pipeline {
  agent any

  stages {
    stage('Clone Git Repo') {
      steps {
                git(
                    url: "https://github.com/rambaris/nginx.git",
                    branch: "main",
                    changelog: true,
                    poll: true
                )
            }
    }
    stage('Build Docker image') { 
            steps { 
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/rambaris/nginx.git']])
                sh 'docker build -t rambaris/myservernginx .'}
    }
    
    stage('Login to Docker Hub') {          
           steps{ 
                withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
                sh "docker login -u ${env.dockerUser} -p ${env.dockerPassword}"                                         
                echo 'Login Completed'      
            }           
            }
    }

    stage('Push Image to Docker Hub') {         
           steps{                            
                sh 'sudo docker rambaris/myservernginx'           
                echo 'Push Image Completed'       
             }            
    }
        
      
    
        node {
                 def remote = [:]
                 remote.name = 'Ansible-Prod-VM'
                 remote.host = '52.157.111.182'
                 remote.user = 'azureuser'
                 remote.password = 'Y4$vynb1gM83'
                 remote.allowAnyHosts = true
                 stage('Remote SSH') {
                 sshCommand remote: remote, command: "docker run -d -t servernginx -p 1137:1337 rambaris/myservernginx"
         
        }
    }
  }
}
