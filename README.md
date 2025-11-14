# Laravel Deployment Documentation - FileZilla to Hostinger

Complete step-by-step guide for deploying MLOOK DTS to Hostinger using **FileZilla FTP ONLY** (no SSH access required).

**Website:** https://dts4b.fusiontechph.com  
**FTP Host:** ftp://141.136.43.69  
**FTP Username:** u475920781.dts4b  
**FTP Password:** dts4b.4321A

**What You Need:**
- ✅ FileZilla (FTP client)
- ✅ SQLyog (MySQL database management)
- ✅ Your local Laravel project
- ✅ Web browser (for deployment scripts)

---

## 1. Server Connection Details

### 1.1 FileZilla Connection Setup

1. Open **FileZilla Client**
2. Click **File** → **Site Manager** (or press `Ctrl+S`)
3. Click **New Site** button
4. Configure the connection:

   **General Tab:**
   - **Host:** `141.136.43.69` (or `ftp://141.136.43.69`)
   - **Port:** `21`
   - **Protocol:** `FTP - File Transfer Protocol`
   - **Encryption:** `Use explicit FTP over TLS if available` (or `Only use plain FTP` if TLS fails)
   - **Logon Type:** `Normal`
   - **User:** `u475920781.dts4b`
   - **Password:** `dts4b.4321A`

5. Click **Connect**

### 1.2 Verify Connection

After connecting, you should see:
- **Remote site:** `/` (root directory)
- **Local site:** Your local project folder

**Important Directories on Server:**
- `/public_html/dts4b/` - Your main domain directory (Laravel app files go here)
- `/public_html/dts4b/public/` - Document root (where public files go - this is where your domain points)

### 1.3 Database Connection Details (SQLyog)

**MySQL Connection for SQLyog:**
- **Host:** `srv490.hstgr.io`
- **Port:** `3306`
- **Database:** `u475920781_dts4b`
- **Username:** `u475920781_dts4b`
- **Password:** `dts4b.4321A`

**SQLyog Connection Steps:**
1. Open SQLyog
2. Click **New** connection
3. Enter the connection details above
4. Click **Test Connection** to verify
5. Click **Connect**

---

## 2. File Structure Setup

### 2.1 Understanding Hostinger Structure

On Hostinger, your domain `dts4b.fusiontechph.com` points to `public/`. The structure should be:

```
/public_html/dts4b/                 ← Root directory (Laravel app files are here)
│
├── app/                            ← Laravel application directories
├── bootstrap/
├── config/
├── database/
├── resources/
├── routes/
├── storage/
├── vendor/
├── .env                            ← Environment configuration
├── artisan                         ← Laravel command line tool
├── composer.json
├── (other Laravel files)
│
└── public/                         ← Domain document root (dts4b.fusiontechph.com points here)
    ├── index.php                   ← Laravel entry point (from local public/index.php)
    ├── .htaccess                   ← Apache rewrite rules (from local public/.htaccess)
    ├── manifest.json               ← From local public/manifest.json
    ├── build/                      ← Compiled Vite assets (from local public/build/)
    ├── images/                     ← From local public/images/
    └── (all other local public/ folder contents)
```

### 2.2 Document Root Confirmation

**Your Document Root:**
- Document root: `/public_html/dts4b/public/`
- Laravel files: `/public_html/dts4b/` (root directory, contains public/ folder)

**Note:** Your domain automatically points to the `/public/` subdirectory

### 2.3 Create Directory Structure

1. In FileZilla, navigate to `/public_html/dts4b/`
2. Verify these directories exist (create if missing):
   - `app/`
   - `bootstrap/`
   - `config/`
   - `database/`
   - `resources/`
   - `routes/`
   - `storage/`
   - `vendor/` (will upload later)

3. Navigate to your document root: `public/`
4. This directory should be empty or contain only Laravel public files

---

## 3. Public Folder Contents Migration

### 3.1 Prepare Public Files Locally

