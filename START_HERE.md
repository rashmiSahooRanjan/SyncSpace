# 🚀 SyncSpace - GETTING STARTED (5 Minutes)

## Step-by-Step Guide to Run Your Application

---

## ⚡ Option 1: FASTEST WAY (Automated - Windows)

### Open PowerShell or Command Prompt and run:

```cmd
# 1. Install everything
install.bat

# 2. Seed sample data
cd server
npm run seed

# 3. Start both servers
cd ..
start-dev.bat
```

**That's it!** The application will open at http://localhost:5173

---

## ⚡ Option 2: FASTEST WAY (Automated - macOS/Linux)

### Open Terminal and run:

```bash
# Make scripts executable
chmod +x install.sh start-dev.sh

# 1. Install everything
./install.sh

# 2. Seed sample data
cd server
npm run seed

# 3. Start both servers
cd ..
./start-dev.sh
```

**That's it!** The application will open at http://localhost:5173

---

## 📝 Option 3: Manual Step-by-Step

### Prerequisites Check

```bash
# Check Node.js is installed (should be v16+)
node --version

# Check npm is installed
npm --version

# Check MongoDB is running
# Windows: Check Services for "MongoDB"
# macOS: brew services list | grep mongodb
# Linux: sudo systemctl status mongod
```

### Step 1: Install Backend

```bash
# Navigate to server folder
cd server

# Install dependencies
npm install

# Create environment file
# Windows:
copy .env.example .env

# macOS/Linux:
cp .env.example .env

# Edit .env if needed (default values work with local MongoDB)
```

### Step 2: Seed Sample Data

```bash
# Still in server folder
npm run seed
```

You should see:
```
✅ Seed data created successfully!

Login credentials:
Email: admin@syncspace.com | Password: password123
Email: john@syncspace.com | Password: password123
Email: jane@syncspace.com | Password: password123
Email: bob@syncspace.com | Password: password123
```

### Step 3: Install Frontend

```bash
# Open NEW terminal/command prompt
# Navigate to client folder
cd client

# Install dependencies
npm install

# Create environment file
# Windows:
copy .env.example .env

# macOS/Linux:
cp .env.example .env
```

### Step 4: Start Backend Server

```bash
# In the first terminal (server folder)
npm run dev
```

You should see:
```
🚀 Server running on port 5000
📡 Socket.IO running on port 5000
🌍 Environment: development
MongoDB Connected: localhost
```

### Step 5: Start Frontend Server

```bash
# In the second terminal (client folder)
npm run dev
```

You should see:
```
  VITE v4.x.x  ready in xxx ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
```

### Step 6: Open Browser

Go to: **http://localhost:5173**

---

## 🔐 Login

Use any of these demo accounts:

| Email | Password | Description |
|-------|----------|-------------|
| admin@syncspace.com | password123 | Admin user with full access |
| john@syncspace.com | password123 | Regular member |
| jane@syncspace.com | password123 | Regular member |
| bob@syncspace.com | password123 | Regular member |

---

## ✅ What You Should See

### 1. After Login:
- Dashboard with "SyncSpace Project" workspace
- Stats showing 4 members, 1 board, 0 documents, 0 files

### 2. Click on "SyncSpace Project":
- Workspace overview
- Team members list
- Recent boards section

### 3. Click on "Boards" in sidebar:
- Kanban board "Sprint 1"
- Three columns: To Do, In Progress, Done
- 6 pre-loaded tasks

### 4. Try Drag & Drop:
- Drag tasks between columns
- **Open in TWO browser windows**
- Watch real-time updates! 🎉

---

## 🧪 Test Real-Time Features

### Test 1: Real-Time Tasks
1. Open http://localhost:5173 in **two different browsers** (Chrome + Edge)
2. Login to both with different accounts
3. Go to Boards
4. In one browser, drag a task
5. Watch it update instantly in the other browser! ✨

### Test 2: Notifications
1. Create a new task
2. Assign it to another user
3. Login as that user in another window
4. Check notifications bell icon 🔔

---

## 🛠️ Troubleshooting

### Problem: "Cannot connect to MongoDB"

**Solution:**
```bash
# Windows - Start MongoDB
# Open Services and start "MongoDB" service
# OR install MongoDB from: https://www.mongodb.com/try/download/community

# macOS
brew services start mongodb-community

# Linux
sudo systemctl start mongod
```

**Alternative:** Use MongoDB Atlas (cloud)
1. Create free account: https://www.mongodb.com/cloud/atlas
2. Create cluster
3. Get connection string
4. Update `MONGODB_URI` in `server/.env`

### Problem: "Port 5000 already in use"

