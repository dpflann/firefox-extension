# Developing a Firefox Extension #

The example is taken from [Robert Nyman](http://blog.mozilla.com/addons/2009/01/28/how-to-develop-a-firefox-extension/). The goal is to write a simple extension that will show you an alert of the number of all links with a `target` attribute.

Extensions are different from plugins, which help the browser display specific content like playing multimedia files. Extensions are also different from search plugins, which plug additional search engines in the search bar.

## Setup ##

## Development profile ##

You need a working installation of [Firefox](http://www.mozilla.org/firefox/)

It is a good to create a development profile

	# on OSX
	$ /Applications/Firefox.app/Contents/MacOS/firefox -profilemanager
	
	# on Linux
	$ ./firefox -profilemanager
	
Choose `Create Profile` in the dialog and follow the steps.

### Configuration Settings ###

Open Firefox with your development profile. Enter `about:config` in the address bar. 

Recommended settings

These are good to enable extension errors in the Firefox Error Console (Tools > Error Console), disable XUL caching and such.

	javascript.options.showInConsole = true
	nglayout.debug.disable_xul_cache = true
	browser.dom.window.dump.enabled = true

### Extension Directory ###

#### Basic Directory Structure ####

	mdkir extension
	cd extension
	mkdir -p chrome/content
	touch chrome/chrome.manifest
	mkdir -p defaults/preferences
	touch defaults/install.rdf
	mkdir -p locale/en-US
	mkdir skin

#### install.rdf ####
	
	<?xml version="1.0"?>

	<RDF xmlns="http://www.w3.org/1999/02/22-RDF-syntax-ns#"
	     xmlns:em="http://www.mozilla.org/2004/em-RDF#">

		<Description about="urn:mozilla:install-manifest">
			<em:id>linktargetfinder@robertnyman.com</em:id>

			<em:name>Link Target Finder</em:name>
			<em:version>1.0</em:version>
			<em:type>2</em:type>
			<em:creator>Robert Nyman</em:creator>

			<em:description>Finds links that have a target attribute</em:description>
			<em:homepageURL>http://www.robertnyman.com/</em:homepageURL>
			<em:optionsURL>chrome://linktargetfinder/content/options.xul</em:optionsURL>

			<em:targetApplication>

				<Description>
					<em:id>{ec8030f7-c20a-464f-9b0e-13a3a9e97384}</em:id>
					<em:minVersion>5.*</em:minVersion>
					<em:maxVersion>7.0</em:maxVersion>

				</Description>
			</em:targetApplication>
		</Description>
	</RDF>

Explanation of tags

In the `Description` node
- `em:id` Unique developer id, of your own choosing. Also used for pointing to this extension (see below)
- `em:name`  Name of extension.
- `em:version` Current version of your extension.
- `em:type` The type declares that is an extension, as opposed to, for instance, a theme.
- `em:creator` Name of developer
- `em:description` Describes your extension functionality. Will be shown in the Tools > Add-ons window.
- `em:homepageURL` The URL of your extension’s web site.
- `em:optionsURL` The Chrome URL to where you will have your file for editing options/preferences.

In the `Description/em:targetApplication` node
- `em:id` Id of Firefox applciation
- `em:minVersion` Minimum version of Firefox required to run the extension. [Valid Application Versions](https://addons.mozilla.org/en-US/firefox/pages/appversions).
- `em:maxVersion`
	The maximum version of Firefox required to run the extension. [Valid Application Versions](https://addons.mozilla.org/en-US/firefox/pages/appversions).

##### Valid Application Versions #####

Taken from [here](https://addons.mozilla.org/en-US/firefox/pages/appversions)

- GUID: `{ec8030f7-c20a-464f-9b0e-13a3a9e97384}`
- Versions: `0.3`, `0.6`, `0.7`, `0.7+`, `0.8`, `0.8+`, `0.9`, `0.9.0+`, `0.9.1+`, `0.9.2+`, `0.9.3`, `0.9.3+`, `0.9.x`, `0.9+`, `0.10`, `0.10.1`, `0.10+`, `1.0`, `1.0.1`, `1.0.2`, `1.0.3`, `1.0.4`, `1.0.5`, `1.0.6`, `1.0.7`, `1.0.8`, `1.0+`, `1.4.0`, `1.4`, `1.4.1`, `1.5b1`, `1.5b2`, `1.5`, `1.5.0.4`, `1.5.0.*`, `2.0a1`, `2.0a2`, `2.0a3`, `2.0b1`, `2.0b2`, `2.0`, `2.0.0.4`, `2.0.0.8`, `2.0.0.*`, `3.0a1`, `3.0a2`, `3.0a3`, `3.0a4`, `3.0a5`, `3.0a6`, `3.0a7`, `3.0a8pre`, `3.0a8`, `3.0a9`, `3.0b1`, `3.0b2pre`, `3.0b2`, `3.0b3pre`, `3.0b3`, `3.0b4pre`, `3.0b4`, `3.0b5pre`, `3.0b5`, `3.0pre`, `3.0`, `3.0.9`, `3.0.11`, `3.0.12`, `3.0.*`, `3.1a1pre`, `3.1a1`, `3.1a2pre`, `3.1a2`, `3.1b1pre`, `3.1b1`, `3.1b2pre`, `3.1b2`, `3.1b3pre`, `3.1b3`, `3.5b4pre`, `3.5b4`, `3.5b5pre`, `3.5`, `3.5.*`, `3.6a1pre`, `3.6a1`, `3.6a2pre`, `3.6b1pre`, `3.6b2`, `3.6`, `3.6.4`, `3.6.*`, `3.7a1pre`, `3.7a1`, `3.7a2pre`, `3.7a2`, `3.7a3pre`, `3.7a3`, `3.7a4pre`, `3.7a4`, `3.7a5pre`, `3.7a5`, `3.7a6pre`, `4.0b1`, `4.0b2pre`, `4.0b2`, `4.0b3pre`, `4.0b3`, `4.0b4pre`, `4.0b4`, `4.0b5pre`, `4.0b5`, `4.0b6pre`, `4.0b6`, `4.0b7pre`, `4.0b7`, `4.0b8pre`, `4.0b8`, `4.0b9pre`, `4.0b9`, `4.0b10pre`, `4.0b10`, `4.0b11pre`, `4.0b11`, `4.0b12pre`, `4.0b12`, `4.0`, `4.0.*`, `4.2a1pre`, `5.0a2`, `5.0`, `5.*`, `6.0a1`, `6.0a2`, `6.0`, `6.*`, `7.0a1`, `7.0a2`, `7.0`, `7.*`, `8.0a1`, `8.0a2`, `8.0`, `8.*`, `9.0a1`, `9.0a2`, `9.0`, `9.*`, `10.0a1`


#### chrome.manifest ####

The chrome of Firefox is everything around the content window. i.e. web browser toolbar, menus, statusbar etc. The next file for our extension, which will probably feel a bit awkward to edit, is the `chrome.mainfest` file. This one, however, is in conjunction with `install.rdf` the key to how your extension will be added to Firefox, and how it will work.

	content     linktargetfinder    chrome/content/
	content     linktargetfinder    chrome/content/ contentaccessible=yes
	overlay chrome://browser/content/browser.xul chrome://linktargetfinder/content/browser.xul

	locale	linktargetfinder	en-US	locale/en-US/

	skin	linktargetfinder	classic/1.0	skin/
	style	chrome://global/content/customizeToolbar.xul	chrome://linktargetfinder/skin/skin.css

For now we don't care about what the options mean. The interested can take a look at the [Chrome Manifest](https://developer.mozilla.org/en/Chrome_Manifest)

#### Pointing to development directory ####

Instead of keeping reinstalling new versions of your extension, you can point to the directory where you keep your extension project directory (which of course is under version control). The profiles are stored under
	
	## on OSX
	$ cd ~/Library/Application Support/Firefox/Profiles
	
	## on Linux
	$ cd ~/.mozilla/firefox
	
and will be named with letters and numbers, a dot (.) and finally followed by the profile name.

In this folder there is a folder called `extensions`. In it, create a file with a unique name for you (this will have to be the same as you chose for your `em:id` value in your install.rdf file).

In the case of the example, create a file named `linktargetfinder@robertnyman.com`, without any extension, and in it just point it to where you will have your code, e.g. `C:\extensions\` (Windows) or `~/Sites/linktargetfinder/` (Mac, Linux).