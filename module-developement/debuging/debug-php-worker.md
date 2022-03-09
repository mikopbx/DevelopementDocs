# Debug PHP Worker

At the beginning of work, several key workers are launched on the PBX:

* **WorkerApiCommands** - Service for processing REST API requests
* **WorkerCallEvents** - Service for recording raw CDR data
* **WorkerCdr** - Service for recording the final CDR data
* **WorkerModelsEvents** - Service for processing changes in PBX settings.
* **WorkerNotifyByEmail** - Email notification service.

To simplify debugging of these php services, the command has been added:

```
pbx-console debug WorkerCdr 192.168.0.12
```

* **WorkerCdr**   - service name
* **192.168.0.12** - IP address of the PC from which debugging is performed. PHP Storm should be running on the PC
