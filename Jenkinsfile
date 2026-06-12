pipeline {
    agent any

    environment {
        IMAGE_NAME = "myapp"
        CONTAINER_NAME = "myapp-container"
        PORT = "8081"
    }

    stages {

        stage('Maven 打包') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('构建镜像') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }

        stage('停止旧容器') {
            steps {
                sh """
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                """
            }
        }

        stage('启动新容器') {
            steps {
                sh """
                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p ${PORT}:${PORT}\
                        ${IMAGE_NAME}:${BUILD_NUMBER}
                """
            }
        }
    }

    post {
        success {
            echo "✅ 部署成功！访问 http://localhost:${PORT}/hello 验证"
        }
        failure {
            echo "❌ 构建失败，请查看日志"
        }
    }
}