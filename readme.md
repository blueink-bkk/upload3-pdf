# Upload3-pdf

## Operations:
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
