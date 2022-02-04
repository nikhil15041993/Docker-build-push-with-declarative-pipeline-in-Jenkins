# Docker-build-push-with-declarative-pipeline-in-Jenkins


* step 1 Create credentials on Jenkins for GitHub and docker hub
* step 2 install Docker and Docker pipeline plugin

Here is the pipeline code
=========================

```

pipeline{
    
    agent any
    
   tools {
  maven 'maven-3.8'
}
    
    environment{
        imagename = "nikhilnambiars/devops"
        registryCredential = 'docker-hub2'
        dockerImage = ''
    }
    
    stages{
        stage('scm'){
            steps{
                git credentialsId: 'ssh-key', url: 'git@gitlab.com:contact.nikhilnambiar/webhookproject.git'
            }
        }
        
        
        stage('maven build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('build docker'){
            steps{
                script{
                    dockerImage=docker.build imagename
                }
            }
        }
        stage('docker delploy'){
            steps{
                
                script{
                   
                    docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
        }
    }
}
}
```

