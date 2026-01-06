# File Upload & Display Troubleshooting Guide

## Problem
Database has URLs like:
- `http://localhost:5000/uploads/photos/1.-68373-1764749143339-943062552.png`
- `http://localhost:5000/uploads/pdfs/Agenda-Town-Hall-1764749910566-594631423.pdf`

But when accessing these URLs, you get `Cannot GET /uploads/...` → **Files don't exist on disk.**

## Root Cause Analysis

The URLs are stored in the database, which means:
1. ✓ Upload endpoint accepted the request
2. ✓ MongoDB saved the URL record
3. ✗ But the actual file was never saved to disk

**Why this happens:**
- Multer `destination` callback or `filename` callback had an error
- Server crashed during upload
- File was deleted after upload
- OR: The files were uploaded with a different naming scheme

## Solution: Re-upload Files

Since the database already has URLs stored but files don't exist, the cleanest fix is to:

1. **Delete the old entries** from MongoDB (optional cleanup)
2. **Re-upload new files** (which will create the files on disk)

### Steps to Fix:

#### Step 1: Verify Server is Running
```powershell
cd 'C:\Users\IT Administrator\website\FAQ-manager-main\server'
npm run dev
```
Wait for: "Server is running on port 5000"

#### Step 2: Verify Client is Running (new terminal)
```powershell
cd 'C:\Users\IT Administrator\website\FAQ-manager-main\client'
npm run dev
```
Wait for the dev server to start.

#### Step 3: Open the App
- Go to http://localhost:3000/faq
- Click on the FAQ entry "How do I create an account?"
- Click "Edit"

#### Step 4: Clear Old Media URLs
- In the Edit modal, click the delete (X) button on:
  - Photo field
  - Video field
  - PDF field
- This clears the old broken URLs

#### Step 5: Re-Upload Files
- Upload a new photo, video, and PDF
- Watch the browser console (F12 → Console tab) for log messages
- Confirm you see: "File uploaded successfully!"

#### Step 6: Check if Files Exist
After uploading, run this in PowerShell:
```powershell
Get-ChildItem -Recurse 'C:\Users\IT Administrator\website\FAQ-manager-main\server\uploads'
```

You should see files like:
```
photos\photo-1704067200-123456789.png
videos\video-name-1704067205-987654321.mp4
pdfs\document-1704067210-555666777.pdf
```

#### Step 7: Save and View
- Click "Update" to save the FAQ with new media URLs
- Navigate back to the FAQ detail page
- Check if media displays in "Media & Resources" section

## If Files Still Don't Appear After Re-Upload

Run this diagnostic in the server folder:
```bash
npm list multer
```

If multer is not installed or has errors, reinstall:
```bash
npm install multer --save
```

Then restart the server (`npm run dev`).

## If Still Not Working

The issue could be:
1. **CORS** - Check server logs for CORS errors (unlikely, but possible)
2. **Express static middleware** - Verify it's mounted in `src/app.ts`
3. **File permissions** - Windows might be blocking file creation

For file permissions on Windows, try running PowerShell as Administrator or changing the uploads folder to a different location (Documents, Downloads, etc.) that doesn't require special permissions.

---

**Next: Please follow the steps above and let me know:**
- Do files appear in `server/uploads/` after re-uploading?
- Do you see the file at `http://localhost:5000/uploads/...` when accessed directly?
- Does media display on the FAQ detail page?
