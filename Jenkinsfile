pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Building containers and starting..'
                echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
                echo "workspace : ${env.WORKSPACE}"
                //sh 'cd /home/epad/thick_test_v4_october_26_plugintest/epad_lite_dist'
                //sh 'ls -l'
                dir("${env.WORKSPACE}/testFolder"){
                    sh "pwd"
                    // sh 'wget ftp://epad-public.stanford.edu/epadLite/epad-dist-0.4.zip'
                    sh 'git clone https://github.com/RubinLab/epad-dist.git'
                    sh 'ls -l'
                    // sh 'unzip  epad-dist-0.4.zip'
                    // sh ''
                }
                dir("${env.WORKSPACE}/testFolder/"){
                    sh 'mkdir couchdbloc'
                    sh 'mkdir mariadbloc'
                    sh 'mkdir pluginData'
                    sh 'mkdir tmp'
                    sh 'chmod 777 couchdbloc'
                    sh 'chmod 777 mariadbloc'
                    sh 'chmod 777 pluginData'
                    sh 'chmod 777 tmp'
                   
                }
                // dir("${env.WORKSPACE}/testFolder/epad-dist-master/"){
                dir("${env.WORKSPACE}/testFolder/epad-dist/"){
                    sh 'ls -l'
                    sh 'pwd'
                    sh 'cp /home/epad/epad-dist/epad.yml ./'
                    sh "sed ':a;N;\$!ba;s/branch[^/\\n]*/branch:${env.BRANCH_NAME}/3 w epadedited.yml' epad.yml"
                    sh 'rm epad.yml'
                    sh 'mv epadedited.yml epad.yml'
                    sh 'cat epad.yml'
                    sh './configure_epad.sh ../epad_lite_dist ./epad.yml'
                   
                }
                dir("${env.WORKSPACE}/testFolder/epad_lite_dist/"){
                    withEnv(['COMPOSE_HOME=/usr/local/bin']) {
                        sh 'docker stop epadlitetestcont || true'
                        sh 'docker rm epadlitetestcont || true'
                        sh '$COMPOSE_HOME/docker-compose build --no-cache'
                        sh '$COMPOSE_HOME/docker-compose up -d'
                     }
                }
            }
        }
        stage('check all containers health status') {
            steps {
                 waitUntil {
                    script{
                        output = sh(returnStdout: true, script: 'docker ps -a --filter health=healthy | wc -l')
                        if (output.toInteger() >= 4){
                            echo "containers are ready"
                            return true
                        }else{
                            return false
                        }
                    }
                 }
                echo 'Deploying....'
                   sh 'docker ps -a'
                echo 'finished....'
                //dir("${env.WORKSPACE}/"){
                //        sh 'sudo rm -rf ./*'
                //}
            }
        }
        stage('Build test container) {
            //agent { dockerfile true }
            steps {
                dir("${env.WORKSPACE}/testFolder/"){
                    sh 'mkdir newbuild'
                }
                dir("${env.WORKSPACE}/testFolder/newbuild"){
                    sh 'docker rmi testjs:latest || true'
                    sh "git clone -b ${env.BRANCH_NAME} https://github.com/RubinLab/epadjs.git ./"
                    sh "cd epadjs"
                    sh 'cp /home/epad/Dockerfile ./'
                    sh 'ls -l'
                    sh 'pwd'
                    script{
                        dockerImage = docker.build("testjs:latest","--no-cache")
                    }
                   sh 'docker run --name epadjstestcont --detach testjs:latest'
                }
            }
        }
        stage('start testing') {
            steps {
                echo 'Testing..'
                sh 'printenv'
                echo "using the branch ${env.BRANCH_NAME}"
                echo "using the commit ${env.GIT_COMMIT}"
                echo "using local branch ${env.GIT_LOCAL_BRANCH}"
                sh 'docker exec epadjstestcont sh -c "cd /app && ./node_modules/.bin/react-scripts test --watchAll=false"'
             
                
            }
               // post {
                //  always {
                 //       dir("${env.WORKSPACE}/testFolder/epad_lite_dist/"){
                  //          withEnv(['COMPOSE_HOME=/usr/local/bin']) {
                   //             sh '$COMPOSE_HOME/docker-compose down'
                    //         }
                    //    }
                 // }

                  //success {
                  //    githubNotify status: "SUCCESSFUL" , description: "building succeed for "
                  //}

                  //failure {
                  //    githubNotify status: "FAILED" , description: "building failed for "
                  // }
               // }
        }
        stage('clean after test') {
            //agent { dockerfile true }
            steps {
                dir("${env.WORKSPACE}/"){
                    sh 'sudo rm -rf testFolder'
                }
                sh 'docker stop epadjstestcont || true'
                sh 'docker rm epadjstestcont || true'
            }
        }
    }
}
