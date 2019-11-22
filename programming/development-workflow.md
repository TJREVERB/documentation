---
description: TJREVERB 2019-20
---

# Development Workflow

## Overview

The TJ CubeSat team uses the following workflow when developing and testing code for pFS.

This entire processes is outlined [here](https://github.com/TJREVERB/pfs/blob/master/CONTRIBUTING.md).

1. Submit an [Issue](development-workflow.md#issues) on the [GitHub Repo](https://github.com/TJREVERB/pfs)
2. Create a branch using the following format `[issue_tag]-[issue_number]`
3. Locally, `checkout` that branch and begin developing 
4. Once you have finished developing and have done some\(minimal\) testing, submit a Pull Request
5. If your Pull Request requires changes, make change and resubmit
   1. Continue this loop until no more changes are requested
6. Merge the Pull Request and delete the branch

## Issues

GitHub Issues provide a nice, seamless way of recording and tracking any changes to the current code base

Learn more about GitHub Issues [here](https://guides.github.com/features/issues/)

### Creating Issues

This process is outlined [here](https://github.com/TJREVERB/infrastructure/tree/master/user_templates/templates/ISSUE)

1. Navigate to the target repository \(e.g. [https://github.com/TJREVERB/pfs](https://github.com/TJREVERB/pfs) \)
2. Navigate to the Issues tab
3. Click on the green "New Issue" button in the top right hand corner
4. Add a title to the new Issue
5. Add a body to the new Issue
   1. The body should explain what the issue is for
6. Add a label to the new Issue
   1. Click on the gear next to "Labels" on the right hand side column
   2. Choose a tag from the drop down box
7. Add a project to the new Issue
   1. Click on the gear next to "Projects" on the right hand side column
   2. Choose a project from the drop down box
8. Add assignees\(if relevant\)
9. Submit the Issue

## Pull Requests

In order to merge new code into the `master`branch, it is necessary to submit a Pull Request\(PR\) with the current programming lead and project leads as reviewers. Once the PR has been reviewed and any requested changes have been made will one of the leads merge the developmental branch into the `master`branch. 

To learn more about branching click [here](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell). To learn more about Pull Requests click [here](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests). There are **brief** explanations [below](development-workflow.md#branching).

#### Creating Pull Requests

This process is outlined [here](https://github.com/TJREVERB/infrastructure/tree/master/user_templates/templates/PULL_REQUEST)

1. Navigate to the target repository \(e.g. [https://github.com/TJREVERB/pfs](https://github.com/TJREVERB/pfs) \)
2. Navigate to the Pull Requests tab
3. Click on the green "New pull request" button in the top right hand corner
4. Add a title to the new Pull Request
5. Add a body to the new Pull Request
   1. The body should include a Description, Steps to Test\(how to test your changes\), and an Affected Areas\(what files did you add/edit/delete\) section
   2. Make sure to reference the original Issue 
6. Add a label to the new Pull Request
   1. Click on the gear next to "Labels" on the right hand side column
   2. Choose a tag from the drop down box
7. Add a project to the new Pull Request
   1. Click on the gear next to "Projects" on the right hand side column
   2. Choose a project from the drop down box
8. Add assignees
   1. This is different than "Reviewers". The person submitting the PR is always the "Assignee"
9. Add reviewers
   1. Reviewers should be the current programming lead along with the project leads
10. Submit the Pull Request

#### Branching

In most modern development workflows, it is often necessary to maintain a version\(or a "branch"\) of working code. Such code has been properly developed and tested and is proven to work.By convention, the place where this code is stored is called the `master`branch.

 The general rule of thumb is "Everything in `master`is immediately deployable""

Since `master`should only contain code that is verified to work, the development of new code has to be separate from `master`. Therefore, we create a "copy" of the `master`branch and make any change to the code base in the copied version. This is what is called branching.

Creating new branches is simple. Learn about creating branches [here](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-and-deleting-branches-within-your-repository). Once you create a new branch, run these commands in your local repository.

```text
$ git fetch #updates your local environment with the new branch
$ git checkout <branch name> #tells git that you are editing on the specified branch
```

#### Pull Requests

Because we want to ensure no buggy or nonworking code enters the `master`branch, it is necessary to review any incoming changes to the `master`branch. The way we conduct these ad hoc code reviews is through Pull Requests.

After a developer has created a new branch, made changes to that branch, and has tested their changes, the developer can submit a Pull Request on GitHub. The process to submit a Pull Request is outlined [above](development-workflow.md#creating-pull-requests). A Pull Request is basically a request to the official maintainers of the repository\(the programming lead and the project leads\) to allow a developer's changes into the `master` branch. Once a Pull Request is submitted, one of the leads will review the requested changes to the `master`and make sure that the changes are legitimate. If changes are required, the reviewer will request such changes and the developer is responsible for making those changes and resubmitting the Pull Request. 

When changes listed out in a Pull Request are deemed appropriate, one of the leads will _merge_ the development branch into the `master`branch. Merging basically means to take all the changes made in the development branch and add them to the `master`branch.





