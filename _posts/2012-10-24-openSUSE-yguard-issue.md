---
layout: post
tags : [java, ant, yguard, awt, headless]
---
{% include JB/setup %}

Recently we've installed a new headless PC with openSUSE 12.2 to run tests. We're testing GUI using [FEST Swing Library](http://code.google.com/p/fest/) and for running each parallel set of tests in isolation we use [jenkins Xvnc plugin](https://wiki.jenkins-ci.org/display/JENKINS/Xvnc+Plugin). It starts up an Xvnc server before every build and tears it down after build is finished. Each time it looks for a free display number, then sets DISPLAY environment variable.

The problem that we've faced was during obfuscation when Xvnc server is started and DISPLAY variable is set:

	/opt/hudson/workspace/Testing MySQL/dev_frozen/build.xml:705: The following error occurred while executing this line:
	/opt/hudson/workspace/Testing MySQL/dev_frozen/build.xml:429: java.lang.NoClassDefFoundError: java.awt.Container
		at java.lang.Class.getDeclaredMethods0(Native Method)
		at java.lang.Class.privateGetDeclaredMethods(Class.java:2427)
		at java.lang.Class.privateGetPublicMethods(Class.java:2547)
		at java.lang.Class.getMethods(Class.java:1410)
		at com.yworks.yguard.obf.Cl.scanExtSupers(Unknown Source)
		at com.yworks.yguard.obf.Cl.scanExtSupers(Unknown Source)
		at com.yworks.yguard.obf.Cl.scanNameSpaceExcept(Unknown Source)
		at com.yworks.yguard.obf.Cl.resolveOptimally(Unknown Source)
		at com.yworks.yguard.obf.ClassTree$4.classAction(Unknown Source)
		at com.yworks.yguard.obf.ClassTree.walkTree(Unknown Source)
		at com.yworks.yguard.obf.ClassTree.walkTree(Unknown Source)
		at com.yworks.yguard.obf.ClassTree.walkTree(Unknown Source)
		at com.yworks.yguard.obf.ClassTree.walkTree(Unknown Source)
		at com.yworks.yguard.obf.ClassTree.walkTree(Unknown Source)
		at com.yworks.yguard.obf.ClassTree.walkTree(Unknown Source)
		at com.yworks.yguard.obf.ClassTree.walkTree(Unknown Source)
		at com.yworks.yguard.obf.ClassTree.resolveClasses(Unknown Source)
		at com.yworks.yguard.obf.GuardDB.createMap(Unknown Source)
		at com.yworks.yguard.obf.GuardDB.remapTo(Unknown Source)
		at com.yworks.yguard.ObfuscatorTask.execute(Unknown Source)
		at com.yworks.yguard.YGuardTask.execute(Unknown Source)
		at org.apache.tools.ant.UnknownElement.execute(UnknownElement.java:291)
		at sun.reflect.GeneratedMethodAccessor4.invoke(Unknown Source)
		at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
		at java.lang.reflect.Method.invoke(Method.java:597)
		at org.apache.tools.ant.dispatch.DispatchUtils.execute(DispatchUtils.java:106)
		at org.apache.tools.ant.Task.perform(Task.java:348)
		at org.apache.tools.ant.Target.execute(Target.java:390)
		at org.apache.tools.ant.Target.performTasks(Target.java:411)
		at org.apache.tools.ant.Project.executeSortedTargets(Project.java:1397)
		at org.apache.tools.ant.helper.SingleCheckExecutor.executeTargets(SingleCheckExecutor.java:38)
		at org.apache.tools.ant.Project.executeTargets(Project.java:1249)
		at org.apache.tools.ant.taskdefs.Ant.execute(Ant.java:442)
		at org.apache.tools.ant.taskdefs.CallTarget.execute(CallTarget.java:105)
		at org.apache.tools.ant.UnknownElement.execute(UnknownElement.java:291)
		at sun.reflect.GeneratedMethodAccessor4.invoke(Unknown Source)
		at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
		at java.lang.reflect.Method.invoke(Method.java:597)
		at org.apache.tools.ant.dispatch.DispatchUtils.execute(DispatchUtils.java:106)
		at org.apache.tools.ant.Task.perform(Task.java:348)
		at org.apache.tools.ant.Target.execute(Target.java:390)
		at org.apache.tools.ant.Target.performTasks(Target.java:411)
		at org.apache.tools.ant.Project.executeSortedTargets(Project.java:1397)
		at org.apache.tools.ant.Project.executeTarget(Project.java:1366)
		at org.apache.tools.ant.helper.DefaultExecutor.executeTargets(DefaultExecutor.java:41)
		at org.apache.tools.ant.Project.executeTargets(Project.java:1249)
		at org.apache.tools.ant.Main.runBuild(Main.java:801)
		at org.apache.tools.ant.Main.startAnt(Main.java:218)
		at org.apache.tools.ant.launch.Launcher.run(Launcher.java:280)
		at org.apache.tools.ant.launch.Launcher.main(Launcher.java:109)

It tooks couple of hours for me to find out that the reason for this thing is Xvnc plugin, which sets up DISPLAY variable and forces JVM to run in headful mode. I've guessed that class loader fails to load some library in such case. 

Then I've decided to run following test sample after running Xvnc:

	import javax.swing.*;

	public class FrameTest extends JFrame
	{
	    public static void main(String[] args)
	    {
	        FrameTest f = new FrameTest();
	        f.setTitle("Test");
	        f.setBounds(0, 0, 100, 100);
	        f.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
	        f.setVisible(true);
	    }
	}

This results in following error:

	Exception in thread "main" java.lang.UnsatisfiedLinkError: /usr/lib64/jvm/jdk1.6.0_35/jre/lib/amd64/xawt/libmawt.so: libXtst.so.6: cannot open shared object file: No such file or directory
		at java.lang.ClassLoader$NativeLibrary.load(Native Method)
		at java.lang.ClassLoader.loadLibrary0(ClassLoader.java:1807)
		at java.lang.ClassLoader.loadLibrary(ClassLoader.java:1703)
		at java.lang.Runtime.load0(Runtime.java:770)
		at java.lang.System.load(System.java:1003)
		at java.lang.ClassLoader$NativeLibrary.load(Native Method)
		at java.lang.ClassLoader.loadLibrary0(ClassLoader.java:1807)
		at java.lang.ClassLoader.loadLibrary(ClassLoader.java:1724)
		at java.lang.Runtime.loadLibrary0(Runtime.java:823)
		at java.lang.System.loadLibrary(System.java:1028)
		at sun.security.action.LoadLibraryAction.run(LoadLibraryAction.java:50)
		at java.security.AccessController.doPrivileged(Native Method)
		at sun.awt.NativeLibLoader.loadLibraries(NativeLibLoader.java:38)
		at sun.awt.DebugHelper.<clinit>(DebugHelper.java:29)
		at java.awt.Component.<clinit>(Component.java:566)
Could not find the main class: FrameTest.  Program will exit.

After googling around the solution came:

	zypper in libXtst-devel libXtst6
