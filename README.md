# 📝 Todo App - Fullstack Project (React + FastAPI)

A production-ready Todo App with React frontend and FastAPI backend, deployable on **Azure Linux VMs** using either **public IP** or **private IP via NGINX proxy**. Perfect for DevOps learners deploying fullstack apps to the cloud.

---

## 🔧 Choose Your Setup Path

Depending on your working style, pick one of the two:

### 🖥️ Option A: **Work Directly on Azure VMs**

- Code, build & run everything inside the VMs.
- No file transfer needed.

👉 Go to: [📂 VM-Based Setup](#-vm-based-setup)

---

### 💻 Option B: **Work Locally & Deploy to Azure VMs**

- Develop and build React frontend locally.
- Copy `build/` output to frontend VM.

👉 Go to: [🚀 Local Build + VM Deploy](#-local-build--vm-deploy)

---

## 📂 Project Structure

```
todo-frontend/
├── public/
├── src/
│   └── TodoApp.js        # Frontend logic + backend API URL config
├── build/                # Created after `npm run build`
├── package.json
└── README.md
```

---

## ⚙️ Prerequisites

On **VM or Local**, ensure:

- ✅ Node.js (v16.x)
- ✅ npm
- ✅ Python 3.8+
- ✅ pip

To install Node.js on Ubuntu:

```bash
curl -s https://deb.nodesource.com/setup_16.x | sudo bash
sudo apt install nodejs -y
```

---

## 📂 VM-Based Setup

### Step 1: SSH into Your VM

```bash
ssh username@<vm-public-ip>
```

---

### Step 2: Clone Your Code (if needed)

```bash
git clone https://github.com/Riteshatri/todoFrontendReactMonolithic.git
cd todoFrontendReactMonolithic
```

---

### Step 3: Configure API URL

➡️ Open the frontend config file and update the backend API URL.

#### 🖥️ When you're working on Linux VM:

```bash
nano src/TodoApp.js
```


Then update:

##### 👉 Public IP of Backend VM:
```javascript
const API_BASE_URL = 'http://<backend-vm-public-ip>:8000/api';
```

##### 👉 Private IP with NGINX:
```javascript
const API_BASE_URL = 'http://<frontend-vm-public-ip>/api';
```

---

### Step 4: Install & Build Frontend

```bash
npm install
npm run build
```

---

### Step 5: Setup Backend (FastAPI)

```bash
pip install fastapi uvicorn mysql-connector-python
uvicorn main:app --host 0.0.0.0 --port 8000
```

---

### Step 6: (Optional) Install & Configure NGINX (for Private IP)

```bash
sudo apt update
sudo apt install nginx -y
sudo nano /etc/nginx/sites-available/default
```

#### 🔄 Insert Proxy Configuration 🔥

📌 Add this block **just above** the line `root /var/www/html;`:

```nginx
location /api {
   proxy_pass http://<PrivateIP-of-backend-vm>:8000;
   proxy_set_header Host $host;
   proxy_set_header X-Real-IP $remote_addr;
   proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
   proxy_set_header X-Forwarded-Proto $scheme;
}
```

Then restart:

```bash
sudo service nginx restart
```

---

### Step 7: Deploy Frontend to NGINX Root

```bash
sudo rm -rf /var/www/html/*
sudo cp -r build/* /var/www/html/
sudo service nginx restart
```

---

## 🚀 Local Build + VM Deploy

### Step 1: Clone & Build Locally

```bash
git clone https://github.com/Riteshatri/todoFrontendReactMonolithic.git
cd todoFrontendReactMonolithic
```
### Step 2: Configure API URL

➡️ Open the frontend config file and update the backend API URL.

#### 💻 When you're working on Windows:
Edit API in `src/TodoApp.js` as explained below: -

- Open the `src/TodoApp.js` file in **VS Code**, **Notepad++**, or any text editor.
- Find the line where `API_BASE_URL` is defined.

Then update:

### Step 3.a:👉 Public IP of Backend VM:
```javascript
const API_BASE_URL = 'http://<backend-vm-public-ip>:8000/api';
```

### Step 3.b:👉 Private IP with NGINX:
```javascript
const API_BASE_URL = 'http://<frontend-vm-public-ip>/api';
```

### (Optional) Install & Configure NGINX (for Private IP)
These all steps should be followed into our vm...
```bash
sudo apt update
sudo apt install nginx -y
sudo nano /etc/nginx/sites-available/default
```

### 🔄 Insert Proxy Configuration 🔥

📌 Add this block **just above** the line `root /var/www/html;`:

```nginx
location /api {
   proxy_pass http://<PrivateIP-of-backend-vm>:8000;
   proxy_set_header Host $host;
   proxy_set_header X-Real-IP $remote_addr;
   proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
   proxy_set_header X-Forwarded-Proto $scheme;
}
```

Then restart:

```bash
sudo service nginx restart
```


### Step 4:👉 Install node and build folder (Dependencies):
```bash
npm install
npm run build
```

---

### Step 2: SCP Build Folder to Frontend VM

```bash
scp -r build/* username@<frontend-vm-ip>:/home/username/
```

---

### Step 3: SSH into Frontend VM and Setup NGINX

```bash
ssh username@<frontend-vm-ip>
sudo apt update && sudo apt install nginx -y
```

#### 🧹 Clean and Deploy:

```bash
sudo rm -rf /var/www/html/*
sudo cp -r /home/username/* /var/www/html/
sudo service nginx restart
```

---

## 🔐 Azure SQL Firewall Rule (When infrastructure/Code is made/written in terraform , instead of azure portal)

### Allow the VM’s Public IP in Azure SQL Server Firewall

Follows these steps to connect backend to Azure SQL:

1. Go to Azure Portal
2. Find your SQL Server → Networking
3. Add a firewall rule:
   - Name: `backend-vm-access`
   - Start IP = End IP = `<backend-vm-public-ip>`
4. Click Save

---

## ✅ Final Decision Table

| You Build Where | Backend Communication | Needed Setup             | Access App At                     |
|-----------------|------------------------|--------------------------|-----------------------------------|
| VM              | Public IP              | FastAPI only             | http://backend-ip:8000/api        |
| VM              | Private IP + NGINX     | NGINX proxy + FastAPI    | http://frontend-ip/api            |
| Local           | Public IP              | SCP + NGINX (simple)     | http://frontend-ip                |
| Local           | Private IP + NGINX     | SCP + NGINX proxy setup  | http://frontend-ip/api            |

---

## 📌 Tips to Validate Setup

- ✅ Run `uvicorn` and check if backend is listening on 8000.
- ✅ Visit `http://<frontend-ip>` in your browser.
- ✅ Check browser DevTools → Network → API calls hitting `/api/todos`

---

## 💬 Contributing

Pull requests are welcome. For major changes, please open an issue first.

---

## 📃 License

MIT License

---

## 👨‍💻 Author

**Ritesh Sharma**  
🔗 [LinkedIn](https://www.linkedin.com/in/riteshatri)
