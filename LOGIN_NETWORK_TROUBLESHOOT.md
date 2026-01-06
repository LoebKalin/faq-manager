# Login & Network Access Troubleshooting Guide

## Issue: Cannot Access Login or Media from Network IP

### Quick Checklist

- [ ] Both server and client are running
- [ ] Server running on port 5000
- [ ] Client running on port 3000
- [ ] Using correct network IP (not localhost)
- [ ] Firewall allows ports 5000 and 3000
- [ ] Browser console shows no CORS errors

## Step 1: Verify Servers are Running

### Check Server (Port 5000)
```powershell
# In server folder
npm run dev
# Should see: "Server is running on port 5000"
```

### Check Client (Port 3000)
```powershell
# In client folder
npm run dev
# Should see: "compiled client successfully"
```

## Step 2: Test Network Access

### Via Browser
1. Get your machine's IP: `ipconfig` (look for IPv4 Address like 192.168.x.x or 10.x.x.x)
2. Access via IP:
   - Client: `http://YOUR_IP:3000`
   - Server API: `http://YOUR_IP:5000/faqs`

### Via PowerShell
```powershell
# Test if server responds from network IP
Invoke-RestMethod -Uri "http://YOUR_IP:5000/faqs" -Method Get

# Test login endpoint
$body = @{
    username = "Admin"
    password = "Admin123"
} | ConvertTo-Json

Invoke-RestMethod -Uri "http://YOUR_IP:5000/auth/login" -Method Post -Body $body -ContentType "application/json"
```

## Step 3: Check Browser Console

1. Open login page: `http://YOUR_IP:3000/login`
2. Press F12 to open Developer Tools
3. Go to **Console** tab
4. Enter username and password, click Login
5. Look for console messages:
   ```
   Login attempt to: http://YOUR_IP:5000
   Login response: 200 {...}
   Token stored, redirecting to /faq
   ```

## Step 4: Check Network Tab

1. Same page, go to **Network** tab
2. Click Login
3. Look for request to `/auth/login`
4. Check:
   - Status: Should be 200
   - Response: Should contain `{"token": "..."}`

## Common Issues & Fixes

### Issue: "Failed to fetch" or Network Error

**Cause:** Server not reachable from network IP

**Fix:**
```bash
# 1. Verify server is running
# 2. Check if port 5000 is listening
netstat -ano | findstr :5000

# 3. Check firewall - allow ports 5000 and 3000
# Windows Firewall: Add exceptions for Node.js processes
# Or disable firewall temporarily for testing
```

### Issue: CORS Error

**Cause:** Server doesn't allow cross-origin requests

**Fix:** This should already be handled (cors() is enabled), but verify in server output:
- Should see CORS middleware enabled
- No CORS errors in browser console

### Issue: Invalid Credentials

**Cause:** User "Admin" not seeded to database

**Fix:** Create the user:
```bash
cd server
node seed-superadmin-simple.js
# Should show: ✅ Superadmin user created successfully!
```

### Issue: Login Works but Cannot Access `/faq`

**Cause:** Token not properly stored or retrieved

**Fix:**
1. Open DevTools (F12)
2. Go to **Application** tab
3. Check **Local Storage** → verify `token` is present
4. Refresh page - should stay on `/faq` if authenticated
5. If redirected to login, token is not persisting

### Issue: Media (Photos/Videos) Not Loading on Network IP

**This was fixed!** Components now use dynamic API URL based on current location.

**Verify fix:**
1. Upload a photo on network IP
2. Check if URL contains your IP (not localhost)
3. Expected URL: `http://YOUR_IP:5000/uploads/photos/filename.jpg`
4. Not: `http://localhost:5000/uploads/photos/filename.jpg`

## Step 5: Enable Verbose Logging

If still not working, add these commands to see detailed logs:

### Server Debug Mode
```bash
cd server
DEBUG=* npm run dev
```

### Browser Network Monitor
1. Open DevTools → Network tab
2. Filter by "auth" or "login"
3. Click each request to see headers and response
4. Check Response tab for error messages

## Step 6: Manual API Testing

### Test Login Endpoint
```powershell
$apiUrl = "http://10.200.237.188:5000"
$credentials = @{
    username = "Admin"
    password = "Admin123"
} | ConvertTo-Json

$response = Invoke-RestMethod -Uri "$apiUrl/auth/login" `
  -Method Post `
  -Body $credentials `
  -ContentType "application/json"

Write-Host "Token: $($response.token)"
```

### Test FAQ Fetch with Token
```powershell
$token = "your_token_here"
$headers = @{
    Authorization = "Bearer $token"
}

Invoke-RestMethod -Uri "http://10.200.237.188:5000/faqs" `
  -Method Get `
  -Headers $headers
```

## Known Working Configuration

- **Server:** Node.js with Express, running on `0.0.0.0:5000`
- **Client:** Next.js, running on `0.0.0.0:3000`
- **Network:** Accessed via IP `10.200.237.188`
- **Auth:** JWT tokens in localStorage
- **CORS:** Enabled via cors() middleware
- **Media URLs:** Dynamic based on client location

## Quick Reset

If everything is broken:

```bash
# 1. Kill both processes (Ctrl+C in both terminals)

# 2. Clear node_modules and reinstall
cd server
npm install
npm run dev

# In another terminal:
cd client
npm install
npm run dev

# 3. Seed admin user
cd server
node seed-superadmin-simple.js

# 4. Access via IP
# Client: http://YOUR_IP:3000/login
# Server: http://YOUR_IP:5000/faqs
```

## Support Info to Collect

If you still can't access:
1. Your machine IP: `ipconfig | findstr "IPv4"`
2. Server logs output (copy full text)
3. Browser console errors (F12 → Console)
4. Network tab errors (F12 → Network)
5. `localhost:5000/faqs` works? (Yes/No)
6. `YOUR_IP:5000/faqs` works? (Yes/No)
7. `YOUR_IP:3000/login` page loads? (Yes/No)
8. Login attempt error message from console
