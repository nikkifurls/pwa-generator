- [Progressive Web App (PWA) Generator](#progressive-web-app-pwa-generator)
  - [About](#about)
  - [Usage](#usage)
    - [Options](#options)
  - [File Structure](#file-structure)
    - [`files` Directory](#files-directory)
    - [`inc` Directory](#inc-directory)
    - [`templates` and `template-parts` Directories](#templates-and-template-parts-directories)
  - [Project File Structure](#project-file-structure)
    - [`fonts` Directory](#fonts-directory)
    - [`img` Directory](#img-directory)
    - [`js` Directory](#js-directory)
    - [`scss` Directory](#scss-directory)
  - [Build Process](#build-process)
  - [Fonts](#fonts)
    - [Serif](#serif)
    - [Sans serif](#sans-serif)
    - [Misc](#misc)
  - [Example Projects](#example-projects)
    - [nicolefurlan.com / https://github.com/nikkifurls/nicolefurlan.com](#nicolefurlancom--httpsgithubcomnikkifurlsnicolefurlancom)
    - [dogsafefoods.com / https://github.com/nikkifurls/nicolefurlan.com](#dogsafefoodscom--httpsgithubcomnikkifurlsnicolefurlancom)
  - [Contributions](#contributions)
  - [Get in Touch](#get-in-touch)
# Progressive Web App (PWA) Generator
PWA Generator is a static site generator that builds and deploys Progressive Web Apps to [Netlify](https://netlify.com).

## About
PWA Generator builds static websites based on the project configuration specified in `projects.json`. It can generate everything needed for a website to functions a a progressive web app, e.g. it functions offline and it is installable. It can also deploy it to [Netlify](https://netlify.com).

## Usage
List all projects configured for building in `projects.json`
```
./pwagenerator.php projects
```

Build, deploy, and/or generate icons for a project using project configuration in `projects.json`             
```
./pwagenerator.php [project] [option]
options:
	-v verbose
	-b build
	-i generate icons
	-d deploy
```

For example:
```
./pwagenerator exampleproject.com -b
```

To create a new project, add it to `projects.json`, then build it.

### Options
- Verbose (`-v`): Provides a more verbose output for the other option selected (`-b`, `-i`, or `-d`),
- Build (`-b`): Builds the project. During this process, PWA Generator generates all files needed for the project to function. See [Build Process](#build-process) for more information.
- Generate Icons (`-i`): Generates icons for the project. During this process, PWA Generator sets up a config array and generates a set of favicons from the `img/favicon.svg` file in the project directory using [real-favicon](https://www.npmjs.com/package/cli-real-favicon).
- Deploy (`-d`): Deploys the project. During this process, PWA Generator increments the project version and deploys the project directory to [Netlify](https://netlify.com) using the `netlify_id` set in `projects.json` and [netlify-cli](https://www.npmjs.com/package/netlify-cli).

## File Structure

### `files` Directory
The `files` directory contains files that are copied into the project when it is built.

Fonts in the `fonts` directory will only be copied over if they are listed in the `fonts` object in `projects.json`, e.g.:
```
"fonts": {
	"heading": "Roboto Slab",
	"body": "Roboto"
}
```

Files in the `opt` directory will only be copied over if they are listed in the `opt_files` object in `projects.json`, e.g.:
```
"opt_files": [
	"opt/google_auth.js"
]
```

### `inc` Directory
The `inc` directory contains files that are used to execute project tasks.

- `class-build.php` contains functionality for building, deploying, and generating favicons.
- `class-cli.php`  contains functionality for command line operations.
- `class-projects.php` contains functionality for getting projects from `projects.json`.
- `class-text.php` contains functionality for formatting text.

### `templates` and `template-parts` Directories
The `templates` and `template-parts` directories contain files that are used for building pages.

## Project File Structure
When a project is built for the first time, several files and directories are created in the project directory.
- `index.php`: Will not get overwritten by future builds and should contain any markup and functionality that are specific to the project.
- `js/main.ts`: Will not get overwritten by future builds and should contain any functionality that is specific to the project.
- `scss/style.scss`: Will not get overwritten by future builds and should contain any styles that are specific to the project.
- `_redirects`: [Netlify redirects file](https://docs.netlify.com/routing/redirects/). Can contain redirect and rewrite rules.
- `.gitignore`: Git ignore file. Contains a list of files and folders that Git should not track.
- `manifest.json`: Web app manifest file. Contains data that tells the browser about the progressive web app and how it should behave when installed.
- `package.json`: `npm` project data file. Contains project data for the `npm` package manager.
- `package-lock.json`: `npm` dependency version file. Contains dependency versions for packages installed by the `npm` package manager. It is automatically generated by `npm` when a package is installed.
- `robots.txt`: Robots file. Contains a list of rules to help search engines determine which pages they should and shouldn't crawl.
- `sitemap.xml`: Sitemap file. Contains a list of pages to help search engines with content discovery and thereby improves search engine optimization (SEO).
- `sw.js`: Service worker file. Contains functionality which caches assets and allows for offline browsing and a downloadable "app-like" experience.
- `tsconfig.json`: Webpack TypeScript compilation configuration file.
- `webpack.config.js`: Webpack configuation file for generating the `bundle.[hash].js` from the `js/*.ts` and `scss/*.scss` files.

### `fonts` Directory
The `fonts` directory contains font files that are copied over from `files/fonts` during the build process. Any changes made in this directory will get overwritten by future builds.

### `img` Directory
The `img` directory contains images and files that are generated via the `-i` command. A `favicon.svg` should be placed in this directory prior to running that command. 

This directory should also contain:
- Any images included in the `cache_files` object in `projects.json`. If included in `cache_files` and in the `nav.image` object in `projects.json`, the image would be used in the top header nav bar. If included in `cache_files` and in the `header.image` object in `projects.json`, the image would be used in the main header.
- A `share.jpg` image used for social media sharing.
- A featured image for any posts included in `posts.json`, which should be named the URL of the post with a file extension of `.jpg`.
- Any images included in the `screenshots` object in `projects.json`.

### `js` Directory
The `js` directory contains `.ts` files that are copied over from `files/js` during the build process, and a `bundle.[hash].js` file that is generated from these `.ts` files and files in the `scss` directory.

This directory also contains a `main.ts` file, which will be generated as a blank file on the initial build. It will not get overwritten by future builds and should contain any functionality specific to the project.

This directory can also optionally contain a `main.js`, if an uncompiled file is needed. If this is needed, this file should also be included in the `cache_files` object in `projects.json`.

### `scss` Directory
The `scss` directory contains `.scss` files that are copied over from `files/scss` during the build process.

This directory also contains a `style.scss` file, which will be generated on the initial build as a file containing only a `:root {}` declaration containing the fonts and colors from the `fonts` and `colors` objects in `projects.json`. It will not get overwritten by future builds and should contain any styles specific to the project.

## [Build Process](#build-process)
1. Gets the project configuration from `projects.json` depending on the `project` passed in via the command line.
2. Constructs several arrays that are used later in the build process.
	- The `$this->project_data['files']['compile']` array is used to compile files into the project directory.
		- Pages are added to `$this->project_data['files']['compile']`
	- The `$this->project_data['files']['copy']` array is used to copy files into the project directory.
		- Files in `files/scss` and `files/js` are added to `$this->project_data['files']['copy']`.
		- Files in `files/opt` are only added to `$this->project_data['files']['copy']` if they are also listed in the `opt_files` object in `projects.json`, e.g.:
			```
			"opt_files": [
				"opt/google_auth.js"
			]
			```
		- Font files for the heading font, body font, and Fontawesome are added to `$this->project_data['files']['copy']`.
	- The `$this->project_data['files']['cache']` array is used to add files to the `sw.js` file for caching by the service worker.
		- Files used to generate pages, `data.json` (used to generate additional pages to create an app-like search experience) and `posts.json` (used to generate blog posts), are added to `$this->project_data['files']['cache']`.
		- Files listed in the `cache_files` object in `projects.json` are added to `$this->project_data['files']['cache']`, e.g.:
			```
			"cache_files": [
				"img/logo_header.svg",
				"img/logo_nav.svg",
				"img/shortcut.png"
			]
			```
		- Files in `files/opt` are only added to `$this->project_data['files']['cache']` if they are also listed in the `opt_files` object in `projects.json`, e.g.:
			```
			"opt_files": [
				"opt/google_auth.js"
			]
			```
		- Pages are added to `$this->project_data['files']['cache']`
			- If the page is a `post` (e.g. extracted from `posts.json`), an image is also added to `$this->project_data['files']['cache']`, using the page URL and the `.jpg` file extension.
		- Font files for the heading font, body font, and Fontawesome are added to `$this->project_data['files']['cache']`
3. Adds pages `$this->project_data['sitemap']['urls']` for generating the `sitemap.xml` file later on.
4. Creates and populates the project directory if it doesn't already exist:
	- `index.php` file
	- `scss` directory and `scss/style.scss` with a `:root {}` declaration containing the fonts and colors from the `fonts` and `colors` objects in `projects.json`, e.g.
	```
	"fonts": {
		"heading": "Roboto Slab",
		"body": "Roboto"
	},
	"colors": {
		"main": {
			"normal": "92D7DA",
			"dark": "489094"
		},
		"accent": {
			"normal": "FA96C0",
			"dark": "B34F78"
		}
	}
	```
	- `js` directory and `js/main.ts`
	- `opt` directory 
	- `.gitignore` file with `node_modules`
	- `sw.js` service worker file
5. Copies files listed in `$this->project_data['files']['copy']` array into project directory.
6. Generates a `bundle.[hash].js` file from the `js/*.ts` and `scss/*.scss` files provided using [Webpack](https://webpack.js.org/) via [webpack-cli](https://www.npmjs.com/package/webpack-cli), then uses the `php` command with the `project`, `page`, and `project_data` passed in as arguments to compile each file in `$this->project_data['files']['compile']` into a minified `.html` file.
7. Generates the `manifest.json` file. The `manifest.json` file is copied into the project directory, then modified according to values in `projects.json` (`categories`, `screenshots`, `shortcuts`, `android_app_id`, and `apple_app_id`).
8. Replaces the following patterns in files in `$this->project_data['files']['compile']` and `$this->project_data['files']['copy']`:
	- `***FILES***`: Files from `$this->project_data['files']['cache']`
	- `***URLS***`: URLs from `$this->project_data['redirects']` and `$this->project_data['sitemap']['urls']`
	- `***REDIRECT_URLS***`: URLs from `$this->project_data['files']['compile']` and `$this->project_data['redirects']`
	- `***URL***`: The project URL from `$this->project_data['url']`
	- `***TITLE***`: The project title from `$this->project_data['title']`
	- `***DESCRIPTION***`: The project description from `$this->project_data['description']`
	- `***VERSION***`: The project version from `$this->project_data['version']` (extracted from `sw.js`)
	- `***DATE***`: date('Y-m-d')

## Fonts
These are the fonts available for using in the `fonts` object in `projects.json`, e.g.:
```
"fonts": {
	"heading": "Roboto Slab",
	"body": "Roboto"
}
```

### Serif
- [Arvo](https://fonts.google.com/specimen/Arvo)
- [Bitter](https://fonts.google.com/specimen/Bitter)
- [Bree Serif](https://fonts.google.com/specimen/Bree+Serif)
- [Lora](https://fonts.google.com/specimen/Lora)
- [Merriweather](https://fonts.google.com/specimen/Merriweather)
- [Playfair Display](https://fonts.google.com/specimen/Playfair+Display)
- [Roboto Slab](https://fonts.google.com/specimen/Roboto+Slab)

### Sans serif
- [Lato](https://fonts.google.com/specimen/Lato)
- [Merriweather Sans](https://fonts.google.com/specimen/Merriweather+Sans)
- [Montserrat](https://fonts.google.com/specimen/Montserrat)
- [Nunito](https://fonts.google.com/specimen/Nunito)
- [Nunito Sans](https://fonts.google.com/specimen/Nunito+Sans)
- [Open Sans](https://fonts.google.com/specimen/Open+Sans)
- [Oxygen](https://fonts.google.com/specimen/Oxygen)
- [Poppins](https://fonts.google.com/specimen/Poppins)
- [Quicksand](https://fonts.google.com/specimen/Quicksand)
- [Raleway](https://fonts.google.com/specimen/Raleway)
- [Roboto](https://fonts.google.com/specimen/Roboto)
- [Work Sans](https://fonts.google.com/specimen/Work+Sans)

### Misc
- [Amatic SC](https://fonts.google.com/specimen/Amatic+SC)
- [Bebas Neue](https://fonts.google.com/specimen/Bebas+Neue)
- [Oswald](https://fonts.google.com/specimen/Oswald)
- [Pacifico](https://fonts.google.com/specimen/Pacifico)
- [Rye](https://fonts.google.com/specimen/Rye)
- [Special Elite](https://fonts.google.com/specimen/Special+Elite)

## Example Projects
The following open source projects have been built using PWA Generator. Check out the website, the GitHub repo, and the project configuration for `projects.json` below.

### [nicolefurlan.com](https://nicolefurlan.com) / [https://github.com/nikkifurls/nicolefurlan.com](https://github.com/nikkifurls/nicolefurlan.com)
```
{
	"url": "nicolefurlan.com",
	"title": "Nicole Furlan",
	"description": "Software engineer on a pursuit of equality, compassion, and justice for all.",
	"keywords": "nicole furlan, nicole pitts",
	"categories": [],
	"netlify_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	"gtm_id": "G-XXXXXXXXXX",
	"fbpixel_id": null,
	"repixel_id": null,
	"google_ads": false,
	"amazon_ads": false,
	"other_ads": false,
	"google_api": {
		"google_api_client_id": null,
		"google_api_client_key": null,
		"google_api_key": null,
		"google_api_scope": null,
		"google_api_url": null,
		"google_api_callback": null
	},
	"fonts": {
		"heading": "Playfair Display",
		"body": "Raleway"
	},
	"fontawesome": true,
	"android_app_id": {
		"free": null,
		"paid": null
	},
	"apple_app_id": {
		"free": null,
		"paid": null
	},
	"screenshots": [
		"img/screenshot.png"
	],
	"shortcuts": null,
	"links": [],
	"nav": {
		"image": "img/logo_nav.svg"
	},
	"header": {
		"title": "Hi! I'm Nicole",
		"description": "I'm a Software Engineer working on building a better world for us all",
		"image": "img/background.webp",
		"image_credit": "Photo by <a href=\"https://pbase.com/tclout/root\" alt=\"Thomas Cloutier Photography\">Thomas Cloutier Photography</a>"
	},
	"social": {
		"mailto": "hi@nicolefurlan.com",
		"facebook": null,
		"twitter": null,
		"github": "nikkifurls",
		"patreon": null,
		"paypal": "donate?hosted_button_id=M7MMF3EWQTLKG",
		"portfolio": null,
		"yelp": null,
		"tripadvisor": null,
		"custom": [
			{
				"url": null,
				"label": null,
				"text": null,
				"link": null
			}
		]
	},
	"author": {
		"name": "Nicole Furlan",
		"twitter": "nicolemfurlan"
	},
	"redirects": [
		{
			"from": "/donate",
			"to": "https://www.paypal.com/donate?hosted_button_id=M7MMF3EWQTLKG"
		}
	],
	"pages": [],
	"cache_files": [
		"img/background.webp",
		"img/logo_nav.svg"
	],
	"opt_files": []
}
```
### [dogsafefoods.com](https://dogsafefoods.com) / [https://github.com/nikkifurls/nicolefurlan.com](https://github.com/nikkifurls/dogsafefoods.com)
```
{
	"url": "dogsafefoods.com",
	"title": "Dog Safe Foods",
	"description": "Sharing food with your dog? Make sure it's safe first",
	"keywords": "dog safe foods, dog safe human food, dog safe human foods, safe for dogs to eat, can dogs eat, can my dog eat, can dogs have, can my dog have, good human food for dogs, food dog can eat",
	"categories": [
		"pets",
		"food"
	],
	"netlify_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	"gtm_id": "G-XXXXXXXXXX",
	"fbpixel_id": "xxxxxxxxxxxxxxx",
	"repixel_id": "xxxxxxxxxxxxxxxxxxxxxxxx",
	"google_ads": true,
	"amazon_ads": false,
	"other_ads": false,
	"google_api": {
		"google_api_client_id": null,
		"google_api_client_key": null,
		"google_api_key": null,
		"google_api_scope": null,
		"google_api_url": null,
		"google_api_callback": null
	},
	"fonts": {
		"heading": "Roboto Slab",
		"body": "Roboto"
	},
	"fontawesome": true,
	"android_app_id": {
		"free": "com.dog_safe_foods.twa",
		"paid": "com.dogsafefoods.twa"
	},
	"apple_app_id": {
		"free": null,
		"paid": null
	},
	"screenshots": [
		"img/screenshot1.png",
		"img/screenshot2.png",
		"img/screenshot3.png"
	],
	"shortcuts": [
		{
			"name": "View Safe Foods",
			"description": "View all foods that are safe for dogs to eat",
			"url": "./safe",
			"icons": [
				{
					"src": "img/icon-thumbs-up.png",
					"sizes": "512x512"
				}
			]
		},
		{
			"name": "View Unsafe Foods",
			"description": "View all foods that are not safe for dogs to eat",
			"url": "./unsafe",
			"icons": [
				{
					"src": "img/icon-thumbs-down.png",
					"sizes": "512x512"
				}
			]
		}
	],
	"links": [],
	"nav": {
		"image": "img/logo_nav.svg"
	},
	"header": {
		"title": null,
		"description": null,
		"image": "img/logo_header.svg",
		"image_credit": null
	},
	"social": {
		"mailto": "hi@dogsafefoods.com",
		"facebook": null,
		"twitter": "dogsafefoods",
		"github": "nikkifurls/dogsafefoods",
		"patreon": null,
		"paypal": "donate?hosted_button_id=M7MMF3EWQTLKG",
		"portfolio": "nicolefurlan.com",
		"yelp": null,
		"tripadvisor": null,
		"custom": [
			{
				"url": "https://catsafefoods.com",
				"label": "Cat Safe Foods",
				"text": null,
				"link": "<i class='fas fa-cat'></i>"
			}
		]
	},
	"author": {
		"name": "Nicole Furlan",
		"twitter": "nicolemfurlan"
	},
	"redirects": [
		{
			"from": "/healthy",
			"to": "/safe 301!"
		},
		{
			"from": "/unhealthy",
			"to": "/unsafe 301!"
		},
		{
			"from": "/healthy.html",
			"to": "/safe 301!"
		},
		{
			"from": "/unhealthy.html",
			"to": "/unsafe 301!"
		},
		{
			"from": "/* food=:food",
			"to": "/:food 301!"
		}
	],
	"pages": {
		"*": {
			"url": "",
			"title": "",
			"description": "Sharing ***TITLE*** with your dog? Make sure it's safe first",
			"keywords": "can dogs eat ***TITLE***, can dogs have ***TITLE***, dogs ***TITLE***, dogs and ***TITLE***, ***TITLE*** safe for dogs, ***TITLE*** bad for dogs, ***TITLE*** ok for dogs, is it safe for dogs to eat ***TITLE***, is it safe for dogs to have ***TITLE***"
		},
		"safe": {
			"url": "safe",
			"title": "Safe Foods for Dogs",
			"description": "Sharing food with your dog? Make sure it's on this list of safe foods for dogs first",
			"keywords": "healthy human food for dogs, safe human food for dogs, human food that dogs can eat, good human food for dogs"
		},
		"unsafe": {
			"url": "unsafe",
			"title": "Unsafe Foods for Dogs",
			"description": "Sharing food with your dog? Make sure it's not on this list of unsafe foods for dogs first",
			"keywords": "unhealthy human food for dogs, food not to feed dogs, bad food for dogs, bad human food for dogs"
		}
	},
	"cache_files": [
		"img/logo_header.svg",
		"img/logo_nav.svg",
		"img/icon-thumbs-up.png",
		"img/icon-thumbs-down.png",
		"ads.txt",
		"sellers.json"
	],
	"opt_files": []
}
```

## Contributions
Contributions are welcome! If you discover an issue, or have an idea for an improvement, feel free to create an issue.

## Get in Touch
- Email: [hi@nicolefurlan.com](mailto:hi@nicolefurlan.com)
- Twitter: [@nicolemfurlan](https://twitter.com/nicolemfurlan)