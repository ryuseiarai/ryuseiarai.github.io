# Jekyll実行環境を用意する

## Install Ruby

Ruby実行環境を用意する。バージョン管理できるようrbenvをインストールする。

```zsh
% brew install rbenv ruby-build
% echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshrc
% echo 'eval "$(rbenv init - zsh)"' >> ~/.zshrc
% source ~/.zshrc
```

Rubyをインストールする。

```set up Ruby
% rbenv install --list
2.6.9
2.7.5
3.0.3
jruby-9.3.2.0
mruby-3.0.0
rbx-5.0
truffleruby-21.3.0
truffleruby+graalvm-21.3.0

Only latest stable releases for each Ruby implementation are shown.
Use 'rbenv install --list-all / -L' to show all local versions.

% rbenv install 2.7.5
% rbenv global 2.7.5
% ruby -v
ruby 2.7.5p203 (2021-11-24 revision f69aeb8314) [x86_64-darwin21]

※rbenvが反映されない場合はもう一度書きコマンド実行
% rbenv init
```

```
% gem install bundler
% vi Gemfile
source "https://rubygems.org"
gem 'github-pages', group: :jekyll_plugins

% bundle install --path vendor/bundle
```

```
% bundle exec jekyll serve 
```

