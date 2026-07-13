```groovy
pipeline {
    agent any

    parameters {
        choice(name: 'ENTORNO', choices: ['staging', 'produccion'], description: 'Entorno')
        booleanParam(name: 'EJECUTAR_DEPLOY', defaultValue: false, description: '¿Ejecutar deploy?')
    }

    environment {
        APP_NAME = "jenkins-demo-app"
    }

    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Instalar dependencias') {
            steps {
                bat 'node -v'
                bat 'npm install'
            }
        }

        stage('Test') {
            steps {
                bat 'npm test'
            }
        }

        stage('Build') {
            steps {
                bat 'npm run build'
                archiveArtifacts artifacts: 'dist/**', fingerprint: true
            }
        }

        stage('Deploy') {
            when {
                expression { params.EJECUTAR_DEPLOY }
            }
            steps {
                echo "Desplegando ${APP_NAME} en ${params.ENTORNO}"
            }
        }
    }

    post {

        success {
            echo "Pipeline ejecutado correctamente."

            emailext(
                subject: "Jenkins SUCCESS - ${JOB_NAME} #${BUILD_NUMBER}",
                body: """
Hola Daniel,

El pipeline terminó correctamente.

Proyecto: ${JOB_NAME}
Build: ${BUILD_NUMBER}

Estado: SUCCESS

Puedes revisar los detalles aquí:
${BUILD_URL}
""",
                to: "powertecno775@gmail.com"
            )
        }

        failure {
            echo "El pipeline falló."

            emailext(
                subject: "Jenkins FAILED - ${JOB_NAME} #${BUILD_NUMBER}",
                body: """
Hola Daniel,

El pipeline falló.

Proyecto: ${JOB_NAME}
Build: ${BUILD_NUMBER}

Estado: FAILED

Revisa los detalles aquí:
${BUILD_URL}
""",
                to: "powertecno775@gmail.com"
            )
        }

        always {
            echo "Build #${BUILD_NUMBER} finalizado."
        }
    }
}
```
