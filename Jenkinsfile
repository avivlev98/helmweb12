pipeline {
    agent any 
    stages {
      stage('git repo & clean'){
        steps {
          // Trying to clone new changes from git, deleting exsisting files, if the files doesnt exsists i tell him to mark it as OK and clone the new ones.
          catchError {
            script {
              try {
                    sh "rm -r helmweb12"
                    sh "rm -r webchart12"
                    sh "git clone git@github.com:avivlev98/helmweb12.git"
                  } catch (Exception e) {
                      build_ok = true
                      sh "git clone git@github.com:avivlev98/helmweb12.git"
                      echo e.toString()
                  }
                }
            }
        }
        post {
          success {
              echo 'git repo & clean stage successful'
            }
            failure {
              echo 'Compile stage failed'
              error('Stage is aborted due to failure of git repo & clean stage')

            }
          }
        }
      
               
        stage('Deploy Chart') {
          steps {
            catchError {
              // Connect to AKS check if the chart exsists so just upgrade it to the newer version, else create a new one.
              kubeconfig(caCertificate: '', credentialsId: 'chartkube', serverUrl: 'https://devops-interview-0b426a9d.hcp.westeurope.azmk8s.io:443') {
                // some block
                sh '''
                
                CHART=webchart12/
                NAMESPACE=aviv
                PACKAGE=avivwebchart12
                
                cd /var/lib/jenkins/workspace/helmpipe/helmweb12/
                DEPLOYED=$(helm list -n $NAMESPACE |grep -E "^${PACKAGE}" |grep deployed |wc -l)
                
                    if [ $DEPLOYED -eq 0 ] ; then
                   
                      #cd /var/lib/jenkins/workspace/helmpipe/helmweb12/
                      helm install ${PACKAGE} ${CHART} -n $NAMESPACE
                      
                    else
                      helm upgrade ${PACKAGE} ${CHART} -n $NAMESPACE
                    fi
                    echo "deployed!"
                '''
            }
          }
        }
        post {
          success {
              echo 'Deploy stage successful'
            }
            failure {
              echo 'Deploy stage failed'
              error('Deploy is aborted due to failure of deploy stage')

            }
          }
        }
    }
}

