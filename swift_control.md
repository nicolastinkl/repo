# swift control实现原理

参考文献：
- [https://developer.apple.com/library/ios/documentation/swift](https://developer.apple.com/library/ios/documentation/swift)


以下是具体代码:
```

import Foundation

/*!
swift控件实现原理
*/
protocol TargetAction{
    func performAction()
}

struct TargetActionWrapper<T:AnyObject>:TargetAction {

    weak var target : T?

    var action:(T)->()->()

    func performAction() {

        if let t  = target{
            action(t)() // real return a function ()->()
        }
    }
}


enum ControlsEvents{
    case TouchUpInside
    case ValueChanged
    case TouchDownOutside
}


class TKControl {

    var actions = [ControlsEvents:TargetAction]()

    //settargetaction Target/Action/ControlStatus
    func setTarget<T : AnyObject>(target:T,action:(T)->()->(),controlEvent:ControlsEvents)
    {
        let item = TargetActionWrapper(target: target, action: action)
        actions[controlEvent] = item
    }

    //add events
    func performActionForControlEvent(controlEvent: ControlsEvents) {
        actions[controlEvent]?.performAction()
    }

    //remove events
    func removeTargetForControlEvent(controlEvent: ControlsEvents) {
        actions[controlEvent] = nil
    }

    func log()
    {
       // println("some body jiekuan summary")
    }
}

```
