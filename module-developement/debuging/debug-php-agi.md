# Debug PHP-AGI

To debug a PHP AGI script, you need:

* Get the IP address of the PC from which debugging will be performed - e.g. **192.168.1.65**
* Set environment variables: `export XDEBUG_CONFIG="remote_port=9000 remote_host=`**`192.168.1.65`**`  ``remote_enable=1 remote_mode=req remote_autostart=0 remote_connect_back=0";`
* Restart the asterisk process

Full example:

```
killall safe_asterisk; 
killall asterisk; 
export XDEBUG_CONFIG="remote_port=9000 remote_host=192.168.1.65  remote_enable=1 remote_mode=req remote_autostart=0 remote_connect_back=0";
nohup safe_asterisk -f > /dev/null 2>&1 &
```

In MikoPBX, it is enough to run the command:

```
xdebug-enable-agi 192.168.1.65
```

Be careful! Debugging the AGI can disrupt the operation of the PBX
