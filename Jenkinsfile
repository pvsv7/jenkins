pipeline {
    agent any      // {label 'jdk17'}              
    environment {   //environment variable that can be accessed across all stages of the pipeline
        GREETING = 'Hello Jenkins'
    }
    // options {
    //     // timeout(time: 1, unit: 'HOURS')
    //     // disableConcurrentBuilds()
    //     // ansiColor('xterm')
    // }
properties([  
    parameters {
        string(name: 'STUDENT', defaultValue: 'John', description: 'Who should I say hello to?')
        text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the STUDENT')
        booleanParam(name: 'TOGGLE', defaultValue: true, description: 'Toggle this value')
        choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')
        choice(name: 'DAY', choices: ['School_day', 'Holiday'], description: 'Do you want to Deploy or Rollback chrome driver')
        password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
        [$class: 'DynamicReferenceParameter',
            choiceType: 'ET_FORMATTED_HTML',
            omitValueField: true,
            name: 'uniform_colour',
            description: 'Specify uniform colour',
            referencedParameters: 'DAY',
            script: [
                $class: 'GroovyScript',
                fallbackScript: [
                    classpath: [],
                    sandbox: true,
                    script:
                        'return[\'nothing.....\']'
                ],
                script: [
                    classpath: [],
                    sandbox: true,
                    script:
                        """
                            if(DAY.equals('Holiday')) {
                                inputBox='<input type="hidden" name="value" value="no">'
                            } else {
                                inputBox = '<input type="text" name="value" value="" placeholder="uniform_colour">'
                            }
                        """
                ]
            ]
        ]
        ,
    }
])    
    // build
    stages {
        stage('Travel') {
            when {
                expression { params.DAY == 'School_day' }
            }
            steps {
                echo 'Go to school'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Terraform') {
            steps {
                script {
                    echo 'Here I wrote shell script'

                    if (params.ACTION == "apply") {
                        sh 'echo "terraform apply"'
                    } else {
                        sh 'echo "terraform destroy"'
                    }
                }
            }
        }
        stage('check params & env'){
            steps{
                sh """
                    echo "Student name: ${params.STUDENT}"
                    echo "$GREETING"
                    echo "Build number is $BUILD_NUMBER"
                    def declarative_variable = "work_within_stage" 
                    echo "$declarative_variable"
                """
            }
        }
        stage('Build chrome Docker Image') {
            steps {
           // withAWS(roleAccount:'39878787792', role:'jenkins-operation-role'){
                dir('jenkins/hello.sh') {
                    script {
                        sh "bash day.sh ${params.STUDENT} ${params.DAY}"
                    }
                }
            }
        }
    }
    // post build
    post { 
        always { 
            echo 'I will always say Hello again!'
        }
        failure { 
            echo 'this runs when pipeline is failed, used generally to send some alerts'
        }
        success{
            echo 'I will say Hello when the pipeline is successful'
        }
    }
}
