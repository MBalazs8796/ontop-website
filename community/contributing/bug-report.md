# Bug report

If you are sure you found a bug, use [GitHub Issues](https://github.com/ontop/ontop/issues) to submit the report; otherwise, start a discussion in the [mailing list](https://groups.google.com/d/forum/ontop4obda).

When reporting a bug, please make sure that you are using [the latest version of Ontop](/guide/getting-started) and provide a minimal reproducible example.

## Reporting a possible bug
* Perform a search between current and past issues. If it is an open issue, add a comment to the existing issue instead of opening a new one.
* Let us know which version and which setting of Ontop you are using (Protégé, SPARQL endpoint, CLI, Java API)
* State what happened and also state what you expected to see.
* If you have precise steps to reproduce the bug, explain each step and send us a set of ontology/mappings/query (as small as possible) so we can reproduce the problem. Otherwise send us the complete ontology, mapping and query files.
* If you cannot reliably reproduce the test provide details about how often the problem happens and under which conditions it normally happens.
* If the program generated an error, report what the error message was and how it occurred.
* Add the log file in debug mode to help us to track the issue (more information follows).

### How to generate the log file

#### Enable Protégé log

You should go to the folder *conf* of Protégé and modify the line of the *logback.xml* file from `<root Level="info">` to `<root Level="debug">`.
When you restart Protégé you will have more information in the command line, that can be useful to understand the problem.
You can send us the generated log file from Protégé starting from *Window -> Show log... -> Show log file -> protege.log*

#### Enable Command Line Interface log
Go to the folder *log* and modify the line of the *logback.xml* file from `<root Level="ERROR">` to `<root Level="debug">`.

#### Enable logging in the Docker image
Define the environment variable [`ONTOP_DEBUG`](https://hub.docker.com/r/ontop/ontop) (`ONTOP_DEBUG = "true"`).


## Asking for help or information
See [the support page](/community/support).
