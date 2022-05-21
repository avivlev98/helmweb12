pipeline {
    agent any 
    stages {
        stage('git repo & clean'){
            steps{
                script {
                  try {
                      sh "rm -r helmweb12"
                  } catch (Exception e) {
                      build_ok = true
                      git url: 'https://github.com/avivlev98/helmweb12.git'
                      echo e.toString()
                  }
                }
            }
        }
        stage('Deploy Chart') {
            steps {
                
                kubeconfig(caCertificate: '', credentialsId: 'chartkube', serverUrl: 'https://devops-interview-0b426a9d.hcp.westeurope.azmk8s.io:443') {
                // some block
                sh '''
                
                CHART=webchart12/
                NAMESPACE=aviv
                PACKAGE=avivwebchart12
                
                cd /var/lib/jenkins/workspace/helmpipe/helmweb12/
                DEPLOYED=$(helm list -n $NAMESPACE |grep -E "^${PACKAGE}" |grep DEPLOYED |wc -l)
                
                    if [ $DEPLOYED == 0 ] ; then
                   
                      cd /var/lib/jenkins/workspace/helmpipe/helmweb12/
                      helm install ${PACKAGE} ${CHART} -n $NAMESPACE
                      
                    else
                      helm upgrade ${PACKAGE} ${CHART} -n $NAMESPACE
                    fi
                    echo "deployed!"
                '''
                }
            }
        
        }
    }
}
