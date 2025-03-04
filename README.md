# My Auto Deploy - Node.js Application

This project is a **Node.js application** deployed on an **Amazon Linux EC2 instance** using **PM2** and **GitHub Actions** for automated deployment.

## 🚀 Project Setup

### 1️⃣ **Set Up Amazon Linux EC2 Instance**

1. **Launch an EC2 Instance** (Amazon Linux 2 or Amazon Linux 2023).
2. **Connect to EC2 via SSH**:
   ```sh
   ssh ec2-user@your-ec2-instance-ip
   ```
3. **Update packages**:
   ```sh
   sudo yum update -y
   ```
4. **Install Node.js & PM2**:
   ```sh
   sudo yum install -y nodejs npm
   npm install -g pm2
   ```
5. **Install Git** (if not installed):
   ```sh
   sudo yum install -y git
   ```

### 2️⃣ **Clone the Project on EC2**

```sh
cd /var/www/
git clone https://github.com/your-username/my-auto-deploy.git
cd my-auto-deploy
npm install
```

### 3️⃣ **Start the Application with PM2**

```sh
pm start index.js --name index
pm2 save
pm2 startup
```

## 🚀 GitHub Actions Auto Deployment

### 4️⃣ **Set Up a Self-Hosted GitHub Runner**

1. **On GitHub**, go to: **Settings → Actions → Runners → New Self-Hosted Runner**
2. **Follow GitHub’s instructions to install the runner** on EC2:
   ```sh
   mkdir actions-runner && cd actions-runner
   curl -o actions-runner-linux-x64.tar.gz -L https://github.com/actions/runner/releases/latest/download/actions-runner-linux-x64.tar.gz
   tar xzf actions-runner-linux-x64.tar.gz
   ./config.sh --url https://github.com/your-username/my-auto-deploy --token YOUR_GITHUB_TOKEN
   ./run.sh
   ```

### 5️⃣ **Set Up GitHub Actions Workflow**

Create `.github/workflows/deploy.yml` in your project:

```yaml
name: Deploy to EC2

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: self-hosted

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Pull the latest code
        run: |
          cd /var/www/my-auto-deploy
          git pull origin main

      - name: Install Dependencies
        run: |
          cd /var/www/my-auto-deploy
          npm install

      - name: Restart PM2 Application
        run: |
          cd /var/www/my-auto-deploy
          pm2 restart index || pm2 start index.js --name index
```

### 6️⃣ **Push Workflow to GitHub**

```sh
git add .github/workflows/deploy.yml
git commit -m "Added GitHub Actions deployment workflow"
git push origin main
```

## 🚀 **Verifying Deployment**

1. **Go to GitHub → Actions** to check deployment status.
2. **On EC2, check PM2 process:**
   ```sh
   pm2 list
   ```
   Expected output:
   ```
   ┌─────┬───────┬──────┬────────┬──────────┬───────────┬──────────┐
   │ id  │ name  │ mode │ status │ cpu      │ memory    │ uptime   │
   ├─────┼───────┼──────┼────────┼──────────┼───────────┼──────────┤
   │  0  │ index │ fork │ online │ 0.2%     │ 50MB      │ 3h       │
   └─────┴───────┴──────┴────────┴──────────┴───────────┴──────────┘
   ```

## 🎯 **Troubleshooting**

- **Runner Offline?** Restart it on EC2:
  ```sh
  cd ~/actions-runner
  ./run.sh
  ```
- **Deployment Fails?** Check logs:
  ```sh
  pm2 logs
  ```

## ✅ **Conclusion**

- **Push changes to **``** → Auto deploys to EC2.**
- **PM2 keeps app running & restarts it on reboot.**
- **GitHub Actions automates the deployment process.**

🎉 Your project is now fully automated! 🚀

