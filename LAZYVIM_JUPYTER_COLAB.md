# Jupyter notebooks in LazyVim (and Google Colab)

Yes. LazyVim can be the editor; Colab can be one place you **run** the same `.ipynb`. You already have the Neovim side wired: `~/.config/nvim/lua/plugins/jupyter.lua` (jupytext + molten + quarto).

Colab does **not** run inside Neovim. Two environments share a notebook file.

---

## What you already have

| Piece | Role |
|---|---|
| `jupytext.nvim` | Open `.ipynb` as markdown/quarto in the buffer |
| `molten-nvim` | Run cells with a **local** Jupyter kernel |
| `quarto-nvim` | `<leader>rc` run cell, `<leader>rA` run all |
| Jupyter in `~/.local/share/nvim/venv` | Lab/kernel already installed |

You also have LazyVim **Python** extra, so LSP works in those cells.

---

## Local practice in LazyVim (fastest)

From the project dir:

```bash
cd ~/Desktop/py-biostats
nvim lecture2_practice.ipynb
```

If the file does not exist yet, create a stub first:

```bash
~/.local/share/nvim/venv/bin/python -c "
import nbformat as nbf
nb = nbf.v4.new_notebook()
nb.cells = [
    nbf.v4.new_markdown_cell('# MSBD520 Lecture 2 practice'),
    nbf.v4.new_code_cell('import pandas as pd\nimport numpy as np'),
]
nbf.write(nb, 'lecture2_practice.ipynb')
"
```

Then in Neovim:

1. Open `lecture2_practice.ipynb` — it should look like quarto (` ```{python} ` cells).
2. `:MoltenInit` or `<leader>mi` — pick the kernel (the nvim venv is fine).
3. `<leader>rc` — run current cell. Output shows as virtual text / output window (`<leader>mo` to enter it).
4. Write the file as usual. jupytext writes the real `.ipynb` on disk.

Cell syntax in the buffer:

````markdown
```{python}
import pandas as pd
```
````

That **is** the notebook. You are not fighting JSON.

---

## Using Google Colab and LazyVim

Same file, two runtimes.

### Simple loop

1. Edit and run locally in LazyVim (molten).
2. Upload `lecture2_practice.ipynb` to [colab.research.google.com](https://colab.research.google.com) → File → Upload, **or** put the folder in Google Drive and `File → Open notebook → Drive`.
3. After Colab edits: Download `.ipynb` back into `~/Desktop/py-biostats` and reopen in nvim.

### Less painful loop (git)

- Push the repo (or this folder) to GitHub.
- In Colab: File → Open notebook → GitHub.
- After Colab work: download or commit from Colab (File → Save a copy in GitHub) and `git pull` on the Mac.

Do not expect molten to talk to a Colab GPU kernel. Colab’s kernel is in the browser; molten uses a kernel on this machine.

---

## Packages for lecture 2–3 drills

The nvim venv may not have pandas/numpy/matplotlib. Either:

```bash
~/.local/share/nvim/venv/bin/pip install pandas numpy matplotlib seaborn scipy
```

or create a project venv and, after `:MoltenInit`, pick **that** kernel.

Colab already has those packages.

---

## Keys worth remembering

| Key | Action |
|---|---|
| `<leader>mi` | Start kernel |
| `<leader>rc` | Run cell |
| `<leader>ra` | Run this cell and above |
| `<leader>rA` | Run all |
| `<leader>mo` / `<leader>mh` | Show / hide output |
| `<leader>mv` | Run visual selection |
