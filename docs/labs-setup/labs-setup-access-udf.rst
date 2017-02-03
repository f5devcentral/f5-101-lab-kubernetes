.. _access_udf:

Connecting to UDF
=================

We consider that you have access to UDF for the different labs. 

Depending on what you want to learn, you may leverage different blueprints, solution: 

* If you just want to see how you can demo our solution, you may use Eric Chen's blueprint: `F5 Kubernetes demo <https://hive.f5.com/docs/DOC-42789>`_ 

.. note:: 

   you need to be an F5 employee to access this doc and also to have access to UDF. 

* If you want to see how to setup our solution on top of a running kubernetes environment in details, please continue with this guide. This guide will help you to either setup your own environment or leverage F5 UDF (you must be a F5 employee) to learn about this. 


Create your environment
-----------------------

If you want to setup your own kubernetes environment, you need to create your own deployment reflecting what has been explained in the previous section. Please go to the Cluster setup guide to do this: :ref:`my-cluster-setup`


Start your environment
----------------------

Connect to UDF and go to deployment. 

Select the relevant blueprint based on your need: 

* If you need a standalone Kubernetes deployment (1 master, 2 nodes), find the '[Kubernetes] how to setup ASP and CC (Velcro)' blueprint and deploy it
* if you need a all-in-one deployment, find the 'all-in-one - Kubernetes lab'

.. warning:: 

   Those blueprints have Kubernetes already deployed. You don't have to do the cluster setup guide or standalone setup guide

==================  ====================  ====================  ============  ====================
     Hostname           Management IP        Kubernetes IP          Role         Login/Password
==================  ====================  ====================  ============  ====================
     Master 1             10.1.1.1            10.1.10.11          Master           root/default
      node 1              10.1.1.2            10.1.10.21           node            root/default
      node 2              10.1.1.3            10.1.10.22           node           root/default
      Win7                10.1.1.4            10.1.10.50          Jumpbox            user/user
==================  ====================  ====================  ============  ====================

Access your environment
-----------------------

If you deployed one of the existing blueprint mentioned above; Once your environment is started, find the 'Jumpbox - Win7' component under 'Components' and launch RDP (in the ACCESS menu)

.. image:: ../images/Launch-RDP.png
   :scale: 50%
   :align: center

Click on the shortcut that got downloaded and it should open your RDP session. The credentials to use are user/user.

*If you have trouble reading the text please see optional directions for changing text size in the Appendix.*

.. warning:: For MAC user, it is recommended to use Microsoft Remote Desktop. You may not be able to access your jumpbox otherwise. It is available in the App store (FREE).
   

.. topic:: Change keyboard input

   The default keyboard mapping is set to english. If you need to change it, here is the method
   
   * Click on the start menu button and type 'Region' in the search field.
   * Click on 'Region and Language' option in the search list
   
   .. image:: ../images/select-region-language.png
      :scale: 50 %
      :align: center

   * Select the 'Keyboards and Languages' tab and click on 'Change keyboards'
   
   .. image:: ../images/select-change-keyboard.png
      :scale: 50 %
      :align: center

   * Add the language you want to have for your keyboard mapping

Once you have access to your environment, you can go directly to the container connector section: :ref:`container-connector`
