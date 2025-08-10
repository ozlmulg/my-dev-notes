# GitHub Actions

## Useful Links

- [GitHub Actions Runners](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners/about-github-hosted-runners)
- [GitHub Actions Events](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)
- [GitHub Actions Events Filters](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
- [GitHub Actions Workflow Skip](https://docs.github.com/en/actions/managing-workflow-runs/skipping-workflow-runs)
- [GitHub Actions Contexts](https://docs.github.com/en/actions/learn-github-actions/contexts)
- [GitHub Actions Expressions](https://docs.github.com/en/actions/learn-github-actions/expressions)
- [GitHub Actions Workflow Syntax](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
- [GitHub Actions Default Environment Variables](https://docs.github.com/en/actions/learn-github-actions/variables#default-environment-variables)
- [GitHub Actions Reuse Workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)

---

## Storing Actions in Repositories & Sharing Actions with Others

In this section, we explain how to create and share custom Actions stored in separate repositories, instead of keeping
them inside the same repository as your workflows.

### How to create and share a custom Action in its own repository:

1. Create a new local project folder  
   This folder should contain your action.yml file and all the code needed for your Action.  
   **Important:** Do not put your action.yml or code inside a .github/actions folder or similar. Keep everything at the
   root level of your new project folder.

2. Initialize a Git repository  
   Run the following command inside your project folder:

`git init`

3. Add and commit your files

`git add .`

`git commit -m "Initial commit for my custom Action"`

4. Create a GitHub repository  
   Create a new repository on GitHub to host your Action.

5. Connect your local repo to GitHub remote

`git remote add origin https://github.com/my-account/my-action.git`

6. Tag a release version  
   It's a good practice to tag your Action versions for reuse:

`git tag -a -m "My action release" v1`

7. Push your code and tags to GitHub

`git push --follow-tags origin main`

8. Use your custom Action in workflows  
   Reference your custom Action in any other workflow by specifying the repository and tag, like this:

`uses: my-account/my-action@v1`