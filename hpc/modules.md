# Modules

Environment module implementations: TCl modules, Lmod.

```bash
$ module avail # see availiable modules
$ module list # show currently loaded modules
$ module load <packages> # load module
$ module unload <packages> # unload module
$ module purge # unload everything
```

* `$PATH` environment variable controls where UNIX system looks for software. Specially `$PATH` is a list of directories that OS searches through for a command before cannot find.&#x20;
* `module load` actually adds a directory to the beginning of `$PATH`&#x20;
* Not only `$PATH`, module command manipulates other environment variables as well.&#x20;
