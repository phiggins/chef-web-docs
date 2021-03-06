=====================================================
dnf_package
=====================================================
`[edit on GitHub] <https://github.com/chef/chef-web-docs/blob/master/chef_master/source/resource_dnf_package.rst>`__

.. tag resource_package_dnf

Use the **dnf_package** resource to install, upgrade, and remove packages with DNF for Fedora platforms. The **dnf_package** resource is able to resolve ``provides`` data for packages much like DNF can do when it is run from the command line. This allows a variety of options for installing packages, like minimum versions, virtual provides, and library names.

.. end_tag

.. note:: .. tag notes_resource_based_on_package

          In many cases, it is better to use the **package** resource instead of this one. This is because when the **package** resource is used in a recipe, the chef-client will use details that are collected by Ohai at the start of the chef-client run to determine the correct package application. Using the **package** resource allows a recipe to be authored in a way that allows it to be used across many platforms.

          .. end_tag

Syntax
=====================================================
A **dnf_package** resource block manages a package on a node, typically by installing it. The simplest use of the **dnf_package** resource is:

.. code-block:: ruby

   dnf_package 'package_name'

which will install the named package using all of the default options and the default action (``:install``).

The full syntax for all of the properties that are available to the **dnf_package** resource is:

.. code-block:: ruby

   dnf_package 'name' do
     arch                       String, Array
     flush_cache                Array
     ignore_failure             TrueClass, FalseClass # defaults to ``false``
     notifies                   # see description
     options                    String
     package_name               String, Array # defaults to 'name' if not specified
     provider                   Chef::Provider::Package::dnf
     retries                    Integer
     retry_delay                Integer
     sensitive                  TrueClass, FalseClass # defaults to ``false``
     source                     String
     subscribes                 # see description
     timeout                    String, Integer
     version                    String, Array
     action                     Symbol # defaults to :install if not specified
   end

where

* ``dnf_package`` tells the chef-client to manage a package
* ``'name'`` is the name of the package
* ``action`` identifies which steps the chef-client will take to bring the node into the desired state
* ``arch``, ``flush_cache``, etc. are the properties available to this resource. See the `Properties </resource_dnf_package.html#properties>`__ section below for more information about all of the properties that may be used with this resource.

Actions
=====================================================
This resource has the following actions:

``:install``
   Default. Install a package. If a version is specified, install the specified version of the package.

``:lock``
   Locks the DNF package to a specific version.

``:nothing``
   .. tag resources_common_actions_nothing

   Define this resource block to do nothing until notified by another resource to take action. When this resource is notified, this resource block is either run immediately or it is queued up to be run at the end of the chef-client run.

   .. end_tag

``:purge``
   Purge a package. This action typically removes the configuration files as well as the package.

``:remove``
   Remove a package.

``:unlock``
   Unlocks the DNF package so that it can be upgraded to a newer version.

``:upgrade``
   Install a package and/or ensure that a package is the latest version. This action will ignore the ``version`` attribute.

Properties
=====================================================
This resource has the following properties:

``arch``
   **Ruby Types:** String

   The architecture of the package to be installed or upgraded. This value can also be passed as part of the package name.

``flush_cache``
   **Ruby Type:** Array

   Flush the in-memory cache before or after a DNF operation that installs, upgrades, or removes a package. Default value: ``[ :before, :after ]``. The value may also be a Hash: ``( { :before => true/false, :after => true/false } )``.

   .. tag resources_common_package_dnf_cache

   DNF automatically synchronizes remote metadata to a local cache. The chef-client creates a copy of the local cache, and then stores it in-memory during the chef-client run. The in-memory cache allows packages to be installed during the chef-client run without the need to continue synchronizing the remote metadata to the local cache while the chef-client run is in-progress.

   .. end_tag

   As an array:

   .. code-block:: ruby

      dnf_package 'some-package' do
        #...
        flush_cache [ :before ]
        #...
      end

   and as a Hash:

   .. code-block:: ruby

      dnf_package 'some-package' do
        #...
        flush_cache( { :after => true } )
        #...
      end

   .. note:: The ``flush_cache`` property does not flush the local DNF cache! Use dnf tools---``dnf clean metadata``, ``dnf clean packages``, ``dnf clean all``---to clean the local DNF cache.

