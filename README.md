The following convention is used for Node modules that depend on C++ libraries. This is to help avoid running into semantic versioning suprises that can flow upstream after new versions are published. 

Following this convention will make it easier to know when dependent applications could be affected and how to version your module and its dependents.

### Convention 

Using Mapnik as an example:

1. Favor using `~` over `.x` to avoid installing older versions
2. If you depend on Mapnik as a library (your module is *not* an app or service), you must leave patch version open `~0.0.z` so that your module is flexible to patch-level updates
3. If you depend on other Mapnik-dependent modules, you must use `~` for them 
4. If you depend on other Mapnik-dependent modules, and you increment them on the minor level, you must also increase your module's version on the minor level even if there are no API-breaking changes