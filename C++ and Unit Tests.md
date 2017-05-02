# Calling C++ Functions from Swift Classes and Unit Tests

In this tutorial, we will see how to call C++ functions from within Swift classes and unit tests. To start, create a single view application and make sure you include unit tests. Arguably the best way to call C++ from Swift is to wrap the C++ code with an Objective-C++ class. To create one, simply add an Objective-C file to your project, and make sure you select the file type to be an empty file. Call it whatever you want, but be sure to select both your project *and* your unit tests as targets. When prompted, do create a bridging header file: Xcode will actually create two, one for your project, and another for your unit tests.

Next, add another file to your project, this time a header file. Give it the same name you gave your Objective-C file, and target both your project and your unit tests. In order to transform an Objective-C file into an Objective-C++ file, you need to rename your `.m` file extension to `.mm`. After that, you will be able to `#include` C++ headers in those files. After renaming, go ahead and create another header file and call it anything you want. With C++ code in Xcode, you can choose to have an implementation file, with extension `.cpp`, or not. We will first explore how to write C++ code with the header file alone. In order to do that, the last step is to rename the last header file you created with an extension `.hpp`.

## Writing C++ code directly on the header file

Open your `.hpp` file and you will see something like:

```cpp
#ifndef Cpp_h
#define Cpp_h
/* insert your code here */
#endif
```

We will now create a simple C++ class that contains a public constructor and destructor. So go ahead an write, between the `#define` and `#endif` tags:

```cpp
class Cpp {
    
    int result;
    
public:
    
    Cpp() { /* constructor */ }
    
    ~Cpp() { /* destructor */ }
    
};
```

Next, add a public method that multiplies two integer without using multiplication:

```cpp
int product(int a, int b) {
    if (a == 0 || b == 0) return 0;
    if (b == 1) return a;
    return a + product(a, b - 1);
}
```

## Wrapping C++ code with an Objective-C++ class

Now open your Objective-C++ header file and replace everything with:

```objective-c
#import <Foundation/Foundation.h>

@interface ObjCpp : NSObject

-(int) product: (int) a : (int) b;

@end
```

We need now to implement this class and method we just declared inside the `.mm` file. So open that and replace everything with:

```objective-c
#import "ObjCpp.h"
#include "Cpp.hpp"

@implementation ObjCpp : NSObject

-(int) product: (int)a : (int)b {
    Cpp cppClass = Cpp();
    return cppClass.product(a, b);
}

@end
```

Note that we are using `#import` to refer to the Objective-C header, but using C++ terminology `#include` to access the C++ class. The method we just defined above is simply a wrapper to our C++ code.

## Calling C++ from Swift classes and unit tests

Open both your bridging header files and put:

```objective-c
#import "ObjCpp.h"
```

You can now access any C++ methods you choose to expose to the Objective-C++ class from within Swift classes and unit tests. Open your `ViewController.swift` and put the code below inside the `viewDidLoad()` method:

```swift
let objCppClass = ObjCpp()
print(objCppClass.product(3, 5))
```

The console should print out `15`. We will now call our Objective-C++ class from within a unit test. Go ahead and open your unit test file. Inside the subclass of `XCTestCase`, create a method:

```swift
func testCpp() {
    let objCppClass = ObjCpp()
    XCTAssertEqual(objCppClass.product(3, 5), 15)
}
```

Click on the play button right next to the method above, and it should compile and succeed. That's it, you can now take advantage of the great gains in processing speed you get over Swift out of a non-blocking, low-overhead language such as C++.