node {
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
        input message: 'Lanjutkan ke tahap Deploy?', ok:'Lanjutkan'
    }
    stage('Deploy') {
        docker.image('cdrx/pyinstaller-linux:python2') {
            try {
                sh 'pyinstaller --onefile sources/add2vals.py'
            } catch (Exception e) {
                echo 'Error: ' + e.toString()
            } finally {
                success {
                    archiveArtifacts 'dist/add2vals'
                    sleep 60
                }
            }
        }    
    }
}