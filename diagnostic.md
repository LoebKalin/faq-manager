# Upload & Display Diagnostic Checklist

## 1. Environment Configuration ✓
- **Client `.env.local`**: 
  - `NEXT_PUBLIC_API_URL=http://localhost:5000` ✓
  - `BASE_URL=http://localhost:5000` ✓

- **Server `.env`**: 
  - `PORT=5000` ✓
  - `MONGO_URI=mongodb://127.0.0.1:27017/faqdb` ✓

## 2. Server Setup
- **Upload Route** (`server/src/api/routes/upload.ts`):
  - Creates `server/uploads/photos|videos|pdfs` directories on startup ✓
  - Sanitizes filenames (spaces → dashes) ✓
  - Returns absolute path as `/uploads/{type}/{filename}` ✓
  - Client prefixes with `NEXT_PUBLIC_API_URL` to get full URL ✓

- **Static Serving** (`server/src/app.ts`):
  - `app.use("/uploads", express.static(path.join(__dirname, "../uploads")))` ✓

## 3. Client Upload Flow
- **FileUpload Component** (`client/components/FileUpload/index.tsx`):
  - Sends file to `${baseUrl}/api/upload?type={type}` ✓
  - Server returns `url: /uploads/{type}/{filename}` ✓
  - Client converts to absolute: `http://localhost:5000/uploads/{type}/{filename}` ✓
  - Stores absolute URL in Redux state and passes to AddQuestionModal ✓

## 4. Database Storage
- **AddQuestionModal** saves `photoUrl`, `videoUrl`, `pdfUrl` to MongoDB ✓

## 5. Display Flow
- **FAQ Detail Page** (`client/pages/faq/[id].tsx`):
  - Fetches FAQ from `http://localhost:5000/faqs/{id}` ✓
  - Passes `data.photoUrl`, `data.videoUrl`, `data.pdfUrl` to MediaDisplay ✓

- **MediaDisplay Component** (`client/components/MediaDisplay/index.tsx`):
  - Normalizes URLs (handles `/server/src/uploads/...` → `/uploads/...`) ✓
  - Renders `<img src={photoSrc}>`, `<video src={videoSrc}>`, or `<a href={pdfSrc}>` ✓

## Tests to Run
1. Start servers (both must be running)
2. Upload a file via Add FAQ modal
3. Check browser console for upload response URL
4. Check if file exists: `server/uploads/{type}/{filename}`
5. Test direct URL in browser: `http://localhost:5000/uploads/{type}/{filename}`
6. Check database entry: `db.faqs.findOne({_id: ObjectId("...")})`
7. Navigate to FAQ detail page and check if media displays

## Next Action
Need to verify:
- Are both servers currently running?
- Do files exist in `server/uploads/`?
- What URL is actually stored in MongoDB?
