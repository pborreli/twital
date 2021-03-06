Twital for Template Designers
=============================

This document gives to you an overview on Twital principles, how to write a template and
and thing to have in mind to make it work well.

Introduction
------------

A template is simply a text file. Twital can generate any HTML/XML format.

To make it work, your templates must match the configured file extension.

By default Twital will compile only templates that ends with ``.twital.xml``, ``.twital.html``, ``.twital.xhtml``
(using respectively XML, HTML5 and XHTML rules to format the output).

A Twital template basically is a Twig template that take advantage of the natural HTML/XML tree structure
(avoiding redundant control flow instructions). All expressions are completely Twig compatible,
just control flow structures (Twig calls them *tags*) are replaced by some Twital *tags* or *attributes*.

Here is a minimal template that illustrates a few basics:

.. code-block:: html+jinja

    <!DOCTYPE html>
    <html>
        <head>
            <title>My Webpage</title>
        </head>
        <body>
            <ul id="navigation">
                <li t:for="item in navigation">
                    <a href="{{ item.href }}">{{ item.caption }}</a>
                </li>
            </ul>

            <h1>My Webpage</h1>
            {{ a_variable }}
        </body>
    </html>


.. tip::

    See :doc:`here<api>` how to use specific output formats as XML or XHTML and HTML5.

IDEs Integration
----------------

Any IDE that supports Twig syntax highlighting and auto-completion should be configured to support Twital.

Here you can find a list of
`IDEs that supports Twig/Twital <http://twig.sensiolabs.org/doc/templates.html#ides-integration>`__

Variables
---------

To print the content of variables you can use exactly the same Twig syntax, using Twig functions, filters ecc.

.. code-block:: xml+jinja

    {{ foo.bar }}
    {{ foo['bar'] }}
    {{ attribute(foo, 'data-foo') }}

.. note::

    To learn more about Twig variables you can read the `Twig official documentation <http://twig.sensiolabs.org/doc/templates.html#variables>`__

Setting Variables
~~~~~~~~~~~~~~~~~
The ``t:set`` attribute acts same as Twig ``set`` tag and allows you to set a variable form a template.

