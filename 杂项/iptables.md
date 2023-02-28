## Saving Rules

Iptables rules are ephemeral, which means they need to be manually saved for them to persist after a reboot.

On Ubuntu, one way to save iptables rules is to use the `iptables-persistent` package. Install it with apt like this:

```bash
sudo apt install iptables-persistent
```

Copy

During the installation, you will be asked if you want to save your current firewall rules.

If you update your firewall rules and want to save the changes, run this command:

```bash
sudo netfilter-persistent save
```

Copy

Other Linux distributions may have alternate ways of making your iptables changes permanent. Please refer to the relevant documentation for more information.