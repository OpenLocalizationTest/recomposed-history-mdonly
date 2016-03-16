# Pull request etiquette and best practices

For optimal functioning of the pull request workflow, keep the following in mind:

- <b>Understand the role of the pull request reviewer. The reviewer:</b>
  - Ensures the basic quality of the content
  - Prevents regressions in the repository
  - Provides feedback before merging

  Pull request reviewers are in a content governance role. The primary intent is not to simply merge whatever is submitted as quickly as possible. Expect feedback that will require you to make updates, especially for new and heavily revised articles.

- <b>Plan ahead with your pull request reviewer:</b>
  - For high-priority pull requests
  - Pull requests for timed/dated releases
  - Pull requests that change or add lots of files

- <b>Pull requests may take a few days to be merged</b>

  While pull request reviewers try to review PRs at least twice daily, all the pull request reviewers have other work to do, and some of that work might be higher priority than reviewing pull requests. 


## Practical guidance to make it easier for us to review and merge your pull requests

Pull requests are not all equal! Pull requests that are small in scope (minor updates to existing articles) are easier and faster to review. Pull requests that contain new articles or that contain a mix of updates and new articles take more time to review. You can help make the pull request queue work better using these best practices:

- Separate minor updates to existing articles from new articles or major rewrites by working on these changes in separate working branches. 

- When you delete articles or images, don't mix the deletions with new content additions or updates. Handle the changes/new content in a separate working branch.

- Do not assume your pull request will be merged immediately or even the same day. We are working through the private and public pull request queues daily, but may not get to all existing PRs. 

- Contact your pull request reviewer to expedite PRs only when absolutely necessary. You can request expedited PR handling for Red Zone, privacy, and security issues; for truly broken customer experiences; and for executive escalations. 

- For releases or refactoring of content, plan ahead with your PR reviewer. You may need his or her help to create a release branch or to coordinate merge times with publishing times so your content is published at the right time.

- If you are trying to coordinate updates made in the ACOM repo (ie, changes to left navigation, landing pages, redirects, or learning maps) with changes you are making in the azure-content-pr repository, you must coordinate that work ahead of time with your PR reviewer. Otherwise, you risk having a lot of broken links.
