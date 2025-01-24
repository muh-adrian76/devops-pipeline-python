node {
    stage('Build') {
        docker.image('python:2-alpine').inside('-v $WORKSPACE:$WORKSPACE -w $WORKSPACE') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }

    stage('Test') {
        docker.image('qnib/pytest').inside('-v $WORKSPACE:$WORKSPACE -w $WORKSPACE') {
            try {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            } finally {
                junit 'test-reports/results.xml'
            }
        }
    }

    stage('Deliver') {
        docker.image('cdrx/pyinstaller-linux:python2').inside('-v $WORKSPACE:$WORKSPACE -w $WORKSPACE') {
            try {
                sh 'pyinstaller --onefile sources/add2vals.py'
            } finally {
                archiveArtifacts 'dist/add2vals'
            }
        }
    }
}
