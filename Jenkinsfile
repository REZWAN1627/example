
def jobname = JOB_NAME.tokenize('/')as String[]
def jobwsnane = jobnane[1]
def branchname = jobnane[2]
def server = Artifactory.server 'jfrog’
def rtMaven = Artifactory.newMavenBuild()
def buildinfo

pipeline {
agent {
node {
label “mulesoft”
}
}
environment { // AZURE CREDENTIAL = "S{JENKINS URL.contains(’jenkinsprod') ? *jenkinsprod
mulesoftProject = "${JOB_NAME.split('/')[1]}"
applicationName = (mulesoftProject.replace( mule-","")).replace( "dummy”, "")
notify = "https://outlook.office. con/uebhook/cOeIFFFL-277c-43bd-0ads- 1cdbSFA9OFbEH795
}
//-----------------------------------------------------------------------
stages {
stage('parse-variables’) {
steps {
script {
if (JOB_NAME.contains ('development')
{
env.variables = 'parameters/variables-dev. json'
} else if (J0B_NAME.contains('test') {
env.variables = 'parameters/variables-test.json'
} else if (JOB_NAME.contains('production’)) {
env.variables = 'parameters/variables-prod.json'
}else{
env.variables ='paraneters/variables-dev.json'
}

def paraminfo = readJSON file : env.variables
env.version = paraminfo.muleVersion
env.region = paraminfo.primaryRegion
env. regionSec = paraninfo.secondaryegion
env.workers = paraminfo.workers
env.workerType = paraminfo.workerType
env.environs = paraminfo. env
env.envSuffix = paraminfo.envSuffix
env.appruntimekey = paraminfo.appruntimekey
env.cis = paraminfo. clientSecret
env.cid = paraminfo.clientId
env.layer = paraminfo.layer
env.appVisualizer = paraminfo.appVisualizer
env.businessGroup = paraminfo.businessGroup

echo "${variables)"
echo "${regionSec}"
echo "${workerType)"

if (env.workerType=='0.1')
workerType = 'MICRO'
else if(env.workerType=='0.2')
workerType = 'SMALL'
else if(env.workerTypes='1.0")
workerType = 'MEDIUM'
else if(env.workerType=='2.0')
workerType = 'LARGE'
else if(env.workerType=='4.0')
workerType = 'XLARGE'
else
workerType = 'MICRO'

echo "${workerType}"
}
}
}
//-----------------------------------------------------------------------
stage(Initialize-Artifactory') {
steps {
rtServer (id: "jfrog", url: "https://mrcgdevops.jfrog.io/mrcgdevops", credentialsId:17a4-4bde-8908-b95¢95802") // credentials to login Jfrog
rtMavenDeployer (id:'central',serverId:'jFrog', releaseRepo:"xjja-mrcg-libs-releases-distribution”, snapshotRepo: "xjja-mrcg-libs-releases-distribution"
rtMavenResolver (id:'central',serverld:'jfrog', releaseRepo:"xjja-lib-releases")

}
}
//-----------------------------------------------------------------------
stage('Build')
{
steps {
withAzureKeyvault (azureKeyVaultSecrets:[
(
envVariable: 'runtimekey',
name: appruntimekey,
secretType: 'Secret']
, credentialDOverride: 'mulesoft-akv', keyVaultURLOverride:'https://mule-prod-eastus-vault.vault.azure.net') {configFileProvider ([configFile (fileld: 'maven-mulesoft-v1', variable:'MAVEN_SETTINGS_XML')]) {
sh""" mvn clean package -DskipTests=true -Dmule.env=${envSuffix} -Dapplication.runtime.key=S{runtimekey} -s $MAVEN_SETTINGS_XML """
}
}
}
}

//-----------------------------------------------------------------------
stage('Test') {
when {
anyOf {
changeRequest target : 'development'
changeRequest target : 'test'
changeRequest target : 'production'
branch 'development'
branch 'test'
branch 'production'
}
}

steps{
withAzureKeyvault(azureKeyVaultSecrets : [
[
envVariable: 'runtimekey’,
name: appruntimekey,
secretType: 'Secret']
, credentiallDOverride :'mulesoft-aky', keyVaultURLOVerride : 'https://mule-prod-eastus.vault.vault.azure net') {
configFileProvider([configFile(fileld : 'maven-mulesoft-v1', variable:'MAVEN_SETTINGS_ XML) {

sh""" mvn clean test -Dmule.env=S{envSuffix} -Dapplication. runtime key=S{runtimekey} -Dmule.reg="ue" -Dhttp.proc.api.client_id=anything -Dhttp.proc.api.client_secret=anything
-Dhttp.sys.api.client_id=anything -Dhttp.sys.api.client_secret=anything -Dhttp.sys.sims.api.client_id=anything -Dhttp.sys.sims.api.client_secret=anything
-Dhttp.sys.sap.api.client_id=anything -Dhttp.sys.sap.api.client_secret=anything -Dhttp.sys.port=10091 -s $MAVEN_SETTINGS_ XML """

sh""" mvn clean test -Dmule.env=S{envSuffix} -Dapplication. runtime key=S{runtimekey} -Dmule.reg="ue" -Dhttp.proc.api.client_id=anything -Dhttp.proc.api.client_secret=anything
-Dhttp.sys.api.client_id=anything -Dhttp.sys.api.client_secret=anything -Dhttp.sys.sims.api.client_id=anything -Dhttp.sys.sims.api.client_secret=anything
-Dhttp.sys.sap.api.client_id=anything -Dhttp.sys.sap.api.client_secret=anything -s $MAVEN_SETTINGS_ XML """
}
}
}

post {
always {
publishHTML(target : [
allowMissing: false,
alwaysLinkToLastBuild: false,
keepAll: true,
reportDir: 'target/site/munit/coverage',
reportFiles:'summary.html',
reportName:'Code Coverage'])
}
}
}
//-----------------------------------------------------------------------
stage('Deployment-Test/Dev Primary') {
when {
anyOf {
 branch 'development'
 branch 'test'
}
}

steps {
script {
if(region=='us-east-1') {
echo 'Yes Primary deployment'
regionP='ue'
if (businessGroup.contains('Int')) {
applicationName = "mrcg-in-${regionP}${applicationName.split('mrcg-integ')[1]}"
} else if (businessGroup.contains('Shared')) {
applicationName = "mrcg-sh-${regionP}${applicationName.split('mrcg-shared')[1]}"
} else if (businessGroup.contains(‘Class')) {
applicationName = "mrcg-cl-${regionP}${applicationName.split('mrcg-class')[1]}"
}else if (businessGroup.contains('APM') {
applicationName = "mrcg-ap-${regionP}{applicationName.split('mrcg-apm')[1]}"
}else { applicationName = applicationName}

echo "S{applicationName}"

withCredentials([usernamePassword(credentialsId :'xhen-anypoint-svc-acct-admin', passwordVariable : 'xjpass', usernameVariable : "xjuser')])
{withAzureKeyvault(azureKeyVaultSecrets : [

[
envVariable: 'runtimekey',
name: appruntimekey,
secretType: 'Secret'],

[
envVariable: 'cis',
name: cis,
secretType: 'Secret'],

[
envVariable: 'cid',
name: cid,
secretType: 'Secret'],

[
envVariable: 'notification',
name: 'jenkins-Notifications-URL',
secretType: 'Secret']

], credentialDOverride : 'mulesoft-akv,keyVaultURLOverride: https://mule-prod-eastus-vault.vault.azure.net') {
configFileProvider([configFile(fileld : 'maven-mulesoft-vl', variable :'MAVEN_SETTINGS_XML')]) {

echo "${applicationName}"
rtMavenRun(tool : 'Maven', pom :'pom.xml',goals:"deploy -DskipTest=true -Dxj.mule.reg='ue' -Dmule.version=${version} -Dapplication.runtime.key=${runtimekey}
-Dxj.ch.region=S{region} -Dxj.ch.workers= ${workers} -Dxj.ch.workertype=${workerType} -Dxj.anypoint.user=${xjuser} -Dxj.anypoint.password=${xjpass}
-Dxj.application.bg=${businessGroup -Dxj.ap.cid=${cid}  -Dxj.ap.ccs=${cis} -Dxj.mule.env=${envSuffix} -Dxj.application.name=${applicationName-${envSuffix}
-Dxj.ap.layername=${appVisualizer} -Dxj.applicationoverrideproperties=false -Dxj.anypoint.environment=${environs} -P cloudhub -s ${MAVEN_SETTINGS_XML")

}
}
}
}else{
echo 'No Primary deployment'
}
}
}
}
//-----------------------------------------------------------------------
stage('Deployment-Test/Dev Secondary') {
when {
anyOf {
 branch 'development'
branch 'test'
}
}

steps {
script {
if(region=='us-west-1') {
echo 'Yes Secondary deployment'
regionP='uw'
echo "123"
echo "${businessGroup}"
echo "${applicationName}"
if (businessGroup.contains('Int')) {
applicationNameS = "mrcg-in-${regionP}${(mulesoftProject.replace("mule-", "")).split('mrcg-integ')[1]}"
} else if (businessGroup.contains('Shared')) {
applicationNameS = "mrcg-sh-${regionP}${(mulesoftProject.replace("mule-", "")).split('mrcg-shared')[1]}"
} else if (businessGroup.contains(‘Class')) {
applicationNameS = "mreg-cl-${regionP}${(mulesoftProject.replace("mule-", "")).split('mrcg-class')[1]}"
}else if (businessGroup.contains('APM') {
applicationNameS = "mrcg-ap-${regionP}{(mulesoftProject.replace("mule-", "")).split('mrcg-apm')[1]}"
}else { applicationName = (mulesoftProject.replace("mule-", ""))}

echo "S{applicationNameS}"
echo "xyz"
echo "${applicationName}"

withCredentials([usernamePassword(credentialsId :
'xhen-anypoint-svc-acct-admin', passwordVariable : 'xjpass', usernameVariable : "xjuser')]) {
withAzureKeyvault(azureKeyVaultSecrets : [

[
envVariable: 'runtimekey',
name: appruntimekey,
secretType: 'Secret'],

[
envVariable: 'cis',
name: cis,
secretType: 'Secret'],

[
envVariable: 'cid',
name: cid,
secretType: 'Secret'],

[
envVariable: 'notification',
name: 'jenkins-Notifications-URL',
secretType: 'Secret']

], credentialDOverride : 'mulesoft-akv, keyVaultURLOverride: https://mule-prod-eastus-vault.vault.azure.net') {
configFileProvider([configFile(fileld : 'maven-mulesoft-vl', variable :'MAVEN_SETTINGS_XML')]) {

echo "${applicationNameS}"
rtMavenRun(tool : 'Maven', pom :'pom.xml',goals:"deploy -DskipTest=true -Dxj.mule.reg='uw' -Dmule.version=${version} -Dapplication.runtime.key=${runtimekey}
-Dxj.ch.region=S{regionSec} -Dxj.ch.workers= ${workers} -Dxj.ch.workertype=${workerType} -Dxj.anypoint.user=${xjuser} -Dxj.anypoint.password=${xjpass}
-Dxj.application.bg=${businessGroup -Dxj.ap.cid=${cid}  -Dxj.ap.ccs=${cis} -Dxj.mule.env=${envSuffix} -Dxj.application.name=${applicationName-${envSuffix}
-Dxj.ap.layername=${appVisualizer} -Dxj.applicationoverrideproperties=false -Dxj.anypoint.environment=${environs} -P cloudhub -s ${MAVEN_SETTINGS_XML")

}
}
}
}else{
echo 'No Secondary deployment'
}
}
}
}

//-----------------------------------------------------------------------
stage('Deployment-Prod/ Primary') {
when {
anyOf {
 branch 'production'
}
}

steps {
script {
if(region=='us-east-1') {
echo 'Yes Primary deployment'
regionP='ue'
if (businessGroup.contains('Int')) {
applicationName = "mrcg-in-${regionP}${applicationName.split('mrcg-integ')[1]}"
} else if (businessGroup.contains('Shared')) {
applicationName = "mrcg-sh-${regionP}${applicationName.split('mrcg-shared')[1]}"
} else if (businessGroup.contains(‘Class')) {
applicationName = "mreg-cl-${regionP}${applicationName.split('mrcg-class')[1]}"
}else if (businessGroup.contains('APM') {
applicationName = "mrcg-ap-${regionP}{applicationName.split('mrcg-apm')[1]}"
}else { applicationName = applicationName}

echo "S{applicationName}"

withCredentials([usernamePassword(credentialsId :'xheon-anypoint-svc-acct-admin', passwordVariable : 'xjpass', usernameVariable : "xjuser')]) {
withAzureKeyvault(azureKeyVaultSecrets : [

[
envVariable: 'runtimekey',
name: appruntimekey,
secretType: 'Secret'],

[
envVariable: 'cis',
name: cis,
secretType: 'Secret'],

[
envVariable: 'cid',
name: cid,
secretType: 'Secret'],

[
envVariable: 'notification',
name: 'jenkins-Notifications-URL',
secretType: 'Secret']

], credentialDOverride : 'mulesoft-akv,keyVaultURLOverride: https://mule-prod-eastus-vault.vault.azure.net') {
configFileProvider([configFile(fileld : 'maven-mulesoft-vl', variable :'MAVEN_SETTINGS_XML')]) {

echo "${applicationName}"
rtMavenRun(tool : 'Maven', pom :'pom.xml',goals:"deploy -DskipTest=true -Dxj.mule.reg='ue' -Dmule.version=${version} -Dapplication.runtime.key=${runtimekey}
-Dxj.ch.region=S{region} -Dxj.ch.workers= ${workers} -Dxj.ch.workertype=${workerType} -Dxj.anypoint.user=${xjuser} -Dxj.anypoint.password=${xjpass}
-Dxj.application.bg=${businessGroup -Dxj.ap.cid=${cid}  -Dxj.ap.ccs=${cis} -Dxj.mule.env=${envSuffix} -Dxj.application.name=${applicationName-${envSuffix}
-Dxj.ap.layername=${appVisualizer} -Dxj.applicationoverrideproperties=false -Dxj.anypoint.environment=${environs} -P cloudhub -s ${MAVEN_SETTINGS_XML")

}
}
}
}else{
echo 'No Primary deployment'
}
}
}
}
//-----------------------------------------------------------------------
stage('Deployment-Prod Secondary') {
when {
anyOf {
 branch 'production'

}
}

steps {
script {
if(region=='us-west-1') {
echo 'Yes Secondary deployment'
regionP='uw'
echo "123"
echo "${businessGroup}"
echo "${applicationName}"
if (businessGroup.contains('Int')) {
applicationNameS = "mrcg-in-${regionP}${(mulesoftProject.replace("mule-", "")).split('mrcg-integ')[1]}"
} else if (businessGroup.contains('Shared')) {
applicationNameS = "mrcg-sh-${regionP}${(mulesoftProject.replace("mule-", "")).split('mrcg-shared')[1]}"
} else if (businessGroup.contains(‘Class')) {
applicationNameS = "mreg-cl-${regionP}${(mulesoftProject.replace("mule-", "")).split('mrcg-class')[1]}"
}else if (businessGroup.contains('APM') {
applicationNameS = "mrcg-ap-${regionP}{(mulesoftProject.replace("mule-", "")).split('mrcg-apm')[1]}"
}else { applicationName = (mulesoftProject.replace("mule-", ""))}

echo "S{applicationNameS}"
echo "xyz"
echo "${applicationName}"

withCredentials([usernamePassword(credentialsId :
'xhen-anypoint-svc-acct-admin', passwordVariable : 'xjpass', usernameVariable : "xjuser')]) {
withAzureKeyvault(azureKeyVaultSecrets : [

[
envVariable: 'runtimekey',
name: appruntimekey,
secretType: 'Secret'],

[
envVariable: 'cis',
name: cis,
secretType: 'Secret'],

[
envVariable: 'cid',
name: cid,
secretType: 'Secret'],

[
envVariable: 'notification',
name: 'jenkins-Notifications-URL',
secretType: 'Secret']

], credentialDOverride : 'mulesoft-akv, keyVaultURLOverride: https://mule-prod-eastus-vault.vault.azure.net') {
configFileProvider([configFile(fileld : 'maven-mulesoft-vl', variable :'MAVEN_SETTINGS_XML')]) {

echo "${applicationNameS}"
rtMavenRun(tool : 'Maven', pom :'pom.xml',goals:"deploy -DskipTest=true -Dxj.mule.reg='uw' -Dmule.version=${version} -Dapplication.runtime.key=${runtimekey}
-Dxj.ch.region=S{regionSec} -Dxj.ch.workers= ${workers} -Dxj.ch.workertype=${workerType} -Dxj.anypoint.user=${xjuser} -Dxj.anypoint.password=${xjpass}
-Dxj.application.bg=${businessGroup -Dxj.ap.cid=${cid}  -Dxj.ap.ccs=${cis} -Dxj.mule.env=${envSuffix} -Dxj.application.name=${applicationName-${envSuffix}
-Dxj.ap.layername=${appVisualizer} -Dxj.applicationoverrideproperties=false -Dxj.anypoint.environment=${environs} -P cloudhub -s ${MAVEN_SETTINGS_XML")

}
}
}
}else{
echo 'No Secondary deployment'
}
}
}
}
//-----------------------------------------------------------------------
stage("Publish") {
when {
anyOf{
branch 'production'
branch 'development'
}
}

steps{
rtPublishBuildInfo(serverID:"jfrog")
}
}
}

post {
always{
script{
cleanWs(cleanWhenAborted:true,cleanWhenFailure: true, cleanWhenNotBuild: true,
cleanWhenSuccess:true , cleanWhenUnstable:true , deleteDirs: true)
}
}

failure{
script {
office365ConnectorSend Message: "${JOB_NAME}-Success",
color: "008000"
status: "Success",
webhookUrl: "${notify}"
}
}

fixed {
script {
office365ConnectorSend Message: "Fixed ${JOB_NAME}-Success",
color: "00cd00"
status: "Fixed",
webhookUrl: "${notify}"
}
}
}
}
