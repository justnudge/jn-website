---
layout: post
title: Querying JVM memory with JMX
tags: [jmx]
---
Monitoring memory utilisation of JMX is an important part of monitoring runtime infrastructure.
There are a number of methods for achieving this such as:

  * Parsing the verbose GC logs.
  * Querying the JVM using remote JMX calls.
  * Querying the JVM using local JMX calls <b>outside</b> of the running JVM.

The first gives you great information including timings for the GC runs but can be a little bit verbose (minor issue I know).  The second (remote JMX) provides some issues in terms of security, either you leave it off and it is insecure or you turn it off and it becomes overly complex (firewalls, SSL and usernames and passwords).  The last option has the following good points:

  * Does not need an agent installed into the JVM.
  * Can query the JVM while it is running.
  * Very light weight.

We are triggering this using NRPE calls and using Nagios as the triggering mechanism.  To make the connection to the JVM you need to be running as the user that is running the VM (i.e. if you are running the VM as tomcat you must make the call as the tomcat user, you cannot use another user or root or it will fail).  The process for connecting to a JVM is as follows:

	VirtualMachine vm = VirtualMachine.attach(Integer.toString(processID));
	String address = vm.getAgentProperties().getProperty(CONNECTOR_ADDRESS);

Now, if the connector address has never been loaded then it will return null, to load it run the following code:

	String javaHome = (String) vm.getSystemProperties().get("java.home");
	String agent = javaHome + "/lib/management-agent.jar";
	vm.loadAgent(agent);

The address can then be gathered by re-running the <code>vm.getAgentProperties().getProperty(CONNECTOR_ADDRESS);</code> again.

Now that you have a VM connection you can run JMX queries to get runtime information from the system:

	List<MemoryPoolMXBean> list = ManagementFactory.getPlatformMXBeans(connection, MemoryPoolMXBean.class);

From here you can query much of the JVM's runtime settings.

I hope that this helps.