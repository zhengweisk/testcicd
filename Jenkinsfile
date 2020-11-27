#!groovy

pipeline{
    agent {
    node {label 'master'}
    }

    environment {
        PATH="/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin"
        GIT_VERSION="latest"
    }
    
    parameters {
        choice(
            choices: 'dev\n\uat\nfat\nprod',
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
                def dockerfile = 'test.dockerfile'
                def testImage = docker.build("test", "/root/argo-cd-hello-world-app-master/")
                dir("${env.WORKSPACE}"){
                    sh """
#                    docker build . -t test:$build_number -f /root/argo-cd-hello-world-app-master/test.dockerfile
                    
                    
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
