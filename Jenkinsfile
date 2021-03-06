pipeline {
    agent any 
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/main_code_updated.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        
        stage('Deliver') { 
            agent any
            environment { 
                VOLUME = '$(pwd)/sources:/src'
                IMAGE = 'cdrx/pyinstaller-linux:python2'
            }
            steps {
                dir(path: env.BUILD_ID) { 
                    unstash(name: 'compiled-results') 
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller --onefile -f main_code_updated.py'" 
                }
            }
            post {
                success {
                    archiveArtifacts "${env.BUILD_ID}/sources/dist/main_code_updated" 
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
                }
            }
        }
    }
}
