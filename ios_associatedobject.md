# iOS AssociatedObject 动态关联

假定我们想要动态地将一个Tap手势操作连接到任何UIView中，并且根据需要指定点击后的实际操作。这时候我们就可以将一个手势对象及操作的block对象关联到我们的UIView对象中。这项任务分两部分。首先，如果需要，我们要创建一个手势识别对象并将它及block做为关联对象。如下代码所示：


very easy to use it:

```
[self setTapActionWithView:someView Block:^{
        //dosomethings

    }];
```
----


```

static char kDTActionHandlerTapBlockKey;
static char kDTActionHandlerTapGestureKey;

- (void)setTapActionWithView:(UIView*) view
                       Block:(void (^)(void))block
{
    UITapGestureRecognizer *gesture = objc_getAssociatedObject(self, &kDTActionHandlerTapBlockKey);

    if (!gesture)
    {
        gesture = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(__handleActionForTapGesture:)];
        [view addGestureRecognizer:gesture];
        objc_setAssociatedObject(self, &kDTActionHandlerTapGestureKey, gesture, OBJC_ASSOCIATION_RETAIN);
    }

    objc_setAssociatedObject(self, &kDTActionHandlerTapBlockKey, block, OBJC_ASSOCIATION_COPY);
}

- (void)__handleActionForTapGesture:(UITapGestureRecognizer *)gesture
{
    if (gesture.state == UIGestureRecognizerStateRecognized)
    {
        void(^action)(void) = objc_getAssociatedObject(self, &kDTActionHandlerTapBlockKey);

        if (action)
        {
            action();
        }
    }
}



```