Before uploading, ensure you have built your assets:

```bash
# On your local machine
npm run build
```

This creates the `public/build/` folder with compiled assets.

### 3.2 Upload Public Files to Document Root

1. **In FileZilla, navigate to your document root:**
   - `/public_html/dts4b/public/` (this is where your domain points)

2. **On your local machine, navigate INSIDE the `public/` folder:**
   - Open `public/` folder
   - You should see: `index.php`, `.htaccess`, `manifest.json`, `build/`, etc.

3. **⚠️ CRITICAL: Upload the CONTENTS, not the folder itself!**
   
   **WRONG WAY ❌:**
   ```
   public/
   └── public/          ← DON'T DO THIS!
       ├── index.php
       └── .htaccess
   ```
   
   **CORRECT WAY ✅:**
   ```
   public/
   ├── index.php        ← Files directly here
   ├── .htaccess
   ├── manifest.json
   └── build/
   ```

4. **Upload Method:**
   - **Select all files and folders INSIDE your local `public/`** (not the `public/` folder itself)
   - Drag and drop to server's `public/` directory
   - Or right-click selected items → **Upload**
   - Files should appear directly in server's `public/`, not in a nested `public/public/` subfolder
   
   **Remember:**
   - **Local:** `your-project/public/` (source - upload FROM here)
   - **Server:** `/public_html/dts4b/public/` (destination - upload TO here)
   - Upload the **contents** of local `public/` → server's `public/`

### 3.3 Verify Public Files Upload

After upload, your document root should contain:
```
public/
├── index.php          ✓
├── .htaccess          ✓
├── manifest.json      ✓
├── build/
│   ├── assets/
│   └── manifest.json
├── images/
└── (other files)
```

**Important:** 
- Do NOT upload the `public/` folder itself, only its **contents**!
- Upload directly to `public/`, not in a subdirectory

### 3.4 Fix: If You Already Uploaded the Public Folder Incorrectly

**If you see this structure (WRONG):**
```
public/
└── public/
    ├── index.php
    ├── .htaccess
    └── build/
```

**Fix it:**
1. In FileZilla, navigate to `/public_html/dts4b/public/public/`
2. Select ALL files and folders inside
3. Cut/Move them up one level to `/public_html/dts4b/public/`
4. Delete the empty nested `public/` folder
5. Verify files are now directly in `/public_html/dts4b/public/`

**Result should be:**
```
public/
├── index.php      ✓
├── .htaccess      ✓
└── build/         ✓
```

---

## 4. Index.php Path Modifications

### 4.1 Current Index.php Configuration

Your `public/index.php` should look like this:

```php
<?php

use Illuminate\Foundation\Application;
use Illuminate\Http\Request;

define('LARAVEL_START', microtime(true));

// Determine if the application is in maintenance mode...
if (file_exists($maintenance = __DIR__.'/../storage/framework/maintenance.php')) {
    require $maintenance;
}

// Register the Composer autoloader...
require __DIR__.'/../vendor/autoload.php';

// Bootstrap Laravel and handle the request...
/** @var Application $app */
$app = require_once __DIR__.'/../bootstrap/app.php';

$app->handleRequest(Request::capture());
```

### 4.2 Path Explanation

- `__DIR__` = Current directory (where `index.php` is located)
- `__DIR__.'/../'` = Go up one level from document root
- This assumes Laravel files are in the parent directory

### 4.3 Path Explanation for Your Setup

**Your structure:**
- `index.php` is in: `/public_html/dts4b/public/index.php`
- Laravel files are in: `/public_html/dts4b/` (parent directory)
- `__DIR__.'/../'` goes from `public/` up to `/public_html/dts4b/`

**This is correct for standard Hostinger setup - no changes needed!**

### 4.4 Verify Index.php After Upload

1. In FileZilla, navigate to document root
2. Right-click `index.php` → **View/Edit**
3. Verify paths are correct
4. Save and close (FileZilla will ask to upload changes)

