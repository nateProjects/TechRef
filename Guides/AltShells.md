
## Fish 
https://fishshell.com/
https://github.com/jorgebucaran/cookbook.fish
https://github.com/explosic4/fish-shell-cookbook
https://fishshell.com/docs/current/tutorial.html

`sudo apt-get install fish`

Example -
```
#!/usr/bin/env fish
for file in $argv[1]/*
    echo $file
end
```

## NuShell
https://www.nushell.sh/ - Great for parsing data!
Install -
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
sudo apt install build-essential
sudo apt install pkg-config libssl-dev
cargo install nu
```

Example -
```
#!/usr/bin/env nu
for file in ($nu.env.1)/* {
    echo $file
}
```

## Elvish
https://elv.sh/
elv.sh/learn/
https://github.com/elves/awesome-elvish

`sudo apt install elvish`

```
#!/usr/bin/env elvish

for file in $@1/* {
    echo $file
```

## Amber
https://amber-lang.com/
https://docs.amber-lang.com/

```
#!/usr/bin/env amber
import { exit, lines } from "std"

let path = "/tmp"
let files = $ls -H {path}$ failed {
  echo "Unable to list files in {path}"
  exit(1)
}

loop file in lines(files)
{
  echo file
}
```

