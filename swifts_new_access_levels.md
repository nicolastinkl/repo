# Swift’s new access levels

Prior to Xcode beta 4, there was one and only one level of access in Swift. Now there are three:

- private – accessible only from within the source file where it’s defined,

- internal – accessible only from any file within the target where it’s defined, and

- public – accessible from any file within the target where’s it’s defined, and from within any other context that imports the current target’s module.


![](http://www.globalnerdy.com/wordpress/wp-content/uploads/2014/07/internal.jpg)

![](http://www.globalnerdy.com/wordpress/wp-content/uploads/2014/07/private.jpg)

![](http://www.globalnerdy.com/wordpress/wp-content/uploads/2014/07/public.jpg)

> A public entity can be “seen” within the file where it’s defined, from any other file in the same application or framework, and by another file any other application or framework that imports the application or framework where the public entity was defined.
