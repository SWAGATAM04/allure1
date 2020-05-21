node {

    stage('Clone repo') {
        git branch: "${APPLICATION_BRANCH}", url: "${APPLICATION_URL}", credentialsId: "jenkins-github"
    }

    stage('Build app') {
        docker.image('php:7.2').inside('-v /var/run/docker.sock:/var/run/docker.sock') {
            sh "php init --env=Development"
        }
        docker.image('composer').inside('-v /var/run/docker.sock:/var/run/docker.sock') {
            sh "composer config -g github-oauth.github.com ${GITHUB_TOKEN}"
            sh "composer update --lock --ignore-platform-reqs"
            sh "composer install --optimize-autoloader --ignore-platform-reqs"
        }
    }

    try {
        stage('Backend tests') {
            docker.image('codeception/codeception').inside('-v /var/run/docker.sock:/var/run/docker.sock --entrypoint=""') {
                sh "/repo/codecept run -c backend/ unit"
            }
        }

        stage('Frontend tests') {
            docker.image('codeception/codeception').inside('-v /var/run/docker.sock:/var/run/docker.sock --entrypoint=""') {
                sh "/repo/codecept run -c frontend/ unit"
            }
        }
    } finally {
        stage('Reports') {
            allure([
                includeProperties: false,
                jdk: '',
                properties: [],
                reportBuildPolicy: 'ALWAYS',
                results: [[path: 'backend/tests/_output/allure-results'],[path: 'frontend/tests/_output/allure-results']]
            ])
        }
    }
}
