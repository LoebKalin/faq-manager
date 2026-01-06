# Department Management System Setup Guide

## Overview
A complete department management system has been added to the FAQ Manager application. Users can now create, read, update, and delete departments through an intuitive web interface.

## What Was Created

### Backend (Server)

#### 1. Department Model (`server/src/api/models/department.ts`)
- **Database Schema**: Mongoose model with fields:
  - `name` (String, required, unique): Department name
  - `description` (String, optional): Department description
  - `createdAt` (Date, auto): Timestamp when created
  - `updatedAt` (Date, auto): Timestamp when last updated

#### 2. Department Controller (`server/src/api/controllers/department.ts`)
- **API Endpoints**:
  - `GET /departments` - Fetch all departments
  - `GET /departments/:id` - Fetch single department by ID
  - `POST /departments` - Create new department
  - `PUT /departments/:id` - Update department
  - `DELETE /departments/:id` - Delete department

#### 3. Department Routes (`server/src/api/routes/department.ts`)
- Express router with all CRUD operations
- Registered in `app.ts` at `/departments` endpoint

### Frontend (Client)

#### 1. Redux Slice (`client/redux/slices/departmentSlice.ts`)
- **State Management**: Redux Toolkit slice with async thunks
- **Actions**:
  - `fetchDepartments`: Load all departments
  - `createDepartment`: Create new department
  - `updateDepartment`: Update existing department
  - `deleteDepartment`: Delete department

#### 2. Department Management Component (`client/components/DepartmentManagement/index.tsx`)
- **Features**:
  - Display all departments in a table
  - Add new department via dialog
  - Edit existing department
  - Delete department with confirmation
  - Error handling and loading states

#### 3. Header Component (`client/components/Header/index.tsx`)
- **Navigation**:
  - Links to FAQs and Departments
  - Responsive design (desktop and mobile)
  - Integrated Logout button
  - Active route highlighting

#### 4. Departments Page (`client/pages/departments.tsx`)
- Route: `/departments`
- Protected by authentication (redirects to login if no token)
- Displays DepartmentManagement component

### Updated Components

#### 1. Redux Store (`client/redux/store.ts`)
- Added department reducer to store configuration

#### 2. FAQ Page (`client/pages/faq/index.tsx`)
- Replaced custom header with new Header component
- Cleaner layout with navigation

## How to Use

### Access Department Management
1. Log in to the admin dashboard at `http://localhost:3000/faq` (or your server IP)
2. Click "Departments" in the navigation menu
3. You'll see the Department Management page

### Add a Department
1. Click the "Add Department" button
2. Enter department name (required)
3. Enter optional description
4. Click "Create"

### Edit a Department
1. Find the department in the table
2. Click the Edit (pencil) icon
3. Modify the name or description
4. Click "Update"

### Delete a Department
1. Find the department in the table
2. Click the Delete (trash) icon
3. Confirm the deletion in the popup
4. Department is removed

## Database Setup

No additional setup needed! The MongoDB collection is created automatically when you:
1. Start the server with `npm run dev`
2. Make the first API call to `/departments`

The collection `departments` will be created in your configured MongoDB database.

## API Examples

### Create Department
```bash
curl -X POST http://localhost:5000/departments \
  -H "Content-Type: application/json" \
  -d '{"name":"Human Resources","description":"HR Department"}'
```

### Get All Departments
```bash
curl http://localhost:5000/departments
```

### Update Department
```bash
curl -X PUT http://localhost:5000/departments/{id} \
  -H "Content-Type: application/json" \
  -d '{"name":"IT Support","description":"Information Technology"}'
```

### Delete Department
```bash
curl -X DELETE http://localhost:5000/departments/{id}
```

## Files Modified/Created

### New Files
- `server/src/api/models/department.ts`
- `server/src/api/controllers/department.ts`
- `server/src/api/routes/department.ts`
- `client/redux/slices/departmentSlice.ts`
- `client/components/DepartmentManagement/index.tsx`
- `client/components/Header/index.tsx`
- `client/pages/departments.tsx`

### Modified Files
- `server/src/app.ts` - Added department routes
- `client/redux/store.ts` - Added department reducer
- `client/pages/faq/index.tsx` - Updated to use Header component

## Features

✅ Full CRUD operations for departments
✅ MongoDB persistence
✅ Redux state management
✅ Responsive UI with MUI components
✅ Error handling and validation
✅ Authentication protection (requires login)
✅ Edit and delete confirmation dialogs
✅ Loading states
✅ Clean, intuitive interface
✅ Mobile-responsive navigation

## Testing

1. Restart both servers:
   ```bash
   # Terminal 1 (Server)
   cd server && npm run dev
   
   # Terminal 2 (Client)
   cd client && npm run dev
   ```

2. Navigate to `http://localhost:3000/faq` and log in

3. Click "Departments" to access the management page

4. Test CRUD operations:
   - Add a new department
   - Edit the department
   - Delete the department
   - Verify changes persist after page reload

## Next Steps (Optional Enhancements)

- Link departments to FAQs (add department field to FAQ model)
- Add department filtering to FAQ list
- Add department statistics dashboard
- Bulk operations (bulk delete, export)
- Department administrators assignment
- Activity logs for department changes
