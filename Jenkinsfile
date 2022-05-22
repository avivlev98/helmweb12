pipeline {
    agent any 
    stages{
      stage('git repo & clean'){
          steps {
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
        
        stage('Deploy or delete'){
            steps{
                script {
                        // Define Variable
                        
                        def USER_INPUT = input(
                                message: 'Are you want to Deploy/Upgrade or to Delete the chart ?',
                                parameters: [
                                        [$class: 'ChoiceParameterDefinition',
                                        choices: ['Deploy/Upgrade','Delete Chart'].join('\n'),
                                        name: 'input',
                                        description: 'Menu - select box option']
                                ])

                        echo "The answer is: ${USER_INPUT}"

                        if( "${USER_INPUT}" == "Deploy/Upgrade"){
                            kubeconfig(caCertificate: '', credentialsId: 'chartkube', serverUrl: 'https://devops-interview-0b426a9d.hcp.westeurope.azmk8s.io:443') {
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


                            
                        } else {
                            //do something else
                            kubeconfig(caCertificate: '', credentialsId: 'chartkube', serverUrl: 'https://devops-interview-0b426a9d.hcp.westeurope.azmk8s.io:443') {
                              sh '''
                
                              CHART=webchart12/
                              NAMESPACE=aviv
                              PACKAGE=avivwebchart12
                            
                              cd /var/lib/jenkins/workspace/helmpipe/helmweb12/
                              helm uninstall ${PACKAGE} -n $NAMESPACE
                        '''
                            }
                        }
        }
            }
        }
    }
 }