.. code-block:: xml+jinja

    <div t:set="name = 'Tom'">
        Hello {{ tom }}
    </div>

    <t:omit t:set="numbers = [1,2], items = {'item':'one'}"/>
    {# no tags will be output-ed since t:omit tag will be omit-ed #}

.. note::

    To learn more about Twig ``set``  you can read the `Twig official documentation <http://twig.sensiolabs.org/doc/tags/set.html>`__

Filters
-------

You can use all Twig filters directly into Twital.
Here is just an example:

.. code-block:: xml+jinja

    {{ name|striptags|title }}
    {{ list|join(', ') }}

You can also use the Twital attribute ``t:filter`` to filter the content of an element.

.. code-block:: xml+jinja

    <div t:filter="upper">
        This text becomes uppercase
    </div>

.. note::

    To learn more about Twig filters  you can read the `Twig official documentation <http://twig.sensiolabs.org/doc/templates.html#filters>`__


Functions
---------

You can use all Twig functions directly from Twital.

For instance, the ``range`` function returns a list containing an arithmetic
progression of integers:

.. code-block:: xml+jinja

    <div t:for="i in range(0, 3)">
        {{ i }},
    </div>

.. note::

    To learn more about Twig filters  you can read the `Twig official documentation <http://twig.sensiolabs.org/doc/templates.html#functions>`__


Control Structure
-----------------
Almost all Twig control structures have a Twital equivalent node or attribute.

For example, to display a list of users provided in a variable called
``users``, use the :doc:`for<tags/for>` attribute:

.. code-block:: xml+jinja

    <h1>Members</h1>
    <ul>
        <li t:for="user in users">
            {{ user.username|e }}
        </li>
    </ul>

The :doc:`if<tags/if>` attribute can be used to test an expression:

.. code-block:: xml+jinja

    <ul t:if="users|length">
        <li t:for="user in users">
            {{ user.username|e }}
        </li>
    </ul>

Go to the :doc:`tags<tags/index>` page to learn more about the built-in attributes and nodes.

To learn more about Twig control structures you can read the `Twig official documentation <http://twig.sensiolabs.org/doc/templates.html#control-structure>`__

Attributes
----------

To create HML/XML attributes you have not to mess up with control structures inside HTML tags,
with Twital things are really easy.

Take a look to the following example, using the :doc:`t:attr<tags/attr>` attribute 
 you can add conditionally an attribute based on the value of ``condition`` expression.

.. code-block:: xml+jinja

    <div t:attr=" condition ? class='header'">
        My Company
    </div>



You can use any twig expression as condition and attribute value. The attribute name must be a literal.

.. code-block:: xml+jinja

    <div t:attr="
        users | length ? class='header'|upper ,
        item in array ? class=item">
        Here wins the last class that condition will be evaluated to true.
    </div>

You can also append some content to existing attributes using the :doc:`t:attr-append<tags/attr-append>`.

.. code-block:: xml+jinja

    <div class="row"
        t:attr-append=" i mod 2 ? class=' even'">
         class will be "row even" if 'i' is odd.
    </div>

When not needed you can omit he condition instruction.

.. code-block:: xml+jinja

    <div t:attr="class='row'" t:attr-append=" class=' even'">
         Class will be "row even"
    </div>


Comments
--------

To comment-out part of a line in a template, you can use the Twig comment syntax 
``{# ... #}``.

Including other Templates
-------------------------

The :doc:`include<tags/include>` tag is useful to include a template and
return the rendered content of that template into the current one:

.. code-block:: xml+jinja

    <t:include from="sidebar.html"/>

Inclusion works exactly as Twig.

.. note::

    To learn more about Twig inclusion techniques  you can read the `Twig official documentation <http://twig.sensiolabs.org/doc/templates.html#including-other-templates>`__


Template Inheritance
--------------------

Twital's template inheritance is almost identical to Twig.
Twital adds just some features useful to define new blocks.


Here we define a base template, ``base.html``, which defines a simple HTML
skeleton document that you might use for a simple two-column page:

.. code-block:: html+jinja

    <!DOCTYPE html>
    <html>
        <head t:block="head">
            <link rel="stylesheet" href="style.css" />
            <title t:block="title">My Webpage</title>
        </head>
        <body>
            <div id="content" t:block="content">
            </div>
            <div id="footer" t:block="footer">
                &copy; Copyright 2011 by <a href="http://domain.invalid/">you</a>.
            </div>
        </body>
    </html>

In this example, the :doc:`t:block<tags/block>` attributes define four blocks that
child templates can fill in. All the ``t:block`` attributes does is to tell the
template engine that a child template may override those portions of the
template.

A child template might look like this:

.. code-block:: xml+jinja

    <t:extends from="base.html">

        <t:block name="title">Index</t:block>

        <t:block name="head">
            {{ parent() }}
            <style type="text/css">
                .important { color: #336699; }
            </style>
        </t:block>

        <t:block name="content">
            <h1>Index</h1>
            <p class="important">
                Welcome to my awesome homepage.
            </p>
        </t:block>

    </t:extends>

The :doc:`t:extends<tags/extends>` node  tells the template
engine that this template "extends" another template. When the template system
evaluates this template, first it locates the parent. The extends tag should
be the first tag in the template.

Note that since the child template doesn't define the ``footer`` block, the
value from the parent template is used instead.

To render the contents of the parent block by using the
`parent <http://twig.sensiolabs.org/doc/functions/parent.html>`__ Twig function.
This gives back the results of the parent block:

.. code-block:: xml+jinja

    <t:block name="sidebar">
        <h3>Table Of Contents</h3>
        ...
        {{ parent() }}
    </t:block>

.. tip::

    The documentation page for the :doc:`extends<tags/extends>` tag describes
    more advanced features like block nesting, scope, dynamic inheritance, and
    conditional inheritance.

.. note::

    To learn more about Twig inheritance you can read the `Twig official documentation <http://twig.sensiolabs.org/doc/templates.html#template-inheritance>`__

Macros
------

Twital also supports Twig macros. It is done thanks to ``t:macro`` note.


A macro is defined via the :doc:`macro<tags/macro>` tag. Here is a small example
(subsequently called ``forms.html``) of a macro that renders a form element:

.. code-block:: xml+jinja

    {% macro input(name, value, type, size) %}
        <input type="{{ type|default('text') }}" name="{{ name }}" value="{{ value|e }}" size="{{ size|default(20) }}" />
    {% endmacro %}

Macros can be defined in any template, and need to be "imported" via the
:doc:`import<tags/import>` tag before being used:

.. code-block:: xml+jinja

    {% import "forms.html" as forms %}

    <p>{{ forms.input('username') }}</p>



.. note::

    To learn more about Twig macros you can read the `Twig official documentation <http://twig.sensiolabs.org/doc/templates.html#macros>`__

Expressions,Literals and Operators
----------------------------------

All expressions, literals and operators that can be used with Twig, can be also used with Twital.

.. note::

    Pay attention to HTML/XML escaping rules (eg: &lt; or > inside attributes).

Whitespace Control
------------------

Twital will try to respect almost all whitespaces that you type.
To remove whitespaces between HTML tags you can use the ``t:spaceless`` attribute:

.. code-block:: xml+jinja

    <div t:spaceless="">
        <strong>foo bar</strong>
    </div>

    {# output will be <div><strong>foo bar</strong></div> #}

More generaly, Twital have the same behaviour of Twig in whitespaces handling.

.. note::

    To learn more about Twig macros you can read the `Twig official documentation <http://twig.sensiolabs.org/doc/templates.html#whitespace-control>`__


Extensions
----------

Twital can be easily extended. To learn how to create your own extension you can
read the :ref:`Extending Twital` chapter.
