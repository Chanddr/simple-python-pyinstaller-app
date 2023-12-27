node {
    stage('Build') {
        docker.image('python:3.12.1-alpine3.19').inside {
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
        environment {
            VOLUME = VOLUME
            IMAGE = IMAGE
        }
        steps {
            dir(path: env.BUILD_ID) {
                unstash(name: 'compiled-results')
                sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
            }
        }
        post {
            success {
                archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
                sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
            }
        }   
    }
}