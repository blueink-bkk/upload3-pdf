# Upload3-pdf

## TOC:
- validate input folders : list of folders for searching pdf files.
- rebuild search-index
- detect pdf-name collisions
- import and validate xlsx
- extract unique pdf file name from xlsx and report pdf not found in search-index.
- report pdf-not-found from alternate inputs
- if option (--merge) is set, main input (rsync,git) gets alternate file names.
- if option (-o <dest-folder>) is set, found pdf are copied to dest folder.
- pull pdf-directory from CMS
- pull constructeurs (publishers) directory from CMS => cIndex
- for each pdf in xlsx report missing in CMS, and commit if option (--create) is set.
- for each pdf in xlsx check constructeur in cIndex, report missing, and commit if option (--create) is set.
- for each pdf in xlsx, find constructeur and add a reference to this pdf. (cross-index)
- report constructeurs without pdf.
- for each constructeur, create relation (catalog->construteur) if not exists.

## Command line Options supersede other instructions.
- (--zero-constructeurs) remove all constructeurs from CMS instance.
- (-o _dest-folder_) pdf-files named in xlsx and found in sIndex are copied into dest-folder.
- (--merge) pdf-files named in xlsx, found in sIndex are copied to main input folder.
- (--move) pdf-files named in xlsx, found in sIndex but only in alternate folders, are moved to main input folder.
- (-h _host-name_) 

## Environment Variables
```
$ export PGHOST=inhelium.com
$ export PGPORT=5432
$ export PGUSER=postgres
$ export PGDATABASE=cms-oacs
$ export PGPASSWORD=my-secret-password
```

## Configuration file
example:
```
---
  host: inhelium.com
  port: 5432
  database: cms-oacs
  user: postgres
  pg_monitor: false
  app_instance: 'cms-236393'
  xlsx: ./0-Heating-Museum-from-start-to-31-December-2018-FRENCN-20190208.xlsx
  pdf_inputs:
    - /media/dkz/Seagate/18.11-Museum-rsync-inhelium/pdf-www
  jpeg_inputs:
    - /media/dkz/Seagate/18.11-Museum-rsync-inhelium/jpeg-www
```
DO NOT STORE the password in that file, it must be set on command line or environments variables.

## Operations
### Settings
- configuration/settings are specified in file `.env.yaml` or on the command line
- the program look first for `./.env.yaml` in current/working directory.
- use option (-y _<yaml-env>_) to open another config file

### validate input folders
- one or more input folders must be specified in env-yaml.
- the first named folder, is the main-folder (often used as Git or rsync folder)
- other folders are _alternate-folders_, were to look for if file not found in main-folder.
- all folders must exists before proceeding to next step.

### Build search-index
- all input pdf-directories are scanned, and each file basename is recorded in sIndex along with
the pdf full name (directory), the file size, timeStamp (maybe checksum later...)
- each entry has 1 or more full-name.

### file-name collisions
- when building the sIndex, each name is _normalized_ using `nor_fn`. 
- Normalization consists in going lowerCase, removing accents, removing double spaces and special characters like "()[]-" etc...
- The normalized name is stored in norIndex.
- Similar file-names, or close spellings will be detected and warnings will be issued.
- If option (--ignore-collisions) not used, the rogram will exit after counting the alerts/collisions.
- Increase verbose level to show alerts. 
- list of collision are recorded in file `./alert-collisions.yaml`.

# Import and validate xlsx
- xlsx file is most of the time specified in yaml configuration file.
- if given on the command line, it supersed yaml config.
- xlsx-not-found is a fatal-error and stops the program.
- xlsx is scanned, validated and reformatted to give (constructeur, auteurs, indexNames[]) for each xlsx line.

# Build xpdf-index named pdf from xlsx
- for each pdf file name found in xlsx, create en entry in xpdf-index.
- add to the pdf-entry, a reference to the xlsx line.
- **Report pdf not found in sIndex**
if new entry, alert1 if pdf not found in sIndex, alert2 if found in alternate folders, not in the main-folder.

# Validate xpdf against CMS.
- pull pdf directory from CMS.
- for each xpdf, alert if not in cms-pdf-directory.
- if option (--commit-pdf), then commit missing pdf.
- report 

# Validate constructeurs against CMS.
- clear constructeurs from CMS instance using option (--zero-constructeurs)
- pull constructeurs directory from CMS.
- Note: constructeurs referenced by their name (nor_au2), not their title.
- Ideally h1 should be used as title: constructeur legal (official) name, but due to incorrect h1, indexName[0] is used as official name instead.
- for each catalog (xlsx line sec!=3), find constructeur and alert if not in cms-constructeurs-directory.
- if option (--commit-constructeurs), then commit missing constructeur.
- if constructeur exists, add a (dirty) reference to this catalog if needed.
- report constructeurs without catalogs.

# Commit relations (cc) catalog-constructeur
- clear relations cc from CMS instance using option (--zero-cc)
- for each constructeur, check for dirty references (not commited) to catalogs.
- commit if option (--commit-cc) is set.

# Validate auteurs against CMS.
- remove/delete auteurs from CMS instance using option (--zero-auteurs)
- pull auteurs directory from CMS.
- Note: auteurs are referenced by their name : nor_au2(title) found in isoc. 
- for each article-s3 (xlsx line sec==3), find auteur(s), alert if not in cms-auteurs-directory.
- if option (--commit-auteurs), then commit missing auteur(s).
- if auteur exists, add a (dirty) reference to this article(s3) if needed.
- report auteurs without articles.

# Commit relations (aa) article-auteur
- clear relations aa from CMS instance using option (--zero-aa)
- for each auteur, check for dirty references (not commited) to articles(s3).
- commit if option (--commit-aa) is set.

# Links to constructeurs
for each xlsx entry:
- if entry is a catalog, record the constructeur, i.e: add to the entry, a reference to constructeur owning the catalog.
- if entry is another document (not a catalog), record the author(s) by addding a reference to auteur(s) found in this xlsx line.
- When built, the xpdf-index gives for each pdf, a list of constructeurs, or a list of auteurs, and the xid associated.



- extract unique pdf file name from xlsx and report pdf not found in search-index.
- report pdf-not-found from alternate inputs
- if option (--merge) is set, main input (rsync,git) gets alternate file names.
- if option (-o <dest-folder>) is set, found pdf are copied to dest folder.
