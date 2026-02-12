# React + Vite

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react) uses [Babel](https://babeljs.io/) (or [oxc](https://oxc.rs) when used in [rolldown-vite](https://vite.dev/guide/rolldown)) for Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react-swc) uses [SWC](https://swc.rs/) for Fast Refresh

## React Compiler

The React Compiler is not enabled on this template because of its impact on dev & build performances. To add it, see [this documentation](https://react.dev/learn/react-compiler/installation).

## Expanding the ESLint configuration

If you are developing a production application, we recommend using TypeScript with type-aware lint rules enabled. Check out the [TS template](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-react-ts) for information on how to integrate TypeScript and [`typescript-eslint`](https://typescript-eslint.io) in your project.


# My React App - DevOps Deployment

## Live Application
ec2-13-48-131-104.eu-north-1.compute.amazonaws.com

## Server Setup

I'm using AWS EC2 with Ubuntu 24.04 in the eu-north-1 region. Port 80 is open for the app and port 22 is open for SSH deployment.

Installed:
- Docker
- Nginx (inside container)
- Git
- Node.js 20 (upgraded from node v12)

## Docker Build & Run

I used a multi-stage Dockerfile to keep the image small. First stage builds the React app with Node, second stage serves it with Nginx.

```bash
docker build -t my-react-app .
docker run -d --name react-app --restart always -p 80:80 my-react-app
```

The `--restart always` makes sure the container comes back up if the server reboots or if it crashes.

## Nginx Configuration

Nginx runs inside the Docker container.

## CI/CD

I set up GitHub Actions to automatically deploy when I push to main. 

The workflow:
1. Connects to the server via SSH
2. Pulls the latest code
3. Rebuilds the Docker image
4. Stops the old container and starts a new one

I added three secrets in GitHub:
- SERVER_IP
- SERVER_USER
- SSH_PRIVATE_KEY

The deployment script:
```bash
cd ~/my-react-app
git pull origin main
sudo docker build -t my-react-app .
sudo docker rm -f react-app || true
sudo docker run -d --name react-app --restart always -p 80:80 my-react-app
```
