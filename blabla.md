# CleanSweep Desktop

#### Student: Iago de Sousa Aragão
#### GitHub/EDX Username: Umbura
#### City/Country: Teresina, Brazil
#### Video Demo: [CleanSweep Desktop](https://youtu.be/a7S-KerRA_o)
#### Video Date: 06/01/2026

*(Note: The video demonstrates version 1.0. The submitted code includes further improvements, such as real-time file size visualization and a dynamic "total size selected" summary, which were added after the recording to enhance user experience.)*

#### Description:

**CleanSweep Desktop** is a robust, GUI-based file management tool designed to solve a very specific, personal problem: **digital disorganization**.

The inspiration for this project came from my own Downloads folder, which had become an unmanageable mess of thousands of files. I found myself in a situation where I didn't know what I could safely delete or where to find important documents amidst the clutter. As CS50 challenges us to build software that solves a real-world problem, I decided to build a tool that would not only organize this chaotic directory but also help me identify "useless" data—specifically, duplicates and files I haven't touched in years.

While many automation scripts exist, I needed a solution that was visual, safe, and easy to use. Therefore, CleanSweep bridges the gap between raw Python scripts and user-friendly software, providing a modern interface built with **CustomTkinter** that allows users to clean, organize, and de-duplicate their folders with confidence.

### Core Features

1.  **Organize (The Core Idea):** This is the heart of the application. It takes a chaotic directory and instantly categorizes loose files into smart folders (Images, Documents, Installers, etc.) based on file extensions.
2.  **Duplicates Finder:** Uses **MD5 Hashing** to find exact file copies. It now displays the size of each file and **automatically calculates the total space** you will recover based on your selection, giving you full control before deletion.
3.  **Cleanup Tool:** Recursively finds "Disk Hogs" (large files) and "Stale Files" (old files). Like the duplicates tab, it provides real-time feedback on the total megabytes selected for removal.

### Project Structure and File Overview

The project follows a modular architecture, separating the Frontend (UI) from the Backend (Logic).

*   **`app.py`**: The entry point and controller. It initializes the main window and injects the backend logic into the UI views.
*   **`backend.py`**: The "brain" of the app. It handles file system operations (`shutil`, `os.walk`, `hashlib`). It is completely decoupled from the UI, meaning it could theoretically run as a CLI tool.
*   **`cleansweep_app/ui/`**: Contains the view logic (`organize.py`, `duplicates.py`, `cleanup.py`), handling user inputs, thread management, and preventing UI freezes during heavy operations.

### Design Choices and Trade-offs

**1. Dependency Management: Why Poetry?**
In previous personal projects, I frequently struggled with "dependency hell"—conflicts between package versions and inconsistent environments across different machines.
*   *Decision:* To avoid these issues, I chose **Poetry** over standard `pip` for this project. Poetry provides a robust lockfile system (`poetry.lock`) that ensures the project runs exactly the same way everywhere, solving the version conflict headaches I faced in the past.

**2. Safety First: The Shift from Deletion to Trash**
In the early development stages, the program used `os.remove()` to permanently delete files. However, considering the chaotic nature of the folders this app targets (like Downloads), the risk of accidentally deleting a critical file was too high.
*   *Decision:* I refactored the entire deletion logic to use the `send2trash` library. Now, files are moved to the OS Recycle Bin. This design choice prioritized **user safety** over code simplicity.

**3. User Experience: CLI vs. GUI**
Initially, I conceived this as a simple Python script. However, running a script in a terminal feels "dangerous" for general file management and lacks visual feedback.
*   *Decision:* I implemented a GUI using **CustomTkinter**. This makes the tool approachable and provides real-time logs (e.g., "Moved file X to Y"), giving the user transparency on what the code is doing.

**4. Recursive vs. Non-Recursive Logic**
*   **Organize:** I decided this should **NOT** be recursive. Moving files out of subfolders automatically can break installed programs or game data. It only organizes the root of the target folder.
*   **Cleanup/Duplicates:** These **ARE** recursive. To effectively free up space, the tool needs to dig deep into subdirectories to find hidden large files or duplicates using `os.walk`, which is robust against Windows permission errors.

**5. Performance vs. Functionality (The "Select All" Dilemma)**
During testing with thousands of files, rendering the list of checkboxes caused UI lag. I initially solved this by limiting the display to the top 150 items.
*   *Decision:* I removed this limit in the final version. While rendering 1,000+ items might cause a momentary freeze, the previous limit broke the "Select All" functionality (it would only delete the visible 150 items). I decided that the **utility** of being able to bulk-delete everything at once was more important than perfect visual smoothness.

**6. UX Improvement: Real-Time Size Calculation**
Initially, the backend only returned file paths. To allow the user to see how much space they were freeing, I needed to display file sizes.
*   *Decision:* Instead of making the UI query the operating system for the file size of every single checkbox (which causes lag), I optimized the `backend.py` to return a list of tuples: `(file_path, file_size)`. This allows the UI to display the size instantly and calculate the total sum mathematically without touching the disk again.

### AI Acknowledgement

As permitted by the CS50 Final Project guidelines, AI tools (specifically LLMs) were used as productivity amplifiers during development. The core architecture, the decision to use Poetry, and the logic flow were designed by me. AI assistance was used for:

*   **UI Boilerplate:** Generating the verbose `CustomTkinter` layout code and frame nesting, which sped up the frontend development significantly.
*   **Security Suggestions:** When I asked about safe deletion methods, AI suggested the `send2trash` library as a best practice alternative to `os.remove`.
*   **Windows Robustness:** During testing, the scanner crashed on locked system folders. AI suggested switching from `pathlib.rglob` to `os.walk` with error handling to make the recursive scan robust against Windows permission errors.
*   **Data Entry:** Generating the extensive dictionary mapping file extensions to folder categories (e.g., mapping .jpg, .png, .gif to "Images").

### Installation

This project was developed using **Poetry** for dependency management to ensure a reproducible environment.

#### Option 1: Using Poetry (Recommended)
If you have Poetry installed:
```bash
poetry install
poetry run python app.py
```

#### Option 2: Using pip
If you prefer standard pip:
```bash
pip install -r requirements.txt
python app.py
```

### Technology Stack
-   **Language:** Python 3.12+
-   **Dependency Manager:** Poetry
-   **GUI:** CustomTkinter
-   **File Safety:** Send2Trash
