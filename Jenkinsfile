pipeline {
    agent any
            stages {
             stage('preparation') {
                   steps {
                         sh 'sudo apt-get install -y unzip'
                         sh 'curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"'
                         sh 'unzip awscliv2.zip -y'
                         sh './aws/install'
                         sh 'apt-get install python-pip -y'
                         sh 'pip install wheel'
                         }
              }
             stage('deregistering instance') {
                    steps {
                          sh 'aws elb deregister-instances-from-load-balancer --load-balancer-name nouman-classic-lb --instances i-0633d6cd62185c1b9'
                          }
               } 
              stage('preparation 2') {
                    steps {
                          sh 'pip wheel  -r requirements.txt'
                          }
               }
              stage('transfer artifacts') {
                    steps {
                          sshPublisher(
                          publishers:
                          [sshPublisherDesc
                          (configName: 'webserver',
                           transfers: [sshTransfer(
                           excludes: 'jenkinsfile,pipeline.groovy',
                           execCommand: 'sudo apt-get install python-pip -y;cd /tmp/tmp/;pip install *.whl;sudo apt install python3-flask -y;sudo apt-get install at;sudo systemctl start atd;sudo systemctl enable atd;cd 01-hello-world/;export FLASK_APP=hello.py;killall flask;echo "flask run --host=0.0.0.0" | at -m now',
                           execTimeout: 35000,
                           flatten: false,
                           makeEmptyDirs: true,
                           noDefaultExcludes: false,
                           patternSeparator: '[, ]+',
                           remoteDirectory: '/tmp',
                           remoteDirectorySDF: false,
                           removePrefix: '', sourceFiles: '**/*')],
                           usePromotionTimestamp: false,
                           useWorkspaceInPromotion: false,
                           verbose: true)])
                          }
              }
             stage('registering instance') {
                    steps {
                          sh 'aws elb register-instances-with-load-balancer --load-balancer-name nouman-classic-lb --instances i-0633d6cd62185c1b9'
                          }
               }  
              }
}
