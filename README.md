# RoboReserve-Flow
# 🤖 Robothon Reserve Scan System

A comprehensive inventory management and reservation system designed for robotics competitions, featuring barcode scanning, team-based reservations, and admin-controlled SKU assignments.

## 📋 Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Installation](#installation)
- [Usage](#usage)
- [System Architecture](#system-architecture)
- [User Roles](#user-roles)
- [Workflow](#workflow)
- [Screenshots](#screenshots)
- [API Documentation](#api-documentation)
- [Contributing](#contributing)
- [License](#license)

---

## ✨ Features

### 🔐 **Admin Panel**
- **Category Management**: Create, edit, and organize part categories with custom codes
- **Parts Inventory**: Add parts with auto-generated unique barcodes
- **Barcode Generation**: Automatic SKU creation in format `CATCODE-PARTCODE-XXXXX`
- **Reservation Management**: Review, assign SKUs, and track all team reservations
- **Team Analytics**: View reservations grouped by team
- **User Management**: Create team accounts with password protection
- **CSV Import/Export**: Bulk operations for categories and reservation data
- **Barcode Scanner Integration**: Physical scanner support for quick SKU assignment

### 👥 **Team/User Panel**
- **Browse Categories**: Organized part catalog with availability display
- **Real-Time Availability**: See instant updates as items are reserved
- **Shopping Cart**: Batch multiple reservations before checkout
- **Reservation History**: Track all requests with assigned barcodes
- **Checkout Limits**: Category-based reservation restrictions
- **Barcode Search**: Find parts by scanning their barcodes
- **Mobile Responsive**: Works on tablets and smartphones

### 🎯 **Smart Features**
- **Dynamic Availability**: Counts decrease immediately when reserved
- **Validation System**: Prevents over-reservation and conflicts
- **Status Tracking**: Complete lifecycle from pending → assigned → issued → returned
- **Admin Remarks**: Communication channel between admin and teams
- **Audit Trail**: Timestamps and user tracking for all actions

---

## 🛠️ Tech Stack

### **Backend**
- **Python 3.8+**
- **Flask 2.x** - Web framework
- **SQLite3** - Database
- **Werkzeug** - Password hashing and security

### **Frontend**
- **HTML5/CSS3** - Modern responsive design
- **JavaScript (Vanilla)** - Dynamic interactions
- **Jinja2** - Template engine

### **Additional Tools**
- **CSV Module** - Import/export functionality
- **Regex** - Barcode generation and validation
- **datetime** - Timestamp management

---

## 📦 Installation

### **Prerequisites**
- Python 3.8 or higher
- pip (Python package manager)
- Web browser (Chrome, Firefox, Edge, Safari)

### **Step 1: Clone/Download Project**
```bash
git clone <repository-url>
cd "working code (kind off)"
```

### **Step 2: Install Dependencies**
```bash
pip install flask werkzeug
```

### **Step 3: Initialize Database**
The database will be created automatically on first run.

### **Step 4: Run Application**
```bash
python app.py
```

### **Step 5: Access Application**
- **Local**: http://127.0.0.1:5000
- **Network**: http://<your-ip>:5000

---

## 🚀 Usage

### **First Time Setup**

#### **1. Admin Login**
```
URL: http://127.0.0.1:5000/admin/login
Default Password: admin2025
```

#### **2. Create Categories**
```
Example Category:
- Name: Wheels
- Code: WHEL (4 characters)
- Description: Various wheel types
- Checkout Limit: 2
```

#### **3. Add Parts**
```
Example Part:
- Name: Omni Wheel
- Category: Wheels
- Description: 4-inch omni-directional wheel
- Quantity: 5 (auto-generates 5 unique barcodes)
```

Barcodes generated:
```
WHEL-OMNIXX-00001
WHEL-OMNIXX-00002
WHEL-OMNIXX-00003
WHEL-OMNIXX-00004
WHEL-OMNIXX-00005
```

#### **4. Create Team Users**
```
Example User:
- Username: team_alpha
- Password: alpha123
- Team Name: TeamAlpha
- Role: team
```

### **Team Workflow**

#### **Step 1: Login**
```
URL: http://127.0.0.1:5000/
Username: team_alpha
Password: alpha123
```

#### **Step 2: Browse & Reserve**
1. Click on a category (e.g., "Wheels")
2. View available parts
3. Click "Reserve" or "Add to Cart"
4. Add optional notes
5. Checkout (if using cart)

#### **Step 3: Check History**
- View all reservations
- See assigned barcodes
- Track status updates

### **Admin Workflow**

#### **Step 1: View Pending Reservations**
1. Login to admin panel
2. Click on team name to see their reservations
3. Identify pending reservations (highlighted in yellow)

#### **Step 2: Assign SKU**
1. Click "📷 Assign SKU" button
2. Either:
   - **Scan barcode** with physical scanner
   - **Select manually** from dropdown
3. Click "Confirm"
4. Status changes to "assigned"

#### **Step 3: Issue Item**
1. Click "Update" on assigned reservation
2. Change status to "issued"
3. Add admin remarks (optional)
4. Hand physical item to team

#### **Step 4: Process Returns**
1. Update status to "returned"
2. Item becomes available again

---

## 🏗️ System Architecture

### **Database Schema**

```
┌─────────────────┐
│   categories    │
├─────────────────┤
│ id              │
│ name            │
│ code            │
│ description     │
│ checkout_limit  │
└─────────────────┘
         │
         │ 1:N
         ▼
┌─────────────────┐
│     parts       │
├─────────────────┤
│ id              │
│ category_id     │──┐
│ name            │  │
│ description     │  │
│ total_quantity  │  │
└─────────────────┘  │
         │           │
         │ 1:N       │
         ▼           │
┌─────────────────┐  │
│   part_items    │  │
├─────────────────┤  │
│ id              │  │
│ part_id         │  │
│ barcode         │  │
│ status          │  │
└─────────────────┘  │
                     │
┌─────────────────┐  │
│     users       │  │
├─────────────────┤  │
│ id              │  │
│ username        │  │
│ password        │  │
│ team_name       │  │
│ role            │  │
└─────────────────┘  │
         │           │
         │ 1:N       │
         ▼           │
┌─────────────────┐  │
│  reservations   │  │
├─────────────────┤  │
│ id              │  │
│ user_id         │──┘
│ part_item_id    │
│ part_id         │
│ category_id     │
│ status          │
│ notes           │
│ admin_remarks   │
│ created_at      │
└─────────────────┘
```

### **Reservation States**

```
┌─────────┐    ┌──────────┐    ┌────────┐    ┌──────────┐
│ pending │───▶│ assigned │───▶│ issued │───▶│ returned │
└─────────┘    └──────────┘    └────────┘    └──────────┘
     │                                             │
     │                                             │
     └──────────────┐                    ┌─────────┘
                    ▼                    ▼
              ┌───────────┐        ┌──────────┐
              │ cancelled │        │ available│
              └───────────┘        └──────────┘
```

---

## 👥 User Roles

### **Admin**
- **Access**: Full system control
- **Password**: `admin2025` (change in production!)
- **Capabilities**:
  - Manage categories, parts, and users
  - Assign SKUs to reservations
  - Update reservation status
  - Export data
  - View all team activities

### **Team Member**
- **Access**: Limited to own team's data
- **Authentication**: Username/password
- **Capabilities**:
  - Browse catalog
  - Reserve items
  - View own history
  - Search by barcode

---

## 🔄 Workflow

### **Complete Reservation Flow**

```
USER SIDE                          ADMIN SIDE
─────────                          ──────────

1. Browse Categories
   │
2. Select Part
   │
3. Click "Reserve"
   │
4. Add Notes (optional)
   │
5. Confirm Reservation
   │
   ├─> Creates "pending" reservation
   │   (part_item_id = NULL)
   │
   └─> Available count decreases
                                   6. View Pending Reservations
                                      │
                                   7. Click "Assign SKU"
                                      │
                                   8. Scan/Select Barcode
                                      │
                                   9. Confirm Assignment
                                      │
                                      ├─> Status: "assigned"
                                      └─> Item: "reserved"
                                      
10. View History                   11. Update to "issued"
    │                                  │
    └─> See assigned barcode           └─> Hand item to team
    
                                   12. Later: Update to "returned"
                                      │
                                      └─> Item becomes "available"
```

---

## 📸 Screenshots

### Admin Dashboard
- View all categories, parts, and reservations
- Color-coded status indicators
- Team-based organization

### Team Reservations
- Pending items highlighted
- One-click SKU assignment
- Barcode scanner integration

### User Interface
- Clean, modern design
- Mobile-responsive layout
- Intuitive navigation

---

## 📚 API Documentation

### **Admin Routes**

| Route | Method | Description |
|-------|--------|-------------|
| `/admin/login` | GET, POST | Admin authentication |
| `/admin` | GET | Admin dashboard |
| `/admin/category/add` | POST | Create category |
| `/admin/category/edit/<id>` | POST | Update category |
| `/admin/category/delete/<id>` | GET | Remove category |
| `/admin/part/add` | POST | Create part with items |
| `/admin/part/add_quantity/<id>` | POST | Add more items |
| `/admin/reservation/assign_sku/<id>` | POST | Assign barcode to reservation |
| `/admin/reservation/update/<id>` | POST | Update reservation status |
| `/admin/user/add` | POST | Create team user |
| `/admin/export` | GET | Export CSV |

### **User Routes**

| Route | Method | Description |
|-------|--------|-------------|
| `/` | GET | Homepage |
| `/user/login` | POST | Team authentication |
| `/user/category/<id>` | GET | View category parts |
| `/user/cart` | GET | View shopping cart |
| `/user/history` | GET | View reservations |
| `/reserve/<id>` | POST | Reserve single item |
| `/cart/add/<id>` | POST | Add to cart |
| `/cart/checkout` | POST | Process cart |

---

## 🧪 Testing

### **Test Scenarios**

#### **Test 1: Basic Reservation**
```
1. Login as team user
2. Reserve a part
3. Verify available count decreases
4. Check history shows pending
5. Login as admin
6. Assign SKU
7. Verify user sees barcode
```

#### **Test 2: Checkout Limits**
```
1. Category limit = 2
2. Reserve 2 items
3. Try to reserve 3rd
4. Verify error message
```

#### **Test 3: Barcode Scanner**
```
1. Admin: Click "Assign SKU"
2. Focus scanner input
3. Scan physical barcode
4. Verify auto-submission
5. Check assignment success
```

---

## 🔧 Configuration

### **Environment Variables**
```python
# app.py - Line 11-13
app.secret_key = "your-secret-key-here"  # Change this!
ADMIN_PASSWORD = "your-admin-password"    # Change this!
DATABASE = "reserve_scan.db"              # Database file name
```

### **Customize Settings**
- **Port**: Change `app.run(port=5000)` to desired port
- **Host**: Modify `host="0.0.0.0"` for network access
- **Debug Mode**: Set `debug=False` for production

---

## 🛡️ Security Recommendations

### **For Production Use:**

1. **Change Default Passwords**
   ```python
   ADMIN_PASSWORD = "strong-password-here"
   ```

2. **Use Environment Variables**
   ```python
   import os
   app.secret_key = os.environ.get('SECRET_KEY')
   ADMIN_PASSWORD = os.environ.get('ADMIN_PASSWORD')
   ```

3. **Enable HTTPS**
   - Use a reverse proxy (nginx, Apache)
   - Obtain SSL certificate

4. **Upgrade Database**
   - Consider PostgreSQL for production
   - Implement proper backups

5. **Add Rate Limiting**
   - Prevent brute force attacks
   - Use Flask-Limiter extension

---

## 📝 Troubleshooting

### **Common Issues**

#### **Database Errors**
```bash
# Delete and recreate database
rm reserve_scan.db
python app.py
```

#### **Port Already in Use**
```bash
# Change port in app.py or kill process
netstat -ano | findstr :5000
taskkill /PID <process_id> /F
```

#### **Items Not Showing as Available**
```bash
# Run diagnostic script
python check_db.py

# Run cleanup script
python cleanup_db.py
```

---

## 📦 File Structure

```
working code (kind off)/
│
├── app.py                      # Main Flask application
├── reserve_scan.db            # SQLite database (auto-generated)
│
├── templates/
│   ├── admin.html             # Admin interface
│   └── user.html              # Team interface
│
├── check_db.py                # Database diagnostic tool
├── cleanup_db.py              # Database cleanup utility
│
├── README.md                  # This file
├── INSTALLATION.md            # Detailed setup guide
├── USER_GUIDE.md             # User manual
├── ADMIN_GUIDE.md            # Admin manual
├── FIX_SUMMARY.md            # Bug fix documentation
├── AVAILABILITY_UPDATE.md    # Feature documentation
├── WORKFLOW_DIAGRAM.txt      # Visual workflow guide
│
└── LICENSE                    # License file
```

---

## 🤝 Contributing

We welcome contributions! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

## 🙏 Acknowledgments

- Designed for Robothon robotics competitions
- Built with Flask and SQLite
- Inspired by modern inventory management systems

---

## 📞 Support

For issues, questions, or suggestions:

- **GitHub Issues**: [Create an issue](https://github.com/yourrepo/issues)
- **Email**: support@example.com
- **Documentation**: See USER_GUIDE.md and ADMIN_GUIDE.md

---

## 🚀 Future Enhancements

- [ ] Email notifications for status updates
- [ ] QR code generation and printing
- [ ] Advanced analytics dashboard
- [ ] Multi-language support
- [ ] API for external integrations
- [ ] Mobile app (iOS/Android)
- [ ] Real-time updates with WebSockets
- [ ] Photo uploads for parts
- [ ] Damage reporting system
- [ ] Reservation calendar view

---

## 📊 Project Stats

- **Language**: Python
- **Framework**: Flask
- **Database**: SQLite
- **Lines of Code**: ~870+
- **Templates**: 2 (admin.html, user.html)
- **Features**: 30+

---

**Made with ❤️ for Robothon Teams**

*Last Updated: October 20, 2025*
