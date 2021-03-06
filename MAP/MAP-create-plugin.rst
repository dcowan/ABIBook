.. _MAP-create-plugin:

===========
MAP Plugins
===========

The Plugin lies at the heart of the MAP framework.  The key idea behind the plugins is to make them as simple as possible to implement.  The interface is defined in documentation and the plugin developer is expected to adhere to it.  The framework leaves the responsibility of conforming to the plugin interface up to the plugin developer.  The plugin framework is based on Marty Alchin's [1] article on a plugin framework for Django.  The plugin framework is very lightweight and requires no external libraries and can be made to work with Python 2 and Python 3 simultaneously.


Workflow Step
=============

The Workflow Step is the basic item that a plugin developers need to place their software within.  A workflow step can be of any size and complexity.  Although it must adhere to the plugin design to work properly with the application.  Every step that wishes to act like a Workflow Step must derive itself from the Workflow step mountpoint.  The Workflow step mountpoint is the interface between the application and the plugin.  The Workflow step mountpoint can be imported like so:

::

 from mountpoints.workflowstep import WorkflowStepMountPoint

A skeleton step is provided as a starting point for the developer to create their own workflow steps.  The skeleton step is actually a valid step in its own right and it will show up in the Step box if enabled.  However the skeleton step has no use other than as an item to drag around on the workflow area.  The skeleton step is discussed below, first however the plugin interface is discussed.

Plugin Interface
----------------

The plugin interface is the layer between the application and the developers plugin.  The plugin interface is not defined by contract as we so often see in Java.  But rather the plugin interface is defined by documentation.  This puts the burden of the specification on the documentation and the conformity of the specification on the developer.  The underlying theory is that the developer is able to follow the specification without the application having to do rigourous checks to make sure this is the case.  The phrase 'If it walks like a duck' is often used.

In this section the specification of the Workflow step plugin interface is given.  It is then upto the developer to make sure their plugin behaves like one.
 
The details of the plugin interface are provided in the documentation of the source code in the relevant source file and additionally here for easy reference.  The documentation provided with the source code is very direct with little explanation the following documentation provides a bit more explanation and discussion on the various aspects of the plugin interface.  The documentation provided here should be considered the slave documentation and the documentation provided with the source code as the master documentation.  

There are essentially, what may be considered, three different levels of the plugin design.

 #. The Musts
 #. The Shoulds
 #. The Coulds
 
Creating a workflow step that satisifies the musts will create an actual workflow step that can be added to the workflow area and interacted with.  But it won't be very useful.  Satisfying the shoulds will usually be sufficient for the very simplest of steps.  Simple steps are for instance ones that provide images, or location information for data.  Doing some of the coulds will create a much more interesting step.

The requirements for creating a step have been kept as simple as possible, this is to allow the developer a quick route into the development of the step content. 

The following three sections discuss these three levels in more detail.

A Step Must
-----------

 * The plugin must be derived from the WorkflowStepMountPoint class defined in the package mountpoints.workflowstep
 * Accept a single parameter in it's __init__ method.
 * Define a name for itself, this must be passed into the initialisation of the base class.
 * Define the methods
 
   ::
  
     def configure(self):
         pass
     
     def getIdentifier(self):
         pass
     
     def setIdentifier(self, identifier):
         pass
     
     def serialize(self, location):
         pass
     
     def deserialize(self, location):
         pass
 
A Step Should
-------------
 
 * Implement the configure method to configure the step.  This is typically in the form of a dialog.  When implementing this function the class method self._configureObserver() should be called to inform the application that the step configuration has finished.
 * Implement the getIdentifier/setIdentifier methods to return the identifier of the step.
 * Implement the serialize/deserialize methods.  The steps should serialize and deserialize from a file on disk located at the given location.
 * Define a class attribute _icon.  That is of the type QtGui.QImage.
 * Information about what the step uses and/or what it provides.  This is achieved through defining ports on the step.
 
A Step Could
------------

 * Implement the method 'portOutput(self)' if it was providing some information to another step.
 * Implement the method 'execute(self, dataIn)' if it uses some information from another step.  If a step implements the 'execute(self, dataIn)' method then it must call '_doneExecution()' when the step is finished.
 * Define a category using the '_category' attribute.  This attribute will add the step to the named category in the step box, or it will create the named category if it is not present.

Ports
=====

A port is a device to specify what a workflow step provides or uses.  A port is described using Resource Description Framework (RDF) triples.  The port description is used to determine whether or not two ports may be connected together.
One port can either use or provide one thing. A single port must not both provide a thing and use a thing.  Ports are ordered by entry position.

Ports are added by using the 'addPort(self, triple)' method from the base class.

Skeleton Step
=============

The skeleton step satisfies the musts of the plugin interface.  It is a minimal step and it is set out as follows.

A Python package with the step name is created, in this case 'skeletonstep',  in the module file we add the code that needs to be read when the plugins are loaded.

The module file performs four functions.  It contains the version information and the authors name of the module.  For instance the skeleton step has a version of '0.1.0' and authors name of 'Xxxx Yyyyy'.  It adds the current directory into the Python path, this is done so that the steps python files know where they are in relation to the python path.  It also (optionally) prints out a message showing that the plugin has been loaded successfully.  But the most important function it performs is to call the python file that contains the class that derives from the workflow step mountpoint.

The 'SkeletonStep' class in the skeletonstep.step package is a very simple class.  It derives from the 'WorkflowStepMountPoint', calls the base class with the name of the step, accepts a single parameter in it's init method and defines the five required functions to satisfy the plugin interface.

When enabled the skeleton step will be a fully functioning step in the MAP Client.

References
==========

[1] http://martyalchin.com/2008/jan/10/simple-plugin-framework/ Marty Alchin on January 10, 2008