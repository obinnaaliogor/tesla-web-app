
pipeline{

environment {
    registry = "obinnaaliogor/tesla-app"  
    dockerImage = ''
    registryCredential = 'dockerhubcredentials'
  }

agent any
tools{
maven "maven3.9.0"
}

stages{

stage('1CodeClone'){
steps{
sh "echo 'Clonning code from GitHub'"
git "https://github.com/obinnaaliogor/tesla-web-app.git"
}
}

/*
stage('2Test+Build'){
steps{
sh "echo 'Creating an Artifacts'"
sh "mvn clean package"
}
}
stage('3CodeQuality'){
steps{
sh "echo 'Performing code Quality Review and Analysis'"
sh "mvn sonar:sonar"
}
}
stage('4Upload2Nexus'){
steps{
sh "echo 'Uploading Artifacts to Nexus'"
sh "mvn deploy"
}
}
*/
stage('Build dockerImage'){
steps{
script {
dockerImage = docker.build registry + ":$BUILD_NUMBER"
}  
}
}

stage('UploadImage2 DockerHub') {
steps{    
script {
docker.withRegistry( '', registryCredential ) {
dockerImage.push()
}
}
}
}

stage('RemoveDockerImages'){
steps{
sh 'docker rmi -f $(docker images -q)'

}
}
stage('Trigger ManifestUpdate') {
steps{
                echo "triggering updatemanifestjob"
                build job: 'tesla-app-k8smanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
        }
}

}
}
