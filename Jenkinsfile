@Library('samlee')_
 
pipeline {
    environment { 

        registry = "samlee18/sample" 

        registryCredential = 'dockerhub_id' 

        dockerImage = '' 

    }
    agent any
    stages{
 
        stage('Checkout'){
            steps{
      
              code_checkout("master", "https://github.com/Samlee97/Training.git")  
              
            }
        }
          stage('Setting docker plugin')
        {
            steps {
                script {
               
        def dockerHome = tool 'mydocker'
        env.PATH = "${dockerHome}/bin:${env.PATH}"
                }
            }
        }
          stage('Building our image') 
        {
            steps
            {
                sh 'docker --version'
                 script 
                   { 
                       try {
                   
                           dockerImage = docker.build registry + ":$BUILD_NUMBER" 
                   }
                      catch(Exception error)
                           {
                               error "Build failed "
                           }
                   
            }
        }
        }
         stage('Push our image') {
            steps {
                script {
                    try {
                    
                        docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
                    catch(Exception error) {
                        error "push failed "
                    }
                        
            }
        }
    }
        stage('Pull our image'){
            steps{
                script{
                    try {
            
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.pull()
                    
                }
                    }
                    catch(Exception error) {
                        error "pull failed "
                    }
            }
            sh  'docker images'
        }
    }
        stage('Run Image'){
           steps{
               
               sh ''' 
               if [ $(docker ps -qf "name=nodejs") ]
                then
                echo "from if block"
                docker kill nodejs && docker rm nodejs
                docker run -d -p 6541:8080 --name nodejs "${registry}":"${BUILD_NUMBER}"
                docker ps
               else
                echo "from else block"
                docker run -d -p 6541:8080 --name nodejs "${registry}":"${BUILD_NUMBER}"
                docker ps
                fi
               '''
               
               
           }
       }   
         
    }
}
        
