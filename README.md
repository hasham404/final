# Flask CRUD Application - Exam Skeleton

## Quick Start

1. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

2. **Run the application**:
   ```bash
   python skeleton_app.py
   ```

3. **Open your browser** and navigate to:
   ```
   http://localhost:5000
   ```

## What's Included

- ✅ **Complete HTML templates** with Bootstrap styling
- ✅ **Database model** (User) with all required fields
- ✅ **Route structure** with proper URL patterns
- ✅ **Error handling** (404, 500 pages)
- ✅ **Form validation** (client-side and server-side)
- ✅ **Search functionality** template
- ✅ **Responsive design** that works on all devices

## What Students Need to Complete

Students need to implement the TODO sections in `skeleton_app.py`:

1. **index()** - Display all users
2. **add_user()** - Create new users
3. **view_user()** - Show user details
4. **update_user()** - Edit existing users
5. **delete_user()** - Remove users
6. **search_users()** - Search functionality

## Features

- **Modern UI**: Bootstrap 5 with responsive design
- **Form Validation**: Both client-side and server-side
- **Error Handling**: Proper 404/500 error pages
- **Flash Messages**: Success and error notifications
- **Search**: Find users by name or city
- **CRUD Operations**: Complete Create, Read, Update, Delete functionality

## File Structure

```
├── skeleton_app.py          # Main application (students complete TODOs)
├── requirements.txt         # Python dependencies
├── templates/              # HTML templates (complete)
│   ├── base.html          # Base template with navigation
│   ├── index.html         # Home page
│   ├── add.html           # Add user form
│   ├── update.html        # Update user form
│   ├── view.html          # View user details
│   ├── search.html        # Search results
│   ├── 404.html           # Not found page
│   └── 500.html           # Server error page
├── EXAM_INSTRUCTIONS.md   # Detailed exam instructions
└── README.md             # This file
```

## Database Schema

The `User` model includes:
- `id`: Primary key
- `first_name`: User's first name
- `last_name`: User's last name
- `email`: Unique email address
- `age`: User's age
- `city`: User's city
- `created_at`: Creation timestamp

## Exam Duration

**60 minutes** - Perfect for a one-hour exam session.

## Support

All templates are complete and functional. Students only need to implement the Python logic in the Flask routes.
