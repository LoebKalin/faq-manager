# Database Categories Setup Guide

## Overview
Categories have been moved from the client (hardcoded constants) to the MongoDB database. This provides:
- **Centralized management** - Add/edit/delete categories from a single source
- **Dynamic loading** - Categories load from the API on app start
- **Scalability** - Easy to add hundreds of categories without client-side bloat
- **API control** - Categories can be managed via REST endpoints

## Architecture

### Backend (Node/Express)
- **Model**: `server/src/api/models/category.ts` - ICategory interface and schema
- **Controller**: `server/src/api/controllers/category.ts` - CRUD operations + seed function
- **Routes**: `server/src/api/routes/category.ts` - REST endpoints
- **API Base**: `http://localhost:5000/categories`

### Frontend (Next.js/React)
- **Redux Store**: `client/redux/slices/categorySlice.ts` - Async thunk to fetch categories
- **Store Configuration**: `client/redux/store.ts` - Includes categoriesReducer
- **Components Using Categories**:
  - `client/components/AddQuestion/AddQuestionModal/index.tsx` - Category selector
  - `client/components/FaqTable/index.tsx` - Category display

## Initial Setup

### Step 1: Start Your Servers
Make sure both server and client are running:
```bash
# Terminal 1 - Backend
cd server
npm run dev

# Terminal 2 - Frontend
cd client
npm run dev
```

### Step 2: Seed the Database with Categories
Make a GET request to the seed endpoint to populate categories:

**Option A: Using PowerShell**
```powershell
Invoke-RestMethod -Uri "http://localhost:5000/categories/seed" -Method Get
```

**Option B: Using cURL**
```bash
curl http://localhost:5000/categories/seed
```

**Option C: Using Browser**
Simply navigate to: `http://localhost:5000/categories/seed`

**Expected Response:**
```json
{
  "message": "Categories seeded successfully",
  "count": 10
}
```

### Step 3: Verify Categories in Database
Check that categories were created:
```bash
curl http://localhost:5000/categories
```

You should see an array of 10 category groups with their subcategories.

## Category Structure

Each category group contains:
```json
{
  "_id": "ObjectId",
  "id": "Hardware",
  "groupIcon": "🖥️",
  "groupName": "Hardware & Devices",
  "subcategories": [
    {
      "id": "Hardware-Desktop",
      "name": "Desktop Computers"
    },
    ...
  ],
  "createdAt": "2025-12-04T...",
  "updatedAt": "2025-12-04T..."
}
```

## API Endpoints

### GET /categories
Fetch all categories
```bash
curl http://localhost:5000/categories
```

### GET /categories/seed
Seed database with default 10 IT Support categories
```bash
curl http://localhost:5000/categories/seed
```

### POST /categories
Create a new category group
```bash
curl -X POST http://localhost:5000/categories \
  -H "Content-Type: application/json" \
  -d '{
    "id": "MyCategory",
    "groupIcon": "🎓",
    "groupName": "Learning & Development",
    "subcategories": [
      {
        "id": "MyCategory-Training",
        "name": "Training Materials"
      }
    ]
  }'
```

### PUT /categories/:id
Update a category group (replace `:id` with MongoDB ObjectId)
```bash
curl -X PUT http://localhost:5000/categories/63f1a2b3c4d5e6f7g8h9i0j1 \
  -H "Content-Type: application/json" \
  -d '{
    "id": "MyCategory",
    "groupIcon": "🎓",
    "groupName": "Learning & Development (Updated)",
    "subcategories": [...]
  }'
```

### DELETE /categories/:id
Delete a category group
```bash
curl -X DELETE http://localhost:5000/categories/63f1a2b3c4d5e6f7g8h9i0j1
```

## Frontend Usage

### In AddQuestionModal
Categories are fetched on component mount via Redux:
```tsx
const { categories } = useAppSelector((state) => state.categories);

React.useEffect(() => {
  if (categories.length === 0) {
    dispatch(fetchCategories());
  }
}, [dispatch, categories.length]);
```

The category dropdown displays all groups and subcategories hierarchically.

### In FaqTable
Category labels and groups are looked up dynamically:
```tsx
const getCategoryLabel = (categoryId: string) => {
  for (const group of categories) {
    const subcategory = group.subcategories.find((sub) => sub.id === categoryId);
    if (subcategory) return subcategory.name;
  }
  return categoryId;
};
```

## Important Notes

1. **First Load**: Categories are fetched when AddQuestionModal or FaqTable first render. There may be a slight delay on first use.

2. **Caching**: Categories are stored in Redux and persist across page navigations. Refresh the page to reload.

3. **Empty State**: If no categories exist in the database, the dropdown will be empty. Run the seed endpoint to populate.

4. **Backward Compatibility**: Old hardcoded constants in `client/constants/categories.ts` are no longer used but can remain as reference documentation.

5. **Production Use**: For production, you may want to:
   - Protect the `/categories/seed` endpoint with authentication
   - Cache categories on the client for performance
   - Add category pagination if you have 1000+ categories

## Troubleshooting

### Categories dropdown is empty
1. Verify server is running: `curl http://localhost:5000/categories`
2. Run the seed endpoint: `curl http://localhost:5000/categories/seed`
3. Check Redux DevTools to see if `categories.ts` loaded data
4. Check browser console for network errors

### "Cannot resolve @/constants/categories" error
- This error may appear in old imports. Update imports to use Redux:
```tsx
// ❌ Old (no longer used)
import { IT_SUPPORT_CATEGORIES } from "@/constants/categories";

// ✅ New (uses Redux)
const { categories } = useAppSelector((state) => state.categories);
```

### Duplicate categories after multiple seeding
- The seed endpoint clears all existing categories before inserting new ones
- Multiple runs are safe and won't create duplicates

## Adding New Categories

To add new categories:

### Via API (Recommended for Production)
```bash
curl -X POST http://localhost:5000/categories \
  -H "Content-Type: application/json" \
  -d '{
    "id": "NewGroup",
    "groupIcon": "🆕",
    "groupName": "New Category Group",
    "subcategories": [
      { "id": "NewGroup-Item1", "name": "Item 1" },
      { "id": "NewGroup-Item2", "name": "Item 2" }
    ]
  }'
```

### Via MongoDB Compass
1. Connect to `mongodb://127.0.0.1:27017/faqdb`
2. Navigate to `categories` collection
3. Add new document with the structure above
4. Refresh the app to see changes

## Performance Optimization

For large category sets (1000+), consider:
1. **Lazy Loading**: Load categories only when the modal opens
2. **Pagination**: Split categories into pages in the API
3. **Search**: Add a search field to filter categories
4. **Client Cache**: Implement Redux persist to cache categories locally

## Summary

✅ **What Changed:**
- Categories now stored in MongoDB `categories` collection
- Client fetches categories via `/categories` API endpoint
- Redux manages category state and caching
- Seed endpoint pre-populates with 10 IT Support category groups

✅ **What Stayed the Same:**
- Category structure (groups with subcategories)
- Grouped dropdown UI in AddQuestionModal
- Category display in FaqTable
- All emoji icons and names

✅ **Next Steps:**
1. Seed the database: `curl http://localhost:5000/categories/seed`
2. Refresh the app
3. Add/edit FAQs - categories should load from the database
4. (Optional) Create admin panel to manage categories without direct API calls
