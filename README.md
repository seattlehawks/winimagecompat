# Windows image compatibility
Testing, samples, and scripts for Windows image compatibility runbook

## Reducing the overhead of maintaining custom labels on each node
The Windows Server run book is predicated on the use of labels that contain the Windows version running on each node, and service constraints that reference those labels to bind a service using images built on a given Windows version to hosts running the same Windows version.

Note: For security reasons the ```docker node update``` command can only be run on Swarm masters, so it can't be used to set node labels based on worker settings that must be determined remotely.

Here are two alternate methods to automate setting labels for worker nodes containing their Windows version:

### Powershell script to add engine label
Docker engine labels are created with entries in the Docker [daemon configuration file](https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file). ```enginelabel.ps1``` creates an engine label named ```engine.labels.windowsversion``` with a value of the Windows version in use in the form of ```major.minor.build.revision```.

The script must be run on the Windows node and is intended for use as a step the node provisioning process.

### UCP API to set node labels
The Universal Control Plane (UCP) API allows setting node labels equivalent to the ```docker node label``` CLI command. ```nodelabel.py``` is a Python script that takes a Windows version as a parameter and creates a ```com.foo.windowsversion``` node label with the specified value on all nodes in the cluster where it does not exist.

The script can be run anywhere a client bundle has been sourced, and is intended as a step in provisioning new nodes in a cluster. This approach assumes all services in the cluster have constraints pinning them to a specific ```com.foo.windowsversion``` label value, allowing new nodes to be added to the cluster without being incorrectly assigned tasks prior to having their ```com.foo.windowsversion``` label set.

#### UCP API reference
https://docs.docker.com/datacenter/ucp/2.2/reference/api/#!/Node/NodeList
https://docs.docker.com/datacenter/ucp/2.2/reference/api/#!/Node/NodeUpdate
