# Yanshi Events Backend

This is the backend for the Yanshi Events management application. It provides API endpoints to manage categories, subcategories, and events.

## Getting Started

### Prerequisites

Make sure you have the following installed:

- Node.js (v14 or later)
- npm (v6 or later)
- PostgreSQL (or any other database supported by Prisma)

### Installation

1. **Clone the repository:**

   ```bash
   git clone "url from the address bar"
   ```

2. **Navigate to the project directory:**

   ```bash
   cd yanshi-events-backend
   ```

3. **Install the dependencies:**

   ```bash
   npm install
   ```

4. **Set up the environment variables:**

   - Copy the `.env.example` file to `.env`:

     ```bash
     cp .env.example .env
     ```

   - The `.env` file already contains a sample `DATABASE_URL`. You may need to update it according to your database setup.

5. **Run Prisma migrations:**

   ```bash
   npx prisma migrate dev
   ```

### Running the Application

To start the application in development mode:

```bash
npm run dev
```

The server will start on `http://localhost:3000`.

### Testing the APIs

To test the APIs, follow these steps:

1. **Open Postman.**

2. **Import the Postman collection:**

   - Go to "File" > "Import".
   - Select the `postman-collection.json` file located in the root of the project directory.

3. **Run the API requests:**

   - Use the requests in the imported collection to interact with the backend. You can create, read, update, and delete categories, subcategories, and events.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

```

### Summary:
- This `README.md` provides clear instructions for setting up and running your project.
- It includes steps to clone the repository, install dependencies, set up environment variables, run the application, and test the APIs using Postman.

You can save this content in a `README.md` file at the root of your project, commit it, and push it to your GitHub repository.
```

sudo nano /etc/nginx/sites-available/default

pm2 start yarn --name "yanshi-events-backend" -- start

or

pm2 start ecosystem.config.cjs

```bash
# Redirect HTTP to HTTPS
server {
 listen 80;
 server_name 13.201.228.6;  # Use your actual public IP

 # Redirect all traffic to HTTPS
 return 301 https://$host$request_uri;
}

# Serve the app over HTTPS
server {
 listen 443 ssl;
 server_name 13.201.228.6;  # Use your actual public IP

 # SSL Configuration
 ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
 ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;

 # Security enhancements (optional)
 ssl_protocols TLSv1.2 TLSv1.3;
 ssl_prefer_server_ciphers on;
 ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384';

 location / {
     proxy_pass http://localhost:4000;
     proxy_http_version 1.1;
     proxy_set_header Upgrade $http_upgrade;
     proxy_set_header Connection 'upgrade';
     proxy_set_header Host $host;
     proxy_cache_bypass $http_upgrade;
 }
}
```
