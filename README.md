# MY_Controller

A Controller base that provides lots of flexible basics for CodeIgniter applications, including:
 - A basic template engine
 - More readily available 'flash messages'
 - Autoloading of language and model files
 - Flexible rendering methods making JSON simple.
 - Automatcially migrating.

 NOTE: This class assumes that a couple of other libraries are in use in your application:

 - [eldarion-ajax](https://github.com/eldarion/eldarion-ajax) for simple AJAX. Only used by the render_json method to return profiler info and notices block.

## Theming Your Application

All theme specific files (like a header, footer, etc) are expected to be found within the view folder in a 'theme' folder.

    application/views/theme

The default file that is used as the layout that is wrapped around all other content is the theme/index.php file. To display the content of your controller's view within the layout, you need to echo out the **$view_content** variable. A sample skeleton theme index file might look like: 

    <?php $this->load->view('theme/header'); ?>

    <?php if (isset($notice)) : ?>
        <?php echo $notice; ?>
    <?php else : ?>
        <div id="notices"></div>
    <?php endif; ?>

    <section id="main-content">
        <div class="row">
            <div class="twelve columns">
                <?= $view_content ?>
            </div>
        </div>
    </section>

    <?php $this->load->view('theme/footer'); ?>

### Rendering the view

Your controller's view are expected to be organized into folders within the views folder, named by your controller name, then the method name. A controller 'help', running method 'index' would look for 'application/views/help/index.php'. 

You tell the controller to display the page with the **render()** call.

    $this->render();

If you need to specify a different view name to use, you can do so with the **view()** call. The only parameter is the name of the view to display.

    $this->view('another_form')->render();

If you need to use a different theme layout, you can use the **layout()** call.

   $this->layout('two_columns')->render();

You can combine both of these at one time.

    $this->layout('two_columns')
         ->view('another_form')
         ->render();

### View Data

To make data available to the view you can use the **set_var()** method. This is automatically made available to the views, just the $data is used within CodeIgniter's help files.

    // In Controller
    $this->set_var($my_data_array);
    $this->set_var('field_name', $value);

If the first parameter is an array it will add all of the name/value pairs to the view data.

## Other Rendering Methods

Your controller also provides a few other useful 'rendering' methods for your convenience. 

### render_text()

If you need to display raw text to the screen, served up as text/plain to the browser, use the **render_text()** command. This must be the last command your method runs as it sets the Output library's final output.

    $this->render_text($string);

If you want to run the text through CodeIgniter's auto_typography method to make the display a little nicer, pass in TRUE as the second parameter.

### render_json()

A dream for working with JSON via AJAX, this method takes the array of data you provide it and renders it out as JSON to the browser. This must be the last command your method runs as it sets the Output library's final output.

    $this->render_json($my_json_data);

If your method setup a message using the set_message() method, this function will attach a 'fragments' leaf with the 'notices' block in it, as rendered by the message() method.

Likewise, it will also provide the output of the profiler as another fragment, if it's turned on. This allows you to use javascript to update the display of your profiling information, even through AJAX.

### render_js()

Outputs a file as a javascript file, and sets the content type. This must be the last command your method runs as it sets the Output library's final output.

### render_realtime()

Not a final render method, like the rest. Instead, this clears the output buffer and turns off output buffering so that content is displayed to the browser as it happens. Very handy for long-running scripts, like cronjobs.

## Notices

To use 'flash messaging' to display simple status messages, you can use the provided **set_message()** method set a message for display. The first parameter is the text you want displayed. The second parameter is the type of message (success, alert, etc). This second parameter is made available in to your theme/notice.php file, which is rendered out and contains the HTML for your notices block. This varies based on the CSS framework (if any) that you are using. 

A simple notices file might look like: 

    <div id="notices" class="row">
        <div class="twelve columns">

            <?php if (isset($notice) && is_array($notice)) : ?>
            <div class="alert-box <?php echo $notice['type']; ?>">
                <a href="#" class="close">&times;</a>
                <?php echo $notice['message']; ?>
            </div>
            <?php endif; ?>

        </div>
    </div>

## Autoloading Models

Your controller can autoload a model file simply by filling in the **$model_file** class variable in your controller.

    class Some_controller extends MY_Controller {

        protected $model_file = 'user_model';
    }

## Autoloading Language Files

Your controller can autoload a language file simply by filling in the **language_file** class variable in your controller.

    class Some_controller extends MY_Controller {

        protected $language_file = 'users';
    }

## Auto Migrations

If you use migrations to keep your database versioned, you can have the controller always make sure the database gets updated on a page load by setting the **auto_migrate** class variable to TRUE in your controller.

    class Some_controller extends MY_Controller {

        protected $auto_migrate = TRUE;
    }

## Simple Asset Management

To help with front-end performance, you can add CSS and Javascript files only when you need them in the method of the controller instead of loading all of your asset files within your theme file.


### Adding CSS Files to Render

All files MUST be located within the 'assets/css' folder.

    $this->add_style('script_name');

These files can then be looped through in your theme files to output the links needed. 

    <?php
        if (isset($stylesheets))
        {
            foreach ($stylesheets as $style)
            {
                echo "<link rel='stylesheet' type='text/css' href='{$style}' />";
            }
        }
    ?>

### Adding Javascript Files to Render

    All files MUST be located within the 'assets/js' folder.

        $this->add_script('script_name');

    These files can then be looped through in your theme files to output the links needed. 

        <?php
            if (isset($external_scripts))
            {
                foreach ($external_scripts as $script)
                {
                    echo "<script src='{$script}'></script>";
                }
            }
        ?>

