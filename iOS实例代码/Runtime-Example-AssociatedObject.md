
	///-----------------------------------------------------------------------------
	/// Associated Objects
	/// http://nshipster.com/associated-objects
	///-----------------------------------------------------------------------------
	
	/**
	 Associated Objects—or Associative References, as they were originally known—are a feature of the Objective-C 2.0 runtime, introduced in OS X Snow Leopard (available in iOS 4). The term refers to the following three C functions declared in <objc/runtime.h>, which allow objects to associate arbitrary values for keys at runtime:
	 
	 objc_setAssociatedObject
	 objc_getAssociatedObject
	 objc_removeAssociatedObjects
	 Why is this useful? It allows developers to add custom properties to existing classes in categories, which is an otherwise notable shortcoming for Objective-C.
	 
	 http://nshipster.com/associated-objects
	 */
	@interface NSObject (AssociatedObject)
	@property (nonatomic, strong) id associatedObject;
	@end
	
	
	@implementation NSObject (AssociatedObject)
	@dynamic associatedObject;
	
	/**
	 It is often recommended that they key be a static char—or better yet, the
	 pointer to one. Basically, an arbitrary value that is guaranteed to be constant,
	 unique, and scoped for use within getters and setters:
	 
	 static char kAssociatedObjectKey;
	 
	 objc_getAssociatedObject(self, &kAssociatedObjectKey);
	 
	 http://nshipster.com/associated-objects
	 */
	- (void)setAssociatedObject:(id)object {
	    objc_setAssociatedObject(self, @selector(associatedObject), object, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
	}
	
	- (id)associatedObject {
	    return objc_getAssociatedObject(self, @selector(associatedObject));
	}
	
	@end
	
	///-----------------------------------------------------------------------------
	/// https://github.com/ltebean/LTNavigationBar
	///-----------------------------------------------------------------------------
	@implementation UINavigationBar (Awesome)
	
	static char overlayKey;
	
	- (UIView *)overlay
	{
	    return objc_getAssociatedObject(self, &overlayKey);
	}
	
	- (void)setOverlay:(UIView *)overlay
	{
	    objc_setAssociatedObject(self, &overlayKey, overlay, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
	}
	
	- (void)lt_setBackgroundColor:(UIColor *)backgroundColor
	{
	    if (!self.overlay) {
	        [self setBackgroundImage:[UIImage new] forBarMetrics:UIBarMetricsDefault];
	        self.overlay = [[UIView alloc] initWithFrame:CGRectMake(0, 0, CGRectGetWidth(self.bounds), CGRectGetHeight(self.bounds) + 20)];
	        self.overlay.userInteractionEnabled = NO;
	        self.overlay.autoresizingMask = UIViewAutoresizingFlexibleWidth;    // Should not set `UIViewAutoresizingFlexibleHeight`
	        [[self.subviews firstObject] insertSubview:self.overlay atIndex:0];
	    }
	    self.overlay.backgroundColor = backgroundColor;
	}
	
	@end
	
	///-----------------------------------------------------------------------------
	/// https://github.com/forkingdog/FDFullscreenPopGesture
	///-----------------------------------------------------------------------------
	@interface UINavigationController (FDFullscreenPopGesture)
	
	@property (nonatomic, strong, readonly) UIPanGestureRecognizer *fd_fullscreenPopGestureRecognizer;
	
	@property (nonatomic, assign) BOOL fd_viewControllerBasedNavigationBarAppearanceEnabled;
	
	@end
	
	@implementation UINavigationController (FDFullscreenPopGesture)
	
	- (UIPanGestureRecognizer *)fd_fullscreenPopGestureRecognizer
	{
	    UIPanGestureRecognizer *panGestureRecognizer = objc_getAssociatedObject(self, _cmd);
	
	    if (!panGestureRecognizer) {
	        panGestureRecognizer = [[UIPanGestureRecognizer alloc] init];
	        panGestureRecognizer.maximumNumberOfTouches = 1;
	        
	        objc_setAssociatedObject(self, _cmd, panGestureRecognizer, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
	    }
	    return panGestureRecognizer;
	}
	
	- (BOOL)fd_viewControllerBasedNavigationBarAppearanceEnabled
	{
	    NSNumber *number = objc_getAssociatedObject(self, _cmd);
	    if (number) {
	        return number.boolValue;
	    }
	    self.fd_viewControllerBasedNavigationBarAppearanceEnabled = YES;
	    return YES;
	}
	
	- (void)setFd_viewControllerBasedNavigationBarAppearanceEnabled:(BOOL)enabled
	{
	    SEL key = @selector(fd_viewControllerBasedNavigationBarAppearanceEnabled);
	    objc_setAssociatedObject(self, key, @(enabled), OBJC_ASSOCIATION_RETAIN_NONATOMIC);
	}
	
	@end
	
	///-----------------------------------------------------------------------------
	/// https://github.com/forkingdog/FDFullscreenPopGesture
	///-----------------------------------------------------------------------------
	@interface UIViewController (FDFullscreenPopGesture)
	
	@property (nonatomic, assign) BOOL fd_interactivePopDisabled;
	
	@property (nonatomic, assign) BOOL fd_prefersNavigationBarHidden;
	
	@property (nonatomic, assign) CGFloat fd_interactivePopMaxAllowedInitialDistanceToLeftEdge;
	
	@end
	
	@implementation UIViewController (FDFullscreenPopGesture)
	
	- (BOOL)fd_interactivePopDisabled
	{
	    return [objc_getAssociatedObject(self, _cmd) boolValue];
	}
	
	- (void)setFd_interactivePopDisabled:(BOOL)disabled
	{
	    objc_setAssociatedObject(self, @selector(fd_interactivePopDisabled), @(disabled), OBJC_ASSOCIATION_RETAIN_NONATOMIC);
	}
	
	- (BOOL)fd_prefersNavigationBarHidden
	{
	    return [objc_getAssociatedObject(self, _cmd) boolValue];
	}
	
	- (void)setFd_prefersNavigationBarHidden:(BOOL)hidden
	{
	    objc_setAssociatedObject(self, @selector(fd_prefersNavigationBarHidden), @(hidden), OBJC_ASSOCIATION_RETAIN_NONATOMIC);
	}
	
	
	- (CGFloat)fd_interactivePopMaxAllowedInitialDistanceToLeftEdge
	{
	#if CGFLOAT_IS_DOUBLE
	    return [objc_getAssociatedObject(self, _cmd) doubleValue];
	#else
	    return [objc_getAssociatedObject(self, _cmd) floatValue];
	#endif
	}
	
	- (void)setFd_interactivePopMaxAllowedInitialDistanceToLeftEdge:(CGFloat)distance
	{
	    SEL key = @selector(fd_interactivePopMaxAllowedInitialDistanceToLeftEdge);
	    objc_setAssociatedObject(self, key, @(MAX(0, distance)), OBJC_ASSOCIATION_RETAIN_NONATOMIC);
	}
	
	@end
	
	///-----------------------------------------------------------------------------
	/// http://www.cnblogs.com/tangbinblog/p/3944316.html
	/// category使用 objc_setAssociatedObject/objc_getAssociatedObject 实现添加属性
	///-----------------------------------------------------------------------------
	
	@interface NSObject (CategoryWithProperty)
	
	@property (nonatomic, strong) NSObject *property;
	
	@end
	
	@implementation NSObject (CategoryWithProperty)
	
	- (NSObject *)property {
	    return objc_getAssociatedObject(self, @selector(property));
	}
	
	- (void)setProperty:(NSObject *)value {
	    objc_setAssociatedObject(self, @selector(property), value, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
	}
	
	@end
