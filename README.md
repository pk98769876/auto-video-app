# Auto Web App Generator (GUI + Web Version Ready)
# Input: User text/script
# Output: Hosted web app using Replit API or local deployment

import os
from pathlib import Path
import tkinter as tk
from tkinter import messagebox, filedialog

def generate_react_app(script_text, output_dir):
    output_path = Path(output_dir)
    src_dir = output_path / "src"
    public_dir = output_path / "public"

    src_dir.mkdir(parents=True, exist_ok=True)
    public_dir.mkdir(parents=True, exist_ok=True)

    app_js = f'''
import React, {{ useState }} from "react";

const scenesFromText = (text) => {{
  return text.split(/[.!?]/).filter(Boolean).map((line, i) => ({
    id: i + 1,
    text: line.trim(),
    image: `https://source.unsplash.com/800x450/?nature,${{i}}`,
    voice: `Voiceover for scene ${{i + 1}}`
  }));
}};

export default function App() {{
  const [script, setScript] = useState("{script_text}");
  const [scenes, setScenes] = useState(scenesFromText("{script_text}"));

  return (
    <div className="p-6 max-w-4xl mx-auto">
      <h1 className="text-3xl font-bold mb-4">Auto Generated Video App 🎬</h1>
      <div className="mt-8 space-y-6">
        {{scenes.map((scene) => (
          <div key={{scene.id}} className="border p-4 rounded shadow">
            <img
              src={{scene.image}}
              alt={{`Scene ${{scene.id}}`}}
              className="w-full h-48 object-cover rounded mb-2"
            />
            <p className="font-semibold mb-1">Scene {{scene.id}}</p>
            <p className="mb-2">{{scene.text}}</p>
            <p className="text-sm text-gray-500">🔊 {{scene.voice}}</p>
          </div>
        ))}}
      </div>
    </div>
  );
}}
'''

    index_js = '''
import React from "react";
import ReactDOM from "react-dom/client";
import "./index.css";
import App from "./App";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
'''

    index_html = '''
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Auto App</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
'''

    package_json = '''{
  "name": "auto-app",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-scripts": "5.0.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build"
  }
}'''

    index_css = 'body { margin: 0; font-family: sans-serif; background: #f0f0f0; }'

    (src_dir / "App.js").write_text(app_js)
    (src_dir / "index.js").write_text(index_js)
    (src_dir / "index.css").write_text(index_css)
    (public_dir / "index.html").write_text(index_html)
    (output_path / "package.json").write_text(package_json)

    print(f"✅ React App generated at: {output_path}")


def launch_gui():
    def on_generate():
        text = input_text.get("1.0", tk.END).strip()
        if not text:
            messagebox.showwarning("Missing Text", "Please enter some text.")
            return
        directory = filedialog.askdirectory(title="Select Output Folder")
        if directory:
            generate_react_app(text, directory)
            messagebox.showinfo("Success", f"App created in: {directory}")

    root = tk.Tk()
    root.title("One-Click Web App Generator")

    tk.Label(root, text="Paste your story or script below:").pack(pady=5)
    input_text = tk.Text(root, wrap=tk.WORD, width=80, height=20)
    input_text.pack(padx=10, pady=10)

    tk.Button(root, text="Generate Web App", command=on_generate, bg="#4CAF50", fg="white", padx=10, pady=5).pack()

    root.mainloop()


if __name__ == "__main__":
    launch_gui()