``ignore_failure``
   **Ruby Types:** TrueClass, FalseClass

   Continue running a recipe if a resource fails for any reason. Default value: ``false``.

``notifies``
   **Ruby Type:** Symbol, 'Chef::Resource[String]'

   .. tag resources_common_notification_notifies

   A resource may notify another resource to take action when its state changes. Specify a ``'resource[name]'``, the ``:action`` that resource should take, and then the ``:timer`` for that action. A resource may notifiy more than one resource; use a ``notifies`` statement for each resource to be notified.

   .. end_tag

   .. tag resources_common_notification_timers

   A timer specifies the point during the chef-client run at which a notification is run. The following timers are available:

   ``:before``
      Specifies that the action on a notified resource should be run before processing the resource block in which the notification is located.

   ``:delayed``
      Default. Specifies that a notification should be queued up, and then executed at the very end of the chef-client run.

   ``:immediate``, ``:immediately``
      Specifies that a notification should be run immediately, per resource notified.

   .. end_tag

   .. tag resources_common_notification_notifies_syntax

   The syntax for ``notifies`` is:

   .. code-block:: ruby

      notifies :action, 'resource[name]', :timer

   .. end_tag

``options``
   **Ruby Type:** String

   One (or more) additional options that are passed to the command.

``package_name``
   **Ruby Types:** String, Array

   One of the following: the name of a package, the name of a package and its architecture, the name of a dependency. Default value: the ``name`` of the resource block See "Syntax" section above for more information.

``provider``
   **Ruby Type:** Chef Class

   Optional. The chef-client will attempt to determine the correct provider during the chef-client run, and then choose the best/correct provider based on configuration data collected at the start of the chef-client run. In general, a provider does not need to be specified.

``retries``
   **Ruby Type:** Integer

   The number of times to catch exceptions and retry the resource. Default value: ``0``.

``retry_delay``
   **Ruby Type:** Integer

   The retry delay (in seconds). Default value: ``2``.

``sensitive``
  **Ruby Type** TrueClass, FalseClass

   Ensure that sensitive resource data is not logged by the chef-client. Default value: ``false``. 

``source``
   **Ruby Type:** String

   Optional. The path to a package in the local file system.

``subscribes``
   **Ruby Type:** Symbol, 'Chef::Resource[String]'

   .. tag resources_common_notification_subscribes

   A resource may listen to another resource, and then take action if the state of the resource being listened to changes. Specify a ``'resource[name]'``, the ``:action`` to be taken, and then the ``:timer`` for that action.

   .. end_tag

   .. tag resources_common_notification_timers

   A timer specifies the point during the chef-client run at which a notification is run. The following timers are available:

   ``:before``
      Specifies that the action on a notified resource should be run before processing the resource block in which the notification is located.

   ``:delayed``
      Default. Specifies that a notification should be queued up, and then executed at the very end of the chef-client run.

   ``:immediate``, ``:immediately``
      Specifies that a notification should be run immediately, per resource notified.

   .. end_tag

   .. tag resources_common_notification_subscribes_syntax

   The syntax for ``subscribes`` is:

   .. code-block:: ruby

      subscribes :action, 'resource[name]', :timer

   .. end_tag

``timeout``
   **Ruby Types:** String, Integer

   The amount of time (in seconds) to wait before timing out.

``version``
   **Ruby Types:** String, Array

   The version of a package to be installed or upgraded. This attribute is ignored when using the ``:upgrade`` action.

Multiple Packages
-----------------------------------------------------
.. tag resources_common_multiple_packages

A resource may specify multiple packages and/or versions for platforms that use Yum, DNF, Apt, Zypper, or Chocolatey package managers. Specifing multiple packages and/or versions allows a single transaction to:

* Download the specified packages and versions via a single HTTP transaction
* Update or install multiple packages with a single resource during the chef-client run

For example, installing multiple packages:

.. code-block:: ruby

   package %w(package1 package2)

Installing multiple packages with versions:

.. code-block:: ruby

   package %w(package1 package2) do
     version [ '1.3.4-2', '4.3.6-1']
   end

Upgrading multiple packages:

.. code-block:: ruby

   package %w(package1 package2)  do
     action :upgrade
   end

Removing multiple packages:

.. code-block:: ruby

   package %w(package1 package2)  do
     action :remove
   end

Purging multiple packages:

.. code-block:: ruby

   package %w(package1 package2)  do
     action :purge
   end

