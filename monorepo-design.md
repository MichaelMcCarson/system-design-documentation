# Monorepo Design

## Environment Variables

For proper grouping of environment variables and standardizing environment variables, each like environment variable should start with a prefix that matches the grouping of the related variables.

Example:

```env
# Some Service
SOME_SERVICE_DISABLED=1
SOME_SERVICE_QUEUE=https://sqs.us-east-1.amazonaws.com/1234567890/some-service-development
SOME_SERVICE_URL=https://localhost:3001/some
```