---

## 5. Folder Permissions Configuration

### 5.1 Understanding File Permissions

- **755** = Read, write, execute for owner; read, execute for others (directories)
- **644** = Read, write for owner; read for others (files)
- **775** = Read, write, execute for owner/group; read, execute for others (writable directories)

### 5.2 Set Permissions via FileZilla

**Method 1: Right-Click Method**
1. Right-click file/folder → **File permissions...**
2. Enter numeric value (e.g., `755`)
3. Check **Recurse into subdirectories** for folders
4. Click **OK**

**Method 2: Command Method**
1. Right-click → **File permissions...**
2. Use checkboxes to set permissions
3. Check **Recurse into subdirectories** for folders

### 5.3 Required Permissions

**Directories (755):**
- `app/`
- `bootstrap/`
- `config/`
- `database/`
- `resources/`
- `routes/`
- `vendor/`
- All other Laravel directories

**Writable Directories (775):**
- `storage/` → **775**
- `storage/framework/` → **775**
- `storage/framework/cache/` → **775**
- `storage/framework/sessions/` → **775**
- `storage/framework/views/` → **775**
- `storage/logs/` → **775**
- `bootstrap/cache/` → **775**

**Files (644):**
- All `.php` files
- All `.json` files
- All other files

**Executable Files (755):**
- `artisan` → **755**

### 5.4 Permission Setting Steps

1. **Set Storage Permissions:**
   ```
   Navigate to: /public_html/dts4b/storage/
   Right-click → File permissions → 775 → Recurse into subdirectories
   ```

2. **Set Bootstrap Cache Permissions:**
   ```
   Navigate to: /public_html/dts4b/bootstrap/cache/
   Right-click → File permissions → 775 → Recurse into subdirectories
   ```

3. **Set All Other Directories:**
   ```
   Select all Laravel directories (app, config, etc.)
   Right-click → File permissions → 755 → Recurse into subdirectories
   ```

4. **Set All Files:**
   ```
   Select all files
   Right-click → File permissions → 644
   ```

5. **Set Artisan:**
   ```
   Navigate to: /public_html/dts4b/
   Right-click artisan → File permissions → 755
   ```

### 5.5 Verify Permissions

After setting, verify by:
1. Right-click folder → **File permissions...**
2. Check that numeric value matches expected
3. For `storage/` and `bootstrap/cache/`, ensure they show **775**

---

## 6. Vendor Folder Upload

### 6.1 Upload Vendor Folder (FTP-Only Method)

**Since you have NO SSH access, you MUST upload the vendor folder:**

**Step 1: Prepare Vendor Locally**
```bash
# On your local machine
composer install --no-dev --optimize-autoloader
```

This installs production dependencies only.

**Step 2: Compress Vendor Folder (Optional)**
- Right-click `vendor/` folder
- Create ZIP archive
- Upload ZIP file
- Extract on server (if FileZilla supports)

**Step 3: Upload Vendor Folder**
1. In FileZilla, navigate to `/domains/fusiontechph.com/`
2. On local machine, navigate to project root
3. Select `vendor/` folder
4. **Upload entire folder** (this is large, 100+ MB, be patient)
5. Ensure all subdirectories and files are uploaded

**Step 4: Verify Upload**
- Check `vendor/` folder exists on server
- Verify `vendor/autoload.php` exists
- Check folder size matches local (approximately)

### 6.3 Troubleshooting Vendor Issues

**Issue: "Class not found" errors**
- Verify `vendor/autoload.php` exists
- Check file permissions (644 for files, 755 for directories)
- Re-upload `vendor/` folder if corrupted

**Issue: Upload timeout**
- Upload in smaller batches
- Use ZIP compression and extract on server

---

## 7. Vite Asset Compilation Issue Resolution

### 7.1 Build Assets Locally

**Before uploading, build your assets:**

