pipeline {
    agent any
    
    stages {
        stage('Execute Commands') {
            steps {
                script {
                    def maxRetries = 3
                    def scriptPath = 'commands.sh'
                    def failedCommands = []
                    
                    // Execute the script to run all commands at least once
                    sh "chmod +x $scriptPath"
                    def exitCode = sh(script: "./$scriptPath", returnStatus: true)
                    
                    if (exitCode != 0) {
                        echo "Script execution failed"
                        currentBuild.result = 'FAILURE' // Mark the build as failed
                        return
                    }
                    
                    // Retry only the failed commands
                    for (def line in readFile(file: scriptPath).readLines()) {
                        if (line.startsWith("echo \"Running command")) {
                            def command = line.substring(line.indexOf('"') + 1, line.lastIndexOf('"'))
                            
                            exitCode = 1
                            
                            for (int retries = 0; retries < maxRetries && exitCode != 0; retries++) {
                                echo "Retrying: $command (Attempt ${retries + 1})"
                                exitCode = sh(script: "./$scriptPath \"$command\"", returnStatus: true)
                                
                                if (exitCode != 0) {
                                    echo "Command failed. Retrying..."
                                }
                            }
                            
                            if (exitCode != 0) {
                                echo "Command failed after retries: $command"
                                failedCommands.add(command)
                            }
                        }
                    }
                    
                    if (!failedCommands.isEmpty()) {
                        echo "Some commands failed after retries."
                        currentBuild.result = 'FAILURE' // Mark the build as failed
                    }
                }
            }
        }
    }
}
