pipeline {
  agent {
    node {
      label 's390x'
    }

  }
  stages {
    stage('Info') {
      steps {
        script {
          INPUT = input id: 'job_type',
          message: "Choose One:",
          ok: "Continue",
          parameters: [ booleanParam(name: 'Build', defaultValue: false),
          booleanParam(name: 'Deployment', defaultValue: false)]
        }

        echo "${INPUT['Build']}"
      }
    }
    stage('Config') {
      when {
        expression {
          INPUT.Deployment == true
        }

      }
      steps {
        script {
          INPUTCONFIG = input id: 'configmap',
          message: "Choose One:",
          ok: "Continue",
          parameters: [ booleanParam(name: 'ConfigMap', defaultValue: false),
          booleanParam(name: 'Deployment', defaultValue: false)]
        }

        echo "${INPUTCONFIG['ConfigMap']}"
      }
    }
    stage('Build Image') {
      when {
        expression {
          INPUT.Build == true
        }

      }
      parallel {
        stage('x86_64') {
          agent {
            node {
              label 'x86_64'
            }

          }
          steps {
            echo "${Build}"
            sh 'docker build -t srtb -f Dockerfile-x86_64 .'
          }
        }
        stage('s390x') {
          agent {
            node {
              label 's390x'
            }

          }
          steps {
            sh 'docker build -t srtb -f Dockerfile-s390x .'
          }
        }
      }
    }
    stage('tag') {
      when {
        expression {
          INPUT.Build == true
        }

      }
      parallel {
        stage('x86_64') {
          agent {
            node {
              label 'x86_64'
            }

          }
          steps {
            sh 'docker tag srtb registry:5000/sicoob/infraestrutura:x86_64-1.0'
          }
        }
        stage('s390x') {
          agent {
            node {
              label 's390x'
            }

          }
          steps {
            sh 'docker tag srtb registry:5000/sicoob/infraestrutura:s390x-1.0'
          }
        }
      }
    }
    stage('push') {
      when {
        expression {
          INPUT.Build == true
        }

      }
      parallel {
        stage('x86_64') {
          agent {
            node {
              label 'x86_64'
            }

          }
          steps {
            sh 'docker push registry:5000/sicoob/infraestrutura:x86_64-1.0'
          }
        }
        stage('s390x') {
          agent {
            node {
              label 's390x'
            }

          }
          steps {
            sh 'docker push registry:5000/sicoob/infraestrutura:s390x-1.0'
          }
        }
      }
    }
    stage('clean') {
      when {
        expression {
          INPUT.Build == true
        }

      }
      parallel {
        stage('x86_64') {
          agent {
            node {
              label 'x86_64'
            }

          }
          steps {
            sh '''docker rmi -f registry:5000/sicoob/infraestrutura:x86_64-1.0
docker rmi -f srtb
'''
          }
        }
        stage('s390x') {
          steps {
            sh '''docker rmi -f registry:5000/sicoob/infraestrutura:s390x-1.0
docker rmi -f srtb'''
          }
        }
      }
    }
    stage('ConfigMap') {
      agent {
        node {
          label 'kubectl'
        }

      }
      when {
        expression {
          INPUTCONFIG.ConfigMap == true
        }

      }
      steps {
        sh '''cd hom;
kubectl -n sicoobnet-mobile-pessoal create configmap config-geral $(
for i in $(ls)
do
        echo "--from-file=$i"
done) -o=yaml --dry-run > config-geral.yaml ;

kubectl -n sicoobnet-mobile-pessoal apply -f config-geral.yaml

'''
      }
    }
    stage('Deployment') {
      agent {
        node {
          label 'kubectl'
        }

      }
      when {
        expression {
          INPUTCONFIG.ConfigMap == true
        }

      }      
      steps {
        sh '''cd deployments ;
kubectl -n sicoobnet-mobile-pessoal delete deployment ctr;        
kubectl -n sicoobnet-mobile-pessoal create -f deployments.yaml'''
      }
    }
  }
}
