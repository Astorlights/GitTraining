Preparation:
```
git clone https://github.com/DevTrainings/premade_remote.git
cd premade_remote
git checkout documentation
git checkout master
git remote remove origin
```

# Working with remote repository

## Introduction

* So far we worked on local repository.
* In real world you would have a remote copy for several reasons:
  * Backup of the code (remote repository is backup on its own but the server
    usually has a backup too).
  * Central repository shared by multiple developers.
* Since _GIT_ is distributed source control management system you
  usually have multiple copies of the repository on various places.
  * All the copies are exactly same and equivalent.
  * You can synchronize with any repository around.
* Central repository usually gives you advices on how to publish
  the repository for the first time.

## Commands

* Let's publish our previously created repository:
  * Create an empty repository on your favorite server (for example _Github_). For simplicity let's use name `premade_remote`
* Setup the remote repository for the local repository:
```
> git remote add origin https://github.com/voloda/premade_remote.git
```
  * _origin_ is an alias used by various commands. It is also a commonly used
   alias for default remote.
  * [https://github.com/voloda/premade_remote.git](https://github.com/voloda/premade_remote.git) is the remote URL of __your__ repository so adjust it accordingly.
* Now publish your entire repository to the server:
```
> git push origin --mirror
Counting objects: 19, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (19/19), 4.08 KiB | 0 bytes/s, done.
Total 19 (delta 1), reused 0 (delta 0)
To https://github.com/voloda/premade_remote.git
 * [new branch]      documentation -> documentation
 * [new branch]      master -> master
```

## Comments

* Your repository can have multiple remotes.
  * This is used for example on _Github_ when you fork a repository and you run _clone_ repository using desktop client.
    * _origin_ remote usually points to your fork.
    * _upstream_ remote points to the main repository.
    * Different versions of the Github Desktop can map repositories differently.
    * You can use ```git remote --verbose``` to see details about mappings.

### Re-mapping local repository to a new server

If you move the repository to another _git_ server you can re-map your
remotes easily.

* Let's consider standard setup with remote named ```origin``` pointing to
  obsolete server.
* Let's remove the current mapping:

```
> git remote remove origin
```

* Now let's add the mapping with new location:

```
> git remote add origin https://server.com/MyRepo.git
```

* Here ```origin``` defines the name of remote.

Or alternatively you can just set the remote URL:

```
> git remote set-url origin https://server.com/MyRepo.git
```