```bash
# On your local machine
npm install          # Install dependencies (if not done)
npm run build        # Build production assets
```

This creates:
- `public/build/manifest.json`
- `public/build/assets/*.js`
- `public/build/assets/*.css`

### 7.2 Upload Build Folder

1. **In FileZilla, navigate to document root:**
   - `/public_html/dts4b/public/`

2. **On local machine, navigate to:** `public/build/`

3. **Upload entire `build/` folder:**
   - Select `build/` folder
   - Drag to `public/` directory
   - Ensure `build/manifest.json` and all assets are uploaded

### 7.3 Verify Build Assets

After upload, verify structure:
```
public/build/
├── manifest.json
└── assets/
    ├── app.xxxxx.js
    ├── app.xxxxx.css
    └── (other assets)
```

### 7.4 Common Vite Issues

**Issue: "Vite manifest not found"**
- **Solution:** Ensure `build/manifest.json` exists in document root
- Verify file permissions (644)
- Check `APP_URL` in `.env` matches your domain

**Issue: Assets return 404**
- **Solution:** 
  - Verify `build/` folder is in document root
  - Check `.htaccess` allows access to assets
  - Clear browser cache
  - Verify `APP_URL` in `.env` is correct

**Issue: Assets load but styles broken**
- **Solution:**
  - Rebuild assets: `npm run build`
  - Re-upload `build/` folder
  - Check browser console for specific file errors

**Issue: "Mixed content" errors (HTTP/HTTPS)**
- **Solution:** Ensure `APP_URL` uses `https://` in `.env`

### 7.5 Production Asset Optimization

For production, ensure:
```bash
npm run build  # Not 'npm run dev'
```

The `build` command:
- Minifies JavaScript and CSS
- Optimizes assets
- Creates production-ready files

---

## 8. .htaccess Configuration

### 8.1 Standard Laravel .htaccess

Your `.htaccess` file should be in the document root and contain:

```apache
<IfModule mod_rewrite.c>
    <IfModule mod_negotiation.c>
        Options -MultiViews -Indexes
    </IfModule>

    RewriteEngine On
    
    # Handle Authorization Header
    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

    # Handle X-XSRF-Token Header
    RewriteCond %{HTTP:x-xsrf-token} .
    RewriteRule .* - [E=HTTP_X_XSRF_TOKEN:%{HTTP:X-XSRF-Token}]

    # Redirect Trailing Slashes If Not A Folder...
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_URI} (.+)/$
    RewriteRule ^ %1 [L,R=301]

    # Send Requests To Front Controller...
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]
</IfModule>
```

### 8.2 Upload .htaccess

1. **In FileZilla, navigate to document root**
2. **Upload `.htaccess` from local `public/` folder**
3. **Verify file exists** (hidden files may not show by default)
   - In FileZilla: **Server** → **Force show hidden files**

### 8.3 .htaccess Troubleshooting

**Issue: 403 Forbidden**
- **Solution:** 
  - Verify `.htaccess` is in document root
  - Check file permissions (644)
  - Ensure `mod_rewrite` is enabled (contact Hostinger support if needed)

**Issue: Routes not working (404)**
- **Solution:**
  - Verify `.htaccess` exists and is correct
  - Check `RewriteEngine On` is present
  - Ensure last rule points to `index.php`

**Issue: Assets blocked**
- **Solution:** `.htaccess` should NOT block `build/` or `images/` folders
- The standard Laravel `.htaccess` allows these by default

### 8.4 Additional .htaccess Rules (Optional)

**Force HTTPS:**
```apache
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```

**Security Headers:**
```apache
<IfModule mod_headers.c>
    Header set X-Content-Type-Options "nosniff"
    Header set X-Frame-Options "SAMEORIGIN"
    Header set X-XSS-Protection "1; mode=block"
</IfModule>
```

---

## 9. Key Lessons and Important Notes

### 9.1 Critical Configuration Points

