# Failure and Rollback Guide

## 1. Common Failures

### Application Container Fails

* Check container status using `sudo docker ps -a`.
* Review logs using `sudo docker logs todo-backend` or `sudo docker logs todo-frontend`.
* Verify that required environment variables are configured.

### Database Connection Failure

* Check MySQL health and logs.
* Verify the database hostname, credentials, and connection settings.
* Confirm that the backend and database can communicate over their Docker network.

### Frontend Cannot Reach Backend

* Verify the backend API URL.
* Check the backend health and browser console.
* Verify CORS settings and EC2 security-group rules.

### Summarization or Slack Failure

* Review backend logs for API authentication or webhook errors.
* Verify that the Cohere API key and Slack webhook URL are configured correctly.
* Never commit secrets to Git.

## 2. Rollback Procedure

1. Identify the last known working Docker image tag.
2. Stop and remove the failed application container.
3. Start a replacement container using the previous working image and the correct environment variables.
4. Check container logs and verify the application endpoints.
5. Confirm that the frontend can access the backend.

## 3. Database Safety

* Avoid deleting database volumes during application rollback.
* Back up the database before making schema or data changes.
* Verify database connectivity after recovery.

## 4. Verification

* Confirm that the frontend loads.
* Confirm that the backend API responds successfully.
* Test creating and updating a Todo.
* Review logs for recurring errors.
