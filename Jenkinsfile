pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
                echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
                echo "workspace : ${env.WORKSPACE}"
                //sh 'cd /home/epad/thick_test_v4_october_26_plugintest/epad_lite_dist'
                //sh 'ls -l'
                // dir("${env.WORKSPACE}/testFolder"){
                //   sh "pwd"
                 //  sh 'wget ftp://epad-public.stanford.edu/epadLite/epad-dist-0.4.zip'
                  // sh 'ls -l'
                  // sh 'unzip  epad-dist-0.4.zip'
               // }
                dir("/home/epad/epad-dist-master/"){
                    sh 'ls -l'
                    sh './configure_epad.sh ../epad_lite_dist ./epad.yml'
                }
                //sh 'mkdir testFolder'
                //sh 'cd testFolder'
                //sh 'wget ftp://epad-public.stanford.edu/epadLite/epad-dist-0.4.zip'
                //sh 'ls -l'
                //sh 'unzip  epad-dist-0.4.zip'
                //sh 'ls -l'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
