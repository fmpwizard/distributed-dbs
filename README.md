## Init system

### Install python:

    ansible-playbook  init.yml -i hosts/vm  -u diego --ask-become-pass
    
### Install mongodb

    ansible-playbook dev.yml --tags mongodb --ask-become-pass -i hosts/vm
    
Then run these on the primary you are selecting: (change ip addresses for hostnames)

```
rs.initiate( {
   _id : "mongo-repl-set",
   members: [ { _id : 0, host : "192.168.2.102:27017" } ]
})
rs.add("192.168.2.103")
rs.add("192.168.2.104")
```

### installing CockroachDB

*I'm sure there is a better way than running 3 diff tags, will look into it later*

    ansible-playbook --tags cockroachdb             --ask-become-pass -i hosts/vm dev.yml
    ansible-playbook --tags cockroachdb-primary     --ask-become-pass -i hosts/vm dbservers-seed.yml 
    ansible-playbook --tags cockroachdb-secondary   --ask-become-pass -i hosts/vm dbservers-non-seed.yml 

### Installing Couchbase community 4.x

    ansible-playbook dbservers.yml --tags couchbase --ask-sudo-pass -i hosts/vm
    ansible-playbook dbservers.yml --tags couchbase --ask-sudo-pass -i hosts/vm

**Yes, run it twice, the first one will fail due to some systemd error but the second run will complete, Couchbase 5.0 will support Ubuntu 16.04 officially**

Go to http://<any couchbase-server-ip/hotname>:8091 and follow the prompts to setup the cluster.
Then go to the other nodes, same port and **join** the cluster setup on the previous step (use the same user/pass to join as defined on prev step)