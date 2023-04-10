# OAuth2 filesmanagement
Autenticación mediante OAuth2


Mas pruebas
Mas pruebas 2
Mas pruebas 3


https://maven.apache.org/maven-release/maven-release-plugin/examples/branch.html

# Generación del container de jenkins
docker run --name jenkins-container -p 8100:8080 -p 50000:50000 -d --network jenkins -v /var/run/docker.sock:/var/run/docker.sock -v /opt/volumes/jenkins-data:/var/jenkins_home -v /opt/volumes/jenkins-certs:/certs/client:ro jenkins/jenkins:2.375.1-lts-jdk17




release:clean release:prepare release:perform -B -Dproject.scm.id=github-server-oauth2 -DtagNameFormat=@{project.version}

release:clean release:branch release:perform -DconnectionUrl=scm:git:https://github.com/agat-prog/mca-filesmanagement-oauth2.git -DbranchName=release -DupdateBranchVersions=true -DupdateWorkingCopyVersions=false -B -Dproject.scm.id=github-server-oauth2 -DtagNameFormat=@{project.version}


release:prepare release:perform release:branch -B -Dproject.scm.id=github-server-oauth2 -DtagNameFormat=@{project.version} -DupdateBranchVersions=true


release:prepare release:perform release:branch -DbranchName=release  -B -Dproject.scm.id=github-server-oauth2 -DtagNameFormat=@{project.version}



release:prepare release:branch -B -Dproject.scm.id=github-server-oauth2 -DtagNameFormat=@{project.version} -DupdateBranchVersions=false -DbranchName=release  -DupdateWorkingCopyVersions=true

release:clean release:prepare  -B -Dproject.scm.id=github-server-oauth2 -DtagNameFormat=@{project.version}