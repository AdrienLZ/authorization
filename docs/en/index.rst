Quick Start
###########

Installation
============

Install the plugin with `composer <https://getcomposer.org/>`__ from your CakePHP
Project's ROOT directory (where the **composer.json** file is located)

.. code-block:: shell

    php composer.phar require cakephp/authorization

Load the plugin by adding the following statement in your project's
``src/Application.php``::

    $this->addPlugin('Authorization');
    // Prior to 3.6.0
    Plugin::load('Authorization');

Getting Started
===============

The Authorization plugin integrates into your application as a middleware layer
and optionally a component to make checking authorization easier. First, lets
apply the middleware. In **src/Application.php** add the following to the class
imports::

    use Authorization\AuthorizationService;
    use Authorization\AuthorizationServiceProviderInterface;
    use Authorization\Middleware\AuthorizationMiddleware;
    use Authorization\Policy\OrmResolver;
    use Psr\Http\Message\ResponseInterface;
    use Psr\Http\Message\ServerRequestInterface;

Add the ``AuthorizationProviderInterface`` to the implemented interfaces on your application::

    class Application extends BaseApplication implements AuthorizationServiceProviderInterface

Then add the following to your ``middleware()`` method::

    // Add authorization (after authentication if you are using that plugin too).
    $middleware->add(new AuthorizationMiddleware($this));

The ``AuthorizationMiddleware`` will call a hook method on your application when
it starts handling the request. This hook method allows your application to
define the ``AuthorizationService`` it wants to use. Add the following method your
**src/Application.php**::

    public function getAuthorizationService(ServerRequestInterface $request, ResponseInterface $response)
    {
        $resolver = new OrmResolver();

        return new AuthorizationService($resolver);
    }

This configures a very basic :doc:`/policy-resolvers` that will match
ORM entities with their policy classes.

Next lets add the ``AuthorizationComponent`` to ``AppController``. In
**src/Controller/AppController.php** add the following to the ``initialize()``
method::

    $this->loadComponent('Authorization.Authorization');

By loading the [authorization component](./Component.md) we'll be able to check
authorization on a per-action basis more easily. For example, we can do::

    public function edit($id = null)
    {
        $article = $this->Article->get($id);
        $this->Authorization->authorize('update', $article);

        // Rest of action
    }

By calling ``authorize`` we can use our :doc:`/policies` to enforce our
application's access control rules. You can check permissions anywhere by using
the :doc:`identity stored in the request <checking-authorization>`.


Further Reading
===============

.. toctree::
    :maxdepth: 2

    /policies
    /policy-resolvers
    /middleware
    /component
    /checking-authorization
    /request-authorization-middleware
