## Preface – how to add content to best practice

Fill out this template and follow the step-by-step process to contribute to 

### Step-by-step process

1. Create a branch from the latest version of `main`
2. Copy this template and give it a relevant filename and title
3. Add the title and filename to the nav section of [`mkdocs.yml`](../mkdocs.yml)
    ```yaml
    nav:
        - Home: 'index.md'
        - Gitlab: 'best_practice.md'
        - Infrastructure: 'hpc_servers.md'
        - 'Tech Stack': 'tech_stack.md'
        - <title>: '<filename.md>' # <- Replace this line
    ```
4. Fill out the template copy (see below) and delete this preface
5. Create a pull request and tag one of the github's maintainers to get the documentation approved and merged:
  - @QMOK
  - @...
6. Bathe in the glory of your peers who will thank you profusely for providing well written documentation that solved all their needs

# Template

- Author(s): *[Who wrote the documentation]*
- Date: *[and when?]*

## Executive summary
*[A 2-3 paragraph summary that informs the reader on:]*
1. Who is the intended audience?
2. What is the aim of the documentation, i.e. what is the expected outcome for the reader?
3. How technical and detailed is the documentation written?

## Prerequisites
*A list of precise prerequisites needed - assume the reader is newly onboarded. What do they need to have installed / access to / know of?*

## Main text
*Try to write in a way that make it clear what needs to be done, with concrete examples, to check if the reader has understood the text. For example:*

> In VSCode log-on to Einstein via SSH, by using the extension [`Remote - SSH`](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). Open up the terminal after log-in (Hotkey: CTRL+j). If successful you should get a prompt like:
    ```zsh
    $ vnna@einstein ~
    ```

## Supplementary text
If there are supplementary and relevant documentation elsewhere, such as a SharePoint page, link this here - this will make it easier to navigate to the source of implicite assumptions 
    


