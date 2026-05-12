How to customize the CloudGate UI
This page describes how to customize the UI via the SDK.

On the web UI, different parts can be replaced.



 

The favicon in the browser tab (= favicon)
The browser title (= tabtitle)
The CloudGate logo (= brand_logo)
The 'Connecting THINGS to the cloud' text. (= tagline)
The Option logo (= company_logo)
 

In the next section, we teach you how to replace these items via the SDK.

Images
In our example, we replace the favicon, 'CloudGate' and 'Option' logo. Make sure you have three different images ready.

Recommended sizes:

CloudGate logo: 250 x 36 pixels
Option logo: 114 x 45 pixels
Favicon: 16 x 16 or 32 x 32 pixels
 

Favicon for Apple
To support Apple products, you should create 4 extra PNG images.

icon-32.png (32 x 32 pixels) 
icon-57.png (57 x 57 pixels) 
icon-72.png (72 x 72 pixels) 
icon-114.png (114 x 114 pixels) 
 

The images can be downloaded here:

File:Images.zip

SDK package
Images
In this step, we prepare an SDK package, which installs the images in the /rom/mnt/cust filesystem.

Let's call our package 'my_company'.

Go to the root SDK directory.

 

cd ~/projects/CloudGate-SDK

mkdir package/my_company



 

Create a files directory contains the www/img folder. You can choose any name you want, but this makes it clear it is part of the web UI.

 

mkdir -p package/my_company/files/www/img



 

Copy the images in that directory.

Your SDK directory should look like this:

 

┌─( jimmyg@goofy ) - ( ~/projects/CloudGate-SDK )

└──┤ tree package/my_company/

package/my_company/

└── files

└── www

└── img

├── brand_logo.jpg

├── company_logo.jpg

├── icon-32.png

├── icon-57.png

├── icon-72.png

├── icon-114.png

└── my_favicon.ico

3 directories, 3 files
 

Important: the icon PNG images MUST be in the same directory as my_favicon.ico.

Makefile
Create a Makefile in package/my_company/Makefile and add the next content to it.

 

include $(TOPDIR)/rules.mk

 

PKG_NAME:=my_company

PKG_VERSION:=1.0

 

include $(INCLUDE_DIR)/package.mk

include $(INCLUDE_DIR)/nls.mk

 

define Package/my_company

SECTION:=base

CATEGORY:=Base system

TITLE:=my_company application

DEPENDS:=+libcg

endef

 

define Build/Prepare

$(call Build/Prepare/Default)

$(CP) -r files/ $(PKG_BUILD_DIR)

endef

 

define Build/Compile

endef

 

define Package/my_company/install

$(INSTALL_DIR) $(1)/www/img

$(CP) -r $(PKG_BUILD_DIR)/files/www/img/* $(1)/www/img

endef

 

$(eval $(call BuildPackage,my_company))

 

When looking at the bold line, after compilation, the files in build_dir/target-arm_v5te_uClibc-0.9.33.2_eabi/my_company-1.0/ are copied to the target image in $(1)/www/img.

 

The $(1) can be read as /rom/mnt/cust. The complete path: /rom/mnt/cust/www/img. You are free to choose the path of course.

 

Menuconfig
Enable the my_company application in menuconfig

 

make menuconfig

Go to 'Base system' -> 'my_company' and press the space bar until you see an *



Exit and save the configuration.

 

UCI configuration
The UCI configuration is used the replace the UI items.

From the SDK root, create a file m2mweb in config/config

 

vim config/config/m2mweb

 

For now, add the next line:

package m2mweb

 

Make sure your image path is found
Remember the /rom/mnt/cust path in the previous sections? We need it now. The web GUI is stored on the file system in /www

 

admin@cgate:~$ ls /www/ -la

      	drwxr-xr-x	8	root 	root      	143	Jan	11	2017	 
 	drwxr-xr-x	7	root	root	0	Oct	18	05:52 ..	 
 	drwxrwxr-x	2	root	root	51	Jan	11	2017	css
 	drwxrwxr-x	2	root	root	241	Jan	4	2017	fonts
 	drwxrwxr-x	2	root	root	45	Jan	4	2017	i18n
 	drwxrwxr-x	2	root	root	261	Jan	11	2017	img
 	-rw-rw-r--	1	root	root	12711	Jan	11	2017	index.html
 	drwxrwxr-x	3	root	root	142	Jan	11	2017	js
 	-rw-rw-r--	1	root	root	5566	Jan	11	2017	login.html
 	-rw-rw-r--	1	root	root	1907	Jan	4	2017	login_js.js
 	drwxrwxr-x	2	root	root	722	Jan	4	2017	partials
           

 

 

 

 

 

 

 

 

 

 

 

We need to tell the GUI that our images are located in a different path. This can be done by adding a symlink. To the config/config/m2mweb configuration, add the following:

 

config default 'my_company'

option path '/rom/mnt/cust/www/img'

 

This creates a symlink 'my_company' in /www pointing to /rom/mnt/cust/www/img. The symlink can be used as a relative path in the custom UI settings.

 

Set the custom UI items
Now we can set the custom UI. In the configuration file, add the following:

 

config ui 'custom_ui'

option brand_logo '/my_company/brand_logo.jpg'

option company_logo '/my_company/company_logo.jpg'

option tabtitle 'My Company'

option favicon '/my_company/my_favicon.ico'

option tagline 'My company tagline'

 

The complete configuration looks like:

 

cat config/config/m2mweb



 

Note 1: the relative path should be read as /www/my_company/brand_logo.jpg.
Note 2: you can also specify a URL. 
Note 3: the Apple icon-xx.png images are searched in the favicon path. option favicon '/my_company/my_favicon.ico' So in /my_company/icon-32.png, /my_company/icon-57.png, ...

Compile
Now, compile your application and upload it to the CloudGate.

make V=99

 

The next image shows the UI after rebooting.



 

Notice the symlink in the /www directory now.

 

admin@cgate:~$ ls /www -la

         	drwxr-xr-x	2  	root 	root  	0	Oct	18	00:01 	.
 	drwxr-xr-x	8	root	root	0	Jan	1	2017	..
 	drwxrwxr-x	2	root	root	51	Jan	11	2017	css
 	drwxrwxr-x	2	root	root	241	Jan	4	2017	fonts
 	drwxrwxr-x	2	root	root	45	Jan	4	2017	i18n
 	drwxrwxr-x	2	root	root	261	Jan	11	2017	img
 	-rw-rw-r--	1	root	root	12711	Jan	11	2017	index.html
 	drwxrwxr-x	3	root	root	142	Jan	11	2017	js
 	-rw-rw-r--	1	root	root	5566	Jan	11	2017	login.html
 	-rw-rw-r--	1	root	root	1907	Jan	4	2017	login_js.js
 	lrwxrwxrwx	1	root	root	21	Oct	18	00:01	my_company -> /rom/mnt/cust/www/img
    	drwxrwxr-x	2	root  	root       	722	Jan	4	2017	partials
 

Hide items
The brand_logo, company_logo, tagline and favicon can be hidden. Just set the UCI item value to 'hidden'.

For example, to hide the company logo, change to configuration to:

 

package m2mweb

 

config default 'my_company'

option path '/rom/mnt/cust/www/img'

 

config ui 'custom_ui'

option brand_logo '/my_company/brand_logo.jpg'

option company_logo 'hidden'

option tabtitle 'My Company'

option favicon '/my_company/my_favicon.ico'

option tagline 'My company tagline'
