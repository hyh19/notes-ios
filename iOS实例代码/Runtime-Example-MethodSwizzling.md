
	///-----------------------------------------------------------------------------
	/// Method Swizzling
	/// http://nshipster.com/method-swizzling
	///-----------------------------------------------------------------------------
	
	#import <objc/runtime.h>
	
	@implementation UIViewController (Tracking)
	
	+ (void)load {
	    static dispatch_once_t onceToken;
	    dispatch_once(&onceToken, ^{
	        Class class = [self class];
	
	        SEL originalSelector = @selector(viewWillAppear:);
	        SEL swizzledSelector = @selector(xxx_viewWillAppear:);
	
	        Method originalMethod = class_getInstanceMethod(class, originalSelector);
	        Method swizzledMethod = class_getInstanceMethod(class, swizzledSelector);
	
	        // When swizzling a class method, use the following:
	        // Class class = object_getClass((id)self);
	        // ...
	        // Method originalMethod = class_getClassMethod(class, originalSelector);
	        // Method swizzledMethod = class_getClassMethod(class, swizzledSelector);
	
	        BOOL didAddMethod =
	            class_addMethod(class,
	                originalSelector,
	                method_getImplementation(swizzledMethod),
	                method_getTypeEncoding(swizzledMethod));
	
	        if (didAddMethod) {
	            class_replaceMethod(class,
	                swizzledSelector,
	                method_getImplementation(originalMethod),
	                method_getTypeEncoding(originalMethod));
	        } else {
	            method_exchangeImplementations(originalMethod, swizzledMethod);
	        }
	    });
	}
	
	#pragma mark - Method Swizzling
	
	- (void)xxx_viewWillAppear:(BOOL)animated {
	    [self xxx_viewWillAppear:animated];
	    NSLog(@"viewWillAppear: %@", self);
	}
	
	@end
	
	
	///-----------------------------------------------------------------------------
	/// https://github.com/forkingdog/FDFullscreenPopGesture
	///-----------------------------------------------------------------------------
	@implementation UIViewController (FDFullscreenPopGesturePrivate)
	
	+ (void)load
	{
	    static dispatch_once_t onceToken;
	    dispatch_once(&onceToken, ^{
	        Class class = [self class];
	        
	        SEL originalSelector = @selector(viewWillAppear:);
	        SEL swizzledSelector = @selector(fd_viewWillAppear:);
	        
	        Method originalMethod = class_getInstanceMethod(class, originalSelector);
	        Method swizzledMethod = class_getInstanceMethod(class, swizzledSelector);
	        
	        BOOL success = class_addMethod(class, originalSelector, method_getImplementation(swizzledMethod), method_getTypeEncoding(swizzledMethod));
	        if (success) {
	            class_replaceMethod(class, swizzledSelector, method_getImplementation(originalMethod), method_getTypeEncoding(originalMethod));
	        } else {
	            method_exchangeImplementations(originalMethod, swizzledMethod);
	        }
	    });
	}
	
	- (void)fd_viewWillAppear:(BOOL)animated
	{
	    // Forward to primary implementation.
	    [self fd_viewWillAppear:animated];
	    
	    if (self.fd_willAppearInjectBlock) {
	        self.fd_willAppearInjectBlock(self, animated);
	    }
	}
	
	@end
	
	///-----------------------------------------------------------------------------
	/// https://github.com/forkingdog/FDFullscreenPopGesture
	///-----------------------------------------------------------------------------
	@implementation UINavigationController (FDFullscreenPopGesture)
	
	+ (void)load
	{
	    // Inject "-pushViewController:animated:"
	    static dispatch_once_t onceToken;
	    dispatch_once(&onceToken, ^{
	        Class class = [self class];
	        
	        SEL originalSelector = @selector(pushViewController:animated:);
	        SEL swizzledSelector = @selector(fd_pushViewController:animated:);
	        
	        Method originalMethod = class_getInstanceMethod(class, originalSelector);
	        Method swizzledMethod = class_getInstanceMethod(class, swizzledSelector);
	        
	        BOOL success = class_addMethod(class, originalSelector, method_getImplementation(swizzledMethod), method_getTypeEncoding(swizzledMethod));
	        if (success) {
	            class_replaceMethod(class, swizzledSelector, method_getImplementation(originalMethod), method_getTypeEncoding(originalMethod));
	        } else {
	            method_exchangeImplementations(originalMethod, swizzledMethod);
	        }
	    });
	}
	
	- (void)fd_pushViewController:(UIViewController *)viewController animated:(BOOL)animated
	{
	    if (![self.interactivePopGestureRecognizer.view.gestureRecognizers containsObject:self.fd_fullscreenPopGestureRecognizer]) {
	        
	        // Add our own gesture recognizer to where the onboard screen edge pan gesture recognizer is attached to.
	        [self.interactivePopGestureRecognizer.view addGestureRecognizer:self.fd_fullscreenPopGestureRecognizer];
	
	        // Forward the gesture events to the private handler of the onboard gesture recognizer.
	        NSArray *internalTargets = [self.interactivePopGestureRecognizer valueForKey:@"targets"];
	        id internalTarget = [internalTargets.firstObject valueForKey:@"target"];
	        SEL internalAction = NSSelectorFromString(@"handleNavigationTransition:");
	        self.fd_fullscreenPopGestureRecognizer.delegate = self.fd_popGestureRecognizerDelegate;
	        [self.fd_fullscreenPopGestureRecognizer addTarget:internalTarget action:internalAction];
	
	        // Disable the onboard gesture recognizer.
	        self.interactivePopGestureRecognizer.enabled = NO;
	    }
	    
	    // Handle perferred navigation bar appearance.
	    [self fd_setupViewControllerBasedNavigationBarAppearanceIfNeeded:viewController];
	    
	    // Forward to primary implementation.
	    if (![self.viewControllers containsObject:viewController]) {
	        [self fd_pushViewController:viewController animated:animated];
	    }
	}
	
	@end
