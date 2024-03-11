---
layout: post
title: GitHub Blog (github.io) 개설 Guide
date: 2024-03-09 23:14 -0800
environment: macOS
tags: GitHub blog
---
> {{ page.environment }} 기반 guide이므로, 다른 platform에서는 절차가 다를 수 있다.
{: .prompt-info }

## Setup GitHub Repository
> 다른 블로그에서는 theme를 적용하려면 [Jekyll Themes][jekyllthemes]에서 다운받아서 덮어씌우라고 하는데, build fail이 발생해서 [chirpy-starter][chirpystarter]를 통해 진행했다.
{: .prompt-info }

1. [GitHub][github]에서 계정을 생성한다.

2. [chirpy-starter][chirpystarter]에 접속한다.

3. 우측 상단 `Use this template` > `Create a new repository`를 클릭한다.

4. `Repository name` input에 `[USER_NAME].github.io`를 입력한다. `[USER_NAME]`은 자신의 `GitHub` 계정 ID이다.

5. `Create repository` 버튼을 클릭한다.

6. `https://github.com/[USER_NAME]/[USER_NAME].github.io`에 접속한 후 `Settings` tab을 클릭한다.

7. `Pages` tab을 클릭하고 `Build and deployment`의 `Source` combobox를 클릭하여 `GitHub Actions`를 선택한다.

## Setup Local Environment
[Jekyll requirements][jekyllrequirements]에서 platform에 따라 설정한다.

1. `terminal` app을 통해 아래 command를 실행하여 `homebrew`를 설치한다. 이미 `homebrew`를 설치한 경우 생략 가능하다.
```text
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

2. `chruby`, `ruby-install`, `xz`를 설치한다.
```text
brew install chruby ruby-install xz
```

3. `ruby`를 설치한다. (오래 걸린다.)
```text
ruby-install ruby 3.1.3
```

4. `terminal` app의 default shell은 `bash` shell이다. 아래 command를 통해 `zshrc` shell로 변경한다. [Which Shell Am I Using? How Can I Switch?][whichshellamiusinghowcaniswitch] 참고.
```text
echo "source $(brew --prefix)/opt/chruby/share/chruby/chruby.sh" >> ~/.zshrc
echo "source $(brew --prefix)/opt/chruby/share/chruby/auto.sh" >> ~/.zshrc
echo "chruby ruby-3.1.3" >> ~/.zshrc # run 'chruby' to see actual version
```

5. `terminal` app 재실행 후, `ruby` version이 3.1.3p185 (2022-11-24 revision 1a6b16756e) 또는 최신 버전인지 확인한다. {{ page.environment }}는 `bash` shell에 기본적으로 2.x version의 `ruby`가 설치되어 있다.
```text
ruby -v
```

6. `jekyll`을 설치한다.
```text
gem install jekyll
```

7. `[USER_NAME].github.io` repository에서 `<> Code` 버튼을 클릭했을 때 표시되는 Clone HTTPS URL을 복사한다.

8. local file을 저장할 경로에서 다음 command를 실행한다. `[GITHUB_IO_REPOSITORY]`는 위에서 복사한 Clone HTTPS URL이다.
```text
git clone [GITHUB_IO_REPOSITORY]
```

9. `Visual Studio Code`를 실행하고, Git Clone을 수행한 경로를 연다.

10. `Terminal`에서 `zsh` shell을 연다.

11. `Gemfile` 내용을 기반으로 필요한 `gem`을 설치한다.
```text
bundle install
```

12. local server를 실행하고, web browser에서 [http://127.0.0.1:4000/](http://127.0.0.1:4000/)에 정상적으로 접속되는지 확인한다.
```text
bundle exec jekyll serve
```

13. `Visual Studio Code`의 `Source Control` tab을 클릭하고 `Stage All Changes`를 클릭한다.

14. Commit Message를 입력하고 `Commit`을 클릭한 후, `Sync Changes`를 클릭한다.

15. `https://[USER_NAME].github.io`에 정상적으로 접속되는지 확인한다. `https://github.com/[USER_NAME]/[USER_NAME].github.io`에 접속한 후 `Actions` tab에서 build 및 deploy 상태를 확인할 수 있다.

# References
- [GitHub](https://github.com)
- [jekyllrb](https://jekyllrb.com)
- [Which Shell Am I Using? How Can I Switch?](https://www.moncefbelyamani.com/which-shell-am-i-using-how-can-i-switch/)

[jekyllthemes]: http://jekyllthemes.org
[chirpystarter]: https://github.com/cotes2020/chirpy-starter
[github]: https://github.com
[jekyllrequirements]: https://jekyllrb.com/docs/installation/#requirements
[whichshellamiusinghowcaniswitch]: https://www.moncefbelyamani.com/which-shell-am-i-using-how-can-i-switch/