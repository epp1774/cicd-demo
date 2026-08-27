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
                echo '=== Test Stage: 运行单元测试 ==='
                sh 'pip3 install -r requirements.txt && python3 -m pytest test_app.py -v'
            }
        }

        stage('Deploy') {
            steps {
                echo '=== Deploy Stage: 部署到 Kind 集群 ==='
                echo '生产环境：kubectl apply -f deployment.yaml'
                echo '（需配置 kubectl + kubeconfig 后启用）'
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
            echo '❌ Pipeline 执行失败，请检查上方日志'
        }
    }
}
