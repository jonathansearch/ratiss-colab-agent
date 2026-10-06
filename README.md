<p align="center">
  <img src="docs/assets/logo.png" alt="RATISS Labs logo" width="180"/>
</p>

[![RATISS Labs](https://img.shields.io/badge/RATISS_Labs-Deep_Tech_Sovereign-06b6d4)](https://github.com/jonathansearch)

# RATISS Colab Agent Control Plane

This repository contains a Google Colab notebook for driving a development agent with **global GitHub** integration. The notebook does not depend on any specific repository: you can select an existing repository accessible from your GitHub account or create a new one.

## Quick links

- **Open the notebook in Colab:** [RATISS_Colab_Agent_Control_Plane.ipynb](https://colab.research.google.com/github/jonathansearch/ratiss-colab-agent/blob/main/RATISS_Colab_Agent_Control_Plane.ipynb)
- **GitHub repository:** [jonathansearch/ratiss-colab-agent](https://github.com/jonathansearch/ratiss-colab-agent)

## Important before you start

Colab provides a temporary environment. The notebook can be interrupted when the runtime expires or when the GPU is no longer available. The notebook therefore saves the task state in a checkpoint file and resumes preparation when it is restarted.

Colab does not guarantee automatic wakeup of a fully stopped runtime. To resume after expiry, you must reopen the notebook, reconnect the runtime and rerun the cells. Changes already pushed to GitHub remain available.

## 1. Create the GitHub token

The notebook asks for a **GitHub Personal Access Token**, called a PAT.

| Format | Type | Usage |
|---|---|---|
| `ghp_...` | Classic PAT | The simplest for general GitHub integration |
| `github_pat_...` | Fine-grained PAT | More precise and recommended to limit permissions |

The token must belong to the GitHub account that owns or can modify the RATISS repositories.

### Classic PAT

In GitHub: **Settings → Developer settings → Personal access tokens → Tokens (classic)**. Enable at minimum the `repo` permission. It notably allows cloning, modifying and pushing to private repositories. To create new repositories, use an account authorized to perform that operation.

### Fine-grained PAT

In GitHub: **Settings → Developer settings → Personal access tokens → Fine-grained tokens**. Choose the owner account, then the accessible repositories. Enable at minimum:

- **Contents: Read and write**;
- **Metadata: Read**;
- **Pull requests: Read and write** if the notebook must create Pull Requests;
- the repository-creation permission only if necessary.

Give the token a reasonable expiration. Never publish the token in the notebook, in a commit, in a screenshot or in a message.

## 2. Open and run the notebook

1. Open the [direct Colab link](https://colab.research.google.com/github/jonathansearch/ratiss-colab-agent/blob/main/RATISS_Colab_Agent_Control_Plane.ipynb).
2. Sign in to Google if Colab asks.
3. Accept copying the notebook into your Colab space if needed.
4. Run the cells in order, top to bottom.
5. Allow the dependency installation and the runtime access.

The first cell installs `git`, `gh`, `PyGithub`, `ipywidgets` and `requests`.

## 3. Enter the GitHub token

On desktop, you can use Colab's **Secrets** panel with a secret named exactly `GITHUB_TOKEN`.

On mobile, the Secrets panel may not appear. Run the setup cell and paste the PAT when it displays:

```text
GitHub Fine-grained token (hidden):
```

The field masks the input. Press **Enter** or the keyboard's confirm key. The notebook should then display:

```text
Connecté à GitHub comme: ton_nom_github
```

If the cell waits indefinitely, stop it and rerun only the setup cell. Check that you pasted the token into the masked field.

## 4. Verify the connection

The diagnostic cell verifies the GitHub identity through the API. It never displays the token.

```text
GitHub OK: samajonathan9-source
Le dépôt privé sera cloné avec le token via un header temporaire.
```

| Message | Fix |
|---|---|
| `GITHUB_TOKEN est vide` | Recreate the secret or enter the PAT in the masked field |
| `Token GitHub refusé` | Check the PAT expiration and permissions |
| `Resource not accessible` | Add Contents and Metadata |
| `Repository not found` | Check `owner/repository` and the account's access |
| `could not read Username` | Rerun the updated notebook and check the PAT |

## 5. Choose an existing repository

In the GitHub interface, write the repository in the form:

```text
owner/repo-name
```

Examples:

```text
samajonathan9-source/ratiss-bio
samajonathan9-source/Crypto-net-veo-
ratiss-labs/ratiss-qpu-ambient
```

Then write a working branch, for example:

```text
agent/diagnostic-auth
```

Click **Sélectionner**, then **Cloner / préparer branche**. The notebook clones the repository into `/content/ratiss-agent/workspace/` and prepares a dedicated branch. It does not push directly to `main` by default.

## 6. Create a new repository

In the same interface: write the name, optionally specify an organization, choose private or public, then click **Créer dépôt**. Then select the created repository and prepare its branch.

Creation requires an appropriate GitHub permission. If it is refused, create the repository manually on GitHub then use its `owner/repo-name` reference in the notebook.

## 7. Working in the repository

The notebook lets you view the Git status, check the active branch, run tests, execute authorized scripts, inspect files and save a checkpoint. Commands are filtered through a whitelist.

```python
status()
checkpoint('tested', 'Tests locaux terminés')
```

## 8. Commit, push and Pull Request

Before pushing, review the diff, run the tests, save a checkpoint and confirm the active branch.

```python
commit_and_push('feat: complete RATISS validation')
```

To open a Pull Request:

```python
open_pull_request(
    'Complete RATISS validation',
    'Tests exécutés dans Colab. Merci de revoir le diff avant fusion.'
)
```

The Pull Request must be reviewed before merging. The notebook does not merge the Pull Request automatically.

## 9. Resuming after Colab expiry

When Colab stops:

1. reopen the notebook;
2. reconnect the runtime;
3. rerun the installation and setup cells;
4. re-enter the PAT if no Colab secret is available;
5. run the **Checkpoint et reprise** cell;
6. check the repository and the branch;
7. reclone the repository if `/content` was wiped;
8. resume the phase indicated by the checkpoint.

Resumption relies on:

```text
/content/ratiss-agent/state.json
```

This file is temporary in Colab. To keep the state across a new session, regularly push changes to a GitHub branch or copy the results to persistent storage.

## 10. Recommended full cycle

```text
Open Colab
  → Install dependencies
  → Provide GITHUB_TOKEN
  → Verify GitHub identity
  → List or select a repository
  → Create a repository if needed
  → Create a working branch
  → Clone
  → Run the task
  → Save a checkpoint
  → Test
  → Review the diff
  → Commit and push
  → Create a Pull Request
  → Human review
  → Merge on GitHub
```

## 11. Security

Never paste a PAT in a Markdown cell. Do not include it in a Git URL. Do not print it with `print`. Never commit it. If the token appears in a log or a screenshot, revoke it immediately in GitHub and create a new one.

The notebook uses temporary Git authentication via an HTTP header to avoid placing the token in the clone URL. The Colab runtime nevertheless remains temporary.

## 12. Limit of the OpenHands-LM-32B model

The notebook prepares the runtime, GitHub and task control. Loading a 32B model locally depends on the available GPU memory. A T4 or a P100 is generally not enough for a full version in standard precision. You must use compatible quantization, a distributed runtime or a remote endpoint.

The GPU cell displays the available hardware before any heavy launch.

## 13. Quick troubleshooting

| Problem | Immediate action |
|---|---|
| The cell waits for the token | Paste the PAT in the masked field then press Enter |
| No Secrets panel on mobile | Use the direct masked input |
| `could not read Username` | Use the updated public version and check the PAT |
| Private repository inaccessible | Add Contents: Read and write |
| Push refused | Check Contents: Read and write and the targeted branch |
| Pull Request refused | Add Pull requests: Read and write |
| Repository creation refused | Add the creation permission or create manually |
| Runtime expired | Rerun the cells, reload the checkpoint and reclone |
| GPU absent | Reconnect the runtime or choose CPU/remote endpoint |

## License and liability

The notebook is provided as a development and prototyping tool. Review each change before pushing or merging it. GitHub permissions must remain limited to the scope actually needed.
