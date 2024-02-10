node {
    try {
        stage('Checkout') {
            checkout scm
        }

        stage('Build') {
            docker.image('python:2-alpine').inside {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        
        stage('Test') {
            docker.image('qnib/pytest').inside {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
        }

        stage('Manual Approval') {
            def userInput = input(id: 'Confirm', message: 'Lanjutkan ke tahap Deploy?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: 'Yes untuk lanjut', name: 'Proceed']])
            if (!userInput) {
                error("Pengguna membatalkan deployment.")
            }
        }

        stage('Deploy') {
            docker.image('python:2-alpine').inside {
                sh 'pip install pyinstaller && pyinstaller --onefile sources/add2vals.py'
            }
        }
    } catch (Exception err) {
        throw err
    } finally {
        if (currentBuild.currentResult == 'SUCCESS') {
            stage('Post Success') {
                archiveArtifacts 'dist/add2vals'
            }
        }
    }
}
