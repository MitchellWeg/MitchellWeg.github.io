---
title: "Embedding a live Jupyter REPL"
date: "2023-04-27"
tags: ["jupyter", "python", "ci/cd"]
---

### Preamble

To further illustrate a point, it is sometimes nice to show some code. Within markdown, you can easily achieve this using either backticks (\`) on each side of the code, or using triple backticks to create a code-block.
However, it is not interactive. And if the reader is feeling really lazy, they won't have the (mental) energy to copy and paste your code into an editor, and run it through a compiler. We can do better. Lets give the reader
the option to run our code directly on our website.

We can harnass the power of Jupyter, a notebook style editor, directly on our website. We can use it's WebAssembly counterpart, called Jupyter Lite, to display and run it directly on our website.

### Installing
Lets first test it locally. Create a virtual environment using: `virtualenv env && source env/bin/activate`. This is not necessary, but this prevents conflicts with globally installed packages.

Lets then install the required packages:

```
pip install jupyter
pip install jupyterlite
```

Along with the Jupyter suite, it will also install the required Python libraries.


To run the server: `jupyter lite serve`. Then go to your browser and navigate to: `localhost:8000/repl/index.html`, and you should be greeted by a Python REPL.


#### Using
Jupyter Lite uses iframes to display it contents, thus you can use them in your HTML:
```
<iframe
  src="http://localhost:8000/repl/index.html?kernel=python"
  height="550px"
  width="100%"
></iframe>
```

This will display its default Python REPL:

(**Note:** it needs some time to initialize the WASM Python backend, so you will see a '...' before you can type something.)

Try it! Type something like:
```python
for i in range(0,10):
  print("hello world")
```

<iframe
  src="http://localhost:8000/repl/index.html?kernel=python"
  height="550px"
  width="100%"
></iframe>


#### GitHub CI/CD
Now lets make it interactive for the readers. This website is currently hosted on GitHub pages, and we can use the CI/CD of GitHub to automatically build and deploy our code.

You can create the CI/CD through github itself, or create a `.github/workflows/pages.yml` file manually. In that file, we need to instruct our deployment to first build Jupyter for us. So lets first setup Python:

```
- name: Setup Python
  uses: actions/setup-python@v4
  with:
    python-version: '3.10'
```

Then, instruct it to install the Jupyter Lite dependencies: 

```
- name: Install Jupyter dependencies
  run: |
    pip install -r requirements.txt
```

Then build Jupyter lite:

```
- name: Build Jupyter 
  run: |
    jupyter lite build
```

Then `git add .github/workflows/pages.yml`, commit and push it, and the REPL should be visible on your website.

