---
title: Docker Environment Variables & Secrets Explained | Secure App Configuration | Cloud & Devops
tags:
  - DevOps
  - Cloud
  - Docker
Youtube_Link: https://www.youtube.com/watch?v=C47ycinu8ow
---

  
# Docker Environment Variables & Secrets Explained | Secure App Configuration | Cloud & Devops


## Why configuration management really matters.

- database connection stings
- API Keys and tokens
- Service URLs and endpoints
- Feature flags and settings
- Password and Credentials

## Good Practice:
- ✅Externalize configuration
- ✅Different configs per environment
- ✅Secure handling of sensitive data.

## Bad Practices:
- ❌ Hardcoding values in code
- ❌ Committing secrets to Git
- ❌ Same Config for all environments

**Note:-** Docker provides mechanism for this through **environment variables** and **docker secrets**.

### Three ways to set environment variables:

1. Command line with -e:
	```bash
	docker run -e
	DATABASE_URL=postgress://db:5432 my-app
	docker run -e API_KEY=abc123 -e
	NODE_ENV=production my-app
	```

2. Environment files with --env-file:
	```bash
	docker run --env-file .env my-app
	```

3.  In Docker file with ENV:
	```
	ENV NODE_ENV=production
	ENV PORT=3000
	```

---

## Security Risks with Environment Variables
_Understanding the Dangers_

> [!note] 📄Environment variables are NOT secure for sensitive data:


### Why ?
- **Visible in docker inspect output**
	Anyone access to docker demon can run docker inspect on your container and see all the environment variables including **passwords** and **API Keys**
- **Visible in container logs**
	Environment variables can appear in logs. If your application logs its configuration or an error it includes environment context and the secrets might leak from there too.
- **Passed to child processes**
	Is the environment variables are inherited  by the child processes. If you application spawns other processes, they all see the secrets.
- **Can leak in error messages**
	If an application crashes and generates an error report,  environment variables might be included there also.
- **Stored in image layers if.**
	If you set environment variables in Docker file using `.env`, they are begged into the image layer and anyone who has the image can extract them. this doesn't mean environment variables are useless. They are perfectly fine for **non-sensitive configuration**. But for your DB `Username` and `Password` you should not use it.

Note:- But for truly  sensitive Data like **database password**, **API Key**, **Private secrets**, **Encryption key**, **Environment** these are aren't alone secure by using ENV file.

So, for production system handling real sensitive data, you need **docker secrets** or **external secret managements**.

### Practical Strategies for Managing  Secrets in real projects

For **non-sensitive configuration**, **regular environments** are fine.
Now you can even use mock or fake credentials for local development. A local test database doesn't need a strong password.
For production, the approach depends on your infrastructure.

If you are on **AWS**, you can use AWS secret manager or parameter store.

If you are on **Azure**, you can use Azure key vault where you can save your sensitive credentials.
That is better than having that in environment file specificlly for your production environment. Not for Development






