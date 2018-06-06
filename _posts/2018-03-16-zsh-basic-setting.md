---
layout: post
title: CentOS Zsh 기본 및 자주 쓰는 플러그인 설치
comments: true
---
```
# yum install -y zsh

# echo $(which zsh) >> /etc/shells

# sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

# cd ~

# git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions

 

# vim ~/.zshrc

```

plugins 에 zsh-autosuggestions, zsh-syntax-highlighting 추가

theme 을 kennethreitz 로 변경(한글 커서 위치 문제 없음)

```

# source ~/.zshrc
```