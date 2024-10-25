pipeline {
    agent any
    
    tools   {
        jdk "jdk17"
        maven "maven3"
    }
    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')    // Fetch AWS Access Key ID from Jenkins credentials
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')// Fetch AWS Secret Access Key from Jenkins credentials
        SCANNER_HOME = tool "sonar-scanner"
        
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "4.213.167.198:8081"
        NEXUS_REPOSITORY = "maven-snapshots"
        NEXUS_CREDENTIAL_ID = "f06037c7-fef2-4229-a71b-84ffb198bd0f"
    }
        
    stages {
        // stage('Hello') {
        //     steps {
        //         echo 'Hello World'
        //     }
        // }
        
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/VarmaRahul/Ekart.git'
            }
        }

        stage('Code Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }
        
        // stage('Unit Testing') {
        //     steps {
        //         sh 'mvn test'
        //     }
        // }
        
        
        // stage('SonarQube Analysis') {
        //     steps {
        //         withSonarQubeEnv('sonar-server') {
        //             sh """ $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=ekart \
        //             -Dsonar.java.binaries=. \
        //             -Dsonar.projectKey=ekart """
        //         }
        //     }
        // }
        
        // stage("OWASP Dependency Check"){
        //     steps{
        //         dependencyCheck additionalArguments: "--scan ./ ", odcInstallation: "DP-Check"
        //         dependencyCheckPublisher pattern: "**/dependency-check-report.xml"
        //     }
        // }

        stage("Build Artifact"){
            steps{
                sh " mvn clean install -DskipTests"
            }
        }
        
        /*
        stage("Publish to Nexus Repository Manager") {
            steps {
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
        */
        
        stage("Build Image"){
            steps{
                sh "docker build -f docker/Dockerfile -t ekart ."
                sh "docker tag ekart xedose/ekart:latest"
            }
        }
        
        // stage("Trivy"){
        //     steps{
        //         sh "trivy image xedose/ekart:latest"
        //     }
        // }
        
        // stage("Push to Docker Hub") {
        //     steps{
        //         script { 
        //             withDockerRegistry(credentialsId: '7f32548b-aeb7-4142-bb98-cbd20aa8e4f8', toolName: 'docker') {
        //                 sh "docker push xedose/ekart:latest "
        //             }
        //         }
        //     }
        // }
        
        // stage("Deploy using Docker") {
        //     steps {
        //         script {
        //             // Check if the container exists
        //             def containerExists = sh(script: "docker ps -aq -f name=ekart", returnStatus: true) == 0
                    
        //             if (containerExists) {
        //                 // Stop and remove the old container, ignoring errors if it doesn't exist
        //                 sh(script: "docker stop ekart || true", returnStatus: true)
        //                 sh(script: "docker rm ekart || true", returnStatus: true)
        //             }
                    
        //             // Run the new container
        //             sh "docker run -d --name ekart -p 8070:8070 xedose/ekart:latest"
        //         }
        //     }
        // }
        
        stage("Integrate EKS with Jenkins") {
            steps   {
                script  {
                    sh "aws eks update-kubeconfig --name demo-eks --region us-east-1"
                    sh "kubectl get ns"
                    sh "kubectl apply -f deploymentservice.yml"
                }
            }
        }


        
        // stage("Deploy using Tomcat"){
        //     steps{
        //         sh "cp /var/lib/jenkins/workspace/Security-pipeline/target/petclinic.war /opt/apache-tomcat/webapps"
        //     }
        // }
        
    }
}
