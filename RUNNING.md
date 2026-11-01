# Collaborative IDE

Run three things, in this order: the database, the backend, then the app.
Open a separate PowerShell window for each one and leave it open.

## What you need

All three are already on this PC. Nothing needs to be installed.

| Part | What it does | Where it is |
| --- | --- | --- |
| MySQL | Stores users and files | `D:\mysql-26.10.0-winx64\mysql-26.10.0-winx64\bin` |
| Backend (Java) | Saves files, runs code, shares edits | `CodeEditorSpring` |
| Flutter app | The editor you see in Chrome | `collaborative_ide` |

The disk C: is almost full, so everything here uses D:.

## Step 1: Start MySQL

Run this in the first window. It stays running, so don't close it.

```powershell
D:\mysql-26.10.0-winx64\mysql-26.10.0-winx64\bin\mysqld.exe --console --datadir=D:\MySQL\codeide-data
```

It is ready when you see `ready for connections` and `port: 3306`.

## Step 2: Start the backend

Open a second window and run:

```powershell
cd "D:\Ide collaborative\CollabTextEditor-main\CollabTextEditor-main\CodeEditorSpring"
java -jar target\CodeEditorSpring-0.0.1-SNAPSHOT.jar --spring.datasource.password=<your-mysql-password>
```

It is ready when you see `Started CodeEditorSpringApplication`.
The backend uses port 9090 for files and port 8080 for live editing.

If the `.jar` file is missing, build it once:

```powershell
.\mvnw.cmd -DskipTests "-Dmaven.repo.local=D:\maven-repo" package
```

## Step 3: Start the app

Open a third window and run:

```powershell
$env:Path += ';D:\flutter_windows_3.47.8-stable\flutter\bin'
$env:PUB_CACHE = 'D:\pub-cache'
cd "D:\Ide collaborative\CollabTextEditor-main\CollabTextEditor-main\collaborative_ide"
flutter run -d chrome --web-port 3000
```

Chrome opens by itself. If it doesn't, go to http://localhost:3000.
The first start can take a minute.

Windows **Developer Mode** must be on, or Flutter stops with a "symlink support"
message. Turn it on in Settings, then System, then For developers.

## Using the app

There is no login. Everyone who opens the app shares the same files.

1. Open a folder, then open or create a file.
2. In the editor, type any word into **Session ID**. This word is your code.
3. Press **Create Session** to start sharing.
4. A friend opens the same file, types the same code, and presses **Join Session**.
5. Anything either of you types now appears for both. Press **Save** to keep
   your changes and **Execute** to run the code.

The small dot at the top right is green when the live connection works.

## Stopping everything

Click each window and press `Ctrl + C`. In the Flutter window you can type `q`
instead. Stop the app first, then the backend, then MySQL.

Each time the backend starts, it resets the database tables. The saved folder
and file lists in the database are cleared.

## If something goes wrong

| What you see | What to do |
| --- | --- |
| `flutter` is not recognized | Run the two `$env:` lines from Step 3 in that window first. |
| "Building with plugins requires symlink support" | Turn on Developer Mode in Windows settings. |
| `Access denied for user 'root'` | The password is wrong. Use the one in Step 2. |
| `Port 9090 was already in use` | The backend is already running. Don't start it twice. |
| `ibdata1 must be writable` | MySQL is already running. Don't start it twice. |
| Page at `localhost:8080` shows nothing | Port 8080 is not a web page. Use `localhost:3000`. |
| Red dot in the app | The backend isn't running. Check the Step 2 window. |
