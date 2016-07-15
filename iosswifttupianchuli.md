# Using vector images in Xcode 6

```
iOS applications are an image-driven species. When developing an app, you need icons in various sizes, Default.png images in different sizes, and also @1x and @2x images for each image file inside the app. All of these images make applications look attractive, but the downside is you have to generate these image files individually. With the introduction of the iPhone 6 and 6 Plus last week, I couldn’t help but think how difficult it will be to manage yet another group of assets: @3x assets.

Fortunately, Apple has provided some great tools in Xcode 6 for managing assets. Even better, this is a way to prepare your apps for future iOS devices. One piece of this is the ability to generate Storyboard-based Launch Images in Xcode 6 and iOS 8, leaving behind the notion of individual images for each device type. Another piece of this technology is the ability to generate vector-based images from a PDF at build-time in Xcode 6. In this article, I want to delve into how you can do the latter, saving yourself time (and your sanity) in the process.

Session 411 from WWDC “What’s New in Interface Builder” discussed—albeit very briefly—Xcode’s support for creating your PNG files at build time from a vectorized PDF. I share with you exactly how this is done.

```


## 1. Step 1. Generate vector PDFs in Illustrator
![](http://martiancraft.com/img/blog/articles/large/20140917_vector-images-xcode6_vector-01.png)
## 2.  Step 2. Set up your Xcode project
![](http://martiancraft.com/img/blog/articles/large/20140917_vector-images-xcode6_vector-02.png)
## 3. Step 3. Watch the magic happen

When you build your project, Xcode will go to work, creating @1x, @2x, and @3x PNG files from the PDF that you’ve used in the Xcode Asset Catalog. For instance, if you had a @1x PDF that was 150px x 150px, then Xcode will generate the following PNG sizes for use in the application:

- @1x PNG at 150px x 150px
- @2x PNG at 300px x 300px
- @3x PNG at 450px x 450px


When you run the application, iOS will automatically pick the appropriate @1x, @2x, or @3x image that Xcode generated based on the device requirements. Be sure to specify your AutoLayout constraints so that the image isn’t resized on larger devices, otherwise you’ll end up with blurry images. Xcode generates raster images that are based on the @1x PDF image instead of scaling a vector at run-time.


> 参考:
[http://martiancraft.com/blog/2014/09/vector-images-xcode6/](http://martiancraft.com/blog/2014/09/vector-images-xcode6/)
