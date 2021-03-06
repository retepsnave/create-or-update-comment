# Create or Update Comment
[![CI](https://github.com/peter-evans/create-or-update-comment/workflows/CI/badge.svg)](https://github.com/peter-evans/create-or-update-comment/actions?query=workflow%3ACI)
[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-Create%20or%20Update%20Comment-blue.svg?colorA=24292e&colorB=0366d6&style=flat&longCache=true&logo=data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA4AAAAOCAYAAAAfSC3RAAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAAM6wAADOsB5dZE0gAAABl0RVh0U29mdHdhcmUAd3d3Lmlua3NjYXBlLm9yZ5vuPBoAAAERSURBVCiRhZG/SsMxFEZPfsVJ61jbxaF0cRQRcRJ9hlYn30IHN/+9iquDCOIsblIrOjqKgy5aKoJQj4O3EEtbPwhJbr6Te28CmdSKeqzeqr0YbfVIrTBKakvtOl5dtTkK+v4HfA9PEyBFCY9AGVgCBLaBp1jPAyfAJ/AAdIEG0dNAiyP7+K1qIfMdonZic6+WJoBJvQlvuwDqcXadUuqPA1NKAlexbRTAIMvMOCjTbMwl1LtI/6KWJ5Q6rT6Ht1MA58AX8Apcqqt5r2qhrgAXQC3CZ6i1+KMd9TRu3MvA3aH/fFPnBodb6oe6HM8+lYHrGdRXW8M9bMZtPXUji69lmf5Cmamq7quNLFZXD9Rq7v0Bpc1o/tp0fisAAAAASUVORK5CYII=)](https://github.com/marketplace/actions/create-or-update-comment)

A GitHub action to create or update an issue or pull request comment.

This action was created to help facilitate a GitHub Actions "ChatOps" solution in conjunction with [slash-command-dispatch](https://github.com/peter-evans/slash-command-dispatch) action.

## Usage

### Add a comment to an issue or pull request

```yml
      - name: Create comment
        uses: peter-evans/create-or-update-comment@v1
        with:
          issue-number: 1
          body: |
            This is a multi-line test comment
            - With GitHub **Markdown**
            - Created by [create-or-update-comment][1]

            [1]: https://github.com/peter-evans/create-or-update-comment
          reactions: '+1'
```

### Update a comment

```yml
      - name: Update comment
        uses: peter-evans/create-or-update-comment@v1
        with:
          comment-id: 557858210
          body: |
            **Edit:** Some additional info
          reactions: eyes
```

### Add comment reactions

```yml
      - name: Add reactions
        uses: peter-evans/create-or-update-comment@v1
        with:
          comment-id: 557858210
          reactions: heart, hooray, laugh
```

### Action inputs

| Name | Description | Default |
| --- | --- | --- |
| `token` | `GITHUB_TOKEN` or a `repo` scoped [PAT](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line). | `GITHUB_TOKEN` |
| `repository` | The full name of the repository in which to create or update a comment. | Current repository |
| `issue-number` | The number of the issue or pull request in which to create a comment. | |
| `comment-id` | The id of the comment to update. | |
| `body` | The comment body. | |
| `edit-mode` | The mode when updating a comment, `replace` or `append`. | `append` |
| `reactions` | A comma separated list of reactions to add to the comment. (`+1`, `-1`, `laugh`, `confused`, `heart`, `hooray`, `rocket`, `eyes`) | |

#### Outputs

The ID of the created comment will be output for use in later steps.
Note that in order to read the step output the action step must have an id.

```yml
      - name: Create comment
        uses: peter-evans/create-or-update-comment@v1
        id: couc
        with:
          issue-number: 1
          body: |
            My comment
      - name: Check outputs
        run: |
          echo "Comment ID - ${{ steps.couc.outputs.comment-id }}"
```

### Where to find the id of a comment

How to find the id of a comment will depend a lot on the use case.
Here is one example where the id can be found in the `github` context during an `issue_comment` event.

```yml
on:
  issue_comment:
    types: [created]
jobs:
  commentCreated:
    runs-on: ubuntu-latest
    steps:
      - name: Add reaction
        uses: peter-evans/create-or-update-comment@v1
        with:
          comment-id: ${{ github.event.comment.id }}
          reactions: eyes
```

Some use cases might find the [find-comment](https://github.com/peter-evans/find-comment) action useful.
This will search an issue or pull request for the first comment containing a specified string, and/or by a specified author.
See the repository for detailed usage.

```yml
      - name: Find Comment
        uses: peter-evans/find-comment@v1
        id: fc
        with:
          issue-number: 1
          comment-author: peter-evans
          body-includes: search string 1
```

### Accessing issues and comments in other repositories

You can create and update comments in another repository by using a [PAT](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) instead of `GITHUB_TOKEN`.
The user associated with the PAT must have write access to the repository.

## License

[MIT](LICENSE)
