# iOS typedef struct
首先回顾C++:

struct _x1 { ...}x1; 和 typedef struct _x2{ ...} x2; 有什么不同？
`其实, 前者是定义了类_x1和_x1的对象实例x1, 后者是定义了类_x2和_x2的类别名x2 `

在系统库`<objc/runtime.h>`可以看到：

```

/// Defines a property attribute
typedef struct {
    const char *name;           /**< The name of the attribute */
    const char *value;          /**< The value of the attribute (usually empty) */
} objc_property_attribute_t;

```


```


/// An opaque type that represents a method in a class definition.
typedef struct objc_method *Method;

/// An opaque type that represents an instance variable.
typedef struct objc_ivar *Ivar;

/// An opaque type that represents a category.
typedef struct objc_category *Category;

/// An opaque type that represents an Objective-C declared property.
typedef struct objc_property *objc_property_t;

struct objc_class {
    Class isa  OBJC_ISA_AVAILABILITY;

#if !__OBJC2__
    Class super_class                                        OBJC2_UNAVAILABLE;
    const char *name                                         OBJC2_UNAVAILABLE;
    long version                                             OBJC2_UNAVAILABLE;
    long info                                                OBJC2_UNAVAILABLE;
    long instance_size                                       OBJC2_UNAVAILABLE;
    struct objc_ivar_list *ivars                             OBJC2_UNAVAILABLE;
    struct objc_method_list **methodLists                    OBJC2_UNAVAILABLE;
    struct objc_cache *cache                                 OBJC2_UNAVAILABLE;
    struct objc_protocol_list *protocols                     OBJC2_UNAVAILABLE;
#endif

} OBJC2_UNAVAILABLE;
/* Use `Class` instead of `struct objc_class *` */

```
- ###函数名隐藏在结构体里，以函数指针成员的形式存储
- ###编译后，只留了下地址，去掉名字和参数表


#以前：
```
@interface XXXXX : NSObject

+ (BOOL)isVerified;

@end
```

#现在
```
typedef struct _acl_entry
{
    BOOL (*isVerified)(void);

} XX_acl_entry; //XXUtil_t


...
+ (XX_acl_entry *)sharedUtil;

```

```

static BOOL _isVerified(void)
{
    //bala bala ...
    return YES;
}

static XX_acl_entry * util = NULL;
@implementation _XXX

+(XX_acl_entry *)sharedUtil
{
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        util = malloc(sizeof(XX_acl_entry));
        util->isVerified = _isVerified;
    });
    return util;
}

+ (void)destroy
{
    util ? free(util): 0;
    util = NULL;
}
@end
```

    XXUtil->isVerified();  //函数调用

> 还有谁能反汇编？？我就说：还有谁！！！
