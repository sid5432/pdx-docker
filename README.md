# pdx-docker
*Docker container for pypdx and pdxdisplay*

Here is a simple docker file container for my [pdxdisplay](https://github.com/sid5432/pdxdisplay)
program, used to view [PDX (Product Data eXchange)](https://en.wikipedia.org/wiki/PDX_(IPC-257X)) 
XML files.  

The *pdxdisplay* program is built on top of my [pypdx](https://github.com/sid5432/pypdx) Python
module that parses a PDX XML file into a database; it is a Python [Flask](http://flask.pocoo.org/)
application that provides a simple interface (via a web browser) to upload and display a PDX
XML file.  For more details, please see the github pages for *pypdx* and *pdxdisplay*.

The *Dockerfile* presented here is for building a docker container that runs *pdxdisplay* with
a [PostgreSQL database](https://www.postgresql.org).  The build depends on the file "supervisord.conf".
To build your own docker container with these files (assuming you have docker installed on your
system), download the files, and run soemthing like

	docker build -t pdxcontainer:latest .
    
in the same directory as the files.  This should pull all the necssary parts (with *Ubuntu:latest*
as the base) and build a docker image called *pdxcontainer:latest*.  If you want a pre-built
container, you can pull this from [dockerhub](https://hub.docker.com/r/sidneyli/pdx/) with

	docker pull sidneyli/pdx:latest

The container runs two main parts: the PostgreSQL database and the *pdxdisplay* app. It exposes
two ports: port 5432 (for PostgreSQL), and port 5000 (for pdxdisplay).  Run the container as
follows:

	docker run -p 5000:5000 sidneyli/pdx:latest

This should start the two services (using the [supervisor](http://supervisord.org/) 
process control system), after which you can 
connect to the *pdxdisplay* app (in your web browser) with the URL

	http://localhost:5000/
    
If you are interested in accessing the PostgreSQL database directly, run the docker container
as

	docker run -p 5000:5000 -p 6432:5432 sidneyli/pdx:latest

to expose the PostgreSQL port (on port 6432 on the host computer).  On the local host computer
connect to the database with

	psql -h localhost -U pdxuser -p 6432 pdx
    
(the database is *pdx*; the username is *pdxuser*).  The password is *billofmaterials*.  For
details about the tables and schema, please visit the [github page on pypdx](https://github.com/sid5432/pypdx).

The *pdxdisplay* docker container is presented here as a convenience for those who want to
try it out without going through the hassle of setting things up (especially the part of
setting up a PostgreSQL database, although it is not really that complicated).  As it is 
right now, the container is somwewhat bloated, and could use some trimming, but it does work.

Most recommendations on docker say **not** to run more than one service inside a container.
The container here does not follow that advice, mainly to make things simpler for the user.
If this violates your sense of decency, or if the bloat of the container bothers you, then
you really should set up your own PostgreSQL database (or use the simplier SQLite3 database),
then downloading the *pypdx* and *pdxdisplay* Python modules/programs and run them
directly (with Python, consider also the [virtual environment](https://docs.python.org/3/library/venv.html)
options). You'll be much happier.

(*Last Revised 2018-02-03*)

