# The Open MSCS 🎓

The open source compendium of graduate computer science course notes.

I built Open MSCS to house and share my course notes, study tips, and resources for OMSCS. You can browse and easily search the site at <https://openmscs.com/>.

## Contributing

📣 Calling all contributors! Contributions welcome! Additions, updates, corrections, and improvements of all kinds! 😄

Many heads are better than one! Feel free to open a [pull request](https://github.com/victoriadrake/open-mscs/pulls) with your contribution, or an [issue](https://github.com/victoriadrake/open-mscs/issues) to request one from the community. You'll get a friendly, no-pressure environment -- whether it's your first or thousandth-and-first open source contribution!

### Getting Started

*You don't need to follow these steps unless you want to set up the repository on your own machine for local development. To browse the site, just visit the link above!*

The site is built from the Markdown files under `docs/` using [MkDocs](https://www.mkdocs.org/) and the [Material theme](https://squidfunk.github.io/mkdocs-material/).

1. Clone this repository:

   ```sh
   git clone https://github.com/victoriadrake/open-mscs.git
   ```

2. Optionally, [create your virtual environment](https://docs.python.org/3/tutorial/venv.html#creating-virtual-environments).
3. You'll need Python, `pip`, and `pipenv`. Check that you have the first two with:

    ```sh
    python --version
    pip --version
    ```

    See instructions here for [installing `pipenv`](https://pipenv.pypa.io/en/latest/install/), optionally using `pipx`. Usually, you can just do `pip install pipenv`.

4. Install dependencies:

   ```sh
   pipenv install
   pre-commit install
   ```

Open the `docs/` folder in your favorite editor. You're ready to add or update notes!

### Local Development

You can run the local development server with `mkdocs serve`. Read the [MkDocs documentation](https://www.mkdocs.org/) for more options.

### Committing

The [pre-commit framework](https://pre-commit.com/) you installed as part of the set up will run a [Git hook](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks) that lints your Markdown files according to the `.markdownlint-cli2.jsonc`. This helps to keep things uniform and tidy.

## Support

For help with this website or repository, please open an [issue](https://github.com/victoriadrake/open-mscs/issues).

For help with OMSCS, come [chat](https://openmscs.com/#quick-links) with your classmates!
