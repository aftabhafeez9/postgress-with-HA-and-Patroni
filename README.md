Clone this repo and run the following command
docker-compose up

After all the containers are up, run the following command to check the status of nodes
docker exec -it node1 patronictl -c /etc/patroni.yml list
+ Cluster: postgres-cluster (7611221409206767638) ------+-----+------------+-----+
| Member | Host  | Role    | State   | TL | Receive LSN | Lag | Replay LSN | Lag |
+--------+-------+---------+---------+----+-------------+-----+------------+-----+
| node1  | node1 | Leader  | running |  1 |             |     |            |     |
| node2  | node2 | Replica | running |  1 |   0/404D5D8 |   0 |  0/404D5D8 |   0 |
| node3  | node3 | Replica | running |  1 |   0/404D5D8 |   0 |  0/404D5D8 |   0 |
+--------+-------+---------+---------+----+-------------+-----+------------+-----+

All nodes are running and node1 is the leader and other two are replica of node1


stop node1
docker stop node1

now check the status again
docker exec -it node1 patronictl -c /etc/patroni.yml list
+ Cluster: postgres-cluster (7611221409206767638) --------+-----+------------+-----+
| Member | Host  | Role    | State     | TL | Receive LSN | Lag | Replay LSN | Lag |
+--------+-------+---------+-----------+----+-------------+-----+------------+-----+
| node2  | node2 | Leader  | running   |  2 |             |     |            |     |
| node3  | node3 | Replica | streaming |  2 |   0/404D6F0 |   0 |  0/404D6F0 |   0 |
+--------+-------+---------+-----------+----+-------------+-----+------------+-----+
node is not running and node2 is automatically becomes the leader by patorni 

now start node1 again
docker start node1

now check the atatis of all nodes
docker exec -it node2 patronictl -c /etc/patroni.yml list
+ Cluster: postgres-cluster (7611221409206767638) --------+-----+------------+-----+
| Member | Host  | Role    | State     | TL | Receive LSN | Lag | Replay LSN | Lag |
+--------+-------+---------+-----------+----+-------------+-----+------------+-----+
| node1  | node1 | Replica | streaming |  2 |   0/404D6F0 |   0 |  0/404D6F0 |   0 |
| node2  | node2 | Leader  | running   |  2 |             |     |            |     |
| node3  | node3 | Replica | streaming |  2 |   0/404D6F0 |   0 |  0/404D6F0 |   0 |
+--------+-------+---------+-----------+----+-------------+-----+------------+-----+

node1 is active and becomes the replica as the node2 is leader right now




