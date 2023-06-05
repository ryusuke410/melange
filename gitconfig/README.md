## Setup

`~/.gitconfig`, `~/.gitignore_global`, `~.ssh/config` を example を参考に編集。

依存物を生成
```
# commit のテンプレは好きなものにしてください。空のファイルでも OK.
touch ~/.stCommitMsg
```


## GitHub 用に ssh 鍵ペアを設定

### 鍵の生成
アカウントごとに鍵ペアが必要。

ssh-keygen: 以下のコマンドで必要に応じて変数を決める。
```
(\
  SSHKEY_SUFFIX=account0 &&\
  EMAIL_ADDR=email-account0@example.com &&\
  SSH_CLIENT_NAME="$(hostname)" &&\
  \
  \
  ssh-keygen -t ed25519 -N '' \
    -C "${SSH_CLIENT_NAME} ${EMAIL_ADDR}" \
    -f ~/.ssh/id_${SSHKEY_SUFFIX} &&\
  :\
)
```

### 鍵の upload
公開鍵コピーして GitHub に upload
```
(\
  SSHKEY_SUFFIX=account0 &&\
  \
  \
  (cat ~/.ssh/id_${SSHKEY_SUFFIX}.pub | pbcopy) &&\
  :\
)
```

## Git config をアカウントごとに設定
`~/account0/` 以下では、git がアカウント用の設定になるように設定する。

`~/.gitconfig`
```
[includeIf "gitdir:~/account0/**"]
  path = ~/.gitconfig_account0
```

`~/.gitconfig_account0`
```
[user]
	name = name-account0
	email = email-account0@example.com

[core]
  sshCommand = ssh -i ~/.ssh/id_account0
```

## ssh config
GitHub アカウントごとに ssh config (`~/.ssh/config`) を編集しなくて OK。ただし、以下は追加しておく必要がある。

`~/.ssh/config`
```
Host github.com
  HostName ssh.github.com
  Port 443
  User git
  IdentityFile ~/.ssh/id_rsa
  IdentitiesOnly yes

Host *
  IdentityFile ~/.ssh/id_rsa
```


## 設定のテスト

```
git -i ~/.ssh/id_account0 -T git@github.com
# =>
# Hi account0! You've successfully authenticated, but GitHub does not provide shell access.

cd ~/account0
$(git config --get core.sshCommand) -T git@github.com
# =>
# Hi account0! You've successfully authenticated, but GitHub does not provide shell access.
```