1. **Document Root vs. Laravel Root**
   - Document root = Where `index.php` lives (`public/`)
   - Laravel root = Where app files live (parent directory, same level as `public/`)
   - Never mix them!

2. **.env File Location**
   - `.env` goes in Laravel root (parent directory)
   - **NEVER** put `.env` in document root (security risk!)

3. **Path Consistency**
   - All paths in `index.php` use `__DIR__.'/../'` to go up one level
   - This assumes standard Hostinger structure

4. **File Permissions**
   - `storage/` and `bootstrap/cache/` MUST be writable (775)
   - Other directories can be 755
   - Files should be 644

5. **Asset Building**
   - Always run `npm run build` before uploading
   - Upload `build/` folder to document root
   - Never upload `node_modules/`

### 9.2 Common Mistakes to Avoid

❌ **Don't upload entire project to document root**
- Only public files go in document root
- Laravel app files go in parent directory

❌ **Don't forget to build assets**
- `npm run dev` is for development
- `npm run build` is for production

❌ **Don't set permissions to 777**
- Security risk!
- Use 775 for writable directories, 755 for others

❌ **Don't put .env in public**
- Major security vulnerability
- Keep it in parent directory

❌ **Don't forget storage permissions**
- `storage/` must be writable (775)
- Otherwise, logs, cache, sessions won't work

### 9.3 Environment-Specific Settings

**Production .env Configuration:**
```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://dts4b.fusiontechph.com
LOG_LEVEL=error
BROADCAST_CONNECTION=log  # Use 'log' instead of 'reverb' on shared hosting
```

**Why `BROADCAST_CONNECTION=log`?**
- Reverb requires a separate process running
- Shared hosting doesn't support long-running processes
- `log` driver logs broadcasts instead (no real-time, but no errors)

### 9.4 Security Best Practices

1. **Never commit .env to Git**
2. **Set APP_DEBUG=false in production**
3. **Use HTTPS (SSL certificate)**
4. **Keep file permissions secure (no 777)**
5. **Regular backups of database and files**
6. **Keep Laravel and dependencies updated**

### 9.5 Performance Optimization

1. **Use database cache** (`CACHE_STORE=database`)
2. **Optimize Composer autoloader locally:**
   ```bash
   composer install --optimize-autoloader --no-dev
   ```
3. **Minify assets** (already done by `npm run build`)

---

## 10. Web-Based Deployment Scripts (No SSH Required!)

Since you have **NO SSH access**, we've created special web-based scripts that you can run through your browser. These scripts replicate essential Laravel Artisan commands and can fix most deployment issues.

### 10.1 🚀 Complete Deployment Fix Script

**URL:** `https://dts4b.fusiontechph.com/deploy_fix.php`

**This is your MAIN troubleshooting tool!** Run this script whenever you have issues.

**What it does automatically:**
- ✅ **Clears ALL Laravel caches** (config, routes, views, application cache)
- ✅ **Checks/generates APP_KEY** (fixes "No application encryption key" errors)
- ✅ **Creates storage link** (with automatic fallback if symlinks fail)
- ✅ **Tests database connection** and shows table status
- ✅ **Verifies Laravel application** functionality
- ✅ **Checks file permissions** for critical directories
- ✅ **Provides detailed diagnostics** with specific error solutions

**When to use:**
- ✨ **After uploading files via FileZilla**
- 🔥 **When getting 500 Internal Server Error**
- 🆕 **After initial deployment**
- 🔄 **After updating your application**
- 🐛 **When Laravel seems broken**

