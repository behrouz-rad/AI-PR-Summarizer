# PR Summarizing using AI

This GitHub Action automatically generates a concise summary of code changes in a pull request (PR) using AI. It simplifies the review process by analyzing diffs and delivering meaningful insights into the modifications. By leveraging the power of large language models (LLMs), it provides developers and reviewers with quick, high-level descriptions of the code changes, making PR reviews more efficient and informed.


## Table of Contents
  - [Key Features](#key-features)
  - [Why Use PR Summarizing?](#why-use-pr-summarizing)
  - [How It Works](#how-it-works)
  - [Challenges with AI Code Reviews](#challenges-with-ai-code-reviews)
  - [Files and Their Roles](#files-and-their-roles)
  - [Inputs](#inputs)
  - [Setup Instructions](#setup-instructions)
  - [Usage](#usage)

## Key Features

- **AI-Powered Summaries**: Summarizes code changes using advanced AI models for easier understanding of PRs.
- **Model Flexibility**: Supports customizable AI models.
- **Caching for Performance**: Efficient use of caching to avoid redundant downloads of AI models.
- **Customizable Prompts**: Supports flexible prompts to guide the AI on how to summarize and review the code changes.
- **Artifact Upload**: Optionally uploads the generated diff file as an artifact for future reference.

## Why Use PR Summarizing?

Manual code review can be tedious, especially for large and complex pull requests. This action helps:
- Reduce review time by summarizing changes.
- Provide high-level context, so reviewers can focus on critical parts.
- Automatically suggest feedback and highlight essential code differences.
  
## How It Works

The action works by following these steps:

1. **Checkout Code**: It checks out the PR branch for analysis.
2. **Fetch and Cache Models**: It pulls the required AI models using the [Ollama](https://ollama.com/) tool and caches them for efficient reuse.
3. **Extract Diffs**: The action computes the diff between the main branch and the PR branch.
4. **Generate Summaries**: The AI model analyzes the diff and provides a summary.
5. **Post Comments**: It automatically posts the generated summary as a comment on the PR.

## Challenges with AI Code Reviews

While this action can provide helpful summaries of changes, it’s important to note that using AI as a full-fledged code reviewer comes with challenges. AI needs to be highly context-aware to provide accurate reviews, which is not always straightforward. Techniques such as [Retrieval Augmented Generation (RAG)](https://github.blog/ai-and-ml/generative-ai/what-is-retrieval-augmented-generation-and-what-does-it-do-for-generative-ai/) are being explored to make AI more context-aware by allowing it to retrieve relevant information during the review process.

At present, the action excels in summarizing changes, offering a broad overview and highlighting potential issues. However, for detailed code reviews requiring deep contextual understanding, human reviewers should remain the primary decision-makers.

## Inputs

| Input Name  | Description                               | Required | Default                  |
|-------------|-------------------------------------------|----------|--------------------------|
| `llm-model` | Name of the LLM model to use for summarizing code changes. | true    | N/A |
| `prompt-file` | The file containing the AI prompt for the review process. | true    | N/A |
| `models-file` | The file listing AI models to be pulled and cached. | true    | N/A |
| `version-file` | The file used as a reference for caching the Ollama tool. | true    | N/A |
| `context-window` | The number of tokens an AI model can process at once, affecting its ability to handle longer inputs and context. | false | 4096 |
| `context-lines` | Sets the number of context lines to include around changes in `git diff`. A higher value provides more surrounding code for better AI analysis of the PR. | false    | 10 |
| `upload-changes` | Whether to upload the diff file as an artifact. | false    | false |
| `fail-on-error` | If set to `true`, the action will terminate if an error occurs during operation. | false    | true |
| `token` | Token for the repository, required to comment on the pull request. | false    | `github.token` |

## Setup Instructions
- `prompt-file`: Contains the prompt text to customize how the AI processes the PR.
- `models-file`: Contains the AI models to be used, listed one per line.
- `version-file`: This file holds version information for Ollama to manage the cache.

Example content for `prompt-file`:
```
You are an expert code reviewer with deep knowledge of software development practices.
You have been given the output of the git diff command, which shows the differences between the original and modified versions of a set of files.
Please review these changes and provide a structured code review, starting with the following statement: 'This is the code review from AI'.
Then, for each file, list the filename as a bullet point and describe the changes in that file as sub-bullets under the filename.
Focus on explaining the purpose of the changes, potential improvements, best practices, and any potential issues.
The review should be professional, concise, and informative.
Answer as quickly as possible in English language.
Here is the git diff output for review:
```

Example content for `models-file`:
```
deepseek-coder-v2:latest
magicoder:latest
```

Example content for `version-file`:
```
# Touch this file to install the latest version of Ollama.
```

## Usage
```yml
name: PR Summary Generator

on:
    workflow_dispatch:
  
    pull_request:
      branches:
        - main
  
    push:
      branches:
        - main

jobs:
  pr_summary:
    runs-on: ubuntu-latest
    name: PR Summarizer
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Summarize PR with AI
        uses: behrouz-rad/ai-pr-summarizer@v1
        with:
          llm-model: 'deepseek-coder-v2:latest'
          prompt-file: ./assets/prompt-file.txt
          models-file: ./assets/models-file.txt
          version-file: ./assets/version-file.txt
          context-window: 4096
          upload-changes: true
          fail-on-error: true
          token: ${{ secrets.GITHUB_TOKEN }}
```

## Demonstration Repository

To see the AI-PR-Summarizer in action, a [test repository](https://github.com/behrouz-rad/AI-PR-Summarizer-Test) has been created. This repository showcases how the action functions and provides a practical example of its capabilities. Feel free to explore it and review the automated pull request summaries generated by the AI.