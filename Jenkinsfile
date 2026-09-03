pipeline {
    agent any
    environment {
        HARBOR_URL = "192.168.83.200:8888"
        HARBOR_PROJECT = "cicd-demo"
    }
    stages {
        stage("Build") {
            steps {
                echo "=== Build Stage: 构建 Docker 镜像 ==="
                sh 'docker build -t ${HARBOR_URL}/${HARBOR_PROJECT}/app:${BUILD_ID} .'
                sh 'docker images | grep cicd-demo'
            }
        }
        stage("Test") {
            steps {
                echo "=== Test Stage: 在容器内运行测试 ==="
                sh 'docker run --rm ${HARBOR_URL}/${HARBOR_PROJECT}/app:${BUILD_ID} python -m pytest test_app.py -v'
            }
        }
        stage("Push") {
            steps {
                echo "=== Push Stage: 推送镜像到 Harbor ==="
                sh 'docker push ${HARBOR_URL}/${HARBOR_PROJECT}/app:${BUILD_ID}'
            }
        }
        stage("Deploy") {
            steps {
                echo "=== Deploy Stage: 使用 Helm 部署到 Kind ==="
                sh "helm upgrade --install cicd-demo ./helm-chart --set image.tag=${BUILD_ID} --namespace default"
                sh 'kubectl rollout status deployment/cicd-demo --timeout=60s'
                sh 'kubectl get pods -l app=cicd-demo'
            }
            when {
                expression { return env.GIT_BRANCH == 'origin/main' }
            }
        }
    }
    post {
        always { echo '清理本地镜像...'; sh 'docker rmi ${HARBOR_URL}/${HARBOR_PROJECT}/app:${BUILD_ID} || true' }
        success { echo '✅ Pipeline 执行成功！' }
        failure { echo '❌ Pipeline 执行失败，请检查上方日志' }
    }
}