**Solution:**
```bash
# Windows
netstat -ano | findstr :5000
# Note the PID and kill it:
taskkill /PID <PID> /F

# macOS/Linux
lsof -ti:5000 | xargs kill
```

Or change port in `server/.env`:
```
PORT=5001
```

### Problem: "Port 5173 already in use"

**Solution:**
```bash
# Windows
netstat -ano | findstr :5173
taskkill /PID <PID> /F

# macOS/Linux
lsof -ti:5173 | xargs kill
```

### Problem: Frontend can't connect to backend

**Check:**
1. Backend is running on http://localhost:5000
2. Check `client/.env` has:
   ```
   VITE_API_URL=http://localhost:5000
   VITE_SOCKET_URL=http://localhost:5000
   ```
3. Restart both servers

### Problem: "Module not found" errors

**Solution:**
```bash
# Delete node_modules and reinstall
# In server folder:
rm -rf node_modules package-lock.json
npm install

# In client folder:
rm -rf node_modules package-lock.json
npm install
```

---

## 📊 Verify Everything is Working

### Backend Health Check
Open browser: http://localhost:5000/api/health

Should see:
```json
{
  "status": "ok",
  "message": "SyncSpace API is running"
}
```

### Test API Login
```bash
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d "{\"email\":\"admin@syncspace.com\",\"password\":\"password123\"}"
```

Should return user data with token.

### Check Socket.IO Connection
1. Open browser console (F12)
2. Go to Network tab
3. Filter by "WS" (WebSocket)
4. You should see Socket.IO connection
5. Status should be "101 Switching Protocols"

---

## 🎯 Quick Feature Tour

### 1. Create a Workspace
- Click "+ Create Workspace" button
- Enter name and description
- Click "Create Workspace"

### 2. Create a Board
- Go to workspace
- Click "+ Create Board" (if you add this button)
- Or use the seeded "Sprint 1" board

### 3. Create a Task
- Go to Kanban board
- Click "+ Add Task"
- Enter task title
- Select column
- Watch it appear in real-time!

### 4. Drag & Drop
- Click and hold any task
- Drag to another column
- Release to drop
- Real-time update! ✨

### 5. Check Notifications
- Click bell icon (🔔) in header
- See recent notifications
- Click to mark as read

---

## 📱 Browser Support

Tested and working on:
- ✅ Chrome/Edge (Recommended)
- ✅ Firefox
- ✅ Safari
- ✅ Opera

---

## 🎓 What to Explore Next

1. **Code Structure**
   - Check `server/models/` - Database schemas
   - Check `server/controllers/` - Business logic
   - Check `client/src/pages/` - UI pages
   - Check `client/src/redux/` - State management

2. **Add Features**
   - Implement Chat page
   - Implement Document Editor
   - Implement File Manager
   - Add more task properties

3. **Customize**
   - Modify CSS in `client/src/styles/`
   - Change colors in CSS variables
   - Add your own logo
   - Customize workspace layouts

---

## 📚 Learn More

- **MongoDB**: https://docs.mongodb.com/
- **Express**: https://expressjs.com/
- **React**: https://react.dev/
- **Node.js**: https://nodejs.org/
- **Socket.IO**: https://socket.io/docs/
- **Redux Toolkit**: https://redux-toolkit.js.org/

---

## 🆘 Need Help?

1. **Check console logs** (F12 in browser)
2. **Check terminal output** for errors
3. **Review documentation**:
   - `README.md` - Full documentation
   - `SETUP.md` - Detailed setup
   - `PROJECT_SUMMARY.md` - Feature overview
4. **Check Network tab** for API errors

---

## ✅ Success Checklist

- [ ] Node.js installed
- [ ] MongoDB running (or Atlas connected)
- [ ] Backend dependencies installed
- [ ] Frontend dependencies installed
- [ ] Sample data seeded
- [ ] Backend running on port 5000
- [ ] Frontend running on port 5173
- [ ] Logged in successfully
- [ ] Can see workspace
- [ ] Can drag and drop tasks
- [ ] Real-time updates working

---

## 🎉 You're All Set!

**Congratulations!** You now have a fully functional real-time collaborative workspace application running locally.

### What you have:
✅ Authentication system
✅ Workspace management
✅ Real-time Kanban boards
✅ Drag & drop tasks
✅ Live notifications
✅ Team collaboration
✅ Socket.IO integration
✅ Beautiful UI with standard CSS

### Start building amazing things! 🚀

---

**Questions? Check the other documentation files:**
- `README.md` - Comprehensive guide
- `SETUP.md` - Detailed setup
- `QUICKSTART.md` - Quick reference
- `PROJECT_SUMMARY.md` - Complete feature list

**Happy Coding! 💻✨**
