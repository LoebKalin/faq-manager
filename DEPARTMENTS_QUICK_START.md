# Department Management - Quick Start

## Starting the Application

```bash
# Terminal 1: Start the backend server
cd server
npm run dev
# Server runs on http://localhost:5000

# Terminal 2: Start the frontend client
cd client
npm run dev
# Client runs on http://localhost:3000
```

## Accessing Departments

1. Go to `http://localhost:3000` or `http://<your-ip>:3000`
2. Log in with your admin credentials (default: Admin / Admin123)
3. Click "Departments" in the top navigation bar
4. You'll see the Department Management interface

## UI Operations

### Add Department
- Click "Add Department" button (top right)
- Enter department name and optional description
- Click "Create"

### Edit Department
- Click the pencil icon (✎) on any row
- Update the fields
- Click "Update"

### Delete Department
- Click the trash icon (🗑) on any row
- Confirm deletion in the dialog
- Department is removed

## Database

- **Database**: MongoDB (configured in `server/.env`)
- **Collection**: `departments`
- **Fields**: name, description, createdAt, updatedAt

### Check MongoDB Connection
```bash
# If MongoDB is running locally
mongo
> use faqdb
> db.departments.find()
```

## API Endpoints

All endpoints are at `http://localhost:5000/departments`

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/departments` | Get all departments |
| GET | `/departments/:id` | Get single department |
| POST | `/departments` | Create department |
| PUT | `/departments/:id` | Update department |
| DELETE | `/departments/:id` | Delete department |

## Request/Response Examples

### Create
```json
POST /departments
{
  "name": "Information Technology",
  "description": "IT Support and Services"
}

Response:
{
  "_id": "507f1f77bcf86cd799439011",
  "name": "Information Technology",
  "description": "IT Support and Services",
  "createdAt": "2025-12-11T10:00:00Z",
  "updatedAt": "2025-12-11T10:00:00Z"
}
```

### Update
```json
PUT /departments/507f1f77bcf86cd799439011
{
  "name": "IT Services",
  "description": "Updated description"
}
```

### Delete
```
DELETE /departments/507f1f77bcf86cd799439011
Response: { "message": "Department deleted successfully" }
```

## Troubleshooting

### Department page shows "No departments found"
- Ensure MongoDB is running
- Check server logs for connection errors
- Try adding a new department

### API calls fail with 500 error
- Check MongoDB connection in `server/.env`
- Verify server is running on port 5000
- Check server terminal for error messages

### Changes don't persist after reload
- Verify MongoDB is running
- Check browser console for errors
- Check that department name is unique

## File Locations

| Component | Path |
|-----------|------|
| Model | `server/src/api/models/department.ts` |
| Controller | `server/src/api/controllers/department.ts` |
| Routes | `server/src/api/routes/department.ts` |
| Redux Slice | `client/redux/slices/departmentSlice.ts` |
| Component | `client/components/DepartmentManagement/index.tsx` |
| Page | `client/pages/departments.tsx` |
| Header Nav | `client/components/Header/index.tsx` |

## Environment Variables

Located in `server/.env`:
```env
PORT=5000
MONGO_URI=mongodb://127.0.0.1:27017/faqdb
API_PROTOCOL=http
API_HOST=10.200.237.188
API_PORT=5000
```

Change `API_HOST` to your network IP for remote access.

## Features Available

✅ Create departments with name and description
✅ View all departments in sortable table
✅ Edit department information
✅ Delete departments with confirmation
✅ Form validation
✅ Error messages
✅ Loading states
✅ Responsive design
✅ Mobile navigation menu
✅ Authentication protection
