node {
    try {
        stage('Build') {
            echo 'Starting Build stage...'
            docker.image('python:2-alpine').inside {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py || exit 1'
            }
            echo 'Build stage completed successfully.'
        }

        stage('Test') {
            echo 'Starting Test stage...'
            docker.image('qnib/pytest').inside {
                try {
                    sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py || exit 1'
                } finally {
                    echo 'Publishing test results...'
                    junit 'test-reports/results.xml'
                }
            }
            echo 'Test stage completed successfully.'
        }

        stage('Deliver') {
            echo 'Starting Deliver stage...'
            docker.image('cdrx/pyinstaller-linux:python2').inside {
                sh 'pyinstaller --onefile sources/add2vals.py || exit 1'
            }
            archiveArtifacts 'dist/add2vals'
            echo 'Deliver stage completed successfully.'
        }
    } catch (Exception e) {
        echo "Pipeline failed: ${e.message}"
        throw e
    }
}
