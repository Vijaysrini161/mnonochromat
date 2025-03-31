# mnonochromat
import tkinter as tk
from tkinter import filedialog, messagebox
from PIL import Image, ImageTk
import os

def open_image():
    """Open an image file from the gallery."""
    file_path = filedialog.askopenfilename(filetypes=[("Image files", "*.jpg;*.png;*.jpeg;*.bmp;*.gif")])
    
    if not file_path:
        return
    
    global img, img_path, original_img
    img_path = file_path
    original_img = Image.open(img_path)
    
    # Display the image
    img = ImageTk.PhotoImage(original_img)
    canvas.create_image(0, 0, anchor=tk.NW, image=img)
    canvas.config(scrollregion=canvas.bbox(tk.ALL))

def convert_to_monochrome():
    """Convert the image to monochrome."""
    if not img_path:
        messagebox.showwarning("Warning", "No image selected!")
        return
    
    global monochrome_img
    monochrome_img = original_img.convert("L")  # Convert to monochrome
    img_mono = ImageTk.PhotoImage(monochrome_img)
    
    canvas.create_image(0, 0, anchor=tk.NW, image=img_mono)
    canvas.config(scrollregion=canvas.bbox(tk.ALL))
    
    # Save monochrome image for downloading
    monochrome_img.save("monochrome_temp.png")

def download_image():
    """Download the monochrome image."""
    if not hasattr(globals(), "monochrome_img"):
        messagebox.showwarning("Warning", "No monochrome image to download!")
        return

    save_path = filedialog.asksaveasfilename(defaultextension=".png",
                                             filetypes=[("PNG files", "*.png"),
                                                        ("JPEG files", "*.jpg"),
                                                        ("All files", "*.*")])
    if save_path:
        monochrome_img.save(save_path)
        messagebox.showinfo("Success", f"Image saved to {save_path}")

# GUI setup
app = tk.Tk()
app.title("Monochrome Image Converter")
app.geometry("800x600")

# Canvas to display the image
canvas = tk.Canvas(app, bg="white", width=700, height=500)
canvas.pack(expand=True, fill=tk.BOTH)

# Buttons
btn_open = tk.Button(app, text="Open Image", command=open_image)
btn_open.pack(side=tk.LEFT, padx=10, pady=10)

btn_convert = tk.Button(app, text="Convert to Monochrome", command=convert_to_monochrome)
btn_convert.pack(side=tk.LEFT, padx=10, pady=10)

btn_download = tk.Button(app, text="Download Image", command=download_image)
btn_download.pack(side=tk.LEFT, padx=10, pady=10)

# Run the app
app.mainloop()
