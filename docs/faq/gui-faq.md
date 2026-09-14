# Graphic User Interface (GUI) FAQ

Frequently asked questions about building desktop graphical applications with Python.

---

## What GUI toolkits exist for Python?

Python offers several mature GUI toolkits suited for different application scales:

| Toolkit | Included in Stdlib? | Look & Feel | Best For |
| :--- | :--- | :--- | :--- |
| **Tkinter** | **Yes** (Built-in) | Native (themed via `ttk`) | Quick utilities, internal tools, educational apps |
| **PySide / PyQt (Qt)** | No (`pip install PySide6`) | Extremely polished, modern | Large-scale desktop apps, commercial software |
| **wxPython** | No (`pip install wxpython`) | 100% native platform widgets | Apps requiring strict native OS integration |
| **Flet** | No (`pip install flet`) | Flutter Material Design | Cross-platform desktop, web, and mobile apps |
| **PyWebView** | No (`pip install pywebview`) | HTML/CSS/JS in native window | Web developers building desktop apps |

---

## Minimal Tkinter Application (Zero Dependencies)

Because `tkinter` is part of Python's standard library, it runs immediately on any Windows, macOS, or Linux machine with Python installed:

```python
import tkinter as tk
from tkinter import ttk, messagebox

def on_click():
    name = entry.get().strip()
    if name:
        messagebox.showinfo("Greeting", f"Hello, {name}! Welcome to ProjectPy.")
    else:
        messagebox.showwarning("Warning", "Please enter your name.")

# 1. Create main window
root = tk.Tk()
root.title("ProjectPy Desktop")
root.geometry("350x150")

# 2. Add styled widgets using ttk (themed Tk)
frame = ttk.Frame(root, padding=20)
frame.pack(fill="both", expand=True)

label = ttk.Label(frame, text="Enter your name:")
label.pack(anchor="w")

entry = ttk.Entry(frame, width=30)
entry.pack(fill="x", pady=5)

button = ttk.Button(frame, text="Submit", command=on_click)
button.pack(pady=10)

# 3. Start GUI event loop
root.mainloop()
```

---

## How do I prevent long-running tasks from freezing the GUI?

All GUI frameworks run an event loop on the main thread. If you perform a heavy computation or make a blocking network request on the main thread, the UI window stops responding:

### Solution: Worker Thread + Queue

```python
import threading
import time
import tkinter as tk
from tkinter import ttk

def heavy_computation(progress_callback):
    """Worker task executing on a background thread."""
    for i in range(1, 101):
        time.sleep(0.02)  # Simulate work
        progress_callback(i)

def start_task():
    button.config(state="disabled")
    
    def worker():
        heavy_computation(update_progress)
        # Re-enable button on GUI thread
        root.after(0, lambda: button.config(state="normal"))
        
    threading.Thread(target=worker, daemon=True).start()

def update_progress(percent):
    # Schedule UI update safely on the Tkinter main thread
    root.after(0, lambda: progress_bar.config(value=percent))

root = tk.Tk()
progress_bar = ttk.Progressbar(root, maximum=100)
progress_bar.pack(fill="x", padx=20, pady=10)

button = ttk.Button(root, text="Start Heavy Task", command=start_task)
button.pack(pady=5)

root.mainloop()
```
