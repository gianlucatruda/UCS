# Useful Code Snippets

- An assortment of useful coding and command line snippets, tricks, and hacks I've collected over the past 8+ years of my work and hobbies.
- Many of these snippets make use of my [aliases](https://github.com/gianlucatruda/dotfiles/blob/master/.config/.aliases), [bash functions](https://github.com/gianlucatruda/dotfiles/blob/master/.config/.functions), and [scripts](https://github.com/gianlucatruda/dotfiles/tree/master/scripts) from [my dotfiles](https://github.com/gianlucatruda/dotfiles).

> [!WARNING]
> DISCLAIMER: This is mainly a resource for myself, made public for my own easy access and maintenance. This is for educational purposes and I accept no liabilities for any inaccuracies or for the misuse of any information presented here. If you (or I) messed something up you may violate laws or terms of service, have your security compromised, or otherwise get in trouble. These are notes for reference, so DO NOT copy-paste things you don't understand. You must read and accept the [license](LICENSE).

---

**Github statistics**

[![Gianluca's GitHub
stats](https://github-readme-stats.vercel.app/api?username=gianlucatruda&count_private=true&show=reviews,discussions_started,discussions_answered,prs_merged,prs_merged_percentage&show_icons=true&rank_icon=percentile)](https://github.com/gianlucatruda)

[![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=gianlucatruda&langs_count=12&count_private=true&layout=compact&size_weight=0.08&count_weight=0.92&&hide=jupyter+notebook)](https://github.com/gianlucatruda)

---

Download a file from the URL (via brower's JS console):

```javascript
var link = document.createElement("a");
link.href = "YOUR_DIRECT_FILE_URL";
link.download = "filename.mp4"; // You can specify the filename
document.body.appendChild(link);
link.click();
document.body.removeChild(link);
```

Imports and setup for standard exploratory analysis in Jupyter notebook:

```python
import numpy as np
import pandas as pd
from matplotlib import pyplot as plt
import seaborn as sns

%matplotlib inline
%config InlineBackend.figure_format = 'retina'

# Set sensible defaults
sns.set()
sns.set_style("ticks")
sns.set_context('paper')
```

Connect Colab notebook to Google Drive

```python
from google.colab import drive
drive.mount('/content/gdrive')
```

Automatically display execution time for each notebook cell

```python
!pip install ipython-autotime
%load_ext autotime
```

Set high-resolution figures for inline matplot results

```python
%matplotlib inline
%config InlineBackend.figure_format = 'retina'
```

Python set recursion limit

```python
sys.setrecursionlimit(2000)
```

Automatically memoise Python functions

```python
from functools import lru_cache

@lru_cache(maxsize=10000)
def my_func(n):
    return n+1
```

`@lru_cache(maxsize=4)` will do the same thing, but least-recently-used with a specified size.

Logging tracebacks when using Python exception handling:

```python
import traceback

try:
    raise Exception('test')
except Exception as e:
    traceback.print_exc()
    ex_str = traceback.format_exc()
```

We can call `Path.mkdir(exist_ok=True)` to make any required directories if they don't already exist.

```python
[path.mkdir(exist_ok=True, parents=True)
 for path in [DATASET_PATH, RESULT_PATH]]
```

Make a plot with subplots with Pandas built-in plotting

```python
df.plot(subplots=True, layout=(1,2))
```

Sklearn's `train_test_split` has build in stratify functionality

```python
from sklearn import cross_validation, datasets

X = iris.data[:,:2]
y = iris.target

cross_validation.train_test_split(X,y,stratify=y)
```

> For example, if variable y is a binary categorical variable with values 0 and 1 and there are 25% of zeros and 75% of ones, stratify=y will make sure that your random split has 25% of 0's and 75% of 1's.

Pandas: Collapse multi-index by relabelling columns:

```python
# We collapse the multi-index and concatenate the names (beautiful!)
df.columns = df.columns.to_series().str.join('_')
df.reset_index(inplace=True)

```

Applying a multi-variable function over a pandas dataframe:

```python
df_aw_loc.apply(lambda x: pgh.encode(x['aw_loc_lat'], x['aw_loc_lon'], 7), axis=1)
```

NB: `axis=1`

Recursive feature elimination with Scikit-learn and Pandas:

```python
from sklearn.feature_selection import RFE
rfe = RFE(DecisionTreeRegressor(), 1).fit(X, y)
# rfe = RFE(LinearRegression(normalize=True), 1).fit(X, y)
feats = pd.DataFrame.from_dict({data.columns[i]: v for i,v in enumerate(rfe.ranking_)}, orient='index', columns=['ranking'])
feats.sort_values(by='ranking').head(30)
```

```python
# Recursive feature elimination on LR
rfe = RFE(LinearRegression(), FEATS).fit(X_train, y_train)
X_best_train = X_train[:,rfe.support_]
X_best_test = X_test[:,rfe.support_]
```

Plot using Pandas, but onto subplots:

```python
import matplotlib.pyplot as plt

fig, axes = plt.subplots(nrows=2, ncols=2)

df1.plot(ax=axes[0,0])
df2.plot(ax=axes[0,1])
```

— via [StackOverflow](https://stackoverflow.com/questions/22483588/how-can-i-plot-separate-pandas-dataframes-as-subplots)

Compress images (esp. for web use) using Imagemagick

```bash
convert <src>.jpg \
-sampling-factor 4:2:0 \
-strip \
-quality 85 \
-interlace Plane \
-gaussian-blur 0.05 \
-colorspace RGB \
<dest>.jpg
```

— via [this post](https://dev.to/feldroy/til-strategies-for-compressing-jpg-files-with-imagemagick-5fn9)

Spectral analysis (with FFT) via [this video](https://youtu.be/UjUKaQKniLM)

```python
Fs = 300
t = np.arange(0, 1, 1/Fs)
f = 20

print(t.shape)

test_signal = 0.5*np.sin(t*np.pi*f*1.2) + 1.3*np.sin(t*np.pi*f*3.2) + 2.5*np.sin(t*np.pi*f*4.5)
plt.plot(t, test_signal)
plt.show()

from scipy.fftpack import fft

# Generate frequency axis
n = np.size(t)
print(n)
fr = (Fs//2) * np.linspace(0, 1, n//2)

# Compute FFT
freqs = abs(fft(test_signal)[0:np.size(fr)])
freqs_m = (2/n) * abs(freqs)

print(fr.shape, freqs.shape, freqs_m.shape)

# Plot results
# plt.plot(fr, freqs)
# plt.show()
plt.plot(fr, freqs_m)
plt.show()
```

Profile a Python script using the [line_profiler](https://github.com/pyutils/line_profiler). Simply add the `@profile` decorator above the function you want profiled, then run:

```bash
kernprof -v -l <script>.py
```

This will display the profile results immediately because of the `-v` option.

Get number of missing values by row in **Pandas**:

```python
df.isnull().sum()
```

Perform fuzzy matching using the [fuzzywuzzy](https://github.com/seatgeek/fuzzywuzzy) library:

```python
# get the top 10 closest matches to "south korea"
matches = fuzzywuzzy.process.extract("south korea", countries, limit=10, scorer=fuzzywuzzy.fuzz.token_sort_ratio)
```

Get the current datetime stamp in Python:

```python
now = datetime.now().strftime("%m-%d-%H_%M_%S")
```

Plotting 3D figures with matplotlib:

```python
X = np.linspace(0, np.pi)
Y = np.linspace(0, np.pi)
X, Y = np.meshgrid(X, Y)
Z = np.sin(Y**2 + X) - np.cos(Y - X**2)

fig = plt.figure()
ax = fig.add_subplot(projection='3d')
ax.plot_surface(X, Y, Z, cmap='copper')
```

Scikit-learn transformations without losing column names:

```python
X_test = pd.DataFrame(StandardScaler().fit_transform(X_test), columns=X_test.columns)
```

Make matplotlib figures readable the easy way:
Increase resolution:

```python
plt.figure(dpi=300)
```

[Scaling elements with Seaborn](https://seaborn.pydata.org/tutorial/aesthetics.html#scaling-plot-elements) (`paper`, `talk`, `notebook`, `poster`):

```python
sns.set_context("paper")
```

— via [a twitter thread](https://twitter.com/MilesCranmer/status/1290800078702116869)

LaTeX in matplotlib (use r-strings):

```python
ax[0][i].set_title(r"$\theta=\frac{\pi}{4}$")
```

Gives: $\theta=\frac{\pi}{4}$

Play YouTube videos above 2x speed:

```javascript
document.getElementsByTagName("video")[0].playbackRate = x;
```

Adjusting pandas legends and subplots via [here](https://stackoverflow.com/questions/46266700/how-to-add-legend-below-subplots-in-matplotlib) and [here](https://stackoverflow.com/questions/4700614/how-to-put-the-legend-out-of-the-plot) and [here](https://stackoverflow.com/questions/6541123/improve-subplot-size-spacing-with-many-subplots-in-matplotlib):

```python
fig.legend(handles, labels,
            framealpha=1.0,
            loc='lower center',
            bbox_to_anchor=(0.5, -0.2),
            fancybox=False,
            shadow=False,
            ncol=3)
subplots_adjust(left=None, bottom=None, right=None, top=None, wspace=None, hspace=None)
plt.tight_layout()
plt.show()
```

**Keep clicking in a window to prevent timeouts**

```javascript
function KeepClicking() {
  console.log("Clicking");
  document.querySelector("<DOM_element>").click();
}
setInterval(KeepClicking, 60000);
```

(NOTE: Make sure you check if this violates ToS first)

You can reference multi-indexed columns in pandas using tuples:

```python
df[df[('my_column', 'mean')] == 2]
```

Merge all files in the current directory into a single `.pdf` file called `output.pdf`

```bash
mergepdf -o output.pdf `ls`
```

Relies on the alias:

```bash
mergepdf='/System/Library/Automator/Combine\ PDF\ Pages.action/Contents/Resources/join.py'
```

Suppress Python warnings within a block of code, from a [stackoverflow question](https://stackoverflow.com/questions/48828824/disable-warnings-in-jupyter-notebook#52294671):

```python
import warnings

def function_that_warns():
    warnings.warn("deprecated", DeprecationWarning)

with warnings.catch_warnings():
    warnings.simplefilter("ignore")
    function_that_warns()  # this will not show a warning
```

Re-load tmux configuration without restarting ([source](https://sanctum.geek.nz/arabesque/reloading-tmux-config/)): Ctrl + B `:source-file ~/.tmux.conf`

Interactive Find-Replace some text in vim ([source](https://medium.com/@schtoeffel/you-don-t-need-more-than-one-cursor-in-vim-2c44117d51db)):

- Select text by searching `/<text>` and hitting return
- Type `cgn` and hit return
- Make the change to one instance and hit Esc
- Repeatedly press the `.` key to apply to neighbouring matches
- Skip instances with `n` key

(But you should probably have just used `:%s/foo/bar/gc`)

Seaborn can do beautiful pairwise-relationship plots:

```python
import seaborn as sns
sns.pairplot(data)
```

**Spellcheck in Vim (British English), following [this guide](https://www.linux.com/training-tutorials/using-spell-checking-vim/)**:

- Enable it for the current buffer with `:setlocal spell spelllang=en_gb`.
- Enable generall with `:set spell spelllang=en_gb`.
- Turn it off with `:set nospell`.
- To move to a misspelled word, use `]s` and `[s`.
- Once the cursor is on the word
  - use `z=`, and Vim will suggest a list of alternatives
  - `zg` will add the word to vim's dictionary
  - `zw` marks a word as incorrect

**Automatic line wrap in Vim**

- Hit `gqq` to reformat a large block of text from a single line into multiple lines. Or visually select and then hit `gq`.
- This won't actually affect Vim or LaTeX rendering, as there are no newlines between the lines.
- You need to set textwrapping appropriately beforehand via: `:set tw=80`
- To join lines, use `J`, with putting spaces.
- To join lines without spaces, use `gJ`.

**Change capitalisation in Vim**

- `gu` to uncapitalise: e.g. `gu$` will uncapitalise the rest of the line.
- `gU` to capitalise: e.g. `gU$` will capitalise the rest of the line.
- The tilde `~` will toggle the capitalisation of the current character.
- `gUaw` will capitalise the entire current word (with whitespace?)
- `guaw` will uncapitalise the entire current word (with whitespace?)

**Handy file navigation in Vim**

- If you have selected text that is the name of a file in the directory, just hit `gf` to go to that file in vim.
- To go back to the file you were in, hit `^` (i.e. ctrl+6 on macOS).

**Random useful Vim stuff**

- `gv` jumps to previously selected text.
- If you make a substitution (find-replace) like `:s/hello/goodbye/` it will apply only to the current line. You can then use `g&` to apply the substitution to the whole text.
- Simplest find-replace in Vim is to search with `/hello`, hit enter, then use `cgn` to replace the word. With `n` and `.` this can be applied over multiple occurrences safely.
- In insert mode, `ctrl+t` and `ctrl+d` (un)indent the line.
- Move cursor to beginning of line with `0`, but to first non-whitespace character of line with `_` (via [this post](https://superuser.com/a/852968)).

Tutorial on **creating annotated heatmaps in pyplot**: https://matplotlib.org/stable/gallery/images_contours_and_fields/image_annotated_heatmap.html#sphx-glr-gallery-images-contours-and-fields-image-annotated-heatmap-py

The [pigar library](https://github.com/damnever/pigar) can automatically build Python requirements.txt files for whole directories. It's like pipreqs, but also works on .ipynb files and can show which lines the imports are on.

**Find and replace all occurrences in all files in a directory**

Inspired by [this stackoverflow answer](https://stackoverflow.com/questions/6758963/find-and-replace-with-sed-in-directory-and-sub-directories#6759339).

```bash
find ./ -exec sed -i '' 's/find/replace/' {} \;
```

(Note this differs by platform, so check the docs for GNU vs BSD differences.)

This may require the [this hack to work on macOS](https://stackoverflow.com/questions/19242275/re-error-illegal-byte-sequence-on-mac-os-x)

```bash
export LC_CTYPE=C; export LANG=C
:set spell spelllang=en_gb
```

**Create an encrypted archive** (user will be prompted for a password):

```bash
zip -e -r compressed.zip path/to/dir
```

(NOTE: this is very weak encryption that can easily be cracked. Do not rely on this beyond slowing someone down. Install and use `7z` with AES-256 if you need real encruption.)

Making expandible sections in Jekyll, via [this post](https://www.tomordonez.com/jekyll-text-expand-collapsible-markdown/)

```
<details>
	<summary>Click to expand</summary>
	Long content here
	and here
</details>
```

Iteratively generate boilerplate LaTeX figures from a directory of well-named images:

```python
from pathlib import Path
images = [i for i in Path('./').glob("*.png")]
for img in sorted(images):
    ref = str(img.stem).replace('_', '\_')
    print(f"""
        \\begin{{figure}}[ht]
            \centering
            \includegraphics[width=\\textwidth]{{figs/{img.name}}}
            \caption{{TODO:{ref}}}
            \label{{fig:{ref}}}
        \end{{figure}}
        """)
```

[Watermark extension](https://github.com/rasbt/watermark) for iPython / Jupyter

```bash
pip install watermark
```

```python
%load_ext watermark
%watermark
```

Cool 2D and 3D interactive plots with plotly, from [this guide](https://www.kaggle.com/sivakarsiva/pca-vs-lda-vs-umap-vs-t-sne).

```python
import plotly.io as plt_io
import plotly.graph_objects as go

def plot_2d(component1, component2):

    fig = go.Figure(data=go.Scatter(
        x = component1,
        y = component2,
        mode='markers',
        marker=dict(
            size=20,
            color=y, #set color equal to a variable
            colorscale='Rainbow', # one of plotly colorscales
            showscale=True,
            line_width=1
        )
    ))
    fig.update_layout(margin=dict( l=100,r=100,b=100,t=100),width=2000,height=1200)
    fig.layout.template = 'plotly_dark'

    fig.show()

def plot_3d(component1,component2,component3):

    fig = go.Figure(data=[go.Scatter3d(
        x=component1,
        y=component2,
        z=component3,
        mode='markers',
        marker=dict(
            size=10,
            color=y,                # set color to an array/list of desired values
            colorscale='Rainbow',   # choose a colorscale
            opacity=1,
            line_width=1
        )
    )])

    # tight layout
    fig.update_layout(margin=dict(l=50,r=50,b=50,t=50),width=1800,height=1000)
    fig.layout.template = 'plotly_dark'

    fig.show()

```

**Adding styles to (HTML) pandas dataframes (in notebooks)**

From [this article](https://towardsdatascience.com/prettifying-pandas-dataframes-75c1a1a6877d). The `Styler` an HTML `<table>`, which can be styled with CSS.

```python
cell_hover = {  # for row hover use <tr> instead of <td>
    'selector': 'td:hover',
    'props': [('background-color', '#ffffb3')]
}
index_names = {
    'selector': '.index_name',
    'props': 'font-style: italic; color: darkgrey; font-weight:normal;'
}
headers = {
    'selector': 'th:not(.index_name)',
    'props': 'background-color: #000066; color: white;'
}
s.set_table_styles([cell_hover, index_names, headers])
```

```python
s.set_table_styles([  # create internal CSS classes
    {'selector': '.true', 'props': 'background-color: #e6ffe6;'},
    {'selector': '.false', 'props': 'background-color: #ffe6e6;'},
], overwrite=False)
cell_color = pd.DataFrame([['true ', 'false ', 'true ', 'false '],
                           ['false ', 'true ', 'false ', 'true ']],
                          index=df.index,
                          columns=df.columns[:4])
s.set_td_classes(cell_color)
```

A very detailed guide is available [here in the Pandas docs](https://pandas.pydata.org/pandas-docs/stable/user_guide/style.html).

**Ignore warnings in Python**

From [this StackOverflow thread](https://stackoverflow.com/questions/879173/how-to-ignore-deprecation-warnings-in-python).

```python
import warnings
warnings.filterwarnings("ignore", category=DeprecationWarning)
```

**Using Jupyterlab inside pipenv (custom kernel)**

```bash
pipenv install ipykernel
pipenv shell
python -m ipykernel install --user --name=<my-virtualenv-name>
pipenv run jupyter lab
```

Then in Jupyterlab, select the kernel named `<my-virtualenv-name>`.
— via [this stackoverflow thread](https://stackoverflow.com/questions/47295871/is-there-a-way-to-use-pipenv-with-jupyter-notebook)

**Importing Python modules dynamically based on a directory**

This imports the `wgan_gp.py` script from `path/to/my/src` into the system path so it's kept separate from the rest of the notebook. Inspired by [this StackOverflow thread](https://stackoverflow.com/questions/67631/how-to-import-a-module-given-the-full-path).

```python
import sys
from pathlib import Path

SRCDIR = Path("path/to/my/src")
sys.path.append(str(SRCDIR))

# Optional use autoreload to import latest version on each re-run
%load_ext autoreload
%autoreload 2

from wgan_gp import WGAN_GP_Synthesiser
```

**Use log scales for axes in matplotlib**

```python
ax.set_yscale('log')
ax.set_xscale('log')
```

**Sort columns of DataFrame alphabetically** ([ref.](https://www.statology.org/pandas-sort-columns-by-name/))

```python
df = df[sorted(df.columns)]
```

**Stage only modified/deleted files with git**

```bash
git add -u
```

**Nvidia GPU status**

To continuously probe `nvidia-smi` (every 10 seconds) for only the memory:

```bash
nvidia-smi --loop=10 -q --display="MEMORY"
```

**When you want to convert all possible columns in a pandas dataframe to numeric types as simply as possible**
Via [this stackoverflow post](https://stackoverflow.com/questions/34844711/convert-entire-pandas-dataframe-to-integers-in-pandas-0-17-0)

```python
df = df.apply(pd.to_numeric, errors='ignore')
```

**Recursively delete files matching a pattern**

Via [this stackexchange thread](https://unix.stackexchange.com/questions/84852/delete-files-matching-pattern).

```bash
find /path/to/directory -type f -name '<regex_pattern>' -delete
```

**Set YouTube volume via console**

```javascript
document.getElementsByClassName("video-stream")[0].volume = 0.5;
```

**Only plot half of correlation matrix**
Via [this article](https://www.kdnuggets.com/2021/04/awesome-tricks-best-practices-kaggle.html).

```python
houses = pd.read_csv('data/melb_data.csv')

# Calculate pairwise-correlation
matrix = houses.corr()

# Create a mask
mask = np.triu(np.ones_like(matrix, dtype=bool))

# Create a custom diverging palette
cmap = sns.diverging_palette(250, 15, s=75, l=40,
                             n=9, center="light", as_cmap=True)

plt.figure(figsize=(16, 12))

sns.heatmap(matrix, mask=mask, center=0, annot=True,
             fmt='.2f', square=True, cmap=cmap)

plt.show();

```

**Make data science code deterministic (fixed seed)**
Via [this tweet](https://twitter.com/kastnerkyle/status/1473361479143460872).

```python
def seed_everything(seed=42):
    random.seed(seed)
    np.random.seed(seed)
    os.environ["PYTHONHASHSEED"] = str(seed)
    torch.manual_seed(seed)
    torch.cuda.manual_seed_all(seed)
    torch.backends.cudnn.deterministic = True
    torch.backends.cudnn.benchmark = False

```

**Git commit history**
Via [this article](https://www.deployhq.com/git/viewing-previous-commits).

- `git log -p` shows you the line changes made.
- `git show <commit_hash>` shows line changes to a specified commit.

**Re-run bash command with timeout and sleep**

Will try run `<command>` every 10 seconds and timeout if it runs for more than 5s

```bash
until timeout 5s sleep 10; do <command>; done
```

In general, `timeout 10 <command>` will run a command and terminate after 10s.

**Open URLs in browser with Python**
via [this post](https://www.csestack.org/code-python-to-open-url-in-browser/)

```python
# importing webbrowser python module
import webbrowser
#Assigning URL to be opened
strURL = "http://www.csestack.org"
#Open url in default browser
webbrowser.open(strURL, new=2)
```

**Enabling notifications in your Jupyter notebooks**
Via [this post](https://towardsdatascience.com/enabling-notifications-in-your-jupyter-notebooks-for-cell-completion-68d82b02bbc6). Uses [jupyternotify](https://github.com/ShopRunner/jupyter-notify).

Install

```bash
pip install jupyternotify
```

Initialise

```python
%load_ext jupyternotify
```

Use

```python
%%notify -m "My message"
```

**Pyplot mosaic for subplot layouts**

Discovered from [this tweet](https://twitter.com/leifdenby/status/1491808998131126273).

```python
_mos = """
AABC
AADE
"""
fig, axes = plt.subplot_mosaic(mosaic=_mos, figsize=(11, 5))
axes["A"] # References the large region A
```

**Prune unused docker containers**

```bash
docker image prune -a
```

Helps clean things up if you got [max depth exceeded](https://stackoverflow.com/questions/47272611/docker-max-depth-exceeded) error from rebuilding layered images.

**DataFrame from normalised JSON**

Flattens nested JSON / dict into dataframe

```python
import json
with open('data/nested_array.json','r') as f:
    data = json.loads(f.read())
df_nested_list = pd.json_normalize(data)
```

**Use tee to write to stdout and a file at the same time**

Append to the given FILEs, do not overwrite:

```bash
echo "example" | tee -a FILE
```

Print standard input to the terminal, and also pipe it into another program for further processing:

```bash
echo "example" | tee /dev/tty | xargs printf "[%s]"
```

**Ping google every 60s with timestamps and write output to log**

Helpful for seeing if/when internet connection went down.

```bash
until timeout 30s sleep 60; do ping -c 5 --apple-time www.google.com; done | tee -a wifi_log.txt
```

**Remove duplicate frames in video**

> I have a 4k display but record to 1080p, bit blurry but I'm not really using it to find small copy of text, but to see general state of things. Recording in 20fps as well.
> I have a small shellscript that takes all video files recorded by OBS, and runs them through this ffmpeg command.
> Using mpdecimate removes duplicate frames, so if nothing is happening on your screen (although smaller changes gets ignored, like my clock showing the seconds), it removes those duplicate frames.
> So one ~1 minute video of you thinking for 40 seconds can get reduced to 20 seconds. Not uncommon for some of my video files to go from multi-GB to just ~100 MB when removing all the pauses.

```bash
ffmpeg -i in.mkv -map 0:v -vf mpdecimate,setpts=N/FRAME_RATE/TB out.mp4
```

https://news.ycombinator.com/item?id=32223240

**Fixing `jupyter-nbconvert` when it can't find templates on macOS (homebrew)**

```
ln -s /opt/homebrew/share/jupyter/nbconvert/ $HOME/Library/Jupyter
```

**Converting Jupyter notebooks to markdown (for Hugo blog)\***

```
python3.11 -m jupyter nbconvert --to markdown notebook.ipynb
```

(Tables output by pandas are html formatted and the conversion isn't Hugo-compatible, so needs extra steps)

**Find all files edited in the past 12 hours and tee them to a file and stdout\***

```bash
sudo find / -type f -mmin -$((12*60)) | tee find_results.txt
```

**Interactive git commands**
Using `-p` after a command lets you interactively process _parts_:

- `git add -p` to manually review each chunk for staging

Using `-i` after a command allows interactive line-by-line work.

**Advanced mkdir**

- `mkdir -p path/to/directory` will make any parents along the way!
- `mkdir -p folder/{sub1,sub2}/{sub1,sub2,sub3}` will make all permutations (i.e. 6 new folders)
- `mkdir -p folder/{1..10}/{1..100}` will make nested folders 1-100 in each folder 1-10

**Open previous shell command in editor**
`fc`

**Yanking parts of line in bash shell**
ctrl-u, ctrl-w, ctrl-y - cutting and pasting text in the command line
Unlike some other ctrl-based commands, these actually seem to work on macOS

**Compgen, fzf, and xargs wizardry**
Source: [Become a shell wizard in ~12 mins](https://www.youtube.com/watch?v=IYZDIhfAUM0)

- `compgen -c` shows all commands you can run in bash on your system.
- So `compgen -c | fzf | xargs man` lets you fuzzy-find any command on your system, hitting enter will open its manual page!

**Incredibly based neovim trick that everyone should know**

If you go between two files / buffers in the same instance, you can use `ctrl^` to rapidly toggle between them.

**Clipboard filtering macOS with pbpaste and pbcopy piping**
Via [jefftk's lesswrong post](https://www.lesswrong.com/posts/THrvd53ktGirsRBng/clipboard-filtering)

Here's a pattern I find pretty useful:

```
pbpaste | some_command | pbcopy
```

For example:

- Converting spaces to tabs, for pasting into a spreadsheet program: `pbpaste | tr ' ' '\t' | pbcopy`
- Converting tabs and newlines to html table formatting: `pbpaste | sed 's/^/<tr><td>/' | sed 's/\t/<td>/g' | pbcopy`
- Escape angle brackets and ampersands for html: `pbpaste | sed 's/&/\&amp;/g; s/</\&lt;/g; s/>/\&gt;/g;' | pbcopy` (I used this on itself before pasting into this post.)
- Convert newlines-indicate-paragraphs text to html: `pbpaste | sed 's/^/<p>/' | pbcopy` (I use this in putting together the [kids text posts](https://www.jefftk.com/news/kidsgroup).)
- Any time I want to do find-and-replace when working with software that doesn't support it well.

**Reload file into vim buffer if there are changes elsewhere**

It seems to prompt a fair bit of the time, but if not, you can always use `:e!` to discard unwritted changes and reload.

**Finding and screening files you want to delete**
This is especially useful after uninstalling something on macOS, as applications tend to bloat up Library and System directories.

```bash
sudo find //System/ -iname "*ilenam*" -type d | grep -i "filename"
```

```bash
sudo find ~/Library -iname "*ilenam*" -type d | grep -i "filename"
```

**Notes from [30 Vim commands you NEED TO KNOW (in just 10 minutes)](https://youtu.be/RSlrxE21l_k)**

- `*` matches all occurrences of word under cursor
- You're actually using sed when you type `:%s/foo/bar/g` (which changes all `foo` to `bar` in the current buffer `%`, applied globally `g`)
- `:reg` shows all registers and contents
- `"<register><motion>` is the syntax for accessing other registers. i.e. `"` followed by the register, then whatever your motion is.
- Depending on your system `*` or `+` are a register linked to system clipboard.
- `%` register always holds the current file's name.
- Macros: store and replay a series of motions
  - `q<register>` to start recording macro to register (e.g. `qh`)
  - `q` finishes recording macro.
  - `@<register>` replays the macro written to register `<register>`
  - You can apply numerics too, like `5@h` will apply macro in register `h` five times.

[[2024-07-30]] TIL about some very useful bash tricks: - `echo -e` will respect `\n` newline characters, so no more empty echo statements to spread output. - `set -x` will print out the following commands run, you can wrap a line in `(set -x; <bash>)` to only echo that line or put it at some point in the script to print all commands therafter. - `set -v` is like `set -x`, but won't resolve the inline scripts and variables. - If you enabled somewhere unwrapped, disable with `set +x` or `set +v`
[[2024-07-30]] The most based thing I've ever done in a terminal?
`find . -type f ! -path '*/.git/*' -exec echo -e "\n---\n# FILE: {}\n" \; -exec cat {} \; | iconv -t UTF-8//IGNORE | llm --model 4t --system "You are a highly-advanced 300 IQ senior systems engineer. Find security issues in these dotfiles before the intern pushes them to a public repo. Reply in concise markdown. Don't give general advice, but do a thorough code review mentioning the specific files by name." | tee ~/0-Inbox/$(ecdt)_dotfiles-report.md`

[[2024-08-17]] via [Laurie Voss (@seldo) on X](https://x.com/seldo/status/1823126087423099192)

> This absurd "one-liner" will show you the name of the command running on each port on MacOS, which is something I need to do constantly so leaving it here:
> `sudo lsof -iTCP -sTCP:LISTEN -n -P | awk 'NR>1 {print $9, $1, $2}' | sed 's/.*://' | while read port process pid; do echo "Port $port: $(ps -p $pid -o command= | sed 's/^-//') (PID: $pid)"; done | sort -n`

[[2025-04-07]] compress images and videos in a folder and then strip all metadata:

```bash
for file in *.jpg; do magick "$file" -strip -interlace Plane -quality 25% "${file%.*}_web.jpg"; done
for file in *.mp4; do ffmpeg -i "$file" -c:v libx264 -crf 32 -preset slower -c:a aac -b:a 96k -movflags +faststart "${file%.*}_web.mp4"; done
exiftool -All= *
```

**Turning in Python script into an executable with uv**
via [Lazy self-installing Python scripts with uv](https://treyhunner.com/2024/12/lazy-self-installing-python-scripts-with-uv/?featured_on=pythonbytes) and others

```
#!/usr/bin/env -S uv run --script
# /// script
# requires-python = ">=3.12"
# dependencies = [
#     "ffmpeg-normalize",
# ]
# ///
```

Make executable: `chmod +x my_script.py`

**Image magick montage**

An image magick bash script for making montages/collages of screenshots for FF colour checks:

```bash
#!/bin/bash

# Loop over each directory in the current folder
for dir in */ ; do
  # Remove trailing slash from directory name for output filename
  dirname="${dir%/}"
  echo $dirname

  # Run montage on all images in the directory, label with filename, set label size, and name output after directory
  montage -pointsize 60 -label '%t' "$dir"/*.png -tile 5x -geometry +0+0 "${dirname}_collage.png"
done

echo "Combining collages..."
montage *_collage.png -tile 1x -geometry +0+0 colourcheck.png

echo "Done"
```

**Helpful film commands from FF workflows**

Checksum all files in a directory and log to a timestamped file:

```bash
find . -type f -exec shasum -a 256 {} \; >> $(ecdt)_checksums-sha256.txt
```

Full directory tree saved to timestamped file:

```bash
tree -a //Volumes/WorkingSSD/PROJECT/ >> //Volumes/WorkingSSD/PROJECT/00_DATA_MANAGEMENT/01_META/$(ecdt)_WorkingSSD_PROJECT.txt
```

Dry run for syncing two project drives with `rsync` to see what will happend:

```bash
rsync -avh --exclude '.DS_Store' --exclude="._*" --exclude="PROXIES/*" //Volumes/WorkingSSD/PROJECT/ //Volumes/YourSSD/PROJECT/ --stats --dry-run
```

Running the sync from one drive to another, logging the verbose output of files transferred to a timestamped log file:

```bash
rsync -avh --exclude '.DS_Store' --exclude="._*" --exclude="PROXIES/*" //Volumes/WorkingSSD/PROJECT/ //Volumes/YourSSD/PROJECT/ --stats | tee ~/0-Inbox/$(ecdt)_sync.txt
```

**Docker (compose) tricks for listing all containers / services with details**

Get all running container images with tags

```bash
docker ps --format "table {{.Names}}\t{{.Image}}"
```

For compose services specifically

```bash
docker compose images
```

Get image digests (most precise pinning)

```bash
docker images --digests
```

**Download mp3 files from a website, clear existing metadata dynamically with `id3v2`**:

(Note: genre `183` is audiobook)

```bash
wget "https://website.com/file.mp3"
id3v2 --delete-all **/*.mp3
for file in *.mp3; do id3v2 --artist="Arist Name" --album="Album Name" --comment="Comment text" --genre=183 --song="${file%.*}" "$file"; done
exiftool file.mp3
```

**Useful bash tricks**

- `echo -e` will respect `\n` newline characters, so no more empty echo statements to spread output.
- `set -x` will print out the following commands run, you can wrap a line in `(set -x; <bash>)` to only echo that line or put it at some point in the script to print all commands therafter.
- `set -v` is like `set -x`, but won't resolve the inline scripts and variables.
- If you enabled somewhere unwrapped, disable with `set +x` or `set +v`
- In bash, single quotes prevent immediate execution of internal scripts, but double quotes don't.

The most based thing I've ever done in a terminal?

```bash
find . -type f ! -path '*/.git/*' -exec echo -e "\n---\n# FILE: {}\n" \; -exec cat {} \; | iconv -t UTF-8//IGNORE | llm --model 4t --system "You are a highly-advanced 300 IQ senior systems engineer. Find security issues in these dotfiles before the intern pushes them to a public repo. Reply in concise markdown. Don't give general advice, but do a thorough code review mentioning the specific files by name." | tee ~/0-Inbox/$(ecdt)_dotfiles-report.10/21/2025
```

**`ffmpeg` conversion of mp4 to GIF with 5x speedup, rescaling, and custom colour palette that reduces artifefacts and file size (GPT-4 special)**

```bash
ffmpeg -i input.mp4 -filter_complex "[0:v]fps=10,scale=1000:-1,setpts=0.2*PTS[v];[v]split[v1][v2];[v1]palettegen[p];[v2][p]paletteuse" -y output.gif
```

**A nice way to explore the logs of `llm` is with `datasette`**

```bash
datasette "$(llm logs path)"
```

**Use `wget` to download massive 70GB film render from dropbox over a resumable, bandwidth-limited connection**

```bash
wget -v --show-progress --limit-rate=2.5m --tries=200 --continue "https://www.dropbox.com/PRIVATEURL?rlkey=PRIVATEKEY&dl=1"
```

(NOTE: in the past, I've used `--no-check-certificate` when the above fails, but apparently this is very unsafe.)

**`g` is underused but deeply based in vim**
`gj`, `gk`, `g0`, `g$`
via [Vim Tips You Probably Never Heard of - YouTube](https://www.youtube.com/watch?v=bQfFvExpZDU)"

**The 5 main ways of dealing with _fallible functions_ in Rust**

(based on [A half-hour to learn Rust](https://fasterthanli.me/articles/a-half-hour-to-learn-rust#fallible-functions-result-t-e))

Functions that can fail typically return a `Result<T, E>` which is an enum over `Ok(T)` and `Err(E)`

1. If you want to panic in case of failure, you can `.unwrap()` the result.
2. Or `.expect("my custom message")`, to unwrap with a message.
3. Or you can `if let Ok(T)`
4. Or you can `match` (and propagate the error case)
5. Or, use `?` as syntactic shorthand for propagating the error (equivalent to 4)

**Reading lines from an input file in Rust:**

```rust
use std::fs::read_to_string;

fn main() {
    let input = read_to_string("input.txt").expect("Reading file");
    for line in input.lines() {
        println!("{:?}", line);
    }
}
```

**Rust functional tricks**

`.windows` in Rust let's you iterate over overlapping subslices of arbitrary length, which would have been very handy in today's AoC. [Windows in std::slice - Rust](https://doc.rust-lang.org/std/slice/struct.Windows.html)

- Remember: `.iter().fold()` is how you do some kind of accumulator over iterators in Rust.

**Downloading best-quality audio-video pairs with `yt-dlp`**
(combining as Apple-compatible mp4)

```bash
yt-dlp -f 'bv+ba/b' --merge-output-format mp4 --postprocessor-args "-c:v libx264 -c:a aac -movflags +faststart" <URL>
```

- #TIL [[Useful-code-snippets]] Clippy taught me that `if x < 1 || x > 999` can be replaced with `if !(1..=999).contains(&x)` in Rust
- [[Useful-code-snippets]] `cargo watch -w src -x check -x test -x run` watches a Rust project's `src` directory and performs `check`, `test`, and `run` on modification.
  - #TIL Because cargo let's you say `cargo test <module>` to run only some tests, cargo watch let's you do `cargo watch -w src -x check -x 'test <module>'` to only run those tests live, which is nice for snappier response and efficiency.
- #TIL about [filter_map](https://rustbites.com/posts/bite-017/) in Rust, which combined `.filter()` and `.map()` and allows for elegant use of the `?`. The returned iterator yields only the values for which the supplied closure returns `Some(value)`.
  - With some extensive prompting and tweaking, I finally got `o1-preview` to give it to me for my [AoC day 3 code](https://github.com/gianlucatruda/aoc-2024/commit/2fa5a625cd79feeab50e0afdd5349dfb96968742)
- #TIL [[Useful-code-snippets]] Nice HashMap pattern in Rust:
  - `dic.entry(*k).or_insert_with( ).push(v);`
  - `dic.entry(*k).or_default().push(v)`
- #TIL `#[allow(dead_code)]` tells clippy to ignore unused code in Rust
- #TIL [[Useful-code-snippets]] Find and replace recursively from the command line with grep and sed: `grep -rl 'https://gianlucatruda.github.io/bit-of-a-tangent-website' . | xargs sed -i 's|https://gianlucatruda.github.io/bit-of-a-tangent-website|https://podtangent.com|g'`
- [[Useful-code-snippets]] New LLM workflow: `rg -l "week-2024-W44" ~/Obsidian/Daily/ | sort | xargs -n 1 -I {} sh -c 'echo "\n---\n{}\n---\n"; cat {}' > 0-Inbox/week-2024-W44.md` gets all my Obsidian daily logs for week 44 of 2024 and creates a single markdown concatenation (sorted by name and thus by date). Then run `cat 0-Inbox/week-2024-W44.md | llm -s "From these notes, concisely summarise everything I worked on, learned, and accomplished this past week. Write in concise langauge and markdown. I'll copy-paste your output direcrly into my weeknotes."`
- [[Useful-code-snippets]] I can get all my Readwise highlights for (only for newly created) a given week with `rg -l0 "week-2024-W44" ~/Obsidian/Readwise/ | xargs -0 cat | pbcopy` assuming that I ran the sync within a reasonable timeframe.
- #TIL [[Useful-code-snippets]] `bin(n)[2:]` is a quick way to get clean binary string of int `n` in python
- #TIL [[Useful-code-snippets]] `uvx files-to-prompt . -e js -c | llm -m o3-mini -s "Write extensive documentation (in Markdown) for what this code is, how it works, and how to add new functionality." >> explanation.md` via [o3-mini is really good at writing internal documentation](https://simonwillison.net/2025/Feb/5/o3-mini-documentation), but could also use [repomix](https://www.npmjs.com/package/repomix)
- #TIL [[Useful-code-snippets]] `npx repomix . --include "src/**/*.js,**/*.md"` , but can also do `npx repomix --remote https://github.com/gianlucatruda/TableDiffusion --include "**/*.py" --remote-branch master`
- #TIL [[Useful-code-snippets]] Pull a PR down as `pr-review` branch: `git fetch origin pull/PR_NUMBER/head:pr-review`
- [[Useful-code-snippets]] `rsync -avh --exclude '.DS_Store' --exclude="._*" //Volumes/WorkingSSD/PROJECT/ //Volumes/YourSSD/PROJECT/`
- #TIL about hidden "AppleDouble resource fork files" (starting with `._`) that only show up in the terminal (not Finder with hiddens on) and have been polluting my rsync commands to synchronise drives.
- #TIL The `fs_usage` utility presents an ongoing display of system call usage information pertaining to filesystem activity.
  - [[Useful-code-snippets]] `sudo fs_usage | grep disk*` to see everything accessing disks, or `sudo fs_usage | grep disk4` specifically to filter by disk4
- [[Useful-code-snippets]] Finally got [aider](https://aider.chat/docs/usage/commands.html) to install with uv and be runnable! Thanks claude + uv docs for the help!
  - `uv tool install --force --python python3.12 aider-chat@latest`
  - `uv tool run --from aider-chat aider`
- [[Personal-system-changelog]] Downloading [Qwen_QwQ-32B-Q8_0.gguf](https://huggingface.co/bartowski/Qwen_QwQ-32B-GGUF/blob/main/Qwen_QwQ-32B-Q8_0.gguf) via wget (take 2)
  - [[Useful-code-snippets]] `wget -v --show-progress --limit-rate=1.5m --tries=200 "https://huggingface.co/bartowski/Qwen_QwQ-32B-GGUF/resolve/main/Qwen_QwQ-32B-Q8_0.gguf" --directory-prefix ~/models -O Qwen_QwQ-32B-Q8_0.gguf --continue`
- #TIL About `rsync` backup and delete features and how to recreate TimeMachine features with them (thanks for the tips, Claude). Used it to develop these [[Useful-code-snippets]] for syncing HDDs: `rsync -avh --backup --backup-dir="//Volumes/FriendArchive/Temporary/Deleted_$(ecdt)" --delete-after --exclude '.DS_Store' --exclude="._*" //Volumes/Friend/Projects/ //Volumes/FriendArchive/Projects/ --progress --stats | tee ~/0-Inbox/$(ecdt)_rsync_log.txt`
- #TIL [[Useful-code-snippets]] In Rust you can just use `!dbg(<expression>)` which will do the equivalent of Python f-strings with `{expr=}` but also returns the value so you can use it inline. Via [Python f-strings {expr=} || {farid rener}](https://faridrener.com/2025/03/15/equals-fstrings.html)
- [[Personal-system-changelog]] [[Useful-code-snippets]] [aider-custom alias with all my preferred flags · gianlucatruda/dotfiles@eb431f0](https://github.com/gianlucatruda/dotfiles/commit/eb431f01516652a8a7b76bf4d614ab91cddc71a6)
- [[Useful-code-snippets]] `npx defuddle-cli parse https://gianluca.ai/about --md -o gianluca-ai-about.md` to download URL as markdown with [kepano/defuddle-cli](https://github.com/kepano/defuddle-cli). Or just use [Defuddle Playground](https://kepano.github.io/defuddle/)
- #TIL about [memray](https://github.com/bloomberg/memray) and used it via uvx to generate a flamegraph:
- [[Useful-code-snippets]] compress images or videos and then strip metadata:
  - `for file in *.JPG; do magick "$file" -strip -interlace Plane -quality 25% "${file%.*}_web.jpg"; done`
  - `for file in *.mp4; do ffmpeg -i "$file" -c:v libx264 -crf 32 -preset slower -c:a aac -b:a 96k -movflags +faststart "${file%.*}_web.mp4"; done`
  - `exiftool -All= *`
- [[Useful-code-snippets]]: `dust -n 50 --depth 4 --reverse --print-errors ~/ | tee ~/3-Resources/Logs/$(ecdt)_dust_gianluca.txt`
- #TIL [[Useful-code-snippets]] for checksumming all files in a path: `find path/to-files/**/* -type f -exec shasum -a 256 {} \; >> checksums_sha256.txt`
- #TIL [[Useful-code-snippets]] The ArchWiki is pretty great for explanations and example usage scripts. For instance, [rsync - ArchWiki](https://wiki.archlinux.org/title/Rsync) gives scripts for running weekly automated backups with `rsync`
- #TIL [[Useful-code-snippets]] Python's `open()` function can also work with `stdin` (0), `stout` (1), and `stderr` (2), which is very handy for UNIX-like CLI tools that can pipe in and out.

```python
# Read from stdin (0)
with open(0, "r") as input:
    data = input.read()

data = data.lower()

try:
    # Write to stdout (1)
    with open(1, "w") as output:
        output.write(data)
except Exception as e:
    # Write to stderr (2)
    with open(2, "w") as output:
        output.write(f"Error: {e}")

```

- #TIL [[Useful-code-snippets]] via [dynomight](https://dynomight.net/links-3/), you can link to any text on any page with the built-in browser feature: `https://dynomight.net/grug#:~:text=phenylalanine`
  - e.g. https://gianluca.ai/books/#:~:text=beauty
  - `:~:text=`
  - Right-click option "works out of the box in Safari and Chrome-esque browsers. In Firefox (from my cold, dead hands) you currently have to edit a preference."
    - `about:config` > `dom.text_fragments.create_text_fragment.enabled`. Set to true.
- [[Useful-code-snippets]] [[backup-and-sync-refactor-2025]] rclone command for downloading all Google Drive contents: `rclone copy -v --progress --bwlimit 500k:1.5M --log-file=$HOME/3-Resources/Logs/$(ecdt)_rclone.log GoogleDriveRemote: //Volumes/MyBackupSSD/GOOGLE_DRIVE`
- #TIL [[Useful-code-snippets]] for displaying text easily in the CLI with [oh-my-logo](https://github.com/shinshin86/oh-my-logo): `npx oh-my-logo "Gianluca" sunset --filled`
- [ ] [[Useful-code-snippets]] for using local #LLMs to process and summarise notes for [[weekly-review]]
  - `uvx files-to-prompt $(rg "week-2025-W36" --files-with-matches --sort=created ~/Obsidian/Daily) > week-2025-W36 && glow -p week-2025-W36`
  - `cat ~/Obsidian/0-Inbox/weekly-review-2025-W36.md | llm -m gemma3:27b-custom -s "Summarise this weekly review/plan doc. Don't give your own commentary or any emotions. You just plainly summarise the information, nothing more nothing less. Summarise in one short paragraph (2-4 sentences) the major points and key factors in EACH subsection (Recap, Reflect, Priorities, Intentions, Tasklist). Use specific names, terms, and details." >> $(ecdt)_W36-gemma-summary.md`
- [[Useful-code-snippets]] for searching #Obsidian vault by tags and/or pages:
  - `rg -i "Financial-and-tax|#financial" --sort=created ~/Obsidian/ | less`
  - `rg -i "personal-system-changelog|#systems" --sort=created ~/Obsidian/ > ~/0-Inbox/$(ecd)_systems-changes.txt`
  - `rg "\- \[ \]" ~/Obsidian/Daily/2025-09* --sort=created` (can pretty much replace the way I use [[Tasks-view]], but at the expense of no iOS solution)
- [[Useful-code-snippets]] for querying #Obsidian vault:
  - `fd . -t f --change-newer-than 30d ~/Obsidian/Daily/ | xargs rg "\- \[[/, ]\]" --sort=created`
  - `fd . -t f --exclude 'Readwise/*' --change-newer-than 9d ~/Obsidian -0 | xargs -0 rg "\- \[[/, ]\]" --sort=created`
  - `fd . -t f --exclude 'Readwise/*' --change-newer-than 9d ~/Obsidian/Daily/ -0 | xargs -0 rg "summary:" -C 3 --sort=created`
  - `fd . -t f --change-newer-than 10d ~/Obsidian/Daily/ -0 | xargs -0 rg "week: 2025-W37" --sort=created`
  - `fd . -t f --change-newer-than 10d ~/Obsidian/Daily/ -0 | xargs -0 rg "week: 2025-W37" --sort=created --files-with-matches | xargs uvx files-to-prompt | glow -p`
  - `fd . -t f --change-newer-than 7d ~/Obsidian/ --exclude "Readwise/*" -0 | xargs -0 rg "2025-W38" --sort=created --files-with-matches -0 | xargs -0 uvx files-to-prompt | glow -p`
- [[Useful-code-snippets]] #TIL I can use the [watchfiles](https://pypi.org/project/watchfiles/) tool to do live reloading / hot reloading (on file changes) in a similar fashion to `cargo watch`. I just do this via `uvx` with `uvx watchfiles "python main.py"`
  - It also works with just about any bash command: `uvx watchfiles "cat test.txt"`
  - According to the docs, `watchfiles "some command" src` will run `some command` when files in `src` change
  - It turns out that `cargo-watch` is deprecated, so I installed the recommended [watchexec](https://github.com/watchexec/watchexec), which works on arbitrary commands like `watchfiles` does
- [[Useful-code-snippets]] Final clone of Google Drive: `rclone copy -v --progress --bwlimit 750k:4M --log-file=$HOME/3-Resources/Logs/$(ecdt)_rclone.log GoogleDriveRemote: //Volumes/MyBackupSSD/GOOGLE_DRIVE`
- [[Useful-code-snippets]] `gitleaks dir -v .` to show leaked secrets in a directory using [gitleaks](https://github.com/gitleaks/gitleaks?utm_source=alphasignal&utm_campaign=2025-07-21&asuniq=156f916e)
- [[Useful-code-snippets]] #TIL you can trim videos without re-encoding in `ffmpeg` by setting start and duration (`t`): `ffmpeg -i movie_H264.mp4 -ss 00:00:30 -t 00:05:33 -c copy movie_H264-trim_v2.mp4`
- [[Useful-code-snippets]] Pipe extensive `fastfetch` JSON details to a file: `fastfetch --pipe --config all --format json > ~/3-Resources/Logs/$(ecdt)_fastfetch-all.json`
- [[Useful-code-snippets]] Quicker #Todoist inbox digest: `cp ~/Downloads/Inbox.csv ~/4-Archives/4-Exports/Todoist/$(ecd)_Todoist-Inbox.csv && gt-todoist-export ~/4-Archives/4-Exports/Todoist/$(ecd)_Todoist-Inbox.csv | pbcopy`
- [[Useful-code-snippets]] Some useful syntax for #Obsidian bases to interface with my time-period tagging system, as tested in [[my-first-base.base]]
  - `year-2025` (`year-{{date:YYYY}}`)
    - `file.tags.contains("year-"+today().format("YYYY"))`
  - `quarter-2025-Q3` (`quarter-{{date:YYYY}}-Q{{date:Q}}`)
    - `file.tags.contains("quarter-"+today().format("YYYY")+"-Q"+today().format("Q"))`
  - `month-2025-08` (`month-{{date:YYYY-MM}}`)
    - `file.tags.contains("month-"+today().format("YYYY")+"-"+today().format("MM"))`
  - `week-2025-W35` (`week-{{date:YYYY}}-W{{date:ww}}`)
    - `file.tags.contains("week-"+today().format("YYYY")+"-W"+today().format("ww"))`
- [[Useful-code-snippets]] All #Readwise entities created week 36 of 2025: `uvx files-to-prompt $(rg --files-with-matches "week-2025-W36" ~/Obsidian/Readwise/) | bat`
  - `uvx files-to-prompt $(rg --files-with-matches "week-2025-W36" ~/Obsidian/Readwise/) | llm -m gpt-5 --no-stream "Summarise the interesting ideas, mental models, and poignant quotes from my highlights for the week. Write them up as a structured markdown list (mention sources) that I can copy-paste into my Obsidian vault."`
- [[Useful-code-snippets]]: `alias notify='echo -e "\a"; echo "Done at $(date)"'` allows you to do things like `long_task && notify` and get alerted when it's done.
- [[Useful-code-snippets]] for searching #Obsidian vault by tags and/or pages:
  - `rg -i "Financial-and-tax|#financial" --sort=created ~/Obsidian/ | less`
  - `rg -i "personal-system-changelog|#systems" --sort=created ~/Obsidian/ > ~/0-Inbox/$(ecd)_systems-changes.txt`
  - `rg "\- \[ \]" ~/Obsidian/Daily/2025-09* --sort=created` (can pretty much replace the way I use [[Tasks-view]], but at the expense of no iOS solution)
- #TIL essential tricks in `ranger`: `S` opens shell in current directory, `!` allows running a shell command (aliases?), `r` opens an "open with" menu: `0` will do `open -- "$@"` with will open a directory in Finder and files in the default app. `dU` shows size of directory (and subs)
- [[Useful-code-snippets]] for querying #Obsidian vault -> [[obsidian-refactor]]
  - `fd . -t f --change-newer-than 30d ~/Obsidian/Daily/ | xargs rg "\- \[[/, ]\]" --sort=created`
  - `fd . -t f --exclude 'Readwise/*' --change-newer-than 9d ~/Obsidian -0 | xargs -0 rg "\- \[[/, ]\]" --sort=created`
  - `fd . -t f --exclude 'Readwise/*' --change-newer-than 9d ~/Obsidian/Daily/ -0 | xargs -0 rg "summary:" -C 3 --sort=created`
- [[Useful-code-snippets]] `fd . -t f --change-newer-than 5d --exclude "Full Document Contents/*" --exclude "Readwise Syncs*" ~/Obsidian/Readwise/ -0 | xargs -0 uvx files-to-prompt | bat `
- Experiment: Used a clever bash chain to pipe all my Readwise highlights from the week into GPT-5 and have it generate [[Weekly Research & Reading Report — W38 2025]]
  - `fd . -t f --change-newer-than 5d --exclude "Full Document Contents/*" --exclude "Readwise Syncs*" ~/Obsidian/Readwise/ -0 | xargs -0 uvx files-to-prompt | llm --model gpt-5 --no-stream -o reasoning_effort medium -s "Here is a dump of my Readwise highlights from the past few days. Create a detailed report (using direct quotes) that summarises the key themes and takeaways from my research and general reading. The report should be comprehensive and detailed, but easy to skim and get the gist of. Use markdown along with [[wikilinks]] so it will be compatible with my Obsidian vault as a doc I can paste in." > $(ecdt)_reading_summary.md && notify`
- [[Useful-code-snippets]] for downloading first 1 hour of long #YouTube video as 1080p mp4: `yt-dlp -f "best[height<=1080][ext=mp4]" --download-sections "*0:00-1:00:00" "VIDEO_URL"`
- [[Useful-code-snippets]] #TIL `cut -c 1-3000 <file>` gives first 3000 chars of `<file>`
- [[Useful-code-snippets]] #TIL use `-a <image>` to attach images to `llm`, via [LLM CLI reference](https://llm.datasette.io/en/stable/help.html)
  - You can do multiple attachments like: `llm -m gpt-5 --no-stream "Describe what you see in the attachments in detail, with focus on aesthetics and styling." -a G2S3b3jbMAAsVDO.jpeg -a G2S3cgsbUAEIV7D.jpeg -a G2S3d0WacAEIvTB.jpeg -a G2S3e23akAAbp_4.jpeg > $(ecdt).md && notify`
- [[Useful-code-snippets]] Watermarking: `magick -density 100 Doc.pdf -pointsize 60 -fill "rgba(255,0,0,0.05)" -font Arial-Bold -gravity center -annotate +0+0 "watermark text" output.pdf`
- #TIL [[Useful-code-snippets]] Powerful bit-hack (bitwise operation) tricks and algorithms (in Python):
  - You can see if something is a power of 2 with: `n > 0 and (n & (n - 1)) = 0`
  - Kernighan's algorithm for counting set bits

**Set git committer email to normal public one for current repo**:

```bash
git config user.email "<name@email.com>"
```

**Mark all github notifications as done (clears spam) from `gh` CLI**:

```bash
gh api --method PUT /notifications
```

---

tmux has 5 built-in layouts, all via `prefix + M-<n>` (Meta/Alt key) or by running `select-layout <name>`:

┌────────────┬───────────────────────────────┬───────────────────────────────────────┐
│ Keybinding │ Command │ Effect │
├────────────┼───────────────────────────────┼───────────────────────────────────────┤
│ prefix M-1 │ select-layout even-horizontal │ Equal width, side by side │
├────────────┼───────────────────────────────┼───────────────────────────────────────┤
│ prefix M-2 │ select-layout even-vertical │ Equal height, stacked │
├────────────┼───────────────────────────────┼───────────────────────────────────────┤
│ prefix M-3 │ select-layout main-horizontal │ One large pane on top, rest below │
├────────────┼───────────────────────────────┼───────────────────────────────────────┤
│ prefix M-4 │ select-layout main-vertical │ One large pane on left, rest on right │
├────────────┼───────────────────────────────┼───────────────────────────────────────┤
│ prefix M-5 │ select-layout tiled │ Grid (best for many panes) │
└────────────┴───────────────────────────────┴───────────────────────────────────────┘

**Use Bash strict mode**

```bash
#!/usr/bin/env bash
set -euo pipefail

# Optional: provide a helpful error message
trap 'echo "Error on line $LINENO"; exit 1' ERR
```

What it does:

- -e / errexit: exit if any command fails
- -u / nounset: exit on unset variables
- -o pipefail: pipelines fail if any command in the pipeline fails

Optional: adjust IFS if needed:

```bash
IFS=$'\n\t'
```

**Auditing a git repo and its contributors** via [The Git Commands I Run Before Reading Any Code](https://piechowski.io/post/git-commands-before-reading-code/)

What Changes the Most

```sh
git log --format=format: --name-only --since="1 year ago" | sort | uniq -c | sort -nr | head -20
```

Who Built This

```sh
git shortlog -sn --no-merges
git shortlog -sn --no-merges --since="6 months ago"
```

Where Do Bugs Cluster

```sh
git log -i -E --grep="fix|bug|broken" --name-only --format='' | sort | uniq -c | sort -nr | head -20
```

Is This Project Accelerating or Dying

```sh
git log --format='%ad' --date=format:'%Y-%m' | sort | uniq -c
```

How Often Is the Team Firefighting

```sh
git log --oneline --since="1 year ago" | grep -iE 'revert|hotfix|emergency|rollback'
```

**Timing commands on macOS**

Use `time` when you just want elapsed/runtime numbers:

```sh
time sleep 3
/usr/bin/time sleep 3
```

Both show timing data such as real, user, and sys time.

Use `/usr/bin/time -l` when you also want resource usage details:

```sh
/usr/bin/time -l sleep 3
```

On macOS, `-l` adds profiling-style stats like maximum resident set size, page reclaims, page faults, and context switches, which is useful when you are comparing not just how long something took, but also how much CPU and memory pressure it created.
