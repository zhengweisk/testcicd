#!groovy

pipeline{
    agent {
    node {label 'master'}
    }

    environment {
        PATH="/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin"
        GIT_VERSION="latest"
        def dockerfile = 'test.dockerfile'
    }
    
    parameters {
        choice(
            choices: 'dev\nuat\nfat\nprod',
            description: 'choose deploy environment',
            name: 'deploy_env'
        )
        string(name: 'version', defaultValue: '1.0.0', description: 'bulder version')
    }
    
    stages{
        stage("Checkout test repo"){
            steps{
                sh 'git config --global http.sslVerify false'
                dir("${env.WORKSPACE}"){
                    git branch: 'main', credentialsId: "", url: "https://github.com/zhengweisk/hello.git"
                }
            }
        }
        stage("Print env variable"){
            steps{
                dir("${env.WORKSPACE}"){
                    sh """
                    echo "[INFO] Print env variable"
                    echo "Current deployment environment is $deploy_env" >> test.properties
                    echo "The build is $version" >> test.properties
                    echo "[INFO] Done..."
                    """
                }
            }
        }
        stage("Check test properties"){
            steps{
                dir("${env.WORKSPACE}"){
                    sh """
                    echo "check test properties!"
                    echo "[INFO] Build finished..."
                    """
                }
            }
        }
        stage("go build and img"){
            steps{

                dir("${env.WORKSPACE}"){
//                    script {
//                        docker.build("test:${env.BUILD_ID}","/root/argo-cd-hello-world-app-master/")
//                }
           sh '''
           docker build . -t 643690352380.dkr.ecr.us-east-1.amazonaws.com/hello:$BUILD_NUMBER -f /root/argo-cd-hello-world-app-master/test.dockerfile
           '''
                }
            }
        }
        stage("push docker img"){
            steps{
                dir("${env.WORKSPACE}"){
                script {
                  docker.withRegistry("https://643690352380.dkr.ecr.us-east-1.amazonaws.com", "ecr:us-east-1:aws-ecr") {
                    docker.image("643690352380.dkr.ecr.us-east-1.amazonaws.com/hello:$BUILD_NUMBER").push()
                    }
                    }
                }
            }
        }
        stage("deploy"){
            steps{
                dir("${env.WORKSPACE}"){
            sh '''
            cd /root/argocd/hello && /usr/local/sbin/kustomize edit set image 643690352380.dkr.ecr.us-east-1.amazonaws.com/hello:$BUILD_NUMBER && 	git pull && git commit -am "bugfix" && git push
            '''
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
