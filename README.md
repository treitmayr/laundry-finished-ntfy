# LAUNDRY FINISHED NOTIFIER

This small repo shows how to turn a "dumb" washing machine into a somewhat smarter
one which is able to notify you via [ntfy.sh](https://ntfy.sh/) about the finished
laundry and the energy it consumed.

This can be done with a simple smart socket running [Tasmota](https://tasmota.github.io/docs/)
firmware, i.e. without running any MQTT broker or full-fledged home automation system.

## Design

The provided rules perform the following steps:
1. Wait for the current power consumption to rise to `StartWatts` or higher.
2. Store the current value of the total measured energy, and send a notification (topic "laundry") about the start of the laundry to the ntfy.sh server.
3. Wait for the current power consumption to drop to `DoneWatts` or lower.
4. Wait for the grace period.
5. Compute the energy difference and send a notification (topic "laundry") about the end of the laundry to the ntfy.sh server.

If during steps 4 the power rises back to at least `StartWatts`, the grace period timer is stopped and execution returns to step 3.

## Usage

### Setup

**Prerequisits**

* A smart socket already flashed with Tasmota, e.g. rebranded Gosund SP111 work well.
* Working network connection from PC to the socket
* Ansible (Ansible Core package is sufficient)
* [Tobias Richter's tasmota ansible role](https://galaxy.ansible.com/tobias_richter/tasmota)
* A running instance of [ntfy.sh](https://ntfy.sh/) (e.g. a self-hosted instance works well)
* Some ntfy.sh client(s), e.g. a cell phone, subscribed to the topic "laundry".

**Customization**

`inventory.yml`
* Adjust the host name to match yours.
* Adjust the power limit variables `StartWatts` and `DoneWatts` (specified in Watts) to match the standby and operational power of your washer.
* Adjust the grace period (specified in seconds) to slightly exceed the maximum time it takes you to pause the washer and add some more clothes.

`playbook.yml`
* Adjust the variables at the top of `playbook.yml` to fit your host name (for ntfy.sh) and your language.
* Please check the other settings (below the variables) if they match your needs,
  e.g. turning off MQTT, etc.
* Possibly add further settings.

**Invocation**

`ansible-playbook -i inventory.yml playbook.yml`

### Operation

**Prerequisits**

* Working network connection to the smart socket.

**Usage**

* Plug in your washing machine.
* Start the laundry.
* Never again forget to take out your wet clothes.