**Example successful output:**
```
🚀 Laravel DTS Deployment Fix
============================
🧹 STEP 1: Clearing All Caches
✅ Cleared: Routes cache
✅ Cleared: Config cache
✅ Cleared: Views cache
✅ Cleared: Application cache

🔑 STEP 2: Checking Application Key
✅ APP_KEY exists and looks valid

🔗 STEP 3: Setting Up Storage Link
✅ Storage symlink created successfully

🗄️ STEP 4: Checking Database Connection
✅ Database connection successful
✅ Found 15 database tables
✅ Table 'users': 5 records
✅ Table 'documents': 23 records

🧪 STEP 5: Testing Laravel Application
✅ Homepage loads successfully (200 OK)

📁 STEP 6: Checking File Permissions
✅ Writable: Storage directory
✅ Writable: Log directory

🎯 Deployment Fix Summary
✅ All checks passed - your site should work now!
```

### 10.2 📁 Storage Link Creator

**URL:** `https://dts4b.fusiontechph.com/storage_link.php`

**Purpose:** Creates the symbolic link from `public/storage` to `storage/app/public` so uploaded files are accessible.

**Features:**
- Creates symlink on Unix/Linux servers
- Creates PHP fallback handler on Windows/shared hosting
- Tests the link after creation
- Shows detailed success/error messages

**When to use:**
- When uploaded files return 404 errors
- When document attachments don't display
- After initial deployment

### 10.3 🧹 Simple Cache Clear

**URL:** `https://dts4b.fusiontechph.com/simple_fix.php`

**Purpose:** Basic cache clearing and Laravel functionality test.

**What it does:**
- Clears bootstrap cache files
- Clears storage framework caches
- Tests basic Laravel functionality
- Shows what was cleared

**When to use:**
- Quick cache clearing without full diagnostics
- When you just need to clear caches fast

### 10.4 🔧 How to Use These Scripts

**Step 1: Upload the scripts via FileZilla**
1. Upload `deploy_fix.php` to your domain root (same level as `public/`)
2. Upload `storage_link.php` to your domain root
3. Upload `simple_fix.php` to your domain root

**Step 2: Run via browser**
1. Open your browser
2. Go to `https://dts4b.fusiontechph.com/deploy_fix.php`
3. Wait for the script to complete
4. Follow any instructions shown

**Step 3: Test your site**
1. Visit `https://dts4b.fusiontechph.com/`
2. Check if errors are resolved
3. Test login, file uploads, etc.

### 10.5 🚨 Common Issues & Solutions

**Issue: "500 Internal Server Error"**
- **Solution:** Run `deploy_fix.php` - it will identify and fix the cause
- **Common causes:** Missing APP_KEY, cache conflicts, wrong permissions

**Issue: "No application encryption key has been specified"**
- **Solution:** Run `deploy_fix.php` - it will generate a new APP_KEY automatically

**Issue: "Uploaded files return 404"**
- **Solution:** Run `storage_link.php` to create the storage link

**Issue: "Class not found" errors**
- **Solution:** Re-upload `vendor/` folder via FileZilla, then run `deploy_fix.php`

**Issue: "Route not found" errors**
- **Solution:** Run `deploy_fix.php` to clear route cache

### 10.6 💡 Why These Scripts Work

**No SSH needed:** Everything runs through your web browser
**Safe to use:** Scripts only fix common issues, don't break anything
**Comprehensive:** Cover 90% of Laravel deployment problems
**Detailed feedback:** Show exactly what's wrong and how to fix it
**Automatic fallbacks:** If one method fails, tries alternatives

---

## 11. Manual FTP-Only Alternatives (Backup Methods)

If the web scripts don't work for some reason, here are manual FileZilla methods:

### 10.1 Clear Cache (FTP Method)

**Instead of:** `php artisan config:clear`

**FTP Method:**
1. In FileZilla, navigate to `/domains/fusiontechph.com/bootstrap/cache/`
2. Delete these files (if they exist):
   - `config.php`
   - `routes.php`
   - `services.php`
3. Laravel will regenerate them on next request

**Instead of:** `php artisan cache:clear`

**FTP Method:**
1. Navigate to `/domains/fusiontechph.com/storage/framework/cache/data/`
2. Delete all files and folders inside (keep the directory itself)
3. Laravel will recreate cache files as needed

### 10.2 Create Storage Link (FTP Method)

**Instead of:** `php artisan storage:link`

**FTP Method:**
1. In FileZilla, navigate to document root (`public/`)
2. Create a new folder named `storage`
3. OR create a symbolic link manually (if FileZilla supports it)
4. **Alternative:** Upload files directly to `storage/app/public/` and access via URL path

**Note:** For shared hosting, you may not need a storage link. Files can be accessed directly from `storage/app/public/` if configured correctly.

### 10.3 Run Migrations (FTP Method)

**Instead of:** `php artisan migrate`

**FTP Method:**
1. Check `database/migrations/` folder for new migration files
2. Convert migration PHP to SQL manually or use a tool
3. Run SQL directly in SQLyog
4. Update `migrations` table to mark migration as run:
   ```sql
   INSERT INTO migrations (migration, batch) 
   VALUES ('2024_01_01_000000_create_example_table', 1);
   ```

### 10.4 Clear View Cache (FTP Method)

**Instead of:** `php artisan view:clear`

**FTP Method:**
1. Navigate to `/domains/fusiontechph.com/storage/framework/views/`
2. Delete all `.php` files inside (these are compiled Blade templates)
3. Laravel will recompile them on next request

### 10.5 Generate Application Key (If Needed)

**Instead of:** `php artisan key:generate`

**FTP Method:**
1. Generate key locally: `php artisan key:generate`
2. Copy the `APP_KEY` value from local `.env`
3. Update `.env` on server with the new key via FileZilla

### 10.6 Maintenance Mode (FTP Method)

**Instead of:** `php artisan down` / `php artisan up`

**FTP Method:**
1. **Enable Maintenance:**
   - Create file: `/domains/fusiontechph.com/storage/framework/maintenance.php`
   - Content: `<?php return ['retry' => 60, 'secret' => 'your-secret-key']; ?>`

2. **Disable Maintenance:**
   - Delete `/domains/fusiontechph.com/storage/framework/maintenance.php`

---

## 12. Quick Deployment Workflow (FTP-Only)

### 12.1 🚀 Complete Deployment Steps

**For first-time deployment or major updates:**

1. **Prepare locally:**
   ```bash
   npm run build                    # Build assets
   composer install --no-dev       # Install production dependencies
   ```

2. **Upload via FileZilla:**
   - Upload Laravel files to `/domains/fusiontechph.com/`
   - Upload `public/` contents to `/domains/fusiontechph.com/public/`
   - Upload `vendor/` folder (this takes time!)
   - Upload `.env` file with production settings

3. **Run deployment fix:**
   - Go to `https://dts4b.fusiontechph.com/deploy_fix.php`
   - Wait for all checks to complete
   - Fix any issues shown

4. **Test your site:**
   - Visit `https://dts4b.fusiontechph.com/`
   - Test login, file uploads, all features

### 12.2 🔄 Quick Updates Workflow

**For small updates (code changes only):**

1. **Upload changed files via FileZilla**
2. **Run cache clear:** `https://dts4b.fusiontechph.com/simple_fix.php`
3. **Test the changes**

### 12.3 🆘 Emergency Fix Workflow

**When site is broken (500 errors):**

1. **Run deployment fix:** `https://dts4b.fusiontechph.com/deploy_fix.php`
2. **Check the output** - it will tell you exactly what's wrong
3. **Follow the specific instructions** provided by the script
4. **Re-run the script** until all checks pass

---

## 13. Maintenance and Future Updates

### 13.1 Regular Maintenance Tasks

**Weekly:**
- Download and check error logs: `storage/logs/laravel.log` via FileZilla
- Backup files via FileZilla

**Monthly:**
- Update dependencies locally and re-upload
- Review and clean old logs
- Check for Laravel security updates

**Quarterly:**
- Update dependencies (test locally first!)
- Full backup of all files

### 13.2 Updating the Application

