djade
=====

Jade mixins that produce Django template tags for Django and Django-CMS

Designers and Front-end developers love HTMl pre-processors that make creating prototypes and websites a more efficient and clean process. Using Jade's lean syntax rules, we truly are able to make HTML behave more Pythonic. Plus its just great to look at and parse without all the cruft that comes with pHTML (plain HTML).

Unfortunately, the efficiency disappears when we go about integrating our Jade templates into our favorite Python framework, Django. In-roads have been made to bring Jade to Django in the form of PyJade, but the two step conversion (Jade -> Django templates -> pHTML) is harmful at best to server processes. Coupled with PyJade being incompatible with compression[1], this isn't going to work in production.

In order to keep using Jade, we need to compile it down to HTML. We would then need to integrate the resultant HTML into Django Templates and this just puts us back where we started, only marginally ahead of the game if we had written in just pHTML to start with.

So what to do? Jade by its very nature relies on indentation for its syntax and converts the first element into an HTML element. Any content written after the first element and a ' ' (space) is considered plain text content. In addition, Jade sees symbols, such as "{%" and "{{", as the start of plain text[2]. This means that we can jsut add Django Template tags directly into the Jade code.

Awesome! But...

White space is important, so by putting in Django templatetags directly into the code, we lose our sense of indentation and hierarchy when we begin to use multiple Django templatetags.

Jade mixins that output Django templatetags are the answer. Jade mixins can take and expand block content. So any code indented below a Jade mixin is brought into that mixin and can be used via the Jade `block` declaration. Problem solved! We get to keep our nice hierarchy and indentation, remain readable, and keep with Jade and Python syntax styles.

Read on for whats included-

[1] Jade is a cousin to HTML, it needs to be compiled to become actual HTML
[2] Jade uses `mixin` or `+` to call functions/mixins and uses `#{ variable }` to interpolate.

## Included Django Tags

* if (`expression`)
* for (`expression`)
* with (`expression`)
* extends (`filename`)
* load (`tag_library_1`, `tag_library_2`)
* static (`resource`, `context_variable_name`)
* url (`resource`, `context_variable_name`)
* block (`block_name`)
* comment
* _ (`tag_name`, `expression`)
    
    Custom tag builder if needed. This does not create a closing templatetag.
    
    ex.
      `+_("wellfire", "CobeyPotter")` -> `{% wellfire CobeyPotter %}`
      

* __ (`tag_name`, `expression`)

    Custom tag builder if needed. This creates a closing template tag and block expands
    
    ex.
      ```
        +__("wellfire", "CobeyPotter")
          h2 Guitars owned
          ul
            li Ovation Balladeer Special (2)
            li Fender Telecaster HH
            li Fender Bouzouki
      ```
      
      becomes
      
      ```
        {% wellfire CobeyPotter %}
          <h2>Guitars owned</h2>
          <ul>
            <li>Ovation Balladeer Special (2)</li>
            <li>Fender Telecaster HH</li>
            <li>Fender Bouzouki</li>
          </ul>
        {% endwellfire %}
      ```
            

New templatetags forthcoming...

## Included Django-CMS Tags

Compatible with Django-CMS 3

* placeholder (`placeholder_name`)
* placeholder_or (`placeholder_name`)
* placeholder_inherit (`placeholder_name`)
* placeholder_inherit_or (`placeholder_name`)
* show_placeholder(`placeholder_name or expression`)
* show_editable_page_title
* show_uncached_placeholder (`placeholder_name or expression`)
* page_attribute (`attribute name or expression`)
* show_menu (`expression`)
* show_menu_below_id (`expression`)
* show_sub_menu (`expression`)
* show_breadcrumb (`expression`)
* render_block (`block_name`)
* render_plugin (`plugin_name or variable`)
* cms_toolbar
* stack (`stack_name`)

## Caveats

1. This is a work in progress and I hope to expand on it, both with new tags and new more powerful ways to instantiate the mixins.

2. You can call mixins the old style: `mixin placeholder("awesomesauce")`, but who likes writing? Use `+` instead: `+placeholder("awesomesauce")`

3. If you think the Django tag should take a block of code (`if`, `with`, etc), then it does in Jade too. Just indent as you normally would and the Djade mixin will take it along for the ride.

3. Jade can only output simple variables inline to elements:
    `p: a(href=awesome.slug)= awesome.name`

    Mixins can only be declared (and run) on their own lines. Mixins can not be declared as assignments. So you can't do this:
    ```
      div.wheres-the-awesome
        a( href= +url("awesome") ) Bring It.
    ```
    or
    
    ```
      div.wheres-the-awesome
        a( href="/bring-it" )= +include('bringit.html')
    ```

    Instead, put the declarations on their own line:
    
    ```
      +url('bringit as bringit_var')
      div.wheres-the-awesome
        a( href="{{ bringit_var }}" ) Bring It.
    ```
    or
    
    ```
      div.wheres-the-awesome
        a( href="/bring-it")
          +include('bringit.html')
    ```
  
    In Jade, whitespace is important, so this is also a correct and accepted way:
   
    `p: a(href="{% if expression %}{{ awesome_on }}{% endif %}") {% include awesomeness %}`


More to come...