Notifications, via an implicit name:

.. code-block:: ruby

   package %w(package1 package2)  do
     action :nothing
   end

   log 'call a notification' do
     notifies :install, 'package[package1, package2]', :immediately
   end

.. note:: Notifications and subscriptions do not need to be updated when packages and versions are added or removed from the ``package_name`` or ``version`` properties.

.. end_tag

Providers
=====================================================
.. tag resources_common_provider

Where a resource represents a piece of the system (and its desired state), a provider defines the steps that are needed to bring that piece of the system from its current state into the desired state.

.. end_tag

.. tag resources_common_provider_attributes

The chef-client will determine the correct provider based on configuration data collected by Ohai at the start of the chef-client run. This configuration data is then mapped to a platform and an associated list of providers.

Generally, it's best to let the chef-client choose the provider, and this is (by far) the most common approach. However, in some cases, specifying a provider may be desirable. There are two approaches:

* Use a more specific short name---``yum_package "foo" do`` instead of ``package "foo" do``, ``script "foo" do`` instead of ``bash "foo" do``, and so on---when available
* Use the ``provider`` property within the resource block to specify the long name of the provider as a property of a resource. For example: ``provider Chef::Provider::Long::Name``

.. end_tag

This resource has the following providers:

``Chef::Provider::Package``, ``package``
   When this short name is used, the chef-client will attempt to determine the correct provider during the chef-client run.

``Chef::Provider::Package::dnf``, ``dnf_package``
   The provider for the dnf package provider.

Examples
=====================================================
The following examples demonstrate various approaches for using resources in recipes. If you want to see examples of how Chef uses resources in recipes, take a closer look at the cookbooks that Chef authors and maintains: https://github.com/chef-cookbooks.

**Install an exact version**

.. tag resource_dnf_package_install_exact_version

.. To install an exact version:

.. code-block:: ruby

   dnf_package 'netpbm = 10.35.58-8.el5'

.. end_tag

**Install a minimum version**

.. tag resource_dnf_package_install_minimum_version

.. To install a minimum version:

.. code-block:: ruby

   dnf_package 'netpbm >= 10.35.58-8.el5'

.. end_tag

**Install a minimum version using the default action**

.. tag resource_dnf_package_install_package_using_default_action

.. To install the same package using the default action:

.. code-block:: ruby

   dnf_package 'netpbm'

.. end_tag

**To install a package**

.. tag resource_dnf_package_install_package

.. To install a package:

.. code-block:: ruby

   dnf_package 'netpbm' do
     action :install
   end

.. end_tag

**To install a partial minimum version**

.. tag resource_dnf_package_install_partial_minimum_version

.. To install a partial minimum version:

.. code-block:: ruby

   dnf_package 'netpbm >= 10'

.. end_tag

**To install a specific architecture**

.. tag resource_dnf_package_install_specific_architecture

.. To install a specific architecture:

.. code-block:: ruby

   dnf_package 'netpbm' do
     arch 'i386'
   end

or:

.. code-block:: ruby

   dnf_package 'netpbm.x86_64'

.. end_tag

**To install a specific version-release**

.. tag resource_dnf_package_install_specific_version_release

.. To install a specific version-release:

.. code-block:: ruby

   dnf_package 'netpbm' do
     version '10.35.58-8.el5'
   end

.. end_tag

**To install a specific version (even when older than the current)**

.. tag resource_dnf_package_install_specific_version

.. To install a specific version (even if it is older than the version currently installed):

.. code-block:: ruby

   dnf_package 'tzdata' do
     version '2011b-1.el5'
   end

.. end_tag

**Handle cookbook_file and dnf_package resources in the same recipe**

.. tag resource_dnf_package_handle_cookbook_file_and_dnf_package

.. To handle cookbook_file and dnf_package when both called in the same recipe

When a **cookbook_file** resource and a **dnf_package** resource are both called from within the same recipe, use the ``flush_cache`` attribute to dump the in-memory DNF cache, and then use the repository immediately to ensure that the correct package is installed:

.. code-block:: ruby

   cookbook_file '/etc/yum.repos.d/custom.repo' do
     source 'custom'
     mode '0755'
   end

   dnf_package 'only-in-custom-repo' do
     action :install
     flush_cache [ :before ]
   end

.. end_tag
