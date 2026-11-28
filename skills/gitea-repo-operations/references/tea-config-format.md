# tea config format

Path: `~/.config/tea/config.yml`

Shape (example):

```yml
logins:
    - name: local-gitea
      url: http://localhost:3000
      token: <personal-access-token>
      default: false
      ssh_host: localhost:3000
      ssh_key: /data/data/com.termux/files/home/.ssh/id_ed25519
      insecure: false
      user: captainabinav
      created: 1795788940
preferences:
    editor: false
    flag_defaults:
        remote: ""
```

Extract the token with:

```
grep 'token:' ~/.config/tea/config.yml | awk '{print $2}'
```

Extract the user with:

```
grep 'user:' ~/.config/tea/config.yml | awk '{print $2}'
```