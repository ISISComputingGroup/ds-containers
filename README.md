# ds-containers
Data streaming container configuration. 
This currently spins up 3 redpanda brokers as well as the redpanda console which is available on port 8080. 

## Running
To use this compose file first create `.env` on your machine with these contents: 
```.env
EXT_HOST=<yourhost>
```
where `<yourhost>` is your externally facing hostname. 

then run 
`sudo nerdctl compose -f compose.yml up`

