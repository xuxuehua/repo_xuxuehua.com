---
title: "browsh"
date: 2021-03-06 20:58
---
[toc]



# browsh



# installation



## Debian/Ubuntu

```
wget https://github.com/browsh-org/browsh/releases/download/v1.6.4/browsh_1.6.4_linux_amd64.deb
sudo apt install ./browsh_1.6.4_linux_amd64.deb
rm ./browsh_1.6.4_linux_amd64.deb
browsh
```



## Redhat/Fedora

```
curl -o browsh.rpm -L https://github.com/browsh-org/browsh/releases/download/v1.6.4/browsh_1.6.4_linux_amd64.rpm
rpm -Uvh ./browsh.rpm
rm ./browsh.rpm
browsh
```







# Operation

Here is the list of keybindings to use Browsh text-based browser.

- **F1** - Opens the documentation;
- **ARROW KEYS**, **PGUP**, **PGDN** - Scrolling;
- **CTRL+q** - Exit Browsh;
- **CTRL+l** - Focus the URL bar;
- **BACKSPACE** - Go back in history;
- **CTRL+r** - Reload page;
- **CTRL+t** - Open a new tab;
- **CTRL+w** - Close tab;
- **CTRL+TAB** - Switch to next tab;
- **ALT+SHIFT+p** - Takes a screenshot.
- **ALT+m** - Toggles monochrome mode. It is useful for overcoming rendering problems on older terminals;
- **ALT+u** - Toggles the user agent between a desktop and a mobile device. Useful for smaller terminals that want to use a more suitable layout.



# Appendix

https://www.brow.sh/docs/installation/

https://ostechnix.com/browsh-a-modern-text-browser-that-supports-graphics-and-video/

