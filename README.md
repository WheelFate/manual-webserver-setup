# Manual Web Server Setup and Configuration

This repository is a beginner-friendly, step-by-step tutorial on how to manually set up and configure an Nginx web server on a Linux system to host a static website. Understanding this process is a fundamental skill in DevOps and web development.

## Project Goal

To demystify the process of web server setup by manually configuring a server from a clean Linux installation to a live website.

## Table of Contents

1.  [Prerequisites](#prerequisites)
2.  [Project Structure](#project-structure)
3.  [Step-by-Step Guide](#step-by-step-guide)
    *   [Step 1: Accessing Your Server](#step-1-accessing-your-server)
    *   [Step 2: Installing Nginx](#step-2-installing-nginx)
    *   [Step 3: Creating Your Website Files](#step-3-creating-your-website-files)
    *   [Step 4: Configuring Nginx to Serve Your Website](#step-4-configuring-nginx-to-serve-your-website)
    *   [Step 5: Activating the Configuration and Final Testing](#step-5-activating-the-configuration-and-final-testing)
4.  [Next Steps & Further Learning](#next-steps--further-learning)

---

### Prerequisites

*   A fresh installation of a Debian-based Linux distribution (like Ubuntu) on a Virtual Machine (e.g., VirtualBox, VMware) or a cloud provider (e.g., AWS EC2, DigitalOcean Droplet).
*   Basic familiarity with the Linux command line.
*   A user with `sudo` privileges.

---

### Project Structure

```
.
├── nginx-config/
│   └── my-website.conf
├── sample-website/
│   ├── index.html
│   └── style.css
└── README.md
```

*   `nginx-config/`: Contains the Nginx configuration file for our website.
*   `sample-website/`: A simple, ready-to-use static website.
*   `README.md`: The instruction manual you are currently reading.

---

### Step-by-Step Guide

#### Step 1: Accessing Your Server

Connect to your Linux machine using SSH. If you are using a local virtual machine, you can open a terminal directly. First, update your package list:

```bash
sudo apt update && sudo apt upgrade -y
```

#### Step 2: Installing Nginx

Nginx is a high-performance web server. [1] Install it using the `apt` package manager:

```bash
sudo apt install nginx -y
```

Once installed, you can check its status to ensure it's running:

```bash
sudo systemctl status nginx
```

You should see an `active (running)` status.

#### Step 3: Creating Your Website Files

We need a place on the server to store our website's files. It's standard practice to create a directory within `/var/www/`. [3]

First, create a directory for your website:

```bash
sudo mkdir -p /var/www/my-website.com/html
```

Next, copy the provided sample website files from this repository into that directory.

```bash
# On your local machine, navigate to the `sample-website` directory
# and use `scp` to copy the files to your server.
# Replace `your_server_ip` with your server's IP address.
scp -r ./sample-website/* your_username@your_server_ip:/var/www/my-website.com/html/```

Alternatively, you can manually create the `index.html` and `style.css` files on the server and paste the code from this repository.

#### Step 4: Configuring Nginx to Serve Your Website

Nginx's configuration files are located in `/etc/nginx/`. The best practice is to create a new configuration file for each site in the `sites-available` directory. [8]

1.  **Create a new configuration file:**

    ```bash
    sudo nano /etc/nginx/sites-available/my-website.com
    ```

2.  **Add the server block configuration:**

    Copy the contents of `nginx-config/my-website.conf` from this repository and paste them into the nano editor. This file tells Nginx how to handle incoming requests for your domain. [2]

    ```nginx
    server {
        listen 80;
        listen [::]:80;

        server_name my-website.com www.my-website.com;

        root /var/www/my-website.com/html;
        index index.html;

        location / {
            try_files $uri $uri/ =404;
        }
    }
    ```

    *   `listen 80;`: Tells Nginx to listen for incoming connections on port 80 (the default HTTP port). [4]
    *   `server_name`: The domain name(s) that this configuration should respond to.
    *   `root`: The directory where your website files are stored. [5]
    *   `location /`: A block that applies to all requests. `try_files` attempts to find a file that matches the request URI. [2]

#### Step 5: Activating the Configuration and Final Testing

To enable the new configuration, we need to create a symbolic link from `sites-available` to `sites-enabled`. [7]

```bash
sudo ln -s /etc/nginx/sites-available/my-website.com /etc/nginx/sites-enabled/
```

Before restarting Nginx, it's crucial to test the configuration for syntax errors:

```bash
sudo nginx -t
```

If the syntax is okay, you can safely restart Nginx to apply the changes:

```bash
sudo systemctl restart nginx
```

Your website should now be live! You can view it by navigating to your server's IP address in a web browser.

---

### Next Steps & Further Learning

Congratulations on setting up your first web server! Here are some ideas to continue your learning:

*   **Custom Domain:** Purchase a domain name and point it to your server's IP address.
*   **Enable HTTPS:** Secure your site with a free SSL certificate from Let's Encrypt.
*   **Serve Multiple Websites:** Configure another server block to host a second website on the same server.
