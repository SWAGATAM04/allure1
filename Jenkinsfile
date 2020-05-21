pipeline {
agent {
label 'Ubuntu_14'
}
stages {
stage('test') {
steps {
sh 'behave -f allure_behave.formatter:AllureFormatter -o ./allure-results -D browser=chrome feature_file.feature'
}
}
stage('reports') { steps { script { allure([ includeProperties: false, jdk: '', properties: [], reportBuildPolicy: 'ALWAYS', results: [[path: './allure-results']] ]) } } }
}
post('Publish report') {
always {
script { allure([ includeProperties: false, jdk: '', properties: [], reportBuildPolicy: 'ALWAYS', results: [[path: './allure-results']] ]) }
}
}

}
