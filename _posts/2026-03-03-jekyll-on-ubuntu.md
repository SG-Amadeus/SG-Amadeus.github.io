---
title: Jekyll on Ubuntu
description: 在Ubuntu系统上安装Jekyll的完整指南，包括Ruby安装、环境配置和Jekyll安装步骤。
author: SG-Amadeus
date: 2026-03-03 00:00:00 +0800
categories: [Jekyll, Tutorial]
tags: [jekyll, ubuntu, installation, ruby]
---

# Jekyll on Ubuntu

## Install dependencies

Install Ruby and other prerequisites:

```bash
sudo apt-get install ruby-full build-essential zlib1g-dev
```

Avoid installing RubyGems packages (called gems) as the root user. Instead, set up a gem installation directory for your user account. The following commands will add environment variables to your ~/.bashrc file to configure the gem installation path:

```bash
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

Finally, install Jekyll and Bundler:

```bash
gem install jekyll bundler
```

That’s it! You’re ready to start using Jekyll.