=== Pastacode ===
Contributors: willybahuaud, juliobox
Tags: embed, code, version, github, bitbucket, gist, prismjs, code, color highlight, syntaxique coloration
Requires at least: 3.1
Tested up to: 3.7
Stable tag: trunk
License: GPLv2 or later

Use Pastacode to add code into your posts with the awesome PrismJs coloration library. So, past'a code!

== Description ==

With Pastacode, you can easily add code into your posts with the awesome PrismJs coloration library.
You can insert source code into the post editor, from a file, or from webservices like GitHub, Gist, Pastebin or BitBucket. Webservices responses are cached in order to avoid too many HTTP requests.

Don't worry about posts updates while upgrading codes!

Pastacode allows to enhance your snippets using PrismJs plugins (highlightning lines, link functions...).

6 different color schemes are included, and you can build yours.

Available programming languages:

* HTML
* CSS
* JavaScript
* PHP
* C
* C++
* Java
* Sass
* Python
* SQL
* Ruby
* CoffeeScript
* Bash


== Installation ==

1. Unzip Pastacode into your plugin folder
2. Go to Pastacode settings, and configure your color scheme and cache expiration
3. Host your snippets on repositories (or localy)
4. Editing a post, use *Past'a code* button to embed your source code into articles

== Frequently Asked Questions ==

= How to setup a custom cache expiration ? =

Paste these lines into your functions.php theme file :
`add_filter( 'option_pastacode_cache_duration', 'my_pastacode_cache_duration' );
function my_pastacode_cache_duration( $duration ) {
    $duration = DAY_IN_SECOND*3; // 3 days
    return $duration;
}`

= How define a custom color scheme ? =

Paste these lines into your functions.php theme file :
`add_filter( 'option_pastacode_style', 'my_pastacode_style' );
function my_pastacode_style( $scheme ) {
    $scheme = 'my_awesome_style'; //CSS filename into the plugin css directory
    return $scheme;
}`
Get inspired of [the default scheme](http://wabeo.fr/pastacode.css) to build your schemes

= How to filter supported languages ? =

Paste these lines into your functions.php theme file :
`//If you just want php, html, css and javascript support
add_filter( 'pastacode_langs', '_pastacode_langs' );
function _pastacode_langs( $langs ) {
    $langs  = array(
        'php'          => 'PHP',
        'markup'       => 'HTML',
        'css'          => 'CSS',
        'javascript'   => 'JavaScript', );
    return $langs;
}`

= How to add a new provider ? =

Paste these lines into your functions.php theme file :
`//Take WordPress SVN, for example
//register a provider
add_filter( 'pastacode_services', '_pastacode_services' );
function _pastacode_services( $services ) {
    $services['wordpress'] = 'core.svn.wordpress.org';
    return $services;
}

//Define pastabox lightbox inputs
add_action( 'pastacode_fields', '_pastacode_fields' );
function _pastacode_fields( $fields ) { 
    $fields['wordpress'] = array(  // 'wordpress' or 'whatever'
        'classes'     => array( 'wordpress' ), // same value as the key
        'label'       => sprintf( __('File path relative to %s', 'pastacode'), 'http://core.svn.wordpress.org/' ), 
        'placeholder' =>'trunk/wp-config-sample.php', //if placeholder isn't defined, it will be a textarea
        'name'        => 'path_id' //these value return shortcode attribute (path_id, repos, name, user, version)
        );
    return $fields;
}

//Build the function to retrieve the code
// "pastacode_wordpress" hook name (1st param) = "pastacode_" + "wordpress" or "whatever"
add_action( 'pastacode_wordpress', '_pastacode_wordpress', 10, 2 );
function _pastacode_wordpress( $source, $atts ) {
    extract( $atts );
    if( $path_id ) {
        $req  = wp_sprintf( 'http://core.svn.wordpress.org/%s', str_replace( 'http://core.svn.wordpress.org/', '', $path_id ) );
        $code = wp_remote_get( $req );
        if( ! is_wp_error( $code ) && 200 == wp_remote_retrieve_response_code( $code ) ) {
            $data = wp_remote_retrieve_body( $code );
            $source[ 'url' ]  = $req; //url to view source
            $source[ 'name' ] = basename( $req ); //filename
            $source[ 'code' ] = esc_html( $data ); //the code !!   
            //$source[ 'raw' ] contain raw source code. But there are no raw source code delivered by Wordpress SVN             
        }
    }
    return $source;
}`

Do not add you root website!! A contributor can add the shortcode to point your "wp-config.php" to read it!!

== Screenshots ==

1. View of the Past'a code lightbox
2. Default prismJS color scheme
3. *Dark* color scheme
4. *Coy* color scheme
5. *Okaidia* color scheme
6. *Tomorrow* color scheme
7. *Twilight* color scheme
8. *Funky* color scheme

== Changelog ==

= 1.2.1 =
* 21 nov 2013
* Fix bug: when manual provider is selected, no cache.

= 1.2 =
* 15 oct 2013
* The modification of the cache duration do not purge cache anymore
* New button "Purge Cache" in option page, use it to delete all transients (they contains the responded source codes)
* Fix bug when updating option

= 1.1 =
* 12 oct 2013
* Hooks, hooks and hooks.
* Update shortcode format ("type" became "provider", and add "/" before the closing tag)

= 1.0 =
* 10 oct 2013
* Initial release
* Insert codes using a nice lightbox
* Import codes from file, Github, Gist, Pastebin or BitBucket
* 13 languages available
* 6 color schemes
* Cache support for webservices (default duration : 1 week)