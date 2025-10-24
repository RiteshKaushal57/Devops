## Full step-by-step guide for containerizing your Node.js app with Docker  

This guide explains step-by-step how to containerize a Node.js application locally using Docker.

**Step 0 – Project Setup**

Make sure your project folder has the following files:

- `server.js` → your Node.js application  
- `package.json` → lists dependencies like express  
- `Dockerfile` → instructions to build the Docker image  

---

**Step 1 – Write a Dockerfile**
```
# Step 1: Use Node 18 base image
FROM node:18

# Step 2: Set working directory inside the container
WORKDIR /app

# Step 3: Copy package.json and package-lock.json to container
COPY package*.json ./

# Step 4: Install app dependencies
RUN npm install

# Step 5: Copy the rest of the app files
COPY . .

# Step 6: Expose port 3000 for the app
EXPOSE 3000

# Step 7: Command to run the app
CMD ["node", "server.js"]


```

**Step 2 – Write a server.js file**
```
const http = require("http");

const server = http.createServer((req, res) => {
  res.end("Hello Ritesh! Your app is running inside a container.");
});

server.listen(3000, () => {
  console.log("Server running on http://localhost:3000");
});

```

**Step 3 – Write a package.json file**
```
{
  "name": "myapp",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  }
}
```

**Step 4 – Build Your Docker Image**

```
docker build -t myapp .
```
- `docker build` → builds an image from the Dockerfile
- `-t myapp` → names the image myapp
- `.` → current folder as the build context

**Step 5 – Run Your Container Locally**

```
docker run -p 3000:3000 myapp
```
- `docker run` → starts a container from the image
- `-p 3000:3000` → maps container port 3000 to host port 3000
- `myapp` → the image to run

**Step 6 – Verify the App**
```
http://localhost:3000
```  
*You should see your Node.js app running inside the container.*