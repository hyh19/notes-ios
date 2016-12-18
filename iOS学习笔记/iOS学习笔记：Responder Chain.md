
原文链接：[Event Delivery: The Responder Chain][1]

#### Hit-Testing Returns the View Where a Touch Occurred

- iOS uses hit-testing to find the view that is under a touch. Hit-testing involves checking whether a touch is within the bounds of any relevant view objects. If it is, it recursively checks all of that view’s subviews. The lowest view in the view hierarchy that contains the touch point becomes the hit-test view. After iOS determines the hit-test view, it passes the touch event to that view for handling.

- The hit-test view is given the first opportunity to handle a touch event. If the hit-test view cannot handle an event, the event travels up that view’s chain of responders as described in [The Responder Chain Is Made Up of Responder Objects][2] until the system finds an object that can handle it.

#### The Responder Chain Is Made Up of Responder Objects

- Many types of events rely on a responder chain for event delivery. The responder chain is a series of linked responder objects. It starts with the first responder and ends with the application object. If the first responder cannot handle an event, it forwards the event to the next responder in the responder chain.

- A responder object is an object that can respond to and handle events. The UIResponder class is the base class for all responder objects, and it defines the programmatic interface not only for event handling but also for common responder behavior. Instances of the UIApplication, UIViewController, and UIView classes are responders, which means that all views and most key controller objects are responders. Note that Core Animation layers are not responders.

- Events are not the only objects that rely on the responder chain. The responder chain is used in all of the following:
	- Touch events
	- Motion events
	- Remote control events
	- Action messages
	- Editing-menu messages
	- Text editing

#### The Responder Chain Follows a Specific Delivery Path

- If the initial object—either the hit-test view or the first responder—doesn’t handle an event, UIKit passes the event to the next responder in the chain. Each responder decides whether it wants to handle the event or pass it along to its own next responder by calling the nextResponder method.This process continues until a responder object either handles the event or there are no more responders.

[1]:	https://developer.apple.com/library/content/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/event_delivery_responder_chain/event_delivery_responder_chain.html#//apple_ref/doc/uid/TP40009541-CH4-SW2
[2]:	https://developer.apple.com/library/content/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/event_delivery_responder_chain/event_delivery_responder_chain.html#//apple_ref/doc/uid/TP40009541-CH4-SW1