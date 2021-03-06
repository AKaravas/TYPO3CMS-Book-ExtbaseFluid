.. include:: ../Includes.txt

Controlling The Flow
====================

The inventory created in the backend should be shown as a list in the frontend now.
The *view* creates the HTML code out of the product objects to be shown.
Extbase uses the class :php:`\TYPO3\CMS\Fluid\View\TemplateView` of the Fluid extension
as a default for the view.

The connection between the *model* and the *view* is the *controller*. It controls the
sequences inside the extension and is responsible for the ``list`` action in our case.
This includes locating the products which are to be shown, as well as the transmission of the
selected products to the responsible view.

The class name of the controller must end with ``Controller``. Because our controller controls
the display of the inventory we call it ``\MyVendor\StoreInventory\Controller\StoreInventoryController``.

.. tip::

    When naming a controller you are free inside the described frame. We advise to name a
    controller by what he "controls". In big projects these are specially the aggregate root
    objects (see section "aggregates" in chapter 2). For this we could also name our controller
    ``MyVendor\StoreInventory\Controller\ProductController``, as it controls the product.

In our simple example the controller looks like this:

.. code-block:: php

    <?php

    namespace MyVendor\StoreInventory\Controller;

    use MyVendor\StoreInventory\Domain\Repository\ProductRepository;
    use TYPO3\CMS\Extbase\Mvc\Controller\ActionController;

    /**
     * Class StoreInventoryController
     *
     * @package MyVendor\StoreInventory\Controller
     */
    class StoreInventoryController extends ActionController
    {
        /**
         * @var ProductRepository
         */
        private $productRepository;

        /**
         * Inject the product repository
         *
         * @param \MyVendor\StoreInventory\Domain\Repository\ProductRepository $productRepository
         */
        public function injectProductRepository(ProductRepository $productRepository)
        {
            $this->productRepository = $productRepository;
        }

        /**
         * List Action
         *
         * @return void
         */
        public function listAction()
        {
            $products = $this->productRepository->findAll();
            $this->view->assign('products', $products);
        }
    }



Our ``\MyVendor\StoreInventory\Controller\StoreInventoryController`` must be derived from the
``\TYPO3\CMS\Extbase\Mvc\Controller\ActionController``. It contains only the method ``listAction()``.
Extbase identifies all methods that ends with ``Action`` as actions.

In the method ``injectProductRepository()`` the ``ProductRepository`` is instantiated.
Now we can access the repository with ``$this->productRepository`` in all actions.
It is important to get the repository with the injector method, because the repository implements the
*SingletonInterface* and therefore we are not allowed to use the *new* keyword to get an instance of the repository.
There must always be only one repository in memory!

In the listAction we get all products by the repository method ``findAll()``.
This method is implemented in the class ``\TYPO3\CMS\Extbase\Persistence\Repository``.
Which methods are also still for disposition you can read in chapter 6.

As result we get a ``\TYPO3\CMS\Extbase\Persistence\Generic\QueryResult`` object which holds the product objects.
We pass these objects to the view with ``$this->view->assign(…)``.
Without further assistance, at the end of the action the view is
told to return the passed content back to TYPO3, rendered based on an HTML template.

.. code-block:: php

    return $this->view->render();

This line is declined by Extbase for us, if we not initiate the rendering process ourselves.
So we can omit the line in our case.
