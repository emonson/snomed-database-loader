# neo4j install and SNOMED load on fresh Ubuntu 16.04 VM

## Pages used for help installing
[https://neo4j.com/docs/operations-manual/current/installation/linux/debian/]()
[http://www.exegetic.biz/blog/2016/09/installing-neo4j-ubuntu-16-04/]()

## Java dependency for neo4j
```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

### Checking installed Java version
```
rapiduser@rapid-873:~$ java -version
java version "1.8.0_161"
Java(TM) SE Runtime Environment (build 1.8.0_161-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.161-b12, mixed mode)
```

## Setting up to install neo4j
```
wget -O - https://debian.neo4j.org/neotechnology.gpg.key | sudo apt-key add -
echo 'deb http://debian.neo4j.org/repo stable/' | sudo tee -a /etc/apt/sources.list.d/neo4j.list
sudo apt-get update
```

### Installing neo4j itself
*(3.3.3 said not available...)*

`sudo apt-get install neo4j=3.3.0`

### Need unzip to uncompress SNOMED distribution

`sudo apt-get install unzip`

## Anaconda Python 2.7 version

```curl -O https://repo.continuum.io/archive/Anaconda2-5.1.0-Linux-x86_64.sh
bash Anaconda2-5.1.0-Linux-x86_64.sh
pip install py2neo```

## neo4j config changes
*edit* `/etc/neo4j/conf/neo4j.conf` (db in /var/lib/neo4j/data)

*commented out*

`# dbms.directories.import=/var/lib/neo4j/import`

*uncommented and modified*

`dbms.memory.heap.max_size=4G`

### You need to change from the default neo4j password before connecting

```
sudo rm /var/lib/neo4j/data/dbms/auth 
sudo neo4j-admin set-initial-password neo.log
```

### command line needs base64 encoding of the password, so did it in Python

```
In [23]: base64.encodestring('neo.log')
Out[23]: 'bmVvLmxvZw==\n'
```

## Run Python SNOMED loader command

```
python snomed_g_graphdb_build_tools.py db_build --release_type full --mode build --action create --rf2 /home/rapiduser/SnomedCT_USEditionRF2_PRODUCTION_20170901T120000Z/Full/ --release_type full --neopw64 bmVvLmxvZw== --output_dir /home/rapiduser/neo4j_output
```

# Sample Queries

```
// Find shortest path to root
MATCH p=shortestpath((o1:ObjectConcept { sctid: '59820001' })-[:ISA*0..10]->(o2:ObjectConcept { sctid: '138875005' }))
RETURN p;

// core concepts
match (o:ObjectConcept {sctid: '138875005'})<-[:ISA]-(b:ObjectConcept) return o,b;
```