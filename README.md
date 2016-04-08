The following convention is used for Node modules *that depend on* C++ libraries. This is to help avoid semantic versioning suprises that can flow upstream after new versions are published. 

Following this convention will make it easier to know:
- when a module might be affected by any of its newly published dependencies
- how to version your module and its dependencies

### Convention

(Using Mapnik as an example)

1. To avoid installing older versions: **favor using tilde `~` over x-range `0.x` for your module's dependencies**
2. If a module *does* directly depend on Mapnik as a library (and the module is *not* an app or service): **leave patch version flexible by using a tilde `~`.** This ensures that the module is flexible to patch-level Mapnik updates automatically
3. If a module *does not* directly depend on Mapnik, but instead depends on other Mapnik-dependent modules: **use tilde `~` for those dependencies**
4. If a module *does not* directly depend on Mapnik, but instead depends on other Mapnik-dependent modules, and you want the Mapnik-dependent modules to automatically increment on the minor-level: **you must also increment your module's version on the minor-level even if there are no API-breaking changes**


### C++ Module Revert Example

This goes through the scenario of upgrading one C++ node module and downgrading another at the same time, due to breaking changes. 
- The first C++ module includes bug fixes that we want (node-gdal). 
- The second C++ module has breaking changes that we want to revert and stabilize (node-mapnik).

For example, this is the `package.json` for your app, Nacho, at breakage:
```
"name": "Nacho",
"version": "0.1.0", 
"dependencies":
	"mapnik": "3.5.x",
	"gdal": "0.8.x"
```

You want to revert mapnik, but want to upgrade gdal. You would then change `package.json` to:
```
"name": "Nacho",
"version": "0.2.0", 
"dependencies":
	"mapnik": "3.4.x",
	"gdal": "0.9.x"
```


We want to release new Minor tags in this case, instead of Patch tags. For instance:
- If instead of a Minor release of Nacho, you tagged a Patch release, it could lead to unexpected surprises for other apps that requires Nacho...
- Jane has an app that requires Nacho
```"nacho": "0.1.x"```
- Jane automatically updates based on patch versions. This means she would get a new set of GDAL binaries within node-gdal `0.9.x` without knowing it, potentially causing breakages.

Best solution in this case:
Release a new Nacho Minor version `0.2.0` containing the reverted stable node-mapnik and the updated node-gdal. 
```
"name": "Nacho",
"version": "0.2.0", 
"dependencies":
	"mapnik": "3.4.x",
	"gdal": "0.9.x"
```
Immediately after, release a new Nacho Minor version `0.3.0` containing the newest debugged node-mapnik `3.5.x` and the updated node-gdal `0.9.x`:
```
"name": "Nacho",
"version": "0.3.0", 
"dependencies":
	"mapnik": "3.5.x",
	"gdal": "0.9.x"
```

Another way to look at it:

| version  | dependency              | dep version       | description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|----------|-------------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `0.0.0` | node-mapnik   node-gdal | `3.4.x`   `0.8.x` | start here, with previous versions of these node C++ dependencies                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `0.1.0` | node-mapnik   node-gdal | `3.5.x`   `0.8.x` | we had to upgrade node-mapnik to it's latest and greatest, but node-gdal stayed the same                                                                                                                                                                                                                                                                                                                                                                                                                |
| `0.2.0` | node-mapnik   node-gdal | `3.4.x`   `0.9.x` | we ran into an issue with the `0.8.x` version of node-gdal, which is a dependency of node-mapnik - had to update node-gdal and therefore update an older version of node-mapnik for the fix, which required us to downgrade node-mapnik in this repo. This means we have flipped versions and can lead to duplicate C++ binaries. Instead of a `0.0.1` release of this repo, we can publish a quick, one-off in `0.2.0` that takes into account the flipped dependencies without hurting other repos. |
| `0.3.0` | node-mapnik   node-gdal | `3.5.x`   `0.9.x` | Now we can get back on track, with both modules updated.                                                                                                                                                                                                                                                                                                                                                                                                                                                |