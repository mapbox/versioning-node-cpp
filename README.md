The following convention is used for Node modules *that depend on* C++ libraries. This is to help avoid semantic versioning suprises that can flow upstream after new versions are published. 

Following this convention will make it easier to know:
- when a module might be affected by any of its newly published dependencies
- how to version your module and its dependencies

### Convention

(Using Mapnik as an example)

1. To avoid installing older versions: **favor using tilde `~` over x-range `0.x` for your module's dependencies**
2. If a module *does* directly depend on Mapnik as a library (and the module is *not* an app or service): **leave patch version flexible by using a tilde `~`.** This ensures that the module is flexible to patch-level Mapnik updates automatically
3. If a module *does not* directly depend on Mapnik, but instead depends on other Mapnik-dependent modules: **use tilde `~` for those dependencies**
4. If a module *does not* directly depend on Mapnik, but instead depends on other Mapnik-dependent modules, and you want the Mapnik-dependent modules to automatically increment on the minor-level: **you must also increment the module's version on the minor-level even if there are no API-breaking changes**
