# Run all uploaders script

**Author:** `Muhammed Emre Bayraktaroglu`

**Purpose:**
This script automates the execution of all Python scripts in the `src/data_uploaders` folder, essentially running every single data uploader to set up the database for the chatbot. It runs each script sequentially, captures output, and handles errors, making it ideal for batch processing.


---

## Usage

1. **Run the Script:**
   ```bash
   python run_all_uploaders.py
   ```

2. **Output:**
   - Displays the name of each data uploader being executed.
   - Prints the standard output or errors for each script.
   - Stops if a script fails.

---

## Code Overview

1. **`folder_path`:**
   Specifies the directory (`src/data_uploaders`) containing the data uploaders to execute.

2. **Dynamic Execution:**
   Converts filenames to module paths (e.g., `uploader.py` → `src.data_uploaders.uploader`) and runs them using `subprocess.run`.

3. **Error Handling:**
   - Prints errors if a script fails (`stderr`).
   - Stops further execution if the return code is non-zero.

---

### Example Output
```plaintext
Running src.data_uploaders.uploader1...
Output from uploader1...
Running src.data_uploaders.uploader2...
Error in uploader2: Traceback (most recent call last)...
uploader2 failed to execute.
```

---

## Customization

- **Change Directory:** Modify `folder_path` to point to a different script folder.
- **Error Handling:** Adjust how errors are managed (e.g., log errors to a file).

---

## Conclusion
This lightweight script aimed at simplifying the setup process of the database for the chatbot.