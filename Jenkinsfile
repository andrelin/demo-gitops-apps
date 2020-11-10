import java.text.SimpleDateFormat

pipeline {
    options {
        buildDiscarder logRotator(numToKeepStr: '5')
        disableConcurrentBuilds()
    }
    agent {
        kubernetes {
            cloud "kubernetes"
            label "apps"
            serviceAccount "jenkins"
            yamlFile "demo-deployer.yaml"
        }
    }
    stages {
        stage("Deploy Apps") {
            when {
                branch "master"
            }
            steps {
                container("demo-deployer") {
                    withCredentials([
                        usernamePassword(
                            credentialsId: "chartmuseum",
                            usernameVariable: "CHARTMUSEUM_USER",
                            passwordVariable: "CHARTMUSEUM_PASSWORD"
                        ),
                    ]) {
                        script {
                            lock("deploy-apps")) {
                                sh "kubectl apply -f _resources/application.yaml"
                                sh "helmsman --debug --apply -f dsf.toml --kubeconfig kubeconfig.yaml"
                            }
                        }
                    }
                }
            }
        }
    }
}
