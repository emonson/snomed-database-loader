# neo4j install and SNOMED load

Jump to section: [OS X](#osx) · [Ubuntu](#ubuntu) · [Sample queries](#queries)

## <a name="osx"></a>OS X (10.12)

### Homebrew

Homebrew is an easy way to install many packages on Macs. Follow the instructions
on [the homebrew home page](https://brew.sh/).

Short version: Paste into a terminal:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

By default it will install all packages into the `/usr/local` directory. The recommended
procedure is to make this directory writable for non-superusers, and then all the homebrew
installs don't need root permissions.

#### Install neo4j

```
brew update
brew install neo4j
```

#### neo4j config changes

*edit* `/usr/local/Cellar/neo4j/3.3.0/libexec/conf/neo4j.conf` (db in /usr/local/Cellar/neo4j/3.3.0/libexec/data)

*commented out*

`# dbms.directories.import=import`

*uncommented and modified*

`dbms.memory.heap.max_size=4G`

#### Starting neo4j server

The default user:password combination is neo4j:neo4j

When you first access neo4j you'll be forced to change the default password. 
I used `neo.log` if you want to follow the same instructions. (It comes into play below
when you run the Python scripts to load data and have to supply a base64-encoded password.)

To start neo4j, you can type `neo4j start`, but that will require you to kill the process
manually when you want to shut down the server. To have the process running in the console
in a way you can just `cntl-C` to quit, type 

```
neo4j console
```

To access the neo4j browser, put this URL into any web browser:

[http://localhost:7474/]()

#### Base64-encoded password

The command line scripts needs base64 encoding of the password, so did it in Python:

```
import base64
In [23]: base64.encodestring('neo.log')
Out[23]: 'bmVvLmxvZw==\n'
```

### Install Python 2.7 and py2neo

I always use the [Anaconda Python distribution](https://www.anaconda.com/download/) because
it comes with so many useful modules already compiled, and makes it easy to install others,
no matter what operating system you're on. The default is 
The neo4j SNOMED data loading scripts require Python 2 (rather than Python 3), so you
either need to install Python 2.7 to begin with, or if you already have an Anaconda Python 3.6
install, you can 
[easily add a second Python 2.7 environment](https://conda.io/docs/user-guide/tasks/manage-python.html#installing-a-different-version-of-python) 
that you can use to run the scripts.

Since I needed to switch over to Python 2.7 and run from there, I did:

```
source activate py27
conda install py2neo
```

### SNOMED version

I downloaded the US Edition in a zip file

`SnomedCT_USEditionRF2_PRODUCTION_20170901T120000Z.zip`

after unzipping that by double-clicking on it in the Finder, there is a directory called
`Full` within that folder that we'll need to give the path to for the python data loading
scripts. You'll see in the paths for the script arguments that I actually copied this
`Full` directory to where my scripts were, but that's not necessary -- you can point to 
them whereever they are.

### Run Python SNOMED loader command

In a Terminal window (under Applications->Utilities), change directory to the directory
containing the NEO4J Python scripts

```
cd /Users/emonson/Dropbox/People/AskData/MarciBowen/snomed-database-loader/NEO4J
```

Before you run the data loading scripts, as the instructions say, you need to create an 
empty directory for temporary and log files to do during the process. 
*This directory needs to be empty!* Which implies, that if you run part way, get an error,
and need to restart the scripts from the beginning, you'll need to empty out that directory
before it'll run.

```
mkdir neo4j_output
```

Then run the script:

```
python snomed_g_graphdb_build_tools.py db_build --release_type full --mode build --action create --rf2 /Users/emonson/Dropbox/People/AskData/MarciBowen/snomed-database-loader/NEO4J/Full/ --release_type full --neopw64 bmVvLmxvZw== --output_dir /Users/emonson/Dropbox/People/AskData/MarciBowen/snomed-database-loader/NEO4J/neo4j_output
```




## <a name="ubuntu"></a>Ubuntu 16.04 VM

This install was done on a fresh Ubuntu 16.04 virtual machine created on 
[Duke's Research Toolkits system](https://rtoolkits.web.duke.edu/). 
Note that out of the box this is only meant to be accessed through a terminal and SSH. 
OIT does have instructions that work, though, for 
[how to install XRDP](https://vcm.duke.edu/help/14) 
to make the VMs accessible through Microsoft Remote Desktop.

### Pages used for help installing

[https://neo4j.com/docs/operations-manual/current/installation/linux/debian/]()
[http://www.exegetic.biz/blog/2016/09/installing-neo4j-ubuntu-16-04/]()

### Java dependency for neo4j

```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

#### Checking installed Java version

```
rapiduser@rapid-873:~$ java -version
java version "1.8.0_161"
Java(TM) SE Runtime Environment (build 1.8.0_161-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.161-b12, mixed mode)
```

### Setting up to install neo4j

```
wget -O - https://debian.neo4j.org/neotechnology.gpg.key | sudo apt-key add -
echo 'deb http://debian.neo4j.org/repo stable/' | sudo tee -a /etc/apt/sources.list.d/neo4j.list
sudo apt-get update
```

#### Installing neo4j itself
*(3.3.3 said not available...)*

```
sudo apt-get install neo4j=3.3.0
```

#### Need unzip to uncompress SNOMED distribution

```
sudo apt-get install unzip
unzip SnomedCT_USEditionRF2_PRODUCTION_20170901T120000Z.zip
```


### Anaconda Python 2.7 version

```
curl -O https://repo.continuum.io/archive/Anaconda2-5.1.0-Linux-x86_64.sh
bash Anaconda2-5.1.0-Linux-x86_64.sh
pip install py2neo
```

### neo4j config changes

*edit* `/etc/neo4j/conf/neo4j.conf` (db in /var/lib/neo4j/data)

*commented out*

`# dbms.directories.import=/var/lib/neo4j/import`

*uncommented and modified*

`dbms.memory.heap.max_size=4G`

#### You need to change from the default neo4j password before connecting

```
sudo rm /var/lib/neo4j/data/dbms/auth 
sudo neo4j-admin set-initial-password neo.log
```

#### Base64-encoded password

The command line scripts needs base64 encoding of the password, so did it in Python:

```
In [23]: base64.encodestring('neo.log')
Out[23]: 'bmVvLmxvZw==\n'
```

### Run Python SNOMED loader command

Before you run the data loading scripts, as the instructions say, you need to create an 
empty directory for temporary and log files to do during the process. 
*This directory needs to be empty!* Which implies, that if you run part way, get an error,
and need to restart the scripts from the beginning, you'll need to empty out that directory
before it'll run.

```
mkdir neo4j_output
```

Then run the script:

```
python snomed_g_graphdb_build_tools.py db_build --release_type full --mode build --action create --rf2 /home/rapiduser/SnomedCT_USEditionRF2_PRODUCTION_20170901T120000Z/Full/ --release_type full --neopw64 bmVvLmxvZw== --output_dir /home/rapiduser/neo4j_output
```

## <a name="queries"></a>Sample Queries

```
// Find shortest path to root
MATCH p=shortestpath((o1:ObjectConcept { sctid: '59820001' })-[:ISA*0..10]->(o2:ObjectConcept { sctid: '138875005' }))
RETURN p;

// core concepts
match (o:ObjectConcept {sctid: '138875005'})<-[:ISA]-(b:ObjectConcept) return o,b;
```