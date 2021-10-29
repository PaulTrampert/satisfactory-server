pipeline {
    agent any

    triggers {
        upstream(upstreamProjects: 'Docker Images/satisfactory/main', threshold: hudson.model.Result.SUCCESS)
    }

    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }

    stages {
        stage('Deploy Stack') {
            when {
                expression {env.BRANCH_NAME == 'main'}
            }

            steps {
                sh 'docker stack deploy --compose-file docker-compose.yml satisfactory'
            }
        }
    }

    post {
        always {
            sh 'docker system prune --force || true'
        }

        failure {
            emailext body: 'Build log can be found at $BUILD_URL', recipientProviders: [brokenBuildSuspects()], subject: '$JOB_NAME Failed to Deploy', to: 'paul.trampert@ptrampert.com'
        }
    }
}