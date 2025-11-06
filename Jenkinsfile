pipeline {
    agent any

    stages {
        //Parar todos los servicios
        stage('Parando todos los servicios'){
            steps {
                sh '''
                    docker compose -p adj-demo-c down || true
                '''
            }
        
    }

        //Eliminar las imagenes anteriores
        stage('Eliminando imagenes antiguas'){
            steps {
                sh '''
                    IMAGES=$(docker images --filter "label=com.docker.compose.project=adj-demo-c" -q)
                    if [ -n '$IMAGES' ]; then
                        docker images rmi $IMAGES
                    else
                        echo "No hay imagenes por borrar"
                    fi
                '''
            
            }
        }
        //Bajar la actualizacion
        stage('Actualizando...'){
            steps{
                checkout scm
            }
        }


        //Levantar y desplegar el proyecto
        stage('Construyendo y desplegando...'){
            steps {
                sh '''
                    docker compose up --build -d
                '''
            }

        }
    }


    post {
        success {
            echo 'Pipeline ejecutado exitosamente.'
        }

        failure {
            echo 'Error al ejecutar el pipeline.'
        }

        always {
            echo 'Pipeline finalizado.'
        }
    }
}