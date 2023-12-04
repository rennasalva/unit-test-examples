pipeline {
  agent any
  stages {

    stage('Git Checkout') {
            steps {
                script {
                        git branch: 'main',
                        url: 'https://github.com/rennasalva/unit-test-examples'
                }
            }
    }

    
    stage("Reuse Docker Image Across Sub-stages") {
            agent {
              docker {
                image 'remote_zend_php_builder'
                args '--entrypoint=""'
                reuseNode true
              }
          }
          stages {
              stage('PHP ZENDPHP COMPOSER INSTALL') {
              steps {
                echo 'Running PHP 7.4 tests...'
                sh 'php -v && php --ri xdebug'
                echo 'Installing from  Composer'
                sh 'cd $WORKSPACE && composer install --no-progress --ignore-platform-reqs'            
                echo 'Running PHPUnit tests...'
                sh 'php $WORKSPACE/vendor/bin/phpunit -c $WORKSPACE/phpunit.xml  --log-junit $WORKSPACE/reports/report-junit.xml  --coverage-clover $WORKSPACE/reports/clover.xml --testdox-html $WORKSPACE/reports/testdox.html'
                sh 'chmod -R a+w $PWD && chmod -R a+w $WORKSPACE'
                junit '**/reports/*.xml'
              }
        }
        
        stage('PHP ZENDPHP Checkstyle Report') {
          steps {
            sh 'vendor/bin/phpcs  --report-file=$WORKSPACE/reports/checkstyle.xml --standard=$WORKSPACE/phpcs.xml --extensions=php,inc --ignore=autoload.php --ignore=$WORKSPACE/vendor/  $WORKSPACE/src' 
          }
          
        }
        
         stage('PHP ZENDPHP Mess Detection Report') {
          steps {
             sh 'vendor/bin/phpmd $WORKSPACE/src xml  $WORKSPACE/phpmd.xml --reportfile $WORKSPACE/reports/pmd.xml --exclude $WORKSPACE/vendor/ --exclude autoload.php'
          }
          
        }
    
      stage('Report Coverage') {
        steps {
          echo 'GENERATE REPORT CODE COVERAGE.'
          
          publishHTML (target: [
                  allowMissing: false,
                  alwaysLinkToLastBuild: false,
                  keepAll: true,
                  reportDir: './coverage',
                  reportFiles: 'index.html',
                  reportName: "Coverage Report"

          ])
        }
        
      }
  }
}

    
    
    stage('ANSIBLE PING') {
        
          steps {
             sh 'ansible all -i /var/jenkins_home/ansible/hosts --module-name ping'
          }
          
        }
        
    stage('ANSIBLE RUN PLAYBOOK') {
    
          steps {
             sh 'ansible-playbook /var/jenkins_home/ansible/tasks/play.yml  -i /var/jenkins_home/ansible/hosts '
          }
          
        }
    
  }
} 