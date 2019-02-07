pipeline {
    agent any

    stages {
        stage('Configure'){
            steps {
                sh '''
                mkdir -p build;
                cd build;
                cmake -G "Unix Makefiles" \
                      -DOPTION_BUILD_UTILS=ON \
                      -DOPTION_BUILD_EXAMPLES=ON \
                      -DCMAKE_INSTALL_PREFIX=./install \
                      -DCMAKE_BUILD_TYPE=Debug \
                      ..
               '''
            }
        }
        stage('Build') {
            steps {
                echo 'Building..'
                sh '''
                   cd build
                   make -j \
                   '''

            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'

                ## For almost anything to work, fusermount3 has to be setuid root.
                ## To accomplish this - in a reasonably secure manner - under 
                ## a Jenkins job (which normally runs as an unprivileged user) 
                ## we need two 'helpers' and make use of a customized suders rule file for Jenkins.
                ## etc/sudoers.d/jenkins looks like this:
                ##     jenkins <HOSTNAME> = (root) NOPASSWD: /usr/bin/chmod-jenkins, /usr/bin/chown-jenkins
                ## The two files just call the real chown and chmod like this:
                ##  /usr/bin/chown "$@"
                ##   and
                ##  /usr/bin/chmod "$@"
                ## Don't forget to set executable on them !
                sh '''
                   cd build
                   sudo /usr/bin/chown-jenkins root:root util/fusermount3
                   sudo /usr/bin/chmod-jenkins 4755 util/fusermount3
                   python3.6 -m pytest test/
                   '''
                    
            }
        }
    }
}
