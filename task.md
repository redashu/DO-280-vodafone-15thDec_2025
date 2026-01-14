# Splunk Deployment Task

1. Use splunk/splunk:latest image from Docker Hub to create deployment
2. Store required ENV variables in ConfigMap
3. Store required password in Secret
4. Set replica count to 1
5. Create ClusterIP service named `<yourname>svc`
6. Create routes to expose externally
7. Access from web browser
8. **Note:** Default username is `admin` and default port is `8000`