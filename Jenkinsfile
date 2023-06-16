pipeline {
  agent any
  stages {
    stage('Dev-pipeline') {
      steps {
        sh '''    pipeline
        {
            agent any
            
                    environment
                    {
        
                        DEV_SERVER_IP = \'52.66.178.147\';
                        SELENIUM_SERVER = \'3.108.60.202\';
                        QA_SERVER_IP = \'3.110.212.41\';
                    }
                stages
                {
                    stage(\'Dev Deployment\')
                    {
                        steps
                        {
                            sshagent([\'dev-ssh-credentials\'])
                            {
                                   sh \'\'\'
                                    ssh -o StrictHostKeyChecking=no ubuntu@${DEV_SERVER_IP} \'
                                   
                                        sudo fuser -k 80/tcp
                                   
                                        git clone https://github.com/TestLeafInc/jenkins-pipeline
                                        
                                        cd /home/ubuntu/jenkins-pipeline/leafhub
                                        
                                        git pull 
                                        
                                            // Get the commit id --> store it !
                                            // Alternate appach: Make your build with the build number 
                                            // And move to the nexus repo --> Ask your next server
                                        export COMMIT_ID = $(git rev-parse HEAD)
                                        nohup sudo mvn spring-boot:run > /dev/null 2>&1 & 
                                    \'
                                    \'\'\'
                            }
                   
                        }
                    }
                    stage(\'Smoke Tests against Dev\')
                    {
                        steps
                        {
                        // Get call to the Server URL --> 200 
                        sleep(10)
                            sshagent([\'dev-ssh-credentials\'])
                            {
                        
                            sh \'\'\'
                            ssh -o StrictHostKeyChecking=no ubuntu@${SELENIUM_SERVER} \'
                                # Update the package lists for upgrades and new package installations
                                sudo apt-get update                                                       
                        
                                # Install OpenJDK 8
                                sudo apt-get install -y openjdk-8-jdk
                        
                                # Install Maven
                                sudo apt-get install -y maven
                        
                                # Install xvfb
                                sudo apt-get install -y xvfb
                        
                                # Start xvfb
                                Xvfb :99 &
                                export DISPLAY=:99
                        
                                # Download and install Google Chrome
                                if ! command -v google-chrome > /dev/null; then
                                wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
                                sudo dpkg -i google-chrome-stable_current_amd64.deb
                                sudo apt --fix-broken install -y
                                else
                                echo "Google Chrome is already installed."
                                fi
                            
                                # Clone the webdriver-tests repository
                                git clone https://github.com/TestLeafInc/webdriver-leafhub
                            
                                # Move to the webdriver-tests folder
                                cd webdriver-leafhub
                            
                                # pull the changes if any
                                git pull
                            
                                # Run Maven tests
                                mvn clean test -DsuiteXmlFile=smoke.xml -Dserver.ip=52.66.178.147
                                
                            
                                yes | sudo apt install awscli                                                       
                                aws configure set aws_access_key_id AKIA2ZLFKTOADE5HKO5U
                                aws configure set aws_secret_access_key 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
                                aws configure set region ap-south-1
                                aws configure set output json
    
                                # Push the results to S3 (make sure to install and configure awsconfigure before)
                                #aws s3 rb s3://reports12-html12-selenium12 --force
                                aws s3api create-bucket --bucket reports020-html020-selenium020 --region ap-south-1 --create-bucket-configuration LocationConstraint=ap-south-1
                                aws s3 sync reports/ s3://reports020-html020-selenium020
                            
                                \'
                                \'\'\'
                            }
                            
                                
                            }
                            
                        }
                    
        stage(\'QA Deployment\')
        {
            when
            {
                expression {currentBuild.resultIsBetterOrEqualTo(\'SUCCESS\')}
            }
            steps
            {
               sshagent([\'dev-ssh-credentials\'])
               {
                   sh \'\'\'
                   ssh -o StrictHostKeyChecking=no ubuntu@${QA_SERVER_IP} \'
                   
                        sudo fuser -k 80/tcp
                   
                        git clone https://github.com/mithran14/webdriver-leafhub
                            
                                # Move to the webdriver-tests folder
                                #cd webdriver-leafhub
                        
                        cd /home/ubuntu/jenkins-pipeline/leafhub
                        
                        git checkout $COMMIT_ID 
                        
                        nohup sudo mvn spring-boot:run > /dev/null 2>&1 & 
                   
                        \'
                        \'\'\'
               }
               
            }
        }
    }
    
}
                                        '''
      }
    }

  }
}