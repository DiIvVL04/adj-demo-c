pipeline {
    agent any

    stages {
        // Parar los servicios que ya existen o en todo caso hacer caso omiso
        stage('Parando los servicios...') {
            steps {
                bat '''
                    docker compose -p adj-demo-c2 down || exit /b 0
                '''
            }
        }

        // Eliminar las imágenes creadas por ese proyecto
        stage('Eliminando imágenes anteriores...') {
    steps {
        bat '''
            @echo off
            setlocal enabledelayedexpansion
            set "found="

            for /f "tokens=*" %%i in ('docker images --filter "label=com.docker.compose.project=adj-demo-c2" -q') do (
                set "found=1"
                docker rmi -f %%i
            )

            if defined found (
                echo Imagenes eliminadas correctamente
            ) else (
                echo No hay imagenes por eliminar
            )

            exit /b 0
        '''
    }
}


        // Del recurso SCM configurado en el job, jala el repo
        stage('Obteniendo actualización...') {
            steps {
                checkout scm
            }
        }

        // Construir y levantar los servicios
        stage('Construyendo y desplegando servicios...') {
            steps {
                bat '''
                    @echo off
                    echo Limpiando contenedores previos...
                    for /f "tokens=*" %%c in ('docker ps -a -q --filter "name=adj-database"') do (
                        echo Eliminando contenedor conflictivo: %%c
                        docker rm -f %%c
                    )

                    echo Bajando servicios antiguos...
                    docker compose -p adj-demo-c2 down --remove-orphans || exit /b 0

                    echo Reconstruyendo y levantando servicios nuevos...
                    docker compose -p adj-demo-c2 up --build -d

                    echo Servicios levantados correctamente.
                    exit /b 0
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline ejecutado con éxito'
        }

        failure {
            echo 'Hubo un error al ejecutar el pipeline'
        }

        always {
            echo 'Pipeline finalizado'
        }
    }
}

