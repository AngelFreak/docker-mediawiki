# Mediawiki offical with VisualEditor! 
## Sidecar MariaDB, and Parsoid.

I nedded a mediawiki with VisualEditor, but i did not want to build my own image ontop of the official Medaiwiki.

So i created this compose project.

This uses the official Mediawiki docker image, and runs Parsoid as a sidecar container. 

It also includes a database, because why not ? If you don't need it, just remove it.

# Setup:

Clone this repo.

Change directory into the repo.

Download the latest VisualEditor from https://www.mediawiki.org/wiki/Special:ExtensionDistributor/VisualEditor.

Unpack the file tar -xzf FILENAME -C plugins/

Start the stack
```
docker-compose -p wiki up -d 
```
If you use the databse get the random root password
```
docker logs wiki_database_1
```

In your browser go to http://localhost:8080 and follow the setup.

Download the LocalSettings.php file.

Open LocalSettings.php in a editor and add the following:
```
$wgHashedUploadDirectory = true;

$wgAllowExternalImages = true;
$wgFileExtensions = array( 'png', 'gif', 'jpg', 'jpeg', 'pdf', 'doc', 'docx', 'xls', 'xlsx', 'ppt', 'pptx', 'swf', 'gz', 'odt', 'ods', 'odp', 'odg' );
$wgVerifyMimeType = true;

wfLoadExtension( 'VisualEditor' );

// Enable by default for everybody
$wgDefaultUserOptions['visualeditor-enable'] = 1;

// Optional: Set VisualEditor as the default for anonymous users
// otherwise they will have to switch to VE
// $wgDefaultUserOptions['visualeditor-editor'] = "visualeditor";

// Don't allow users to disable it
$wgHiddenPrefs[] = 'visualeditor-enable';

// OPTIONAL: Enable VisualEditor's experimental code features
#$wgDefaultUserOptions['visualeditor-enable-experimental'] = 1;

$wgVirtualRestConfig['modules']['parsoid'] = array(
    // URL to the Parsoid instance
    // Use port 8142 if you use the Debian package
    'url' => 'http://parsoid:8000',
    // Parsoid "domain", see below (optional)
    'domain' => 'localhost',
    // Parsoid "prefix", see below (optional)
    'prefix' => 'localhost'
);
```
Copy the LocalSettings.php into the container
```
docker cp LocalSettings.php wiki_mediawiki_1:/var/www/html/
```
Recreate the container
```
docker-compose -p wiki up -d --force-recreate
```
