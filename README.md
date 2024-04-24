# Home Assistant Lima Setup

This repository can be used to setup [Home Assistant](https://github.com/home-assistant) Supervised with [Lima VMs](https://github.com/lima-vm/lima) on MacOS.
The Home Assistant Supervisor allows to use Home Assistant Add-ons and some other features, see [docs](https://www.home-assistant.io/installation/#advanced-installation-methods). With this you can do extensive testing of Home Assistant without needing additional hardware.

## Getting started

This was tested successfully on a MacBook M1.

### Install Lima
```sh
brew install lima
```

### Create Debian VM
The `homeassistant.yaml` Lima template reflects the requirements specified in the [Home Assistant Supervised requirements](https://github.com/home-assistant/architecture/blob/master/adr/0014-home-assistant-supervised.md#supported-operating-system-system-dependencies-and-versions) but still contains lots of default values.
You can use it as it or adjust it to your specific needs. 
```sh
limactl create --name homeassistant https://raw.githubusercontent.com/maxarndt/homeassistant-lima/main/lima_homeassistant.yaml
# OR ALTERNATIVELY after cloning this repository locally
limactl create --name homeassistant lima_homeassistant.yaml
```

### Start the VM
```sh
limactl start homeassistant
```

### Install Home Assistant Supervised
```sh
limactl shell homeassistant
sudo su -
```

Follow `Step 1`, `Step 3` and `Step 4` of the [Home Assistant supervised-installer guide](https://github.com/home-assistant/supervised-installer?tab=readme-ov-file#installation). You can skip `Step 2` because Docker is already included in your Lima VM.


<details>
  <summary>Help for `Step 3`:</summary>
  At the time of writing these docs the latest version was `1.6.0`:

  ```sh
  cd /tmp
  wget https://github.com/home-assistant/os-agent/releases/download/1.6.0/os-agent_1.6.0_linux_aarch64.deb
  dpkg -i os-agent_1.6.0_linux_aarch64.deb
  ```
</details>

<details>
  <summary>Help for `Step 4`:</summary>
  Select `qemuarm-64` if the configuration UI appears in your terminal.
</details>

### Open Home Assistant UI in your browser
Open http://localhost:8123 in your browser.
It may take some time until all Home Assistant containers are up and running.

### Troubleshoot
```sh
journalctl -f
```
Execute this inside your Lima VM to watch the logs of your Home Assistant setup.

#### Known issues
1. `WARNING (MainThread) [supervisor.jobs] 'BackupManager.do_restore_partial' blocked from execution, system is not healthy - privileged` -> Solved by restarting the VM with `limactl stop homeassistant` followed by `limactl start homeassistant` on the host machine.
2. `WARNING (MainThread) [supervisor.jobs] 'BackupManager.do_restore_partial' blocked from execution, no host internet connection` -> Solved by executing `ha network reload` inside the VM.
