---
date: 2015/9/6
title: "java.io.IOException: failure to login with PowerMock and Hadoop."
author: Jackey Lyu
---

What is it?
===========

 

If you have an application which uses the hadoop filesystem, and you want to do
some unit test on it. But the test environment with no hadoop, so we have to
mock the hadoop FileSystem.

 

It is so sad that, we may occur with the exception on hadoop IOException
"failure to login".

 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
java.io.IOException: failure to login
	at org.apache.hadoop.security.UserGroupInformation$HadoopLoginModule.commit(UserGroupInformation.java:209)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:606)
	at javax.security.auth.login.LoginContext.invoke(LoginContext.java:762)
	at javax.security.auth.login.LoginContext.access$000(LoginContext.java:203)
	at javax.security.auth.login.LoginContext$4.run(LoginContext.java:690)
	at javax.security.auth.login.LoginContext$4.run(LoginContext.java:688)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.login.LoginContext.invokePriv(LoginContext.java:687)
	at javax.security.auth.login.LoginContext.login(LoginContext.java:596)
	at org.apache.hadoop.security.UserGroupInformation.loginUserFromSubject(UserGroupInformation.java:798)
	at org.apache.hadoop.security.UserGroupInformation.getLoginUser(UserGroupInformation.java:760)
	at org.apache.hadoop.security.UserGroupInformation.getCurrentUser(UserGroupInformation.java:633)
	at org.apache.hadoop.fs.FileSystem$Cache$Key.<init>(FileSystem.java:2812)
	at org.apache.hadoop.fs.FileSystem$Cache$Key.<init>(FileSystem.java:2802)
	at org.apache.hadoop.fs.FileSystem$Cache.get(FileSystem.java:2668)
	at org.apache.hadoop.fs.FileSystem.get(FileSystem.java:371)
	at org.apache.hadoop.fs.FileSystem.get(FileSystem.java:170)
	at com.tencent.ieg.spectrum.TaskLocalInfo.createParquetWriter(TaskLocalInfo.java:291)
	at com.tencent.ieg.spectrum.EventProcessor.convertAndWrite(EventProcessor.java:423)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:606)
	at org.powermock.api.mockito.internal.invocation.MockitoMethodInvocationControl$1.invoke(MockitoMethodInvocationControl.java:240)
	at org.mockito.internal.invocation.realmethod.CleanTraceRealMethod.invoke(CleanTraceRealMethod.java:30)
	at org.mockito.internal.invocation.InvocationImpl.callRealMethod(InvocationImpl.java:112)
	at org.mockito.internal.stubbing.answers.CallsRealMethods.answer(CallsRealMethods.java:41)
	at org.mockito.internal.handler.MockHandlerImpl.handle(MockHandlerImpl.java:93)
	at org.powermock.api.mockito.internal.invocation.MockitoMethodInvocationControl.performIntercept(MockitoMethodInvocationControl.java:262)
	at org.powermock.api.mockito.internal.invocation.MockitoMethodInvocationControl.invoke(MockitoMethodInvocationControl.java:190)
	at org.powermock.core.MockGateway.doMethodCall(MockGateway.java:124)
	at org.powermock.core.MockGateway.methodCall(MockGateway.java:185)
	at com.tencent.ieg.spectrum.EventProcessor.convertAndWrite(EventProcessor.java)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:606)
	at org.powermock.reflect.internal.WhiteboxImpl.performMethodInvocation(WhiteboxImpl.java:1873)
	at org.powermock.reflect.internal.WhiteboxImpl.doInvokeMethod(WhiteboxImpl.java:773)
	at org.powermock.reflect.internal.WhiteboxImpl.invokeMethod(WhiteboxImpl.java:638)
	at org.powermock.reflect.Whitebox.invokeMethod(Whitebox.java:401)
	at com.tencent.ieg.spectrum.EventProcessorTest.testConvertAndWriteWithEmptyData(EventProcessorTest.java:391)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

 

That is caused by the PowerMock, it hack the classloader, which make it return
an empty string for getCurrentUser.

 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

How to solve it?
================

Know the reason, and the solution comes.

 

You can solve it by a shell like this,

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
export HADOOP_USER_NAME="some-body-you-like"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

 

Or in the setup of JUnit, add one line like

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
System.setProperty("HADOOP_USER_NAME","something-you-like");
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

 

OK, it is solved in my case, I choose the second one.
