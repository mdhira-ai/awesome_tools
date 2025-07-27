---
sidebar_label: "Gui Framework"
sidebar_position: 2
---

# python + nextjs + pywebview

Read this for more - https://pywebview.flowrl.com/api/

## **create folder** 

| folder name | cmd                                                                                         |
| ----------- | ------------------------------------------------------------------------------------------- |
| backend     | uv init <br></br> uv run main.py                                                               |
| frontend    | npm create vite@latest . or  npx create-next-app@latest <br></br> npm install <br></br> npm build |


<br></br>
## **we need to install some lib** 
	
	`uv add pywebview flask flask-cors pyinstaller` 

## **folder structure**

```plaintext
frontend/
├── app/
├── package.json
└── out/

backend/
├── .venv/
├── main.py
├── build/
└── dist/
```



```python
#main.py
import webview
import mimetypes

# Set the default mimetype for .wasm files
mimetypes.add_type("application/wasm", ".wasm")

# Set the default mimetype for .js files
mimetypes.add_type("application/javascript", ".js")

# Set the default mimetype for .css files
mimetypes.add_type("text/css", ".css")

# Set the default mimetype for .json files
mimetypes.add_type("application/json", ".json")

# Set the default mimetype for .html files
mimetypes.add_type("text/html", ".html")

# Set the default mimetype for .png files
mimetypes.add_type("image/png", ".png")

# Set the default mimetype for .jpg files
mimetypes.add_type("image/jpeg", ".jpg")

# Set the default mimetype for .gif files
mimetypes.add_type("image/gif", ".gif")

# Set the default mimetype for .ico files
mimetypes.add_type("image/x-icon", ".ico")

  
def create_window():

    title = "myapp"
    window = webview.create_window(
        title, "../frontend/out/index.html", width=800, height=600
    )
    return window


def main():
    # Create a webview window
    window = create_window()
    # Start the webview event loop
    webview.start(debug=True)

if __name__ == "__main__":

    main()
```



```javascript
/* next.config.ts */
import type { NextConfig } from "next";
const nextConfig: NextConfig = {
  /* config options here */
  output: 'export',
  trailingSlash: true,
  images: {
    unoptimized: true
  },
};
export default nextConfig;
```


## **build the exe file**
 
- [ ] create backend/setup.py file
- [ ] create backend/version.py file
- [ ] copy below code and paste it according to the file
- [ ] uv run setup.py
- [ ] you can use inno soft for packaging

```python
#setup.py

import subprocess
import sys
import os
import shutil
from version import __version__, __app_name__, __company__, __description__

def create_version_file():
    """Create a version file for PyInstaller with proper encoding"""
    # Convert version to comma-separated format
    version_parts = __version__.split('.')
    while len(version_parts) < 4:
        version_parts.append('0')
    version_tuple = ','.join(version_parts)
    version_template = f"""# UTF-8

# For more details about fixed file info 'ffi' see:
# http://msdn.microsoft.com/en-us/library/ms646997.aspx

VSVersionInfo(

  ffi=FixedFileInfo(

    filevers=({version_tuple}),

    prodvers=({version_tuple}),

    mask=0x3f,

    flags=0x0,

    OS=0x40004,

    fileType=0x1,

    subtype=0x0,

    date=(0, 0)

  ),

  kids=[

    StringFileInfo(

      [

      StringTable(

        u'040904B0',

        [StringStruct(u'CompanyName', u'{__company__}'),

        StringStruct(u'FileDescription', u'{__description__}'),

        StringStruct(u'FileVersion', u'{__version__}'),

        StringStruct(u'InternalName', u'{__app_name__}'),

        StringStruct(u'LegalCopyright', u'Copyright (c) {__company__}'),

        StringStruct(u'OriginalFilename', u'{__app_name__.replace(" ", "")}.exe'),

        StringStruct(u'ProductName', u'{__app_name__}'),

        StringStruct(u'ProductVersion', u'{__version__}')])

      ]),

    VarFileInfo([VarStruct(u'Translation', [1033, 1200])])

  ]

)

"""

    # Write with UTF-8 encoding explicitly

    with open('version_info.txt', 'w', encoding='utf-8') as f:

        f.write(version_template)

  

def build_executable():

    print(f"Building executable v{__version__} with PyInstaller...")

    create_version_file()

    # PyInstaller command with version info

    cmd = [

        'pyinstaller',

        '--onefile',

        '--windowed',

        '--add-data', 'frontend/dist;frontend/dist',

        '--version-file', 'version_info.txt',

        '--name', __app_name__.replace(' ', ''),

        '--hidden-import', 'pkg_resources.py2_warn',

        'main.py'

    ]

    subprocess.run(cmd, check=True)

    # Clean up

    if os.path.exists('version_info.txt'):

        os.remove('version_info.txt')

  

def main():

    try:

        build_executable()

        print(f"Build completed! {__app_name__} v{__version__} is ready in the 'dist' folder.")

    except subprocess.CalledProcessError as e:

        print(f"Build failed: {e}")

        sys.exit(1)

  

if __name__ == '__main__':

    main()

```


```python
#version.py
__version__ = "1.0.1"
__app_name__ = "PyWebview"
__company__ = "My Company"
__description__ = "PyWebview Flask React Application"

```