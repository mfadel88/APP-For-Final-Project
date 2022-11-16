pipeline {
    agent { label 'slave1' }
    stages {
        stage('Build') {
     steps {
                script {
                     withCredentials([usernamePassword(credentialsId: 'Docker-Hub-user', passwordVariable: 'pass', usernameVariable: 'user')]) {
                        sh """
                        docker login -u $user -p $pass
                        docker build -t mfadel8/app:$BUILD_NUMBER .
                        docker push mfadel8/app:$BUILD_NUMBER
                        echo ${BUILD_NUMBER} > ../build
                        """
                    
                    //    }
                     }
                }
                 
            }
        
        } 
    

    
        stage('Deploy') {
        steps {
                script {               
                withCredentials([file(credentialsId: 'k8s-file', variable: 'serviceAcc')]){
                            sh """
                            gcloud auth activate-service-account --key-file="$serviceAcc"
                            
                            gcloud container clusters get-credentials private-k8s-cluster --zone us-central1-a --project final-proj-fadel

                            
                            export NUM=\$(cat ../build)
                            mv Deployment/deploy.yaml Deployment/deploy
                            cat Deployment/deploy | envsubst > Deployment/deploy.yaml
                            rm -f Deployment/deploy
                            kubectl apply -f Deployment/service.yaml
                            kubectl apply -f Deployment/deploy.yaml
                            """
                            }
                        // } 

                    }
                }
        
    }
 }
}
