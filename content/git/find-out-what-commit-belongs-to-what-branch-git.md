Title: Find out what commit belongs to what branch in git
Date: 2013-08-09
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/find-out-what-commit-belongs-to-what-branch-in-git/
disqus_identifier: 838 http://www.linuxsysadmintutorials.com/?p=838
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Find out what commit belongs to what branch in git

Ever needed to find what branch a certain git commit belong to?

Check out <a href="https://github.com/SethRobertson/git-what-branch">git-what-branch</a>.

```
$ cd /srv/git/project.git
$ perl git-what-branch --all ff00bb
remote/banch/example
```
