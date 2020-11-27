#!groovy

pipeline{
    agent {node {label 'master'}}
    
    environment {
        PATH="/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin"
    }
    
    parameters {
        choice(
            choices: 'dev\nprod',
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
        stage("go build"){
            steps{
                dir("${env.WORKSPACE}"){
                    sh """
                    export GIT_VERSION=$(git rev-parse HEAD)
                    docker build . -t test:$GIT_VERSION -f /root/argo-cd-hello-world-app-master/test.dockerfile
                    """
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
