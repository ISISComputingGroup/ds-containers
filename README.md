> [!IMPORTANT]  
> This is no longer used and the automation team will be managing the Kafka/redpanda instances going forwards. we may wish to use again for forwarder/filewriter if we decide a central instance is the way to go.


# ds-containers
Data streaming container configuration for a development cluster. 
This currently spins up 3 redpanda brokers (on port 9092, 29092, 39092 respectively) as well as the redpanda console which is available on port 8080. This can be used with replication, but preferably this is disabled for now as we are somewhat limited on disk space.

<details>
<summary>Host machine details</summary>
<br>
  
Currently this is running on a machine running Rocky 9 on the cloud, with `containerd` installed along with `nerdctl`. Check https://cloud.stfc.ac.uk/machines/ for more details. 

To set this up from scratch create a new machine, create a user `ds` (see keeper for PW) and add to sudoers (required unless you want to try and get rootless containerd working) by editing `/etc/sudoers.d/<newfilehere>` - you can pretty much copy the configuration from the `cloud` file. 
<br>

</details>

## Running

First clone this repo with 
`git clone https://github.com/ISISComputingGroup/ds-containers.git /home/ds/ds-containers`

To use this compose file first modify `.env` on your machine with these contents: 
```.env
EXT_HOST=x
EPICS_CA_ADDR_LIST=z
```
where `EXT_HOST` is your externally facing hostname,
`EPICS_CA_ADDR_LIST` is the EPICS CA gateway you want to use for the forwarder.

then run 
`sudo nerdctl compose -f compose.yml up -d` from ` /home/ds/ds-containers`

To stop all containers, but keep all data, run 
`sudo nerdctl compose -f compose.yml down` from ` /home/ds/ds-containers`


## Kafka setup 

Auto-topic creation is enabled by default, which means if you produce to a topic which does not exist it'll get created with 1 partition and 1 as its replication factor (ie. not replicated on other brokers)

Retention policy is in-place by default, set to 7 days with no byte-limit, longer term we can disable if we need to keep data.

Volumes are persistent so a `nerdctl compose down` will not cause any lost data. 

## Notes for production
This should not be used for production as by [redpandas own definition](https://docs.redpanda.com/current/get-started/quick-start/) deploying with a compose file is not production-worthy. If we were to deploy this on a production machine, we should use Kubernetes or a local deployment, with several machines acting as individual brokers. 
