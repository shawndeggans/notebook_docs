# Automating Documentation Generation and Publishing to GitHub Pages

This tutorial guides you through the process of setting up automated documentation generation from Jupyter notebooks and publishing to GitHub Pages using GitHub Actions.

## Prerequisites

- A GitHub repository containing Jupyter notebooks
- Basic familiarity with GitHub and GitHub Actions

## Step 1: Prepare Your Repository

1. Ensure your Jupyter notebooks are in a specific directory in your repository (e.g., `/notebooks`).

2. Create a `requirements.txt` file in your repository root with the following content:

   ```
   nbconvert
   jupyter
   ```

3. In your repository root, create a new directory called `docs`.

4. Inside the `docs` directory, create a file named `_config.yml` with the following content:

   ```yaml
   title: Fraud Analytics Documentation
   description: Documentation for Fraud Analytics CAR Suites
   theme: jekyll-theme-cayman
   ```

5. In the `docs` directory, create a file named `index.md` with some initial content:

   ```markdown
   ---
   layout: default
   title: Home
   ---

   # Welcome to Fraud Analytics Documentation

   This site contains documentation for our CAR Suites.

   ## Available Documentation:

   (This list will be automatically populated)
   ```

## Step 2: Set Up GitHub Actions Workflow

1. Create a `.github/workflows` directory in your repository root.

2. In this directory, create a file named `docs_generation.yml` with the following content:

   ```yaml
   name: Generate Docs

   on:
     workflow_dispatch:
       inputs:
         reason:
           description: 'Reason for running the workflow'
           required: false
           default: 'Manual trigger'

   jobs:
     convert-and-publish:
       runs-on: ubuntu-latest
       permissions:
         contents: write
       steps:
       - uses: actions/checkout@v4

       - name: Set up Python
         uses: actions/setup-python@v5
         with:
           python-version: '3.x'

       - name: Install dependencies
         run: |
           python -m pip install --upgrade pip
           pip install -r requirements.txt

       - name: Convert notebooks to markdown
         run: |
           for notebook in notebooks/*.ipynb; do
             jupyter nbconvert --to markdown "$notebook" --output-dir docs/
           done

       - name: Update index page
         run: |
           echo "---" > docs/index.md
           echo "layout: default" >> docs/index.md
           echo "title: Home" >> docs/index.md
           echo "---" >> docs/index.md
           echo "" >> docs/index.md
           echo "# Welcome to Fraud Analytics Documentation" >> docs/index.md
           echo "" >> docs/index.md
           echo "This site contains documentation for our CAR Suites." >> docs/index.md
           echo "" >> docs/index.md
           echo "## Available Documentation:" >> docs/index.md
           echo "" >> docs/index.md
           for file in docs/*.md; do
             if [ "$(basename "$file")" != "index.md" ]; then
               echo "- [$(basename "$file" .md)]($(basename "$file"))" >> docs/index.md
             fi
           done

       - name: Commit and push changes
         run: |
           git config --local user.email "github-actions[bot]@users.noreply.github.com"
           git config --local user.name "github-actions[bot]"
           git add docs
           git commit -m "Update documentation (Manual trigger: ${{ github.event.inputs.reason }})" || echo "No changes to commit"
           git push
         env:
           GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
   ```

## Step 3: Enable GitHub Pages

1. Go to your repository on GitHub.
2. Click on "Settings" > "Pages".
3. Under "Source", select "Deploy from a branch".
4. For the "Branch" option, select "main" and "/docs" folder.
5. Click "Save".

## Step 4: Update Your Repository

1. Commit and push the new files and directories you've created:
   - `docs/_config.yml`
   - `docs/index.md`
   - `requirements.txt`
   - `.github/workflows/docs_generation.yml`

2. Ensure your Jupyter notebooks are in the `/notebooks` directory.

## Step 5: Run the Workflow

1. Go to your repository on GitHub.
2. Click on the "Actions" tab.
3. In the left sidebar, you should see "Generate Docs".
4. Click on "Generate Docs".
5. You'll see a "Run workflow" button on the right side.
6. Click "Run workflow", optionally enter a reason, and click "Run workflow" again.

## Step 6: Access Your Documentation

1. After the workflow completes successfully, wait a few minutes for GitHub Pages to update.
2. Your documentation will be available at `https://<username>.github.io/<repository-name>/`.
3. You can find the exact URL in the repository settings under the "Pages" section.

## Troubleshooting

If you encounter permission issues:

1. Go to your repository's Settings > Actions > General.
2. Scroll down to the "Workflow permissions" section.
3. Ensure that "Read and write permissions" is selected.

## Customization

- You can change the Jekyll theme in the `_config.yml` file.
- Modify the `index.md` file to customize the home page of your documentation.
- Adjust the GitHub Action workflow to handle different file structures or add more complex processing as needed.

## Conclusion

You now have an automated system for converting Jupyter notebooks to markdown files and publishing them as a documentation website on GitHub Pages. This system will update whenever you manually trigger the workflow, allowing you to control when documentation is refreshed.
