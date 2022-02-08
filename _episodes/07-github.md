---
title: Remotes in GitLab
teaching: 30
exercises: 0
questions:
- "How do I share my changes with others on the web?"
objectives:
- "Explain what remote repositories are and why they are useful."
- "Push to or pull from a remote repository."
keypoints:
- "A local Git repository can be connected to one or more remote repositories."
- "Use the HTTPS protocol to connect to remote repositories until you have learned how to set up SSH."
- "`git push` copies changes from a local repository to a remote repository."
- "`git pull` copies changes from a remote repository to a local repository."
---

Version control really comes into its own when we begin to collaborate with
other people.  We already have most of the machinery we need to do this; the
only thing missing is to copy changes from one repository to another.

Systems like Git allow us to move work between any two repositories.  In
practice, though, it's easiest to use one copy as a central hub, and to keep it
on the web rather than on someone's laptop.  Most programmers use hosting
services like [GitHub](http://github.com), [BitBucket](http://bitbucket.org) or
[GitLab](http://gitlab.com/) to hold those main copies; we'll explore the pros
and cons of this in the final section of this lesson.

We'll be using GitLab, hosted internally, so that the repositories can be
managed. To start, load up [our GitLab](https://py-ph353.swan.ac.uk/users/sign_up) and create
an account. (Emails are not enabled, so you won't receive a confirmation email.)

Let's start by sharing the changes we've made to our current project with the
world.  Log in to GitLab, then click on the "Create a  Project" button, 

![Creating a Repository on GitLab (Step 1)](../fig/github-create-repo-01.png)
followed by "Create blank project"  

![Creating a repository on GitLab (Step 1a)](../fig/github-create-repo-02b.png)
to
create a new repository called `planets`:

(once you've created this, the button will move to the top right corner)

Name your repository "planets" and then click "Create Project":

![Creating a Repository on GitLab (Step 2)](../fig/github-create-repo-02.png)

The repository is created as "Private" by default; you don't want everyone on the
server to be able to access your coursework, so leave it like this for now.
Of course when writing your own research code, you will want to make it public so
that other scientists can learn from your code, and offer contributions to it.

As soon as the repository is created, GitLab displays a page with a URL and some
information on how to configure your local repository:

![Creating a Repository on GitLab (Step 3)](../fig/github-create-repo-03.png)

This effectively does the following on the GitLab server:

~~~
$ mkdir planets
$ cd planets
$ git init
~~~
{: .bash}

Our local repository still contains our earlier work on `mars.txt`, but the
remote repository on GitLab doesn't contain any files yet:

![Freshly-Made GitLab Repository](../fig/git-freshly-made-github-repo.svg)

The next step is to connect the two repositories.  We do this by making the
GitLab repository a [remote]({{ page.root }}/reference/#remote) for the local repository.
The home page of the repository on GitLab includes the string we need to
identify it:

> ## HTTP vs. SSH
>
> We use HTTP here because it does not require additional configuration.  After
> the workshop you may want to set up SSH access, which is more secure, by
> following one of the great tutorials from
> [GitHub](https://help.github.com/articles/generating-ssh-keys),
> [Atlassian/BitBucket](https://confluence.atlassian.com/display/BITBUCKET/Set+up+SSH+for+Git)
> and [GitLab](https://about.gitlab.com/2014/03/04/add-ssh-key-screencast/)
> (this one has a screencast).
{: .callout}

![Changing the Repository URL on GitLab](../fig/github-find-repo-string.png)

Copy that URL from the browser, go into the local `planets` repository, and run
this command:

~~~
$ git remote add origin http://py-ph353.swan.ac.uk/vlad/planets.git
~~~
{: .bash}

Make sure to use the URL for your repository rather than Vlad's: the only
difference should be your username instead of `vlad`.

We can check that the command has worked by running `git remote -v`:

~~~
$ git remote -v
~~~
{: .bash}

~~~
origin   http://py-ph353.swan.ac.uk/vlad/planets.git (push)
origin   http://py-ph353.swan.ac.uk/vlad/planets.git (fetch)
~~~
{: .output}

The name `origin` is a local nickname for your remote repository. We could use
something else if we wanted to, but `origin` is by far the most common choice.

Once the nickname `origin` is set up, this command will push the changes from
our local repository to the repository on GitLab:

~~~
$ git push origin main
~~~
{: .bash}

~~~
Counting objects: 9, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (9/9), 821 bytes, done.
Total 9 (delta 2), reused 0 (delta 0)
To http://py-ph353.swan.ac.uk/vlad/planets
 * [new branch]      main -> main
Branch main set up to track remote branch main from origin.
~~~
{: .output}

> ## Proxy
>
> If the network you are connected to uses a proxy there is an chance that your
> last command failed with "Could not resolve hostname" as the error message. To
> solve this issue you need to tell Git about the proxy:
>
> ~~~
> $ git config --global http.proxy http://user:password@proxy.url
> $ git config --global https.proxy http://user:password@proxy.url
> ~~~
> {: .bash}
>
> When you connect to another network that doesn't use a proxy you will need to
> tell Git to disable the proxy using:
>
> ~~~
> $ git config --global --unset http.proxy
> $ git config --global --unset https.proxy
> ~~~
> {: .bash}
{: .callout}

> ## Password Managers
>
> If your operating system has a password manager configured, `git push` will
> try to use it when it needs your username and password.  For example, this
> is the default behavior for Git Bash on Windows. If you want to type your
> username and password at the terminal instead of using a password manager,
> type:
>
> ~~~
> $ unset SSH_ASKPASS
> ~~~
> {: .bash}
>
> in the terminal, before you run `git push`.  Despite the name, [git uses
> `SSH_ASKPASS` for all credential
> entry](http://git-scm.com/docs/gitcredentials#_requesting_credentials), so
> you may want to unset `SSH_ASKPASS` whether you are using git via SSH or
> https.
>
> You may also want to add `unset SSH_ASKPASS` at the end of your `~/.bashrc`
> to make git default to using the terminal for usernames and passwords.
{: .callout}

Our local and remote repositories are now in this state:

![GitLab Repository After First Push](../fig/github-repo-after-first-push.svg)

> ## The '-u' Flag
>
> You may see a `-u` option used with `git push` in some documentation.  This
> option is synonymous with the `--set-upstream-to` option for the `git branch`
> command, and is used to associate the current branch with a remote branch so
> that the `git pull` command can be used without any arguments. To do this,
> simply use `git push -u origin main` once the remote has been set up.
{: .callout}

We can pull changes from the remote repository to the local one as well:

~~~
$ git pull origin main
~~~
{: .bash}

~~~
From http://py-ph353.swan.ac.uk/vlad/planets
 * branch            main     -> FETCH_HEAD
Already up-to-date.
~~~
{: .output}

Pulling has no effect in this case because the two repositories are already
synchronized.  If someone else had pushed some changes to the repository on
GitLab, though, this command would download them to our local repository.

> ## GitLab GUI
>
> Browse to your `planets` repository on GitLab.
> Under the Code tab, find and click on the text that says "XX commits" (where "XX" is some number).
> Hover over, and click on, the three buttons to the right of each commit.
> What information can you gather/explore from these buttons?
> How would you get that same information in the shell?
{: .challenge}

> ## GitLab Timestamp
>
> Create a remote repository on GitLab.  Push the contents of your local
> repository to the remote.  Make changes to your local repository and push
> these changes.  Go to the repo you just created on GitLab and check the
> [timestamps]({{ page.root }}/reference/#timestamp) of the files.  How does GitLab record
> times, and why?
{: .challenge}

> ## Push vs. Commit
>
> In this lesson, we introduced the "git push" command.
> How is "git push" different from "git commit"?
{: .challenge}

> ## Fixing Remote Settings
>
> It happens quite often in practice that you made a typo in the
> remote URL. This exercise is about how to fix this kind of issues.
> First start by adding a remote with an invalid URL:
>
> ~~~
> git remote add broken http://py-ph353.swan.ac.uk/this/url/is/invalid
> ~~~
> {: .bash}
>
> Do you get an error when adding the remote? Can you think of a
> command that would make it obvious that your remote URL was not
> valid? Can you figure out how to fix the URL (tip: use `git remote
> -h`)? Don't forget to clean up and remove this remote once you are
> done with this exercise.
{: .challenge}

> ## GitLab License and README files
>
> In this section we learned about creating a remote repository on GitLab, but when you initialized your
> GitLab repo, you didn't add a README.md or a license file. If you had, what do you think would have happened when
> you tried to link your local and remote repositories?
{: .challenge}
