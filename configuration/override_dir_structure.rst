.. index::
    single: Override Symfony

How to Override Symfony's default Directory Structure
=====================================================

Symfony applications have the following default directory structure, but you can
override it to create your own structure:

.. code-block:: text

    your-project/
    ├─ assets/
    ├─ bin/
    │  └─ console
    ├─ config/
    ├─ public/
    │  └─ index.php
    ├─ src/
    │  └─ ...
    ├─ templates/
    ├─ tests/
    ├─ translations/
    ├─ var/
    │  ├─ cache/
    │  ├─ log/
    │  └─ ...
    └─ vendor/

.. _override-config-dir:

Override the Configuration Directory
------------------------------------

The configuration directory is the only one which cannot be overridden in a
Symfony application. Its location is hardcoded as the ``config/`` directory
at your project root directory.

.. _override-cache-dir:

Override the Cache Directory
----------------------------

You can change the default cache directory by overriding the ``getCacheDir()``
method in the ``Kernel`` class of your application::

    // src/Kernel.php

    // ...
    class Kernel extends BaseKernel
    {
        // ...

        public function getCacheDir(): string
        {
            return dirname(__DIR__).'/var/'.$this->environment.'/cache';
        }
    }

In this code, ``$this->environment`` is the current environment (i.e. ``dev``).
In this case you have changed the location of the cache directory to
``var/{environment}/cache/``.

.. caution::

    You should keep the cache directory different for each environment,
    otherwise some unexpected behavior may happen. Each environment generates
    its own cached configuration files, and so each needs its own directory to
    store those cache files.

.. _override-logs-dir:

Override the Log Directory
--------------------------

Overriding the ``var/log/`` directory is the same as overriding the ``var/cache/``
directory. The only difference is that you need to override the ``getLogDir()``
method::

    // src/Kernel.php

    // ...
    class Kernel extends BaseKernel
    {
        // ...

        public function getLogDir(): string
        {
            return dirname(__DIR__).'/var/'.$this->environment.'/log';
        }
    }

Here you have changed the location of the directory to ``var/{environment}/log/``.

.. _override-templates-dir:

Override the Templates Directory
--------------------------------

If your templates are not stored in the default ``templates/`` directory, use
the :ref:`twig.default_path <config-twig-default-path>` configuration
option to define your own templates directory (use :ref:`twig.paths <config-twig-paths>`
for multiple directories):

.. configuration-block::

    .. code-block:: yaml

        # config/packages/twig.yaml
        twig:
            # ...
            default_path: "%kernel.project_dir%/resources/views"

    .. code-block:: xml

        <!-- config/packages/twig.xml -->
        <?xml version="1.0" encoding="UTF-8" ?>
        <container xmlns="http://symfony.com/schema/dic/services"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:twig="http://symfony.com/schema/dic/twig"
            xsi:schemaLocation="http://symfony.com/schema/dic/services
                https://symfony.com/schema/dic/services/services-1.0.xsd
                http://symfony.com/schema/dic/twig
                https://symfony.com/schema/dic/twig/twig-1.0.xsd">

            <twig:config>
                <twig:default-path>%kernel.project_dir%/resources/views</twig:default-path>
            </twig:config>

        </container>

    .. code-block:: php

        // config/packages/twig.php
        $container->loadFromExtension('twig', [
            'default_path' => '%kernel.project_dir%/resources/views',
        ]);

Override the Translations Directory
-----------------------------------

If your translation files are not stored in the default ``translations/``
directory, use the :ref:`framework.translator.default_path <reference-translator-default_path>`
configuration option to define your own translations directory (use :ref:`framework.translator.paths <reference-translator-paths>` for multiple directories):

.. configuration-block::

    .. code-block:: yaml

        # config/packages/translation.yaml
        framework:
            translator:
                # ...
                default_path: "%kernel.project_dir%/i18n"

    .. code-block:: xml

        <!-- config/packages/translation.xml -->
        <?xml version="1.0" encoding="UTF-8" ?>
        <container xmlns="http://symfony.com/schema/dic/services"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:twig="http://symfony.com/schema/dic/twig"
            xsi:schemaLocation="http://symfony.com/schema/dic/services
                https://symfony.com/schema/dic/services/services-1.0.xsd
                http://symfony.com/schema/dic/twig
                https://symfony.com/schema/dic/twig/twig-1.0.xsd">

            <framework:config>
                <framework:translator>
                    <framework:default-path>%kernel.project_dir%/i18n</framework:default-path>
                </framework:translator>
            </framework:config>

        </container>

    .. code-block:: php

        // config/packages/translation.php
        $container->loadFromExtension('framework', [
            'translator' => [
                'default_path' => '%kernel.project_dir%/i18n',
            ],
        ]);

.. _override-web-dir:
.. _override-the-web-directory:

Override the Public Directory
-----------------------------

If you need to rename or move your ``public/`` directory, the only thing you
need to guarantee is that the path to the ``vendor/`` directory is still correct in
your ``index.php`` front controller. If you renamed the directory, you're fine.
But if you moved it in some way, you may need to modify these paths inside those
files::

    require_once __DIR__.'/../path/to/vendor/autoload.php';

You also need to change the ``extra.public-dir`` option in the ``composer.json``
file:

.. code-block:: json

    {
        "...": "...",
        "extra": {
            "...": "...",
            "public-dir": "my_new_public_dir"
        }
    }

.. tip::

    Some shared hosts have a ``public_html/`` web directory root. Renaming
    your web directory from ``public/`` to ``public_html/`` is one way to make
    your Symfony project work on your shared host. Another way is to deploy
    your application to a directory outside of your web root, delete your
    ``public_html/`` directory, and then replace it with a symbolic link to
    the ``public/`` dir in your project.

Override the Vendor Directory
-----------------------------

To override the ``vendor/`` directory, you need to define the ``vendor-dir``
option in your ``composer.json`` file like this:

.. code-block:: json

    {
        "config": {
            "bin-dir": "bin",
            "vendor-dir": "/some/dir/vendor"
        }
    }

.. tip::

    This modification can be of interest if you are working in a virtual
    environment and cannot use NFS - for example, if you're running a Symfony
    application using Vagrant/VirtualBox in a guest operating system.
