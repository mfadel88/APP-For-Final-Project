pipeline {
    agent { label 'slave1' }
    // parameters{
    //     choice(name: 'ENV', choices: {'dev', 'test', 'prod','main'})
    // }
    stages {
        stage('Build') {
     steps {
                script {
                     withCredentials([usernamePassword(credentialsId: 'dockerHub-cred', passwordVariable: 'pass', usernameVariable: 'user')]) {
                    if (env.BRANCH_NAME == 'main') {
                        sh """
                        docker login -u $user -p $pass
                        docker build -t mfadel8/app:$BUILD_NUMBER .
                        docker push mfadel8/app:$BUILD_NUMBER
                        echo ${BUILD_NUMBER} > ../build
                        """
                    
                       }
                     }
                }
                 
            }
        
        } 
    

    
        stage('Deploy') {
        steps {
                script {               
                if (env.BRANCH_NAME == 'prod' || env.BRANCH_NAME == 'dev' || env.BRANCH_NAME == 'test') {
                withCredentials([file(credentialsId: 'JenkinsConfig-minikube', variable: 'cfg')]){
                            sh """
                            export NUM=\$(cat ../build)
                            mv Deployment/deploy.yaml Deployment/deploy
                            cat Deployment/deploy | envsubst > Deployment/deploy.yaml
                            rm -f Deployment/deploy
                            kubectl apply --kubeconfig=${cfg} -f Deployment/service.yaml
                            kubectl apply --kubeconfig=${cfg} -f Deployment/deploy.yaml
                            """
                            }
                        } 

                    }
                }
        
    }
 }
}
