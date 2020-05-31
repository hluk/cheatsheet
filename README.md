This repository contains quick reference for various commands and
configuration.

# Ansible

Test command:

```bash
export ANSIBLE_DEBUG=1
ansible -vvvv --inventory=inventory host.dev.example.com --become-user postgres -k -K -m shell -a whoami
```

Install requirements and run a playbook:

```bash
export ANSIBLE_VAULT_PASSWORD_FILE=$PWD/../ansible-passwords.sh
# or --vault-password-file=...

ansible-galaxy install --force -r playbooks/dev/requirements.yml -p playbooks/dev/roles

ansible-playbook -vv --inventory=inventory --become --ask-pass --ask-become-pass --tags configure_users playbooks/dev/deploy.yml
```

Environment variables for k8s module:

```bash
export K8S_AUTH_API_KEY=$(shell oc whoami --show-token)
export K8S_AUTH_HOST=$(shell oc whoami --show-server)
```

# CMake

```bash
cd build
cmake -G Ninja -DCMAKE_BUILD_TYPE=DEBUG -DCMAKE_INSTALL_PREFIX=$PWD/install ~/dev/copyq
ninja install
```

# OpenShift

- `oc -n proj get pods`
- `oc -n proj get dc`
- `oc -n proj describe pod web-111-abcde`
- `oc -n proj get events`
- `oc -n proj rollout latest dc/web`
- `oc -n proj rsh web-111-abcde`
- `oc -n proj scale --replicas=0 dc web`

Logs:

- [kail](https://github.com/boz/kail) -- logs for all matching pods
- `kail -n proj -l deploymentconfig=web --since=1h`
- `kail -n proj -l service=web --since=20m`

Avoid using additional resources on deployment:

```yaml
---
apiVersion: v1
kind: DeploymentConfig
spec:
  strategy:
    type: Rolling
    rollingParams:
      maxSurge: 0
# ...
```

# Python

Python 3 virtualenv:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Python 2 virtualenv:

```bash
virtualenv -p python2 .venv
source .venv/bin/activate
```

Breakpoint:

```python
import ipdb; ipdb.set_trace()
```

Breakpoint in Python 3.7:

```python
breakpoint()
```

```bash
export PYTHONBREAKPOINT=0
export PYTHONBREAKPOINT=pdb.set_trace
export PYTHONBREAKPOINT=ipdb.set_trace
```

Profiling decorator:

```python
def profile(func):
    import cProfile
    import functools
    import io
    import pstats

    @functools.wraps(func)
    def wrap(*args, **kwargs):
        pr = cProfile.Profile()

        pr.enable()
        result = func(*args, **kwargs)
        pr.disable()

        s = io.StringIO()
        ps = pstats.Stats(pr, stream=s).sort_stats(pstats.SortKey.TIME)
        ps.print_stats()
        print(s.getvalue())
        return result

    return wrap


@profile
def foo():
    pass
```

# Vim

- `:windo difft` -- diff open windows
- `:setl ar` -- automatically reload file if changed
- `:read` -- open file read only
- `q/` -- search history
- `q:` -- command history
- `gf` -- open file which filename is under cursor
- `gi` -- go to last insert mode place
- `g;` -- go to last change
- `g,` -- go to next change
- `gq` -- reformat
- `~` -- change case of letter
- `:g/PATTERN/norm ...` -- do something with each matched line (e.g. delete with `dd`)
- `:args **/*.h | vert sall` -- open all matching files
- https://www.reddit.com/r/vim/wiki/vimrctips

# Zsh

- [Powerlevel10k](https://github.com/romkatv/powerlevel10k/) -- fast status line
- `ALT-H` -- show command help
- `C-x C-e` -- edit command line
