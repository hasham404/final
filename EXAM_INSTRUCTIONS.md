# Flask CRUD Application - Exam Instructions

## Overview
You are provided with a skeleton Flask application that implements a User Management System with CRUD (Create, Read, Update, Delete) operations. Your task is to complete the TODO sections in the code to make the application fully functional.

## Application Structure
```
project/
├── skeleton_app.py          # Main Flask application (COMPLETE THE TODOs)
├── requirements.txt         # Python dependencies
├── templates/              # HTML templates
│   ├── base.html          # Base template with navigation
│   ├── index.html         # Home page - displays all users
│   ├── add.html           # Add new user form
│   ├── update.html        # Update user form
│   ├── view.html          # View user details
│   ├── search.html        # Search users
│   ├── 404.html           # Error page
│   └── 500.html           # Server error page
└── EXAM_INSTRUCTIONS.md   # This file
```

## Database Model
The application uses a `User` model with the following fields:
- `id`: Primary key (auto-increment)
- `first_name`: User's first name (required, 2-50 characters)
- `last_name`: User's last name (required, 2-50 characters)
- `email`: User's email (required, unique, max 120 characters)
- `age`: User's age (required, 1-120)
- `city`: User's city (required, 2-50 characters)
- `created_at`: Timestamp when user was created

## Tasks to Complete

### 1. Setup and Installation
```bash
# Make environment
pythom -m venv venv

# Activate Environment
.\venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run the application
python skeleton_app.py
```

### 2. Complete the Following Functions in `skeleton_app.py`:

#### A. `index()` function (Line ~45)
- **Task**: Query all users from the database and pass them to the template
- **Hint**: Use `User.query.all()`
- **Expected**: Display all users in a table on the home page

#### B. `add_user()` function (Line ~55)
- **Task**: Handle both GET and POST requests for adding new users
- **Requirements**:
  - GET: Render the add user form
  - POST: Process form data, validate input, create new user, save to database
  - Handle validation errors (duplicate email, missing fields, invalid age)
  - Show success/error messages using Flask's flash system
  - Redirect to home page after successful creation

#### C. `view_user(user_id)` function (Line ~85)
- **Task**: Display individual user details
- **Requirements**:
  - Get user by ID from database
  - Handle case when user doesn't exist (404 error)
  - Pass user data to template

#### D. `update_user(user_id)` function (Line ~95)
- **Task**: Handle both GET and POST requests for updating users
- **Requirements**:
  - GET: Display form with current user data pre-filled
  - POST: Process form data, validate input, update user, save to database
  - Handle validation errors
  - Show success/error messages
  - Redirect to home page after successful update

#### E. `delete_user(user_id)` function (Line ~125)
- **Task**: Delete a user from the database
- **Requirements**:
  - Get user by ID from database
  - Delete user from database
  - Show success message
  - Redirect to home page

#### F. `search_users()` function (Line ~135)
- **Task**: Search users by name or city
- **Requirements**:
  - Get search query from URL parameters
  - Search users by first_name, last_name, or city (case-insensitive)
  - Pass results to template

### 3. Key Implementation Points

#### Database Operations
```python
# Query all users
users = User.query.all()

# Get user by ID
user = User.query.get_or_404(user_id)

# Create new user
new_user = User(first_name=first_name, last_name=last_name, email=email, age=age, city=city)
db.session.add(new_user)
db.session.commit()

# Update user
user.first_name = new_first_name
db.session.commit()

# Delete user
db.session.delete(user)
db.session.commit()

# Search users
users = User.query.filter(
    db.or_(
        User.first_name.contains(query),
        User.last_name.contains(query),
        User.city.contains(query)
    )
).all()
```

#### Form Handling
```python
# Get form data
first_name = request.form.get('first_name')
email = request.form.get('email')

# Validate data
if not first_name:
    flash('First name is required!', 'error')
    return render_template('add.html')

# Check for duplicate email
existing_user = User.query.filter_by(email=email).first()
if existing_user:
    flash('Email already exists!', 'error')
    return render_template('add.html')
```

#### Flash Messages
```python
# Success message
flash('User added successfully!', 'success')

# Error message
flash('User not found!', 'error')
```

#### Redirects
```python
# Redirect to home page
return redirect(url_for('index'))

# Redirect to specific user
return redirect(url_for('view_user', user_id=user.id))
```

### 4. Testing Your Implementation

1. **Start the application**: `python skeleton_app.py`
2. **Open browser**: Navigate to `http://localhost:5000`
3. **Test each feature**:
   - Add a new user
   - View user details
   - Update user information
   - Delete a user
   - Search for users
   - Test error handling (try to add duplicate email)

### 5. Expected Behavior

- **Home Page**: Shows all users in a table with action buttons
- **Add User**: Form to create new users with validation
- **View User**: Detailed view of individual user
- **Update User**: Form to edit existing user information
- **Delete User**: Confirmation and removal of user
- **Search**: Find users by name or city
- **Error Handling**: Proper error messages and 404/500 pages

### 6. Grading Criteria

- **Functionality (60%)**: All CRUD operations work correctly
- **Error Handling (20%)**: Proper validation and error messages
- **Code Quality (10%)**: Clean, readable code following Flask best practices
- **User Experience (10%)**: Intuitive navigation and user feedback

### 7. Common Issues and Solutions

#### Issue: Database not created
**Solution**: The `create_tables()` function is called in the main block. Make sure you're running the application correctly.

#### Issue: Form data not being processed
**Solution**: Check that you're using `request.form.get()` and handling both GET and POST methods.

#### Issue: Flash messages not showing
**Solution**: Make sure you're using `get_flashed_messages()` in your templates and calling `flash()` in your routes.

#### Issue: Validation errors
**Solution**: Implement proper validation for all form fields and handle edge cases.

### 8. Time Management
- **Setup**: 5 minutes
- **Basic CRUD**: 30 minutes
- **Search functionality**: 10 minutes
- **Error handling**: 10 minutes
- **Testing**: 5 minutes

**Total Time**: 60 minutes

## Good Luck! 🍀

Remember to:
- Read the TODO comments carefully
- Test each function as you complete it
- Handle edge cases and errors
- Follow Flask best practices
- Keep your code clean and readable
