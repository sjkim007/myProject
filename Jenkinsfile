node {
    properties( [pipelineTriggers([[$class: "GitHubPushTrigger"]]), disableConcurrentBuilds()] )
    def MAUM_ROOT = "/CI/jenkins_build_space/${currentBuild.startTimeInMillis}"

    try {
        withDockerContainer(image: 'maum:latest', args: '-v /home/maum:/home/maum -v /CI/sonar-scanner-3.0.3.778-linux:/CI/sonar-scanner-3.0.3.778-linux -v /CI/jenkins_build_space:/CI/jenkins_build_space -v /usr/lib/node_modules:/usr/lib/node_modules') {
            stage('Clone repository') {
                echo 'Cloning...'
                checkout scm
                sh 'git clean -fdx'
                sh 'git submodule foreach git clean -fdx'
            }

            stage('Build') {
                echo 'Building...'
                timeout(time: 1, unit: 'HOURS') {
                    sh "LD_LIBRARY_PATH=${MAUM_ROOT}/lib ./build.sh ${MAUM_ROOT}"
                }
            }

            stage('Test') {
                echo 'Testing'
                sh "cd ${MAUM_ROOT} && ls -al && cd bin && export MAUM_ROOT=${MAUM_ROOT} && export PATH=$PATH:${MAUM_ROOT}/bin && ./setup config -d && ./svd"
                // 테스트 관련 코드 여기에 추가하시면 됩니다.

                // withSonarQubeEnv {
                //     sh 'sonar-scanner'
                // }
                // sh 'sleep 10'
                // timeout(time: 1, unit: 'HOURS') {
                //     def qg = waitForQualityGate()
                //     if (qg.status != 'OK') {
                //         error "Test Failed"
                //     }
                // }
            }
        }
    }
    catch (err) {
        echo 'ERROR'
        throw err
    }
}

