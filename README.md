# SwiftPMArgumentIssue

This repo contains a simple Swift executable created
with Swift Package Manager. This executable has the 
only purpose to illustrate an issue with Swift Package
Manager taking over arguments intended for the executable
when run with `swift run` from the command line.

Executable code:

```swift
print("Hello, world!")
print("Arguments: \(CommandLine.arguments)")
``` 

Example run:
```
$ swift run
[0/0] Build complete!
Hello, world!
Arguments: [".build/x86_64-apple-macosx/debug/SwiftPMArgumentIssue"]
```

## Argument Collision
The issue is when passing arguments that clash with SwiftPM own
arguments.

### Passing a non-colliding argument

```
$ swift run SwiftPMArgumentIssue --foo bar
[0/0] Build complete!
Hello, world!
Arguments: [".build/x86_64-apple-macosx/debug/SwiftPMArgumentIssue", "--foo", "bar"]
```

Passing any argument used by SwiftPM causes unexpected results.

### Passing `--verbose`
```
$ swift run SwiftPMArgumentIssue --foo bar --verbose
/usr/bin/xcrun --sdk macosx --show-sdk-platform-path
/Applications/Xcode13.2b2.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swiftc -print-target-info
/usr/bin/xcrun --sdk macosx --find xctest
/Applications/Xcode13.2b2.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swiftc -print-target-info
/Applications/Xcode13.2b2.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swift-frontend -frontend -print-target-info -sdk /Applications/Xcode13.2b2.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.1.sdk
/Applications/Xcode13.2b2.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swift-frontend -frontend -print-target-info
/Applications/Xcode13.2b2.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swift-frontend -frontend -emit-supported-features /var/folders/bm/5ty7rqlx5579jr38d16pzy0m0000gr/T/TemporaryDirectory.UoOW68/dummyInput-1.swift
/usr/bin/xcrun --sdk macosx --show-sdk-platform-path
/usr/bin/xcrun vtool -show-build /Applications/Xcode13.2b2.app/Contents/Developer/Platforms/MacOSX.platform/Developer/Library/Frameworks/XCTest.framework/XCTest
/usr/bin/xcrun --sdk macosx --show-sdk-platform-path
/usr/bin/xcrun vtool -show-build /Applications/Xcode13.2b2.app/Contents/Developer/Platforms/MacOSX.platform/Developer/Library/Frameworks/XCTest.framework/XCTest
/usr/bin/xcrun --sdk iphoneos --show-sdk-platform-path
/usr/bin/xcrun vtool -show-build /Applications/Xcode13.2b2.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/Library/Frameworks/XCTest.framework/XCTest
/usr/bin/xcrun --sdk appletvos --show-sdk-platform-path
/usr/bin/xcrun vtool -show-build /Applications/Xcode13.2b2.app/Contents/Developer/Platforms/AppleTVOS.platform/Developer/Library/Frameworks/XCTest.framework/XCTest
/usr/bin/xcrun --sdk watchos --show-sdk-platform-path
/usr/bin/xcrun vtool -show-build /Applications/Xcode13.2b2.app/Contents/Developer/Platforms/WatchOS.platform/Developer/Library/Frameworks/XCTest.framework/XCTest
[0/0] Build complete!
Hello, world!
Arguments: [".build/x86_64-apple-macosx/debug/SwiftPMArgumentIssue", "--foo", "bar"]
```
Note how `--verbose` was used by `swift run` instead of our
executable.


### Passing `-c`:
```
$ swift run SwiftPMArgumentIssue --foo bar -c 200
error: The value '200' is invalid for '-c <configuration>'
Help:  -c <configuration>  Build with configuration
Usage: swift run [<options>] [<executable>] [<arguments> ...]
  See 'run -help' for more information.
```

In this case, `-c` is mistakenly being used by SwiftPM again, 
which expects configuration to be either "debug" or "release". It
is impossible to pass `-c` to our executable being run.

