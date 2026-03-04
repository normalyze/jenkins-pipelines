# Jenkins Manual/UI Pipelines

This repository contains standalone pipelines that can be:
- **Edited directly in GitHub** via the web UI
- **Automatically picked up by Jenkins** Multibranch Pipeline
- **Triggered manually** through Jenkins UI
- **Triggered programmatically** via Jenkins API

## 🎯 **How It Works**

### **1. Jenkins Multibranch Pipeline**
Jenkins automatically scans this repository and creates pipeline jobs for each branch containing a `Jenkinsfile`.

### **2. GitHub Integration**
- Edit pipelines directly in GitHub web UI
- Jenkins automatically detects changes
- Builds trigger automatically on push/PR

### **3. Manual & Programmatic Triggering**
- **Manual**: Trigger through Jenkins UI
- **Programmatic**: Trigger via Jenkins REST API

## 📁 **Repository Structure**

```
jenkins-pipelines/
├── README.md
├── cleanup-old-branches/
│   └── Jenkinsfile
├── deploy-staging/
│   └── Jenkinsfile
├── security-scan/
│   └── Jenkinsfile
├── data-processing/
│   └── Jenkinsfile
└── build-worker/
    └── Jenkinsfile
```

## 🚀 **Getting Started**

### **For Pipeline Authors:**
1. **Create a new branch** for your pipeline
2. **Add a Jenkinsfile** with your pipeline definition
3. **Push to GitHub** - Jenkins will automatically create the job
4. **Test manually** in Jenkins UI

### **For Pipeline Users:**
1. **Open Jenkins UI** → Browse to `jenkins-pipelines`
2. **Select your pipeline branch**
3. **Click "Build with Parameters"** to run manually
4. **Set parameters** and execute

## 🔧 **Pipeline Examples**

### **Simple Cleanup Pipeline:**
```groovy
pipeline {
    agent any
    parameters {
        string(name: 'ENVIRONMENT', defaultValue: 'dev', description: 'Target environment')
        booleanParam(name: 'DRY_RUN', defaultValue: false, description: 'Dry run only')
    }
    stages {
        stage('Cleanup') {
            steps {
                script {
                    echo "Cleaning up ${params.ENVIRONMENT} environment"
                    if (!params.DRY_RUN) {
                        sh 'echo "Performing actual cleanup operations"'
                    }
                }
            }
        }
    }
}
```

### **Security Scan Pipeline:**
```groovy
pipeline {
    agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: security-scan
    image: normalyze/snyk:latest
    command:
    - cat
    tty: true
'''
        }
    }
    parameters {
        string(name: 'SCAN_TYPE', defaultValue: 'code', description: 'Type of scan: code, dependency, container')
        string(name: 'TARGET_BRANCH', defaultValue: 'main', description: 'Branch to scan')
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: "${params.TARGET_BRANCH}"
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    echo "Running ${params.SCAN_TYPE} security scan"
                    sh 'snyk code --json > security-report.json'
                    archiveArtifacts artifacts: 'security-report.json'
                }
            }
        }
    }
}
```

## 🎛 **Best Practices**

### **Pipeline Naming:**
- Use descriptive branch names: `feature/pipeline-name`
- Use kebab-case for pipeline names
- Include environment in name: `deploy-staging`, `cleanup-prod`

### **Parameter Design:**
- Always provide default values
- Use clear descriptions
- Include validation for critical parameters

### **Error Handling:**
- Use try-catch blocks for external operations
- Provide meaningful error messages
- Implement timeout handling

### **Resource Usage:**
- Use appropriate agent types (Docker, Kubernetes, etc.)
- Clean up resources in post sections
- Consider resource limits for shared environments

## 🔗 **Integration with Existing Infrastructure**

### **GitHub Integration:**
- ✅ Automatic webhook triggers
- ✅ Status reporting to GitHub
- ✅ Individual check creation
- ✅ Build status in PR UI

### **Shared Library Functions:**
- ✅ `buildDocker()` - Build and push Docker images
- ✅ `snykScan()` - Run security scans
- ✅ `deployToK8s()` - Deploy to Kubernetes
- ✅ `withAWSCredentials()` - AWS authentication

### **Multi-Organization Support:**
- ✅ Normalyze organization
- 🔄 PFPT-Internal organization (needs setup)
- ✅ Individual repository scanning

## 📞 **Jenkins API Integration**

### **Trigger Pipeline Programmatically:**
```bash
# Trigger pipeline with parameters
curl -X POST \
  -H "Authorization: Bearer ${JENKINS_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{"parameter": [{"name": "ENVIRONMENT", "value": "staging"}]}' \
  "${JENKINS_URL}/job/jenkins-pipelines/job/feature/my-pipeline/build"
```

### **Get Build Status:**
```bash
curl -X GET \
  -H "Authorization: Bearer ${JENKINS_TOKEN}" \
  "${JENKINS_URL}/job/jenkins-pipelines/job/feature/my-pipeline/lastBuild/api/json"
```

## 🚀 **Next Steps**

1. **Create your first pipeline** following the examples
2. **Test in Jenkins UI** to verify functionality
3. **Add to Jenkins Multibranch** configuration
4. **Share with team** for collaborative pipeline development

## 📞 **Support**

- **Jenkins Documentation**: [Jenkins Pipeline Documentation](https://www.jenkins.io/doc/book/pipeline/)
- **Multibranch Pipeline**: [Jenkins Multibranch Documentation](https://www.jenkins.io/doc/book/pipeline/multibranch/)
- **GitHub Integration**: [GitHub Branch Source Plugin](https://plugins.jenkins.io/github-branch-source/)

---

*This repository is part of the Jenkins MVP implementation for DSPM-6740*
