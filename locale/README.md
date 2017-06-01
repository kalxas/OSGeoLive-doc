

# Locale

To get latest pot files
```
cd build
cmake  -LOCALE=ON ..
make locale
```

list pot files
```
ls -R doc/_build/gettext/pot
```

list A language po files
```
ls -R doc/_build/gettext/es
```

## Change only what is needed

See the section **Which resources need change** before continuing.


* Copy the pot files that changed and all the corresponding po files in other languages

```
cp doc/_build/gettext/pgr_createVerticesTable.pot ../locale/pot
cp doc/_build/locale/en/LC_MESSAGES/pgr_createVerticesTable.po ../locale/en/LC_MESSAGES
cp doc/_build/locale/es/LC_MESSAGES/pgr_createVerticesTable.po ../locale/es/LC_MESSAGES
```


### Push the resource to transifex

```
tx push --source -r pgrouting.pgr_createVerticesTable
```

### Pull transtlated strings

```
tx pull -r pgrouting.pgr_createVerticesTable -l es
```

Note: if the file is skip `-f` forces the pull but basically it means:

* locally the po file changed and no push of the resource was done.
* A translation has being downloaded and no further translations on the transifex file have being done 

### clean the build & build the documentation:

```
rm -rf *
cmake  -DWITH_DOC=ON -DBUILD_HTML=ON ..
```

# Which resources need change:

The translation strings are in the po files.
The Engish transtlations should not change unless the documentation changed

### English

Check the English differences with the commited files
```
diff -r doc/_build/gettext/en ../locale/en
```

There might be no difference or the only difference is the creation date:
Sample dff on a file that has not changed output on a file:
```
diff -r doc/_build/locale/en/LC_MESSAGES/withPoints-family.po ../locale/en/LC_MESSAGES/withPoints-family.po
11c11
< "POT-Creation-Date: 2017-05-30 08:52-0500\n"
---
> "POT-Creation-Date: 2017-05-30 08:27-0500\n"
```

TODO: write a script that generates the names of the files that changed


### Other languages

Check the laguage differences with the commited files
```
diff -r doc/_build/locale/es ../locale/es
```

* there are no differences when the file has no translation

Sample output on a translated file:
```
36c39
< msgstr ""
---
> msgstr "Sinopsis"
```

## The pot files

Check the differences with the commited files
```
diff -r doc/_build/gettext/pot ../locale/pot
```

Sample output on a file:
```
diff doc/_build/gettext/VRP-category.pot ../locale/pot/VRP-category.pot
11c11
< "POT-Creation-Date: 2017-05-30 08:52-0500\n"
---
> "POT-Creation-Date: 2017-05-30 08:03-0500\n"
```

# Push to transifex only what is needed


References:

* https://pypi.python.org/pypi/sphinx-intl
