def check_runs = new com.functions.buildGithubCheckScript()

pipeline {
    agent {
        kubernetes {
            label "GitHub-Check-Runs-${UUID.randomUUID().toString().substring(0,8)}"
            defaultContainer 'jnlp'
        }
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    git (credentialsId: 'github-token',
                    url: "https://github.com/devops-factory/" + repoName,
                    branch: "master")
                }
            }
        }
        stage("Build") {
            steps {
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: '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', keyFileVariable: 'privateKey', passphraseVariable: '', usernameVariable: '')]) {
                        try {
                            build_command = sh(script: "mvn clean install", returnStatus: true)
                            check_runs.buildGithubCheck(develop, privateKey, 'success', "build")
                        } catch(Exception e) {
                            check_runs.buildGithubCheck(develop, privateKey, 'failure', "build")
                            echo "Exception: ${e}"
                        }
                    }
                }
            }
        }
        stage("Unit Test") {
            steps {
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: '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', keyFileVariable: 'privateKey', passphraseVariable: '', usernameVariable: '')]) {
                        try {
                            def test = sh(script: "python unitTest.py", returnStdout: true)
                            check_runs.buildGithubCheck(develop, privateKey, 'success', "unit-test")
                        } catch(Exception e) {
                            check_runs.buildGithubCheck(develop, privateKey, 'failure', "unit-test")
                            echo "Exception: ${e}"
                        }
                    }
                }
            }
        }
        stage("Integration Test") {
            steps {
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: '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', keyFileVariable: 'privateKey', passphraseVariable: '', usernameVariable: '')]) {
                        try {
                            def test = sh(script: "python integrationTest.py", returnStdout: true)
                            check_runs.buildGithubCheck(<REPO_NAME>, <COMMIT_ID>, privateKey, 'success', "integration-test")
                        } catch(Exception e) {
                            check_runs.buildGithubCheck(<REPO_NAME>, <COMMIT_ID>, privateKey, 'failure', "integration-test")
                            echo "Exception: ${e}"
                        }
                    }
                }
            }
        }
    }
}
