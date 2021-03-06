Lab 3.2 - F5 Container Connector Usage
======================================

Now that our container connector is up and running, let’s deploy an
application and leverage our F5 CC.

For this lab we'll use a simple pre-configured docker image called 
"f5-hello-world". It can be found on docker hub at
`f5devcentral/f5-hello-world <https://hub.docker.com/r/f5devcentral/f5-hello-world/>`_

App Deployment
--------------

From the jumpbox connect to the Marathon UI at http://10.2.10.21:8080 and click
"Create Application".

.. image:: images/f5-container-connector-create-application-button.png

#. Click on "JSON mode" in the top-right corner

   .. image:: images/f5-container-connector-json-mode.png

#. **REPLACE** the 8 lines of default JSON code shown with the following JSON
   code and click Create Application

   .. literalinclude:: ../marathon/f5-hello-world-app.json
      :language: json
      :linenos:
      :emphasize-lines: 5,9,18,19,22

#. F5-Hello-World is "Deploying"

   .. note:: The JSON app definition specified several things:

      #. What container image to use (line 9)
      #. The BIG-IP configuration (Partition, VS IP, VS Port).
      #. The Marathon health check for this app. The BIG-IP will replicate
         those health checks.
      #. The number of instances (line 5)

   Wait for your application to be successfully deployed and be in a running
   state.

   .. image:: images/f5-container-connector-check-application-running.png

#. Click on "f5-hello-world". Here you will see two instance deployed, with
   their node IP and Port.

   .. image:: images/f5-container-connector-check-application-instance.png

#. Click on one of the <IP:Port> assigned to be redirect there:

   .. image:: images/f5-container-connector-access-application-instance.png

#. We can check whether the Marathon BIG-IP Controller has updated our BIG-IP
   configuration accordingly. Connect to your BIG-IP on https://10.1.1.245 and
   go to Local Traffic --> Virtual Server.

   .. warning:: Don’t forget to select the “mesos” partition or you’ll see
      nothing.
    
   You should have something like this:

   .. image:: images/f5-container-connector-check-app-on-BIG-IP-VS.png

#. Go to Local Traffic --> Pool --> "f5-hello-world_80" --> Members. Here we
   can see that two pool members are defined and the IP:Port match ou
   deployed app in Marathon.

   .. image:: images/f5-container-connector-check-app-on-BIG-IP-Pool_members.png

#. You should be able to access the application. In your browser try to
   connect to http://10.2.10.81

   .. image:: images/f5-container-connector-access-BIGIP-VS.png

#. Scale the f5-hello-world app. Go back to the Marathon UI
   (http://10.2.10.21:8080). Go to Applications --> "f5-hello-world" and click
   "Scale Application".

   Let's increase the number from `2` to `10` instances and click on
   "Scale Application".

   .. image:: images/f5-container-connector-scale-application-UI.png

   Once it is done you should see 10 "healthy instances" running in Marathon UI.

   .. image:: images/f5-container-connector-scale-application-UI-10-done.png

   You can also check your pool members list on your BIG-IP.

   .. image:: images/f5-container-connector-scale-application-BIGIP-10-done.png

   As we can see, the Marathon BIG-IP Controller is adapting the pool members
   setup based on the number of instances delivering this application
   automatically.

#. Scale back the application to `2` to save resources for the next labs.
