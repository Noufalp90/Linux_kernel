# Linux_kernel
Information about Linux kernel internals

# Systemd service
#### Systemd service for normal user:

- Service path on target: `~/.config/systemd/user/` 

  - Usually, the ~/.config/systemd/user/ directory will not exist, so it needs to be created

- Tell systemd to read latest service, issue following command:
```
systemctl --user daemon-reload
```
- Enable user systemd service:
```
systemctl --user enable SERVICE-NAME.service
```

#### ROOT user systemd service:

- service path on target: /etc/systemd/system/

- Tell systemd to read latest service, issue following command: 
```
sudo systemctl daemon-reload
```

- Enable systemd service :
```
sudo systemctl enable SERVICE-NAME.service
```
NOTE:
- To run a service during boot, add following code in the service file.
```
[Install]
WantedBy=multi-user.target
```
- To run a service during shutdown,
```
[Install]
WantedBy=shutdown.target
```

- Type=oneshot
Type is "oneshot", systemd makes sure that no services are being started/stopped until 
our service is fully initialized or until our service has started

#### How to run a service at shutdown

Create a oneshot service with an `ExecStop` with `RemainAfterExit=yes`. you don’t have to have an `ExecStart` with a oneshot service.
```
Type=oneshot
RemainAfterExit=yes
ExecStop=/bin/bash -c "echo Oneshot service - stop"
```