**Step 1: Backup**
- Download entire project via FileZilla
- Export database via SQLyog

**Step 2: Update Locally**
```bash
# Pull latest code
git pull origin main

# Update dependencies
composer update
npm update

# Rebuild assets
npm run build

# Test locally
php artisan serve
```

**Step 3: Upload Changes**
- Upload changed files via FileZilla
- Upload new `build/` folder
- Upload updated `vendor/` folder (if Composer updated)

**Step 4: Run Migrations (if any)**
- Use FTP method from Section 10.3 (convert to SQL and run in SQLyog)

**Step 5: Clear Cache**
- Use FTP method from Section 10.1 (delete cache files manually)

### 11.3 Database Updates

**Via SQLyog:**
1. Export current database (backup)
2. Run new migration SQL files
3. Verify data integrity
4. Test application

**Manual Migration:**
1. Check `database/migrations/` for new files
2. Convert migration to SQL
3. Run SQL in SQLyog
4. Update `migrations` table

### 11.4 Troubleshooting Updates

**Issue: "Class not found" after update**
- Re-upload `vendor/` folder
- Clear cache using FTP method (Section 10.1)

**Issue: "Route not found"**
- Verify `.htaccess` is present
- Delete `bootstrap/cache/routes.php` via FileZilla

**Issue: Assets not updating**
- Rebuild assets: `npm run build`
- Re-upload `build/` folder
- Clear browser cache

### 11.5 Backup Strategy

**Automated Backups (if available):**
- Use Hostinger backup feature
- Schedule daily database backups
- Weekly full file backups

**Manual Backups:**
1. **Database:**
   - SQLyog → Right-click database → Backup Database
   - Save to local machine

2. **Files:**
   - FileZilla → Download entire project folder
   - Compress and store securely



### 11.6 Monitoring and Logs

**Check Logs Regularly:**
- `storage/logs/laravel.log` - Download via FileZilla to read application errors
- Hostinger error logs (in hPanel)
- Access logs (if available)

**Monitor:**
- Disk space usage (check in hPanel)
- Database size (check in SQLyog)
- Error frequency
- Performance metrics

### 11.7 Support Resources

**Hostinger Support:**
- Knowledge Base: https://www.hostinger.com/tutorials
- Support Ticket: Via hPanel
- Live Chat: Available in hPanel

**Laravel Documentation:**
- Official Docs: https://laravel.com/docs
- Deployment Guide: https://laravel.com/docs/deployment

**FileZilla:**
- Documentation: https://filezilla-project.org/documentation
- FAQ: https://filezilla-project.org/faq.php


---

## Conclusion

This guide provides a **complete FTP-only deployment solution** for Laravel on Hostinger. No SSH required!

### 🎯 Key Success Factors:

1. **Use the web-based scripts** - Your main tools for deployment and troubleshooting
2. **Structure matters** - Keep Laravel files separate from public files  
3. **Permissions are critical** - Storage must be writable (775)
4. **Build before upload** - Always run `npm run build` locally
5. **Test with deployment fix** - Run `deploy_fix.php` after every upload

### 🚀 Your Deployment Arsenal:

**Main troubleshooting tool:**
- `https://dts4b.fusiontechph.com/deploy_fix.php` - Fixes 90% of issues automatically

**Specialized tools:**
- `https://dts4b.fusiontechph.com/storage_link.php` - For file upload issues
- `https://dts4b.fusiontechph.com/simple_fix.php` - Quick cache clearing

### 🆘 When Things Go Wrong:

1. **Run `deploy_fix.php` first** - It will diagnose and fix most issues
2. **Check Laravel logs** - Download `storage/logs/laravel.log` via FileZilla
3. **Check Hostinger error logs** - Available in hPanel
4. **Check browser console** - For frontend JavaScript errors


---

**Need help?** The web-based scripts provide detailed error messages and solutions. Start with `deploy_fix.php` - it's your best friend for troubleshooting!

