# Troubleshooting: Categories Not Showing in Dropdown

This guide helps diagnose why categories aren't appearing in the "Add FAQ" modal.

## Step 1: Check if Categories are in Database

### Method 1: Using PowerShell
```powershell
# Check if server is running and categories are available
$response = Invoke-RestMethod -Uri "http://localhost:5000/categories" -Method Get
if ($response.Count -eq 0) {
    Write-Host "❌ No categories in database"
    Write-Host "Proceeding to seed..."
} else {
    Write-Host "✅ Found $($response.Count) category groups"
    $response | ConvertTo-Json | Write-Host
}
```

### Method 2: Seed the Database
Run this in the server directory:
```bash
cd server
npx ts-node --esm seed-categories.ts
```

Expected output:
```
Connected to MongoDB
Cleared existing categories
✅ Successfully seeded 10 category groups
Categories seeded:
  - 🖥️ Hardware & Devices (7 subcategories)
  - 💾 Software & Applications (7 subcategories)
  - ...
```

## Step 2: Check Browser Console

1. Open browser DevTools (F12)
2. Go to Console tab
3. Look for these log messages:

**When modal opens:**
```
[AddQuestionModal] Modal opened, fetching categories...
[fetchCategories] Fetching from: http://localhost:5000/categories
[fetchCategories] Received data: Array(10) [...]
```

**If no logs appear:**
- Modal might not be opening correctly
- Categories might already be loaded (check Redux DevTools)

**If error messages appear:**
- Check network tab for failed requests
- Verify server is running on port 5000

## Step 3: Check Redux DevTools

1. Install Redux DevTools browser extension (if not already installed)
2. Open DevTools (F12) → Redux tab
3. Look for `categories` in the state tree
4. Should see:
```json
{
  "categories": {
    "categories": [
      {
        "id": "Hardware",
        "groupIcon": "🖥️",
        "groupName": "Hardware & Devices",
        "subcategories": [...]
      },
      ...
    ],
    "loading": false,
    "error": null
  }
}
```

## Step 4: Verify Environment Variables

Client must have `NEXT_PUBLIC_API_URL` set:
```bash
# In client/.env.local
NEXT_PUBLIC_API_URL=http://localhost:5000
```

Server must have `MONGO_URI` or `MONGODB_URI` set:
```bash
# In server/.env
MONGODB_URI=mongodb://127.0.0.1:27017/faqdb
# OR
MONGO_URI=mongodb://127.0.0.1:27017/faqdb
```

## Step 5: Manual Test Requests

### Test 1: Check Server is Running
```powershell
Invoke-RestMethod -Uri "http://localhost:5000" -Method Get
```

### Test 2: Fetch Categories via API
```powershell
$categories = Invoke-RestMethod -Uri "http://localhost:5000/categories" -Method Get
Write-Host "Found $($categories.Count) categories"
$categories | ConvertTo-Json | Write-Host
```

### Test 3: Seed Endpoint
```powershell
Invoke-RestMethod -Uri "http://localhost:5000/categories/seed" -Method Get | ConvertTo-Json | Write-Host
```

## Common Issues & Solutions

### Issue: Empty Dropdown with "No categories available"
**Cause:** Database is empty  
**Solution:**
```bash
cd server
npx ts-node --esm seed-categories.ts
```

### Issue: Categories Loading... (stays forever)
**Cause:** API request is failing or server not running  
**Solution:**
1. Verify server is running: `npm run dev` in server folder
2. Check browser console for network errors
3. Ensure port 5000 is not blocked

### Issue: "Module not found" errors
**Cause:** Path aliases not configured  
**Solution:** Verify `tsconfig.json` has `@/constants` path:
```json
"paths": {
  "@/constants/*": ["constants/*"],
  "@/components/*": ["components/*"],
  "@/pages/*": ["pages/*"],
  "@/redux/*": ["redux/*"]
}
```

### Issue: Categories appear but are wrong/old data
**Cause:** Stale data in Redux  
**Solution:**
1. Clear browser cache
2. Refresh page (F5)
3. Reseed database: `npx ts-node --esm seed-categories.ts`

### Issue: Server Error "Cannot find module 'category.ts'"
**Cause:** Route not registered  
**Solution:** Verify `server/src/app.ts` includes:
```typescript
import categoryRoutes from "./api/routes/category.js";
// ...
app.use("/categories", categoryRoutes);
```

## Debug Steps for Next Time

1. **Check server logs** - Look for connection errors
2. **Check browser console** - Look for fetch errors
3. **Check network tab** - Verify API calls to `/categories`
4. **Check Redux** - Verify categories loaded into state
5. **Check MongoDB** - Connect with MongoDB Compass to verify data

## Quick Checklist

- [ ] MongoDB is running
- [ ] Server is running on port 5000
- [ ] Categories are seeded in database
- [ ] `NEXT_PUBLIC_API_URL=http://localhost:5000` in `.env.local`
- [ ] `MONGO_URI` set in server `.env`
- [ ] Client routes include category routes
- [ ] Redux store includes categoriesReducer
- [ ] Browser console shows no errors
- [ ] Network tab shows successful `/categories` requests
- [ ] Redux DevTools shows categories in state

## If All Else Fails

1. **Complete Reset:**
   ```bash
   # Stop both servers (Ctrl+C)
   # Clear MongoDB (delete database)
   # Reseed: npx ts-node --esm seed-categories.ts
   # Restart both servers
   ```

2. **Check Logs:**
   - Server logs should show database operations
   - Browser console should show fetch activity
   - Check network tab for 200 responses

3. **Restart Everything:**
   ```bash
   # Terminal 1 - Backend
   cd server
   npm run dev
   
   # Terminal 2 - Frontend  
   cd client
   npm run dev
   ```

4. **Clear Cache:**
   - Browser: Ctrl+Shift+Delete
   - npm: npm cache clean --force
   - Next.js: Delete `.next` folder

## Support

If categories still don't show after these steps:
1. Check browser console for specific error messages
2. Share the error message from console
3. Verify MongoDB connection string
4. Ensure all 3 files exist:
   - `server/src/api/models/category.ts`
   - `server/src/api/controllers/category.ts`
   - `server/src/api/routes/category.ts`
