# Sequelize ORM Complete Setup Guide for Node.js

A comprehensive guide to setting up and using Sequelize ORM with MySQL and PostgreSQL databases in Node.js applications.

---

## 📖 Table of Contents

1. [What is Sequelize?](#what-is-sequelize)
2. [Installation & Setup](#installation--setup)
3. [Project Structure](#project-structure)
4. [Database Configuration](#database-configuration)
5. [Package.json Scripts](#packagejson-scripts)
6. [Database Operations](#database-operations)
7. [Model Management](#model-management)
8. [Migration Workflows](#migration-workflows)
9. [Seeding Data](#seeding-data)
10. [Laravel vs Sequelize Commands](#laravel-vs-sequelize-commands)
11. [Best Practices](#best-practices)
12. [Troubleshooting](#troubleshooting)

---

## 🤔 What is Sequelize?

**Sequelize** is a powerful Object-Relational Mapper (ORM) for Node.js that supports multiple SQL dialects including PostgreSQL, MySQL, MariaDB, SQLite, and Microsoft SQL Server. It provides:

- **Promise-based API** for modern async/await syntax
- **Model definitions** with validations and associations
- **Migration system** for database schema versioning
- **Seeding capabilities** for populating test data
- **Query optimization** and connection pooling
- **Transaction support** for data integrity

### Key Benefits
- Write JavaScript instead of raw SQL
- Database-agnostic code (switch between databases easily)
- Built-in validations and sanitization
- Automatic SQL injection protection
- Rich ecosystem and community support

**Official Documentation:** [https://sequelize.org/](https://sequelize.org/)

---

## 🛠️ Installation & Setup

### Step 1: Install Core Dependencies

**For MySQL:**
```bash
npm install sequelize sequelize-cli mysql2
```

**For PostgreSQL:**
```bash
npm install sequelize sequelize-cli pg pg-hstore
```

**For both (if switching between databases):**
```bash
npm install sequelize sequelize-cli mysql2 pg pg-hstore
```

### Step 2: Install Additional Utilities

```bash
# Environment variables support
npm install dotenv

# Optional: Cross-platform environment variables
npm install cross-env
```

### Step 3: Initialize Sequelize

```bash
npx sequelize-cli init
```

This command creates the essential folder structure for your project.

---

## 📂 Project Structure

After initialization, your project will have:

```
your-project/
├── config/
│   └── config.json          # Database configuration
├── models/
│   └── index.js             # Model loader and associations
├── migrations/              # Database schema changes
├── seeders/                 # Sample/test data files
├── package.json
└── .env                     # Environment variables (create this)
```

### Folder Descriptions

| Folder/File | Purpose | When to Use |
|-------------|---------|-------------|
| `config/` | Database connection settings | Configure DB credentials and options |
| `models/` | Table definitions and relationships | Define your data structure |
| `migrations/` | Schema versioning and changes | Create/modify tables and columns |
| `seeders/` | Sample data population | Add demo or test data |

---

## ⚙️ Database Configuration

### Method 1: Using config.json (Simple)

Edit `config/config.json`:

**For MySQL:**
```json
{
  "development": {
    "username": "root",
    "password": "your_password",
    "database": "your_app_dev",
    "host": "127.0.0.1",
    "dialect": "mysql",
    "port": 3306
  },
  "test": {
    "username": "root",
    "password": "your_password",
    "database": "your_app_test",
    "host": "127.0.0.1",
    "dialect": "mysql",
    "port": 3306
  },
  "production": {
    "username": "root",
    "password": "your_password",
    "database": "your_app_prod",
    "host": "127.0.0.1",
    "dialect": "mysql",
    "port": 3306
  }
}
```

**For PostgreSQL:**
```json
{
  "development": {
    "username": "postgres",
    "password": "your_password",
    "database": "your_app_dev",
    "host": "127.0.0.1",
    "dialect": "postgres",
    "port": 5432
  },
  "test": {
    "username": "postgres",
    "password": "your_password",
    "database": "your_app_test",
    "host": "127.0.0.1",
    "dialect": "postgres",
    "port": 5432
  },
  "production": {
    "username": "postgres",
    "password": "your_password",
    "database": "your_app_prod",
    "host": "127.0.0.1",
    "dialect": "postgres",
    "port": 5432
  }
}
```

### Method 2: Using Environment Variables (Recommended)

1. **Rename** `config/config.json` to `config/config.js`

2. **Replace content** with:

```javascript
require('dotenv').config();

module.exports = {
  development: {
    username: process.env.DB_USER || 'root',
    password: process.env.DB_PASSWORD || '',
    database: process.env.DB_NAME || 'app_dev',
    host: process.env.DB_HOST || '127.0.0.1',
    port: process.env.DB_PORT || 3306,
    dialect: process.env.DB_DIALECT || 'mysql',
    logging: console.log, // Set to false in production
  },
  test: {
    username: process.env.DB_USER || 'root',
    password: process.env.DB_PASSWORD || '',
    database: process.env.DB_NAME_TEST || 'app_test',
    host: process.env.DB_HOST || '127.0.0.1',
    port: process.env.DB_PORT || 3306,
    dialect: process.env.DB_DIALECT || 'mysql',
    logging: false,
  },
  production: {
    username: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME,
    host: process.env.DB_HOST,
    port: process.env.DB_PORT,
    dialect: process.env.DB_DIALECT,
    logging: false,
    pool: {
      max: 10,
      min: 0,
      acquire: 30000,
      idle: 10000
    }
  }
};
```

3. **Create `.env` file** in project root:

```env
# Database Configuration
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_password
DB_NAME=your_app_name
DB_PORT=3306
DB_DIALECT=mysql

# For PostgreSQL, use:
# DB_PORT=5432
# DB_DIALECT=postgres

# Application
NODE_ENV=development
```

4. **Add to `.gitignore`:**

```gitignore
.env
node_modules/
```

---

## 📦 Package.json Scripts

Add these custom scripts to your `package.json` for easier database management:

```json
{
  "scripts": {
    "dev": "node server.js",
    "migration:create": "sequelize-cli migration:generate --name",
    "migration:run": "sequelize-cli db:migrate",
    "migration:status": "sequelize-cli db:migrate:status",
    "migration:undo": "sequelize-cli db:migrate:undo",
    "migration:undo:all": "sequelize-cli db:migrate:undo:all",
    "seeder:create": "sequelize-cli seed:generate --name",
    "seeder:run": "sequelize-cli db:seed:all",
    "seeder:undo": "sequelize-cli db:seed:undo:all",
    "db:fresh": "npm run migration:undo:all && npm run migration:run && npm run seeder:run",
    "db:reset": "npm run migration:undo:all && npm run migration:run",
    "db:setup": "npm run migration:run && npm run seeder:run"
  }
}
```

### Alternative for pnpm users:

Replace `npm run` with `pnpm run` in the compound commands:

```json
{
  "scripts": {
    "db:fresh": "pnpm run migration:undo:all && pnpm run migration:run && pnpm run seeder:run",
    "db:reset": "pnpm run migration:undo:all && pnpm run migration:run",
    "db:setup": "pnpm run migration:run && pnpm run seeder:run"
  }
}
```

---

## 🚀 Database Operations

### Creating Migrations

**Create a new migration:**
```bash
npm run migration:create create-users-table
```

**Example migration file** (`migrations/YYYYMMDDHHMMSS-create-users-table.js`):

```javascript
'use strict';

module.exports = {
  async up(queryInterface, Sequelize) {
    await queryInterface.createTable('Users', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      firstName: {
        type: Sequelize.STRING,
        allowNull: false
      },
      lastName: {
        type: Sequelize.STRING,
        allowNull: false
      },
      email: {
        type: Sequelize.STRING,
        allowNull: false,
        unique: true
      },
      password: {
        type: Sequelize.STRING,
        allowNull: false
      },
      isActive: {
        type: Sequelize.BOOLEAN,
        defaultValue: true
      },
      createdAt: {
        allowNull: false,
        type: Sequelize.DATE
      },
      updatedAt: {
        allowNull: false,
        type: Sequelize.DATE
      }
    });
  },

  async down(queryInterface, Sequelize) {
    await queryInterface.dropTable('Users');
  }
};
```

### Running Migrations

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `npm run migration:run` | Apply all pending migrations | Deploy new schema changes |
| `npm run migration:status` | Check which migrations ran | Debug migration state |
| `npm run migration:undo` | Rollback last migration | Fix recent migration issues |
| `npm run migration:undo:all` | Rollback all migrations | Complete database reset |

### Migration Examples

**Add a column:**
```bash
npm run migration:create add-phone-to-users
```

```javascript
module.exports = {
  async up(queryInterface, Sequelize) {
    await queryInterface.addColumn('Users', 'phone', {
      type: Sequelize.STRING,
      allowNull: true
    });
  },

  async down(queryInterface, Sequelize) {
    await queryInterface.removeColumn('Users', 'phone');
  }
};
```

**Add an index:**
```javascript
module.exports = {
  async up(queryInterface, Sequelize) {
    await queryInterface.addIndex('Users', ['email'], {
      unique: true,
      name: 'users_email_unique'
    });
  },

  async down(queryInterface, Sequelize) {
    await queryInterface.removeIndex('Users', 'users_email_unique');
  }
};
```

---

## 🏗️ Model Management

### Creating Models

**Generate model with migration:**
```bash
npx sequelize-cli model:generate --name User --attributes firstName:string,lastName:string,email:string
```

**Example model** (`models/user.js`):

```javascript
'use strict';
const { Model } = require('sequelize');

module.exports = (sequelize, DataTypes) => {
  class User extends Model {
    static associate(models) {
      // Define associations here
      User.hasMany(models.Post, {
        foreignKey: 'userId',
        as: 'posts'
      });
    }

    // Instance methods
    getFullName() {
      return `${this.firstName} ${this.lastName}`;
    }

    // Class methods
    static async findByEmail(email) {
      return await this.findOne({ where: { email } });
    }
  }

  User.init({
    firstName: {
      type: DataTypes.STRING,
      allowNull: false,
      validate: {
        notEmpty: true,
        len: [2, 50]
      }
    },
    lastName: {
      type: DataTypes.STRING,
      allowNull: false,
      validate: {
        notEmpty: true,
        len: [2, 50]
      }
    },
    email: {
      type: DataTypes.STRING,
      allowNull: false,
      unique: true,
      validate: {
        isEmail: true
      }
    },
    password: {
      type: DataTypes.STRING,
      allowNull: false,
      validate: {
        len: [6, 100]
      }
    },
    isActive: {
      type: DataTypes.BOOLEAN,
      defaultValue: true
    }
  }, {
    sequelize,
    modelName: 'User',
    tableName: 'Users',
    timestamps: true,
    underscored: false
  });

  return User;
};
```

### Model Relationships

**One-to-Many (User has many Posts):**
```javascript
// In User model
static associate(models) {
  User.hasMany(models.Post, {
    foreignKey: 'userId',
    as: 'posts'
  });
}

// In Post model
static associate(models) {
  Post.belongsTo(models.User, {
    foreignKey: 'userId',
    as: 'author'
  });
}
```

**Many-to-Many (User belongs to many Roles):**
```javascript
// In User model
static associate(models) {
  User.belongsToMany(models.Role, {
    through: 'UserRoles',
    foreignKey: 'userId',
    otherKey: 'roleId'
  });
}

// In Role model
static associate(models) {
  Role.belongsToMany(models.User, {
    through: 'UserRoles',
    foreignKey: 'roleId',
    otherKey: 'userId'
  });
}
```

---

## 🌱 Seeding Data

### Creating Seeders

**Generate a seeder:**
```bash
npm run seeder:create demo-users
```

**Example seeder** (`seeders/YYYYMMDDHHMMSS-demo-users.js`):

```javascript
'use strict';
const bcrypt = require('bcrypt');

module.exports = {
  async up(queryInterface, Sequelize) {
    const hashedPassword = await bcrypt.hash('password123', 10);
    
    await queryInterface.bulkInsert('Users', [
      {
        firstName: 'John',
        lastName: 'Doe',
        email: 'john@example.com',
        password: hashedPassword,
        isActive: true,
        createdAt: new Date(),
        updatedAt: new Date()
      },
      {
        firstName: 'Jane',
        lastName: 'Smith',
        email: 'jane@example.com',
        password: hashedPassword,
        isActive: true,
        createdAt: new Date(),
        updatedAt: new Date()
      },
      {
        firstName: 'Admin',
        lastName: 'User',
        email: 'admin@example.com',
        password: hashedPassword,
        isActive: true,
        createdAt: new Date(),
        updatedAt: new Date()
      }
    ], {});
  },

  async down(queryInterface, Sequelize) {
    await queryInterface.bulkDelete('Users', null, {});
  }
};
```

### Running Seeders

| Command | Purpose |
|---------|---------|
| `npm run seeder:run` | Execute all seed files |
| `npm run seeder:undo` | Remove all seeded data |

---

## 🔄 Laravel vs Sequelize Commands

Laravel developers will find these command mappings helpful:

| Laravel Artisan | Sequelize CLI | NPM Script |
|----------------|---------------|------------|
| `php artisan migrate` | `sequelize-cli db:migrate` | `npm run migration:run` |
| `php artisan migrate:status` | `sequelize-cli db:migrate:status` | `npm run migration:status` |
| `php artisan migrate:rollback` | `sequelize-cli db:migrate:undo` | `npm run migration:undo` |
| `php artisan migrate:reset` | `sequelize-cli db:migrate:undo:all` | `npm run migration:undo:all` |
| `php artisan migrate:fresh` | undo:all + migrate | `npm run db:reset` |
| `php artisan migrate:fresh --seed` | undo:all + migrate + seed | `npm run db:fresh` |
| `php artisan db:seed` | `sequelize-cli db:seed:all` | `npm run seeder:run` |
| `php artisan make:migration` | `sequelize-cli migration:generate` | `npm run migration:create` |
| `php artisan make:seeder` | `sequelize-cli seed:generate` | `npm run seeder:create` |

---

## ✅ Best Practices

### 1. Migration Guidelines
- **One change per migration** - Keep migrations atomic and focused
- **Always provide rollback logic** - Implement both `up` and `down` methods
- **Use descriptive names** - `add-user-email-index` vs `migration1`
- **Test rollbacks** - Ensure your `down` method actually works

### 2. Model Organization
- **Keep models focused** - One model per table, clear responsibilities
- **Use validations** - Validate data at the model level
- **Define associations** - Clearly establish relationships between models
- **Add helpful methods** - Include common queries as class/instance methods

### 3. Environment Management
- **Use environment variables** - Never commit sensitive data
- **Separate configurations** - Different settings for dev/test/prod
- **Enable logging in development** - Disable in production for performance

### 4. Performance Optimization
- **Add database indexes** - For frequently queried columns
- **Use connection pooling** - Configure pool settings for production
- **Implement eager loading** - Prevent N+1 query problems
- **Monitor query performance** - Use logging to identify slow queries

### 5. Security Considerations
- **Validate input data** - Use Sequelize's built-in validators
- **Hash passwords** - Never store plain text passwords
- **Sanitize queries** - Sequelize prevents SQL injection by default
- **Use transactions** - For operations that must succeed/fail together

---

## 🔧 Troubleshooting

### Common Issues and Solutions

**1. "Sequelize CLI not found"**
```bash
# Install globally or use npx
npm install -g sequelize-cli
# OR
npx sequelize-cli --version
```

**2. "Database connection refused"**
- Check if database server is running
- Verify credentials in config file
- Ensure database exists
- Check firewall settings

**3. "Migration already exists"**
```bash
# Check migration status
npm run migration:status

# If stuck, manually update SequelizeMeta table
# Or delete problematic migration file and recreate
```

**4. "Module not found: pg/mysql2"**
```bash
# Install the correct database driver
npm install pg pg-hstore    # For PostgreSQL
npm install mysql2          # For MySQL
```

**5. "Models not loading associations"**
- Ensure all models are properly exported
- Check the `associate` method syntax
- Verify foreign key names match

**6. "Seeder data conflicts"**
```bash
# Clear existing data first
npm run seeder:undo
npm run seeder:run
```

### Development Workflow

**Starting a new feature:**
1. Create migration for schema changes
2. Run migration in development
3. Create/update models
4. Create seeder for test data
5. Test functionality
6. Commit migration and model files

**Deployment checklist:**
1. Backup production database
2. Test migrations on staging
3. Run migrations on production
4. Monitor for issues
5. Have rollback plan ready

---

## 🎯 Quick Start Commands

```bash
# 1. Initialize new project
npx sequelize-cli init

# 2. Create your first model and migration
npx sequelize-cli model:generate --name User --attributes name:string,email:string

# 3. Run the migration
npm run migration:run

# 4. Create a seeder
npm run seeder:create demo-users

# 5. Run the seeder
npm run seeder:run

# 6. Check everything is working
npm run migration:status
```

---

This comprehensive guide should get you up and running with Sequelize ORM in your Node.js applications. Remember to adapt the configurations to match your specific database choice and project requirements.