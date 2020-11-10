u = new utils()
import java.text.SimpleDateFormat

pipeline {
    options {
        buildDiscarder logRotator(numToKeepStr: '5')
        disableConcurrentBuilds()
    }
    agent {
        kubernetes {
            cloud "kubernetes"
            label "rdpmon-dev"
            serviceAccount "jenkins-build"
            yamlFile "helmsman.yaml"
        }
    }
    environment {
        cmAddr = "http://core-chartmuseum.core.svc.cluster.local:8080"
    }
    stages {
        stage("Deploy Dev") {
            when {
                branch "master"
            }
            steps {
                container("helmsman") {
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
