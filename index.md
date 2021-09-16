# Tips and Tools

A non ordered list of tools that I generally find useful. At least one will surprise you (or not)

# [tldr](https://github.com/tldr-pages/tldr)

The tldr-pages project is a collection of community-maintained help pages for command-line tools, that aims to be a simpler, more approachable complement to traditional man pages.

![demo](https://raw.githubusercontent.com/tldr-pages/tldr/main/images/tldr.svg)

# [bat](https://github.com/sharkdp/bat)

A cat clone with syntax highlighting and Git integration.

![demo](https://camo.githubusercontent.com/7b7c397acc5b91b4c4cf7756015185fe3c5f700f70d256a212de51294a0cf673/68747470733a2f2f696d6775722e636f6d2f724773646e44652e706e67)

# [rg](https://github.com/BurntSushi/ripgrep)

Stupidly fast grep

# [Darcs to git](http://darcs.net/Using/Convert)

```bash
$ cd repo
$ git init ../mirror
$ darcs convert export | (cd ../mirror && git fast-import)
```

# [bandwhich](https://github.com/imsnif/bandwhich)

This is a CLI utility for displaying current network utilization by process, connection and remote IP/hostname

![demo](https://raw.githubusercontent.com/imsnif/bandwhich/main/demo.gif)

# [Kitty](https://github.com/kovidgoyal/kitty)

The fast, feature-rich, GPU based terminal emulator

- Offloads rendering to the GPU for lower system load
- Uses threaded rendering for absolutely minimal latency

kitty +kitten ssh myserver

![demo](https://sw.kovidgoyal.net/kitty/_images/screenshot.png)

# Pretty Darcs diff

darcs diff --diff-command "kitty +kitten diff %1 %2"

# [fish](https://github.com/fish-shell/fish-shell)

A wonderful shell <3 

Can be annoying with bash compatibilities. You should also look into [oh my fish](https://github.com/oh-my-fish/oh-my-fish)

# [Firefox google search shortcuts](https://addons.mozilla.org/en-US/firefox/addon/google-search-result-shortcuts/)

Remember when you could easily navigate google results with the keyboard ? Those were the days

# [Vim firefox](https://addons.mozilla.org/en-US/firefox/addon/vimium-ff/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search)

# [present](https://github.com/vinayak-mehta/present)

A terminal-based presentation tool with colors and effects eating some markdown.

[demo](https://raw.githubusercontent.com/vinayak-mehta/present/master/docs/_static/demo.gif)

# [zulip-term](https://github.com/zulip/zulip-terminal)

Zulip Terminal is the official terminal client for Zulip.

Mixed experience. Some bugs here and there, but still a good tool

# [Regex101](https://regex101.com/)

With this, even I can understand or even write a regex

[demo](https://user-images.githubusercontent.com/9568999/106061037-b659a580-60a9-11eb-8ff8-ea1c54ac9084.png)

# Jenkins logs dl

Crappy script to download jenkins logs from the terminal

```bash
#!/bin/bash

source ~/.jenkins.conf

repo=$1
job_number=$2

curl --connect-timeout 5 \
     --max-time 10 \
     --retry 5 \
     --retry-delay 0 \
     --retry-max-time 40 \
     -u "$user:$token" \
     -k "$url/job/$repo/$job_number/consoleText" \
     -o "$repo.$job_number"
```

```bash
➜  ~ cat .jenkins.conf
user=julien
token=blabla
url=https://my.jenkins.url
```

# GitLab Issue Creator

```bash
python3 script "issue title" "issue content"
```

Set assignee_id, PRIVATE-TOKEN, DEFAULT_LABEL, GITLAB_URL
```python
import click
import re
import requests
import os

from sys import argv

assignee_id=0
headers = {
    'PRIVATE-TOKEN': 'TOKEN',
    'Content-Type': 'application/json',
}

title=argv[1]
content=argv[2]

if click.confirm(f'Create issue {title} with content {content} ?', default=True):  
    payload = {'title':title,'labels':'DEFAULT_LABEL','assignee_id':assignee_id,'description':content}
    response = requests.post('https://GITLAB_URL/api/v4/projects/117/issues', headers=headers, json=payload, verify=False)
    print(f'response: {response}')
    print(f'response code: {response.status_code}')
```
