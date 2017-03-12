---
title: Removing Secrets from GitHub
date: 2017-02-16 12:00:53.000000000 -06:00
header:
  teaser: "/assets/MLwpwcA.png"
categories:
- How-to Guides
tags:
- git
- GitHub
- keys
- passwords
- secrets
- sensitive data
header:
  teaser: /assets/rick-github-secrets.jpg
  overlay_image: /assets/gandalf-is-it-secret.jpg
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_color: "#5e616c"
  caption: Image courtesy of New Line Cinema, YouTube
excerpt: Is it secret?! Is it safe?!<br /><br /><br />What to do when you've committed keys, secrets, passwords, and/or other sensitive information to GitHub or any public Git repo
---
I did it. The one universal rule when it comes to source control/Git, and I broke it. I committed my OAuth key for my <a href="http://www.alexdglover.com/youpassbutter-slack-bot-part-1/" target="_blank">YouPassButter Slack app</a> to GitHub. Thankfully it was only a personal project, and not anything more important than that. Either way, I got a little spooked and wanted to go through the process of removing sensitive data from GitHub as a learning opportunity.
Before we get started on the walk-through, there are two things I'd like to mention upfront:

* Because of the distributed nature of Git, if someone cloned or forked your repo while it contained sensitive data, that data is compromised. There is no way to delete your data out of someone else's repo
* I will be following <a href="https://help.github.com/articles/removing-sensitive-data-from-a-repository/">GitHub's published process</a> using the 'git filter-branch' command. I'm mostly documenting this for my own future reference, and as a quasi-punishment for myself to emphasize the importance of not committing keys in the future (I could write "Don't commit keys to GitHub" 1000 times on a blackboard, but this seems more productive)

<h2>Process</h2>

1. If you don't already have the repo cloned locally, do that first, then 'cd' into your repo directory.
2. In my case, the sensitive data was in the 'app.rb' file in the root of my project repo. Before you execute this next command, be aware that <strong>it will delete the local copy of the file</strong> along with updating all of the commits in your commit history. You probably want to save a copy of your file somewhere before running this. With that in mind, let's execute the 'git filter-branch' command and look at the output:


   ```shell
   [alexdglover@host:~/Documents/workspace/rick-and-morty-bot] $ git filter-branch --force --index-filter \
   > 'git rm --cached --ignore-unmatch app.rb' \
   > --prune-empty --tag-name-filter cat -- --all
   Rewrite c6a89ccf770fe1745259af4dd5c1de740a8c2a1e (1/75) (0 seconds passed, remaining 0 predicted)    rm 'app.rb'
   Rewrite 17e90caa961fa7b801701abfbae9ce0c34a2a9d1 (2/75) (0 seconds passed, remaining 0 predicted)    rm 'app.rb'
   Rewrite ec633eb63f1c7eead43157e100006029cf8029a6 (3/75) (0 seconds passed, remaining 0 predicted)    rm 'app.rb'
   ...
   Rewrite c4d38d45a710b7e87c2d75820fe65d204f9014e1 (39/75) (1 seconds passed, remaining 0 predicted)    rm 'app.rb'
   Rewrite d2b25cdd3e6190b2d59c1239d5955245c76a173b (39/75) (1 seconds passed, remaining 0 predicted)    rm 'app.rb'
   Rewrite c4570086709c08364d769bcb1818c0a2631ea111 (39/75) (1 seconds passed, remaining 0 predicted)    rm 'app.rb'

   Ref 'refs/heads/master' was rewritten
   Ref 'refs/remotes/heroku/master' was rewritten
   WARNING: Ref 'refs/remotes/heroku/master' is unchanged
   Ref 'refs/remotes/origin/master' was rewritten
   [alexdglover@host:~/Documents/workspace/rick-and-morty-bot] $
   ```

   The command is going through each commit in the commit history and deleting the file containing the sensitive data. So far, so good.

3. Typically, in this step you'd create a .gitignore file, add the offending file containing sensitive data to the .gitignore, and then re-commit everything. In my case, I can't add app.rb to .gitignore because it's the bulk of my code. Instead, let's try removing the credentials manually (and replacing it with environment variable references), saving that file into our repo, re-committing app.rb, and see what happens.

   ```shell
   [alexdglover@mobile1:~/Documents/workspace/rick-and-morty-bot]$ vim app.rb # I re-created the app.rb file without the sensitive data
   [alexdglover@mobile1:~/Documents/workspace/rick-and-morty-bot]$ git status
   On branch master
   Your branch is up-to-date with 'origin/master'.
   Untracked files:
     (use "git add <file>..." to include in what will be committed)

     app.rb

   nothing added to commit but untracked files present (use "git add" to track)
   [alexdglover@mobile1:~/Documents/workspace/rick-and-morty-bot]$ git add app.rb
   [alexdglover@mobile1:~/Documents/workspace/rick-and-morty-bot]$ git commit -m 'adds app.rb back into repo after removing secrets'
   [master 346e3e5] adds app.rb back into repo after removing secrets
    1 file changed, 202 insertions(+)
    create mode 100644 app.rb
   [alexdglover@mobile1:~/Documents/workspace/rick-and-morty-bot]$ git push
   ```

   You can see that Git thinks app.rb is a <strong>completely</strong> new file, and is unaware that it ever existed. Another good sign. My 'git push' had a minor merge conflict with my GitHub upstream repo, but was trivial. Now I've strayed slightly from GitHub's instructions, let's move on to the last step and see if this works.

4. Finally, we need to execute 'git push origin --force --all' to overwrite our GitHub repository. Let's give it a try:

   ```shell
   [alexdglover@mobile1:~/Documents/workspace/rick-and-morty-bot] $ git push origin --force --all
   Everything up-to-date
   ```

   Well... that was a little anti-climactic. I'm assuming my earlier push already overwrote GitHub. Let's take a look in GitHub's web UI and see if we can find any trace of our sensitive data.

   <a href="http://i.imgur.com/MLwpwcA.png"><img class="aligncenter size-large" src="{{ site.baseurl }}/assets/MLwpwcA.png" alt="GitHub - history for app.rb file" width="1146" height="475" /></a>
   Excellent! I don't see the Slack client ID and secret in the code any more, and the commit history for that file only shows our most recent commit!

5. My repo is very simple and didn't use any other tags or branches. If you committed a secret/key/password to your repo and you do use tags, you'll also need to execute 'git push origin --force --tags' to overwrite all of the tagged commits.

<h2>Post Cleanup Process</h2>

If you're working with other developers, be sure to ask them to rebase (not merge) their local repos after you've cleaned up the sensitive data, otherwise they may re-introduce bad code or commit history containing the bad code. If you haven't done so already, this would also be a good time to change any credentials that you think may have been compromised. In my case, I simply needed to generate a new OAuth client secret for my Slack app.

I hope this was a helpful demonstration, and remember...

<h1>DON'T PUT CREDENTIALS INTO SOURCE CONTROL</h1>
