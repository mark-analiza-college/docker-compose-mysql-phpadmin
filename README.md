# MySQL & phpMyAdmin Docker Setup

This project sets up a MySQL database server and phpMyAdmin web interface using Docker Compose.

## Prerequisites: Installing Docker

### macOS Installation

#### Option 1: Docker Desktop (Recommended)

1. **Download Docker Desktop for Mac**
   - Visit: https://www.docker.com/products/docker-desktop/
   - Choose the version for your Mac:
     - **Apple Silicon (M1/M2/M3)**: Download "Docker Desktop for Mac with Apple Silicon"
     - **Intel Mac**: Download "Docker Desktop for Mac with Intel chip"

2. **Install Docker Desktop**
   - Open the downloaded `.dmg` file
   - Drag Docker.app to your Applications folder
   - Open Docker from Applications
   - Follow the setup wizard

3. **Verify Installation**
   - Docker Desktop should appear in your menu bar (whale icon)
   - Open Terminal and run:
     ```bash
     docker --version
     docker-compose --version
     ```

#### Option 2: Homebrew (Alternative)

```bash
# Install Docker Desktop via Homebrew
brew install --cask docker

# Start Docker Desktop
open /Applications/Docker.app
```

### Windows Installation

#### System Requirements
- Windows 10 64-bit: Pro, Enterprise, or Education (Build 19041 or higher)
- Windows 11 64-bit: Home or Pro version 21H2 or higher
- WSL 2 feature enabled
- Virtualization enabled in BIOS

#### Installation Steps

1. **Enable WSL 2**
   - Open PowerShell as Administrator
   - Run:
     ```powershell
     wsl --install
     ```
   - Restart your computer if prompted

2. **Download Docker Desktop for Windows**
   - Visit: https://www.docker.com/products/docker-desktop/
   - Click "Download for Windows"
   - Run the installer (`Docker Desktop Installer.exe`)

3. **Install Docker Desktop**
   - Follow the installation wizard
   - Ensure "Use WSL 2 instead of Hyper-V" is checked (recommended)
   - Click "Ok" when installation completes
   - Restart your computer if prompted

4. **Start Docker Desktop**
   - Launch Docker Desktop from the Start menu
   - Accept the service agreement
   - Wait for Docker to start (whale icon in system tray)

5. **Verify Installation**
   - Open Command Prompt or PowerShell
   - Run:
     ```cmd
     docker --version
     docker-compose --version
     ```

### Verification Commands

After installation, verify Docker is working correctly:

```bash
# Check Docker version
docker --version

# Check Docker Compose version
docker-compose --version

# Test Docker installation
docker run hello-world
```

If the `hello-world` container runs successfully, Docker is installed correctly!

## Using This Project

### Starting the Services

1. **Navigate to the project directory  or start from root if you already inside**
   ```bash
   cd mysql-php-admin
   ```

2. **Start the containers**
   ```bash
   docker-compose up -d
   ```

   The `-d` flag runs containers in detached mode (background).

### Accessing phpMyAdmin

1. **Open your web browser**
   - Use any modern browser (Chrome, Firefox, Safari, Edge)

2. **Navigate to phpMyAdmin**
   - Go to: **http://localhost:8080**
   - Wait for the login page to load

3. **Login to phpMyAdmin**
   - **Server**: `mysql` (or leave as default)
   - **Username**: `root`
   - **Password**: `root`
   - Click "Go" or press Enter

4. **You should now see the phpMyAdmin interface**
   - On the left sidebar, you'll see available databases
   - The default database `mydb` should be visible
   - You can now browse tables, run SQL queries, and manage your database

### Stopping the Services

```bash
# Stop containers (keeps data)
docker-compose stop

# Stop and remove containers (keeps volumes/data)
docker-compose down

# Stop and remove everything including volumes (deletes data)
docker-compose down -v
```

### Viewing Logs

```bash
# View all logs
docker-compose logs

# View logs for specific service
docker-compose logs mysql
docker-compose logs phpmyadmin

# Follow logs in real-time
docker-compose logs -f
```

### Importing Data to Database

You can import SQL files into your database using either phpMyAdmin's web interface or the command line.

#### Method 1: Import via phpMyAdmin (Web Interface)

1. **Access phpMyAdmin**
   - Open http://localhost:8080 in your browser
   - Login with username `root` and password `root`

2. **Select the database**
   - Click on the database name (e.g., `mydb`) in the left sidebar
   - Or create a new database by clicking "New" in the left sidebar

3. **Open the Import tab**
   - Click on the "Import" tab at the top of the page

4. **Choose your SQL file**
   - Click "Choose File" or "Browse" button
   - Navigate to and select your SQL file (e.g., `mysqlsampledatabase.sql`)
   - The file should be in the project directory

5. **Configure import settings (optional)**
   - **Format**: Leave as "SQL" (default)
   - **Partial import**: Uncheck if you want to import the entire file
   - **Character set**: Usually "utf8" or "utf8mb4"

6. **Execute the import**
   - Click the "Go" button at the bottom
   - Wait for the import to complete
   - You should see a success message with the number of queries executed

7. **Verify the import**
   - Check the left sidebar - you should see new tables appear
   - Click on tables to view the imported data

#### Method 2: Import via Command Line (Docker)

**Option A: Import directly from local file**

1. **Import the SQL file from your local machine**
   ```bash
   # Import into a specific database (mydb)
   docker exec -i mysql mysql -uroot -proot mydb < mysqlsampledatabase.sql
   
   # Or import into MySQL (will use database specified in SQL file)
   docker exec -i mysql mysql -uroot -proot < mysqlsampledatabase.sql
   ```

2. **Verify the import**
   ```bash
   # Connect to MySQL and check tables
   docker exec -it mysql mysql -uroot -proot -e "USE mydb; SHOW TABLES;"
   ```

**Option B: Copy file to container first, then import**

1. **Copy the SQL file into the container**
   ```bash
   # Copy file from your local machine to the container
   docker cp mysqlsampledatabase.sql mysql:/tmp/mysqlsampledatabase.sql
   ```

2. **Import the SQL file from inside the container**
   ```bash
   # Import into a specific database
   docker exec -i mysql mysql -uroot -proot mydb -e "source /tmp/mysqlsampledatabase.sql"
   
   # Or use bash to execute
   docker exec -i mysql bash -c "mysql -uroot -proot mydb < /tmp/mysqlsampledatabase.sql"
   ```

3. **Verify the import**
   ```bash
   # Connect to MySQL and check tables
   docker exec -it mysql mysql -uroot -proot -e "USE mydb; SHOW TABLES;"
   ```

**Note**: Replace `mysqlsampledatabase.sql` with your actual SQL file name, and `mydb` with your target database name if different.

## Project Configuration

- **MySQL Port**: 3306
- **phpMyAdmin Port**: 8080
- **MySQL Root Password**: root
- **Default Database**: mydb

## Troubleshooting

### Docker Desktop Not Starting
- **macOS**: Check System Preferences → Security & Privacy → Allow Docker
- **Windows**: Ensure WSL 2 is properly installed and virtualization is enabled in BIOS

### Port Already in Use
If port 3306 or 8080 is already in use, modify the ports in `docker-compose.yaml`:
```yaml
ports:
  - "3307:3306"  # Change host port to 3307
```

### Reset Everything
```bash
# Remove all containers, networks, and volumes
docker-compose down -v
docker system prune -a --volumes
```

## Additional Resources

- [Docker Documentation](https://docs.docker.com/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [phpMyAdmin Documentation](https://www.phpmyadmin.net/docs/)

