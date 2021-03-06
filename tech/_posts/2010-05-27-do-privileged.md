---
title: using doPrivileged
tags: Security
---

<div class="alert alert-light">Resurrected from <a href="https://web.archive.org/web/20120601230618/http://a1drin.net:80/do_privileged.html">a1drin.net</a></div>


the `doPrivileged` api enables a few usage scenarios which wouldn't have
been possible in its absence.  its quite neatly [documented](http://java.sun.com/j2se/1.4.2/docs/guide/security/doprivileged.html), still, it took
me a while to really get a hang of its usage. this note shares my
understanding on the usage and the capabilities of this neat little
extension to the java access control framework.

#### getting started<a id="sec-1" name="sec-1"></a>

consider the task of implementing the following protected resource:

```java
// code-base 'file:///code/resource'
public class Resource {
  public void read() {
    RuntimePermission permission = new RuntimePermission("resource.read");
    AccessController.checkPermission(permission);
    System.out.println("read"); // actual read implementation 
  }

  public void write() {
    RuntimePermission permission = new RuntimePermission("resource.write");
    AccessController.checkPermission(permission);
    System.out.println("write"); // actual write implementation
  }
}
```

the resource has two operations which check for the necessary permission
before executing the the actual implementation. this allows us to control
access to the operations even in cases when the resource is used from
arbitrary user code. for example, we can implement read-only access to the
resource by enforcing the following policy (assuming that the resource and
user classes belong to separate code-bases)

```java
grant codebase "file:///code/resource" {
 permission java.lang.RuntimePermission "resource.*";
};
grant codebase "file:///code/user" {
 permission java.lang.RuntimePermission "resource.read";
};
```

such a strict policy might limit a few useful use cases. for example, it
might be useful to allow the user code to write to the resource, if we can
program reliable tests that it writes responsibly without corrupting the
resource. one possibly way to implement such a system is to introduce a
wrapper between the user and the resource which intercept all the writes
and block the illegal/harmful ones. something like the following:

```java
// code-base 'file:///code/resource'
public class ResourceWrapper {
  private Resource resource;

  public ResourceWrapper(Resource r){ resource = r; }

  public void write() {
    if(writeIsBad) // filter out the bad stuff.
     return; 

    resource.write();
  }
}
```

as we own and control the wrapper code we grant it all permissions over the
resource, hoping that well behaved user code will be able to write to the
resource by invoking the wrapper instead of the resource directly.

the trouble is that the access controller, by default, requires that the
needed permission is granted to *every* protection domain on the call
stack. *we* know that wrapper is trusted and ensures that the writes it
lets through are harmless regardless of where they orignated from, but the
access controller doesn't. what we need is a mechanism by which a trusted
protection domain can confer its own permissions to a call originating from
an untrusted domain. and that, is exactly, what the `doPrivileged` api is.

all that a `doPrivileged` call does is mark the protection domain which
invoked it as *privileged*. if a *privileged* domain on the stack has the
required permission, the call succeeds regardless of whether or not the
domains below it on the call-stack have the permission. so if our wrapper
is modified as follows:

```java
// code-base 'file:///code/resource'
public class ResourceWrapper {
  //...
  public void write() {
    if(writeIsBad) // filter out the bad stuff.
      return; 

    AccessController.doPrivileged(new PrivilegedAction() {
        public Object run() {
          resource.write();
          return null;
        }
      });
  }
}
```
the users of the resource can invoke regulated writes as follows:


```java
// code-base 'file:///code/user'
public class User {
  public static void main(String args[]) {
    Resource resource = new Resource();
    ResourceWrapper watchfulEyes = new ResourceWrapper(resource);
    // can operate directly
    resource.read(); // we have the permission ourself.
    // operate in a sandbox
    watchfulEyes.write(); // we don't have the permission ourselves.
  }
}
```

#### limiting the permissions<a id="sec-2" name="sec-2"></a>

note that when we used the privileged block to enable the wrapper
permissions to an untrusted invoker, we enabled *all* of them for the scope
of that call. this might be a problem, for example, consider if our
resource was implemented as follows:

```java
public class Resource {
  //...
  public void write() {
    RuntimePermission permission = new RuntimePermission("resource.write");
    AccessController.checkPermission(permission);

    if(!existsAlready) // the resource does not exist
      create(); // create it first.

    System.out.println("write"); // do the actual write.
  }

  public void create() {
    RuntimePermission permission = new RuntimePermission("resource.create");
    AccessController.checkPermission(permission);
    System.out.println("create"); // do the actual 'create'.
  }
}
```
in this version, if a write is invoked on a resource which does not exist
already, the implementation creates it first.  if our intention was only to
allow the user code to write to existing resources but not to give them the
ability to create new ones, our existing wrapper won't be able to enforce
it. in invoking the privileged call it does not specify how many of its own
permissions to enable for the user-code, and thus ends up giving the
`create` permission too.  

to fix this behavior we need a mechanism to specify a subset of permissions
which the trusted protection domain wishes to confer for a privileged
call. and that is achieved by specifying an `AccessControlContext`
parameter in the privileged call.  consider the following wrapper
implementation:

```java
// code-base 'file:///code/resource'
public class ResourceWrapper {
  //...
  public void write() {
    // specify a subset of permission which need to be enabled
    Permissions subset = new Permissions();
    subset.add(new RuntimePermission("resource.write"));
    AccessControlContext context = new AccessControlContext
      ({ new ProtectionDomain(null,subset) });
    AccessController.doPrivileged(new PrivilegedAction() {
        public Object run() {
          resource.write();
          return null;
        }
      }, context);
  }
```
now, the wrapper explicitly specifies that it only wants to enable the
write permission for its caller. this way, user code which writes to
existing resources will work as before but attempts to use the wrapper code
as short-cut to create new resources would fail.

#### summary<a id="sec-3" name="sec-3"></a>

to summarize, the `doPrivileged` api provides a handy mechanism for trusted
code to enable a subset of its permissions for its invokers.
