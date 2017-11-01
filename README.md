Templater
==

This is a small, simple Perl script designed to generate files from template files by using environmental variables and file contents.   
I've written this to use in Docker containers, in particular to allow the of use *Docker swarm secrets* (https://docs.docker.com/engine/swarm/secrets/) as a key/value store for the templates.   
You would insert this script into your container via the Dockerfile and run it in your entrypoint script before launching the main process.


Usage:
====

`templater source_template destination_file`   


Example template:   
   
Text to replace with a value is denoted between a pair of double curly-braces containing the type of lookup; either `env` for environmental variables or `file` for the contents of a file.   
   

example.tmpl:
```
{"this": "{{ env THIS }}","something":"{{ env ELSE }}"}

this = {{ env THAT }}
this="{{ file /tmp/file }}"
```

`templater example.tmpl /etc/app/config.txt`

If the environmental variable or file doesn't exist the script will exit with an error and a non-zero exit code.




Note:
===

The use of this is entirely at your own risk.  This is a very basic, simple templating engine; there's very little in the way of sanity checking for your inputs (deliberately so).  The expected use is using Docker secrets to store small strings (database passwords etc.) which are then inserted into a config file.   

Your container will need `Perl` installed.  This is normally available in everything but the most minimal of container images.


Dockerfile example
===

A suggestion of how to insert the script into your container.

```
FROM yourbaseimage

ADD https://raw.githubusercontent.com/wheelybird/templater/master/templater /usr/local/bin/templater
CHMOD a+x /usr/local/bin/templater

...
...

CMD ["/usr/local/bin/your_entrypoint"]
```
