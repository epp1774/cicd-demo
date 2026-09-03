pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo '=== Build Stage: 构建 Docker 镜像 ==='
                sh 'docker build -t cicd-demo:${BUILD_ID} .'
                sh 'docker images | grep cicd-demo'
            }
        }

        stage('Test') {
            steps {
                echo '=== Test Stage: 在容器内运行测试 ==='
                sh 'docker run --rm cicd-demo:${BUILD_ID} python -m pytest test_app.py -v'
            }
        }

        stage('Deploy') {
            steps {
                echo '=== Deploy Stage: 部署到 Kind 集群 ==='
                sh 'kind load docker-image cicd-demo:${BUILD_ID} --name sre-lab'
                sh "sed -i 's|image: cicd-demo:.*|image: cicd-demo:${BUILD_ID}|g' deployment.yaml"
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl rollout status deployment/cicd-demo --timeout=60s'
                sh 'kubectl get pods -l app=cicd-demo'
            }
            when {
                branch 'main'
            }
        }
    }

    post {
        always {
            echo '清理构建镜像...'
            sh 'docker rmi cicd-demo:${BUILD_ID} || true'
        }
修复 Jenkinsfile

cd ~/cicd-demo

cat > Jenkinsfile << 'EOF'
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo '=== Bui='
                sh 'docker build -t cicd-demo:${BUILD_ID} .'
                sh 'docker images | grep cicd-demo'
            }
        }

        stage('Test') {
            steps {
                echo '=== Test Stage: 在容器内运行测试 ==='
                sh 'docker rupython -m pytest test_app.py-v'
            }
        }

        stage('Deploy') {
            steps {
                echo '=== Deploy Stage: 部署到 Kind 集群 ==='
                sh 'kind loadLD_ID} --name sre-lab'
                sh "sed -i 's|image: cicd-demo:.*|image: cicd-demo:${BUILD_ID}|g'
deployment.yaml"
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl r-demo --timeout=60s'
                sh 'kubectl get pods -l app=cicd-demo'
            }
            when {
                branch 'main'
            }
        }
    }

    post {
        always {
            echo '清理构建镜像...'
            sh 'docker rmi cicd-demo:${BUILD_ID} || true'
        }
        success {
            echo '✅ Pipeline 执行成功！'
        }
        failure {
            echo '❌ Pipeline
        }
    }
}
