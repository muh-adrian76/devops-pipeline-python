node {
    try {
        stage('Build') {
            docker.image('python:2-alpine').inside {
                sh 'ls -l'
                sh 'ls -l ./sources'
                sh 'python -m py_compile ./sources/add2vals.py ./sources/calc.py || exit 1'
            }
        }

        stage('Test') {
            docker.image('qnib/pytest').inside {
                try {
                    sh 'py.test --verbose --junit-xml test-reports/results.xml ./sources/test_calc.py || exit 1'
                } finally {
                    echo 'Publishing test results...'
                    junit 'test-reports/results.xml'
                }
            }
        }
    } catch (Exception e) {
        echo "Pipeline failed: ${e.message}"
        throw e
    }
}
