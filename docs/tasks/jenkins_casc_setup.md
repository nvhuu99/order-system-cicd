## Jenkins Helm Chart + CASC

### Refs:

Tutor: https://blog.devops.dev/jenkins-configuration-as-code-alternative-approach-981bc302d862
Demos: https://github.com/jenkinsci/configuration-as-code-plugin/tree/master/demos

### Problem:

How to make the sidecar "config-reload" pickup the objects like jobs, credentials:

1. The objects first stored in a Configmap, where the key is the **yaml filename** for the sidecar to load
2. The Configmap must have the correct label: `{{ template “jenkins.fullname” $root }}-jenkins-config: "true"`
