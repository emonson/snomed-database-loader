# Access to Duke Research Toolkits Ubuntu install of SNOMED on neo4j

This will only work for people authorized on the project, which right now
are Marci Bowen and Rachel Richesson.

## Log into Research Toolkits site

Go to [the Research Toolkits site](https://rtoolkits.web.duke.edu/)
and use the link in the upper right to log in with your NetID.
(Requires Multi-factor Authentication.)

Under _Current projects_ you shold see one called SNOMED, with a description of
SNOMED CT tools exploration for Nursing. Click on that link to see the project details.

Within the project you'll see a RAPID asset. On the right under ATTRIBUTES it lists
both the hostname and you can click the blue button to see the password.

## Use Microsoft Remote Desktop to access the VM

If you're on a Mac you can download Microsoft Remote Desktop from the App store
(the version with a square icon).

Run Remote Desktop and create a new connection with the big _+_ icon in the upper-left
of the window. Fill in the information about the server in the SNOMED project.

- Connection name: anything you want
- PC name: the hostname from the research toolkits SNOMED project RAPID VM attributes
- User name: rapiduser (listed as "user" on the RT site)
- Password: password copied and pasted after clicking that blue button on the RT site

You can then close that window and double-click on the Connection name in the Remote
Desktop connections list. Windows should open, perhaps full screen, showing you the
Ubuntu (Linux) desktop environment after it finishes logging in.

## Starting neo4j

There's an Applications menu in the upper-left corner of the Ubuntu desktop environment.
First, we need to open a terminal window where we can type commands to run neo4j.

Go to `Applications -> System Tools -> MATE Terminal`

Type in at the blinking cursor:

```
sudo neo4j console
```

and hit return (or enter).

Go to `Applications -> Internet -> Firefox browser`

type in the URL: [http://localhost:7474]()

This should bring up the neo4j browser. The space at the top with the dollar sign
and blinking cursor is where you type in Cypher queries.

## Stopping neo4j

In the console window, hit `control-C` and it should stop the neo4j server process.

Now you can just quit Microsoft Remote Desktop.

## Sample Queries

```
// Find shortest path to root
MATCH p=shortestpath((o1:ObjectConcept { sctid: '59820001' })-[:ISA*0..10]->(o2:ObjectConcept { sctid: '138875005' }))
RETURN p;

// core concepts
match (o:ObjectConcept {sctid: '138875005'})<-[:ISA]-(b:ObjectConcept) return o,b;
```
