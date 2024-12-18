pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "rhxpchispi/myapp:dev"
        EC2_PUBLIC_IP = "107.20.26.223"
        SSH_KEY = "./terraform-ec2-key" // Ruta de la llave SSH en el repositorio
        DOCKER_CONTAINER_NAME = "myapp-en-aws" // Nombre del contenedor que se va a desplegar
    }

    stages {
        stage('Deploy Docker Image') {
            steps {
                script {
                    // Conexión SSH para desplegar el contenedor
                    sh """
                    chmod 400 terraform-ec2-key
                    ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no ubuntu@${EC2_PUBLIC_IP} << EOF
                        set -e
                        
                        echo "===> Tirando la última versión de la imagen: ${DOCKER_IMAGE}"
                        docker pull ${DOCKER_IMAGE}
                        
                        echo "===> Deteniendo el contenedor si está en ejecución"
                        docker stop ${DOCKER_CONTAINER_NAME} || true
                        docker rm ${DOCKER_CONTAINER_NAME} || true

                        echo "===> Desplegando el contenedor"
                        docker run -d --name ${DOCKER_CONTAINER_NAME} -p 80:80 ${DOCKER_IMAGE}
                    EOF
                    """
                }
            }
        }
    }
}