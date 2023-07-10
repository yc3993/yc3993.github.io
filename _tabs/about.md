---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---
Important notes:
 - running command: bundle exec jekyll s
 - home brew install: export HOMEBREW_NO_INSTALL_FROM_API=1
 - security test: com.icbc.ndf

Timeout for Github connections:
1. ping github.com: timeout
2. [github.com ip lookup](https://www.whatsmydns.net/#A/github.com)
3. sudo vi /etc/hosts: put github ip in specified location to hosts file for dns lookup
4. ping github.com again
5. git config --global --unset http.proxy
 

> Add Markdown syntax content to file `_tabs/about.md`{: .filepath } and it will show up on this page.
{: .prompt-tip }
