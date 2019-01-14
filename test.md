# PHP-Simple-Blog

This project is WIP. First stable release is expected in the coming days.

Please test it, and comment. Suggestions, feature requests, and contributions are also welcome.

![alt text](https://github.com/goobemaster/php-simple-blog/blob/master/media/images/doc/preview.png "Preview")

# Table of Contents
- [Overview](#overview)
  - [Features](#features)
  - [Requirements](#requirements)
  - [3rd Party Components](#3rd-party-components)
  - [Licence](#licence)
- [Installation](#installation)
- [Frontend](#frontend)
  - [Layout](#layout)
  - [Posts](#posts)
    - [Featured](#featured)
    - [Single Post View](#single-post-view)
  - [Panel Widgets](#panel-widgets)
    - [Post List](#post-list)
    - [Search](#search)
    - [Tags](#tags)
- [Backend](#backend)
  - [Login](#login)
  - [Panel](#panel)
  - [Post Editor](#post-editor)
  - [Settings](#settings)
    - [General](#general)
    - [Appearance](#appearance)
    - [Widgets](#widgets)
- [Screenshots](#screenshots)

# Overview

PHP-Simple-Blog is a down to earth [free](#licence) **personal blogging web application**. It is **easy to deploy and configure**, and it comes bundled with an **administration panel** too. Start authoring in less than 15 minutes, and really **own what you write**.

The goal is to continuously extend the features of PHP-Simple-Blog without ever compromising **simplicity and ease of use**. If you are looking for a Content Management System, check out [Joomla](https://www.joomla.org/) and [Drupal](https://www.drupal.org/).

*It is written by a software engineer who is a prolific blogger himself:*

*"Although I prefer complex CMS systems even for blogging, I have to come to the conclusion that most people out there are often intimidated by them.*

*Alternative blogging systems are aplenty, but many has its quirks and "personality disorders". And by using free blogging sites comes with the risk of a closure, when you end up with a pile of backup files (best case) and a major headache.*

*I want to ensure that this project will never become an overkill, it won't overwhelm you, so you can concentrate on authoring and social interactions. All along without ever charging for it."*

[Jump to Table of Contents](#table-of-contents)

## Features

- HTML5, and CSS3 compilant classic two-column layout
- Header with custom text, and image
- Footer with copyright notice
- Posts
  - Title
  - Content (html under the hood)
  - Created, modified dates
  - Tags
  - Hits counter
  - Facebook share button
  - Twitter button
- Homepage with list of "featured" posts
- Single post page
- Search result page
  - For keywords
  - For tags
- Panel with configurable widgets:
  - Post list, sorted by chronological or reverse-chronological order
  - Search box for simple keyword searches
  - Weighted list of tags
- Administration panel
  - Authoring, editing, deleting posts
    - Wysiwyg editor
  - List of posts
    - Publish/unpublish posts
  - Widget configuration options
  - Appearance editor with instant preview
  - Apply pre-made themes

[Jump to Table of Contents](#table-of-contents)

## Requirements

**Minimum** requirements:
- Apache web server 2.0
- PHP 5.0.0
- MySql Server 5.0
- HTML5, CSS3, and Javascript 1.8.2 compilant browser:
  - Mozilla Firefox 4
  - Internet Explorer 9
  - Opera 11
  - Safari 6
  - Chrome 28

[Jump to Table of Contents](#table-of-contents)

## 3rd Party Components

PHP-Simple-Blog builds upon, or have integrated:
- MVC Framework: [CodeIgniter](http://www.codeigniter.com/)
- Free icons: [Icons8.com](http://icons8.com/license/)
- Content editor: [CKEditor](http://ckeditor.com/)
- Color Picker: [JSColor](http://jscolor.com/)

[Jump to Table of Contents](#table-of-contents)

## Licence

PHP-Simple-Blog is licensed to you via the [GNU General Public licence version 2](https://github.com/goobemaster/php-simple-blog/blob/master/LICENSE).

Please read it carefully before you start using the software, or if you intended to distribute/modify the source code.

[Jump to Table of Contents](#table-of-contents)

# Installation

## 1. Hosting

The software needs around 16Mb of free space, either on your local machine, or on a web hosting machine. If you plan to embed photos, images or other multimedia files in your posts, a whole lot more space is required.

In an everyday scenario, given you would like to make your blog available on the internet, you have to find a hosting provider. If you do not care about the domain name, sign up for a free hosting service. *In this case your blog's url would look like e.g* **www.awesome-host.com/username/**

If you prefer having a unique and personal domain name, you have to register it first for a determined period of time. Most hosting providers can do this for you, and offer domain + host in a package. *In this case your blog's url would look like e.g* **www.my-awesome-blog.com/**

The hosting service **must** include: **PHP** and **MySQL** support!

You have to ask your hosting provider for **MySQL database authentication details** (hostname, username, password)!

## 2. Uploading

Once you have signed up for such a service, upload the entire latest stable version. Most service allows you to sign in to an administration panel, where you can conveniently upload files.

Alternatively, you can upload via an FTP application to the hosting machine. Ask your provider for the authentication details (server host name, username, password).

Suggested applications:
- [Total Commander](http://www.ghisler.com/) - Windows
- [FileZilla](https://filezilla-project.org/) - Multi platform
- [Gftp](http://gftp.seul.org/) - Linux

## 3. In-browser Installer

Open your blog in a browser, and follow the simple steps:

![Installer Step 1](https://github.com/goobemaster/php-simple-blog/blob/master/media/images/doc/install_1.jpg "Installer Step 1")

![Installer Step 2](https://github.com/goobemaster/php-simple-blog/blob/master/media/images/doc/install_2.jpg "Installer Step 2")

After step 3 the installer cannot be invoked again, and the blog becomes live.

Please go through the installation process as soon as you uploaded the application.

[Jump to Table of Contents](#table-of-contents)

# Frontend

In software terminology frontend is the user facing interface. It is tipically either an application running on your desktop or mobile computer (e.g smartphone), or it is a web application running in a browser.

The PHP-Simple-Blog frontend is composed of several pages. These pages can be loaded via following links, or typing in the url in the expected format manually.

|Page Name|Deeplink|
|---------|--------|
|Homepage |root or /home|
|Single Post Page|/home/post/{post-id}-[{post-title}] |
|Search Results for Keyword | /home/search?keyword={keyword}|
|Search Results for Tag | /home/search?keyword={tag}&tag|

*Whenever an identifier is expected in the url you'll see it in {brackets}. Optional portions are enclosed in [square brackets]. The brackets are not part of the real url.*

[Jump to Table of Contents](#table-of-contents)

## Layout

Currently PHP-Simple-Blog provides a single, classic two-column layout.

![Layout](https://github.com/goobemaster/php-simple-blog/blob/master/media/images/doc/layout.jpg "Layout")

[Jump to Table of Contents](#table-of-contents)

## Posts

[Jump to Table of Contents](#table-of-contents)

### Featured

[Jump to Table of Contents](#table-of-contents)

### Single Post View

[Jump to Table of Contents](#table-of-contents)

## Panel Widgets

[Jump to Table of Contents](#table-of-contents)

### Post List

[Jump to Table of Contents](#table-of-contents)

### Search

[Jump to Table of Contents](#table-of-contents)

### Tags

[Jump to Table of Contents](#table-of-contents)

# Backend

[Jump to Table of Contents](#table-of-contents)

## Login

[Jump to Table of Contents](#table-of-contents)

## Panel

[Jump to Table of Contents](#table-of-contents)

## Post Editor

[Jump to Table of Contents](#table-of-contents)

## Settings

[Jump to Table of Contents](#table-of-contents)

### General

[Jump to Table of Contents](#table-of-contents)

### Appearance

[Jump to Table of Contents](#table-of-contents)

### Widgets

[Jump to Table of Contents](#table-of-contents)

# Screenshots

These were mostly taken during early development. You may notice slight changes versus the current stable version.

![alt text](https://github.com/goobemaster/php-simple-blog/blob/master/media/images/doc/preview_admin.png "Preview")

![alt text](https://github.com/goobemaster/php-simple-blog/blob/master/media/images/doc/preview_admin_list.png "Preview")

![alt text](https://github.com/goobemaster/php-simple-blog/blob/master/media/images/doc/preview_admin_appearance.png "Preview")

![alt text](https://github.com/goobemaster/php-simple-blog/blob/master/media/images/doc/preview_admin_widgets.png "Preview")

![alt text](https://github.com/goobemaster/php-simple-blog/blob/master/media/images/doc/preview_admin_general.png "Preview")

![alt text](https://github.com/goobemaster/php-simple-blog/blob/master/media/images/doc/preview_install.png "Preview")

[Jump to Table of Contents](#table-of-contents)