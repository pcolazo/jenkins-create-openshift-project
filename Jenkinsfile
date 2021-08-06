def cause = currentBuild.getBuildCauses('hudson.model.Cause$UserIdCause')
def userId = cause.userId[0]
def now = new Date()
def hours = now.format("HH", TimeZone.getTimeZone('GMT-3'))
pipeline{
    // START PIPELINE
    agent any
    parameters {
        choice(choices: ['qa', 'pre', 'prod'], description: 'Elija el Ambiente donde quiere crear el PROJECTO', name: 'AMBIENTE')
        string(name: 'PROYECTO' , defaultValue: '', description: 'Nombre del nuevo Proyecto.')
       }
    environment {
        BUILD_USER_ID = "$userId"
        SSHKEY="-i /export/home/jenkins/.ssh/id_rsa"
        PREF=params.AMBIENTE.toUpperCase()
        RED='\033[0;31m'
        GREEN='\033[1;32m'
        NC='\033[0m' // No Color
        BOLD='\033[1m'
    }
    stages {
    // START STAGES
        stage("Deploy en QA - PRE"){
            when {
                expression { params.AMBIENTE == "qa" || params.AMBIENTE == "pre" }
            }
            stages {
                stage("Se crea el proyecto"){
                    steps("create proyecto"){
                        script {
                            drs=""
                            site=""
                            serv = "ocp-host-conect-to-oc"
                            hosts = "02"
                            user = "USER"
                            command = """oc new-project ${params.PROYECTO}"""
                            verificarHost(hosts, serv, user, SSHKEY, drs, command)
                        }
                    }
                }
            }
        }
        stage("Deploy en PROD"){
            when {
                expression { params.AMBIENTE == "prod" }
            }
            stages {
                stage("Se crea el proyecto en PROD"){
                    steps("create proyecto en PROD"){
                        script {
                            drs=""
                            site=""
                            serv = "ocp-host-conect-to-oc"
                            hosts = "02"
                            user = "USER"
                            command = """oc new-project ${params.PROYECTO}"""
                            verificarHost(hosts, serv, user, SSHKEY, drs, command)
                        }
                    }
                }
            }
        }
    // END STAGES
    }
// END PIPELINE
}

// DECLARO FUNCIONES
def verificarHost(host, serv, user, key, drs, ops){
    RED='\033[0;31m'
    GREEN='\033[1;32m'
    NC='\033[0m' // No Color
    BOLD='\033[1m'   
    
    res= sh(script:"""ssh $key $user@$drs$serv"${host}".domain.com '$ops' 2>%1""", returnStatus:true)
        if(res != 0) {
                echo """${RED}[ERROR]${NC}"""
                echo """${res}"""
                failure("el usuario aborto")
            
        } else {
                echo """${GREEN}[SUCCESS]${NC}"""
                echo """${res}"""
        }
        if(site == "TODOS") {
            res= sh(script:"""ssh $key $user@$serv"${host}".domain.com '$ops' 2>%1""", returnStatus:true)
            if(res != 0) {
                    echo """${RED}[ERROR]${NC}"""
                    echo """${res}"""
                    failure("el usuario aborto")
            } else {
                ansiColor('xterm'){
                    echo """${GREEN}[SUCCESS]${NC}"""
                    echo """${res}"""
                }
            }
    }
}
