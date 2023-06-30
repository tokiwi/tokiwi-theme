
# The Tokiwi Starter Theme
Install this theme as you would any other, and be sure the Timber plugin is activated. But hey, let's break it down into some bullets:

Tokiwi Starter Theme is a boilerplate theme to develop themes in an efficient way with the following tools:

### Wordpress plugins

- [Timber](https://timber.github.io/docs/) for a better code separation between logic and template using the [Twig](https://twig.symfony.com/) template engine 
- [Advanced Custom Fields](https://www.advancedcustomfields.com/) for creating structured post types and Gutenberg blocks
- [Polylang](https://polylang.pro/) for managing the translations (Pro version on [Secnote](https://secnote.tokiwi.ch/notes/view/473))

### Web development frameworks
- [Tailwind](https://tailwindcss.com/) the utility first CSS framework for a full customisable style
- [Alpine.js](https://alpinejs.dev/) the lightweight and "component" oriented javascript framework

### Other tools
- [Flickity](https://flickity.metafizzy.co/) a responsive and touchable slider
- [Font Awesome](https://fontawesome.com/) for sexy and complete icon font (Pro version on [Secnote](https://secnote.tokiwi.ch/notes/view/300)) 

## Installing the Theme

1. Clone the repository in the `wp-content/themes` folder.\
`git clone git@github.com:tokiwi/wordpress-theme.git`
2. Go into the created folder `cd wordpress-theme`
3. Installing the composer vendors\
`composer install`
4. Installing the node dependencies\
`npm install`\
You may need to set the credentials to Font Awesome library (Secnote : https://secnote.tokiwi.ch/notes/view/300) \ 
```sh 
npm config set "@fortawesome:registry" https://npm.fontawesome.com/
npm config set "//npm.fontawesome.com/:_authToken" <AUTH_TOKEN>
```
5. Adding the following plugins to the wordpress website
   1. (Advanced Custom Fields)https://fr.wordpress.org/plugins/advanced-custom-fields/
   2. (Polylang)https://fr.wordpress.org/plugins/polylang/

## Running the theme

Run the following npm command `npm run tailwind`

## Manage CSS

The CSS **must** be modified in the [tailwind.css](tailwind.css) file, the [style.css](style.css) will be overwritten by the Post CSS processor when running the `npm run tailwind` script.

## Adding theme colors

### Tailwind

In ([tailwind.config.js](tailwind.config.js)) change the colors

### Admin wordpress

In [functions.php](functions.php) in `set_color_palette` function add the colors. Please be consistent between the Tailwind Config and the `set_color_palette` function especially in the `slug` attribute.

### Link the colors between Wordpress and Tailwind

In the [tailwind.css](tailwind.css) map the colors defined in the [functions.php](functions.php) file to the Tailwind colors class

```css
/* tailwind.css */
.has-<color>-color /* color slug defined in functions.php's set_color_palette function */
{
    @apply text-<color> /* color name defined in tailwind.config.js file*/
}

.has-<color>-background-color /* color slug defined in functions.php's set_color_palette function */
      {
          @apply bg-<color> /* color name defined in tailwind.config.js file*/
      }
```

Example:

```css
/* tailwind.css */
.has-brand-color
{
    @apply text-brand
}

.has-brand-background-color
{
    @apply bg-brand
}
```

### Adding custom Gutenberg Blocks
In [functions.php](functions.php) in `register_custom_blocks` function add the blocks.

Example :

```php

acf_register_block(array(
            'name' => 'card',
            'title' => 'Card', 'tokiwi',
            'description' => 'Displays a card'
            'render_callback' => array($this, 'my_acf_block_render_callback'), /* the function in the functions.php file that render the block */
            'category' => 'formatting',
            'icon' => file_get_contents(get_template_directory() . '/assets/icons/block-card.svg'), /* SVG file link for the icon, can be downloaded from Font Awesome website (regular icons) */
            'keywords' => array('tokiwi', 'card', 'block', 'container'), /* keywords for searching, please keep 'tokiwi' anyways */
            'supports' => array( /* Use this block if you need to add some other Gutenberg Block inside */
                'align' => true,
                'mode' => false,
                '__experimental_jsx' => true
            ),
        ));

```

The block are by default rendered by the twig templates in `templates/block/<name>-block.twig`

## Adding JS and CSS libraries
### Using NPM dependency

In [functions.php](functions.php) in `enqueue_style` function add the resources from node_modules folder.
Example (Flickity JS + CSS) :

```php
wp_enqueue_style('flickitycss', get_template_directory_uri() . '/node_modules/flickity/dist/flickity.min.css');
wp_enqueue_script('flickityjs', get_template_directory_uri() . '/node_modules/flickity/dist/flickity.pkgd.min.js');

```

### Using CDN
Add link in the [templates/html-header.twig](templates%2Fhtml-header.twig) file

## Adding image crops

In [functions.php](functions.php) there's a code snippet to add some image crops.

```php
$ratios = [array(21,9), array(1,1), array(5,1), array(2,1)];
$widths = [3840, 2560, 1920, 1280, 1024, 768, 640, 480, 320];

foreach ($ratios as $ratio) {
    foreach ($widths as $width){
        add_image_size($ratio[0].'/' . $ratio[1] . '_' . $width, $width, $width * ($ratio[1] / $ratio[0]), array('center', 'center'));
    }
}
```

`$ratios` contains the aspect ratio needed (ideally all the ratios displayed on the website theme). For example `array(2,1)` is twice as wide as it is high.

`$widths` contains the max width of the image.

Please keep in mind that each image is cropped (ratios * widths) times. So do not abuse (as I did) to avoid filling server disk with images.

## Displaying image

There is a [Twig macro](https://twig.symfony.com/doc/3.x/tags/macro.html) in `templates/macros.twig` to display image in an efficient way with different image format and sizes (srcset). Please use it when possible.

```twig
{% macro.image(<Image object>, <crop ratio>, <img classes>, <picture classes>, <alt attribute>) %}
```

Example:

```twig
{% import "macros.twig" as macro %}
{{ macro.image(Image(slide.thumbnail), '1/1', 'rounded', 'w-full', slide.title) }}
```

## Translations

The translations are managed with [Polylang](https://polylang.pro/) plugin. (Official documentation : https://polylang.pro/doc/function-reference/)

### Register translation strings

In [functions.php](functions.php) in `add_translations` function you can add some translations string.

Example :

```php
pll_register_string( 'Hello World !', 'Hello World !', 'Tokiwi');
```

### Display translation

In twig templates

```twig
{{ fn('pll_e','Hello World !') }}
```

## Useful links 

- [Wordpress template hierarchy](https://developer.wordpress.org/files/2014/10/Screenshot-2019-01-23-00.20.04.png)
- [Grafikart's Timber presentation](https://grafikart.fr/tutoriels/wordpress-timber-1308)

## Documentation to be done

- Menu internationalization approach
- ...

## Need help ?

Do not hesitate to contact me (Denis) for any question or enhancement request





