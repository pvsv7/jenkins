properties([
    parameters([
        string(name: 'STUDENT', defaultValue: 'John', description: 'Who should I say hello to?'),
        text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the STUDENT'),
        booleanParam(name: 'TOGGLE', defaultValue: true, description: 'Toggle this value'),
        choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something'),
        choice(name: 'DAY', choices: ['School_day', 'Holiday'], description: 'Do you want to Deploy or Rollback chrome driver'),
        password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password'),
        choice(name: 'ACTION', choices: ['Deploy', 'Rollback'], description: 'Do you want to Deploy or Rollback chrome driver'),
        [$class: 'DynamicReferenceParameter',
            choiceType: 'ET_FORMATTED_HTML',
            omitValueField: true,
            name: 'node_chrome_tag',
            description: 'Specify any of the latest 10 chrome node image tags (https://hub.docker.com/r/selenium/node-chrome/tags)',
            referencedParameters: 'ACTION',
            script: [
                $class: 'GroovyScript',
                fallbackScript: [
                    classpath: [],
                    sandbox: true,
                    script: 'return[\'nothing.....\']'
                ],
                script: [
                    classpath: [],
                    sandbox: true,
                    script: """
                        if (ACTION.equals('Rollback')) {
                            inputBox='<input type="hidden" name="value" value="no">'
                        } else {
                            inputBox = '<input type="text" name="value" value="" placeholder="node_chrome_tag">'
                        }
                    """
                ]
            ]
        ]
    ])
])

pipeline {
    agent any
    environment {
        GREETING = 'Hello Jenkins'
    }

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
        stage('check params & env') {
            steps {
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
                dir('jenkins/hello.sh') {
                    script {
                        sh "bash day.sh ${params.STUDENT} ${params.DAY}"
                    }
                }
            }
        }
    }

    post { 
        always { 
            echo 'I will always say Hello again!'
        }
        failure { 
            echo 'This runs when the pipeline is failed, used generally to send some alerts'
        }
        success {
            echo 'I will say Hello when the pipeline is successful'
        }
    }
}
