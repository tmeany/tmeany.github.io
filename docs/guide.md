---
layout: default
title: Getting Started
section: rino/docs/guide
---

<section class="guide">

<div class="page-header">
  <h1>Rino guide</h1>
</div>

Rino aims to be like git but for data, and is made mostly for applications in scientific computing. It will take a snapshot of the input and output of a given simulation run and save it in a well organised repository.

    usage: rino <command> [<args>]

    rino commands:
        init         Initialises a rino repository
        track        Adds files for rino to track
        untrack      Removes files from rino tracking
        commit       Records all the tracked files to the repository
        list         Lists all of the commits made
        checkout     Restores the parameters of a certain commit

<h2 class="doc-header" id="example"><a href="#example">Example</a></h2>

Lets say you have a simulation, or data analysis program organised as follows:

    myanalysis/
      |
      |-- analysis.py
      |-- parameters.json
      |__ output/
      |     |
      |     |-- data.txt
      |     |-- plot.png

The ```analysis.py``` file takes parameters from ```parameters.json``` and generates some data, with a little bit of randomness thrown in, and writes the data and a plot to ```output```.

Lets assume that this program is run __a lot__ and there is a ton of different parameters to be checked out. It could become a pain to manage all the generated data manually. Thats where ```rino``` comes in!


Rino will manage all of the parameters and data for you! Just start with

    $ rino init
    rino: Initialised rino repository.

This will initialise a rino repository in the ```myanalysis``` folder. It simply makes a folder called ```rino/``` and a file called ```rinofile.json```.

    myanalysis/
      |
      |-- analysis.py
      |-- parameters.json
      |__ output/
      |     |
      |     |-- data.txt
      |     |-- plot.png
      |__ rino/
      |     |-- rinofile.json

Now lets say we want Rino to keep tabs on the ```parameters.json``` file and the ```output``` directory.

    $ rino track output/ -p parameters.json

    rino: 'output/' is now being tracked by rino.
    rino: 'parameters.json' is now being tracked as a parameter by rino.

When we want to save a snapshot of this data Rino will take care of it. We could easily
take out the ```-p``` option and have ```rino track output/ parameters.json``` and Rino will still track all the
files, but the ```-p``` option specifies to Rino that ```parameters.json``` contains an input which will be useful later.

To commit these tracked files to the repository we simply write

    $ rino commit -l "some label or tag" -m "some message about the data"

    rino: committing 'parameters.json'
    rino: committing 'output/data.txt'
    rino: committing 'output/plot.png'

where the ```-l``` and ```-m``` options are optional.

Rino will then create a commit in the ```rino/``` directory and put the data there.
Our ```rino``` directory now looks like this:

    rino/
      |-- rinofile.json
      |__ 10_36_Thu_May_2014_903152/
      |     |
      |     |-- commit.json
      |     |-- parameters.json
      |     |__ output/
      |     |     |
      |     |     |-- data.txt
      |     |     |-- plot.png

We can happily continue generating data and committing into Rino. If we want to get a list of the commits we simply
use

    $ rino list

    commit ac977d8c1e2342f29e6c7f79631a0d38
    author: eoinmurray  <me@eoinmurray.io>
    label(s): ['random_data']
    on Fri May 30 14 at 16:54
       a message about the data
    size: 45.3KB, files: 4

    commit b9a4181927d44525a8555fb0280e04d9
    author: eoinmurray  <me@eoinmurray.io>
    label(s): ['random_data', 'run_2']
    on Fri May 30 14 at 16:55
       another descriptive message
    size: 46.2KB, files: 4

    commit 2bf2321c35894bfda4cca5501837403e
    author: eoinmurray  <me@eoinmurray.io>
    label(s): ['random_data', 'exponential', 'run_3']
    on Fri May 30 14 at 16:55
       and another descriptive message....
    size: 46.2KB, files: 4


Now lets say we wish to revert our analysis directory to the state of a certain commit. Its easy:

    $ rino checkout b9a418

    Reverting to parameters from commit <b9a4181927d44525a8555fb0280e04d9>
    restoring: 'parameters.json'

This replaces the parameter files in the working directory with those from the commit. Dont worry
Rino will __not__ overwrite your data if its not committed!

To checkout all of the data and not just the parameters we pass the "-d" option

    $ rino checkout b9a418 -d

    Reverting to parameters from commit <b9a4181927d44525a8555fb0280e04d9>
    restoring: 'output/data.txt'
    restoring: 'output/plot.png'
    restoring: 'parameters.json'

## Commands

<h2 class="doc-header" id="initialise"><a href="#initialise">Initialise</a></h2>

Initialises a rino repository in the current directory

    usage: rino init

<h2 class="doc-header" id="track"><a href="#track">Track</a></h2>

tracks a path to be committed

    usage: rino track <pathspec> -p <pathspec>
       -p <pathspec>
           files/directories that contain inputs or parameters

<h2 class="doc-header" id="untrack"><a href="#untrack">Untrack</a></h2>
untracks a path so that it will not be committed

    usage: rino untrack <pathspec>

<h2 class="doc-header" id="commit"><a href="#commit">Commit</a></h2>

Commits all the tracked files/directories to the repository

    usage: rino commit [-m <message>] [-l <label>]
       -m <message>
          a message to be included in the commit
       -l <label>
          a label, or some identifier for the data
       -h
          shows this help message

<h2 class="doc-header" id="list"><a href="#list">List</a></h2>
Lists all the commits made to the repository

    usage: rino list

<h2 class="doc-header" id="checkout"><a href="#checkout">Checkout</a></h2>
Restores the parameter files from a commit

    usage: rino checkout <commit id> [-d]
       -d
          will also restore the non-parameter data
       -f
          forces overwrite of non-commited data

There are a lot of support services that I'll come up with and eventually add, like a built in server to allow a stylish way to browse simulation runs.

</section>