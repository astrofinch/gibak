Copyright (C) 2008 Mauricio Fernandez <mfp@acm.org>

gibak -- backup tool based on Git
=================================
Since gibak builds upon the infrastructure offered by Git, it shares its main
strengths:
* speed: recovering your data is faster that cp -a...
* full revision history
* space-efficient data store, with file compression and textual/binary deltas
* efficient transport protocol to replicate the backup (faster than rsync)

gibak uses Git's hook system to save and restore the information Git doesn't
track itself such as permissions, empty directories and optionally mtime
fields.

Installation
============
You'll need OCaml and omake to compile gibak (it has been tested with OCaml
3.10.1). Just type

 $ omake

and copy the following executables to a directory in your path:

  find-git-files
  find-git-repos
  gibak
  ometastore

Usage
=====
Run gibak without any options to get a help message.

The normal workflow is:

 $ gibak init        # run once to initialize the backup system
 $ vim .gitignore    # edit to make sure you don't import unwanted files
                     # edit .gitignore files in other subdirectories
                     # you can get a list of the files which will be saved
                     # with  find-git-files  or  gibak ls-uncommited-files
 $ gibak commit      # the first commit will be fairly slow, but the following
                     # ones will be very fast
                     # it takes half an hour to import 2GB worth of data
                     # (200000 files)

.... later ....

 $ gibak commit

The backup will be placed in $HOME/.git. "Nested Git repositories" will be
rsync'ed to $HOME/.git/git-repositories and they will be registered as
submodules in the main Git repository (run  git help submodule  for more
information on submodules). You might want to use a cronjob to save snapshots
of the repositories in $HOME/.git/git-repositories 

After you gibak init, $HOME becomes a git repository, so you can use normal git
commands. If you use "gibak commit", however, new files will automatically be
added to the repository if they are not ignored (as indicated in your
.gitignore files), so you'll normally prefer it to "git commit".

About the backup store
----------------------
The backup data is placed in $HOME/.git. You can mount an external disk there
if you want your backup to reside in a different physical disk for resiliency
against disk crashes. You can also clone the repository (probably using a bare
repository --- without working tree --- for smaller space usage) and rsync
.git/git-repositories to remote machines for further protection.

License
=======
The gibak script is free software: you can redistribute it and/or modify it
under the terms of the GNU General Public License as published by the Free
Software Foundation, either version 2 of the License, or (at your option) any
later version.

The ometastore, find-git-files and find-git-repos programs are distributed
under the terms of the GNU Library General Public License version 2.1 (found
in LICENSE). All .ml source files are referred as "the Library" hereunder.

As a special exception to the GNU Lesser General Public License, you may link,
statically or dynamically, a "work that uses the Library" with a publicly
distributed version of the Library to produce an executable file containing
portions of the Library, and distribute that executable file under terms of
your choice, without any of the additional requirements listed in clause 6 of
the GNU Lesser General Public License.  By "a publicly distributed version of
the Library", we mean either the unmodified Library as distributed by the
author, or a modified version of the Library that is distributed under the
conditions defined in clause 2 of the GNU Lesser General Public License.  This
exception does not however invalidate any other reasons why the executable
file might be covered by the GNU Lesser General Public License.
