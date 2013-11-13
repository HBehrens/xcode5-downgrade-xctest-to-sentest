# Downgrading from `XCTest` to `SenTest`

After struggling with `xctool` https://github.com/facebook/xctool/issues/224 for quite some time I decided to change my Xcode 5 project from `XCTest` back to `SenTest`. This repo repeats the necessary steps on a minimal iOS project.

My goal is to be able to run tests from
 
- Xcode 5.0.2,
- the shell with `xctool` http://github.com/facebook/xctool , and
- AppCode 2.5.1 

## Verify Tests run properly

Starting with Version 5, Xcode automatically creates unit tests based on `XCTest`. Checkout 88da09ef3e383c2ab14023b206206d28e7de9136 to see that this setup works flawlessly on

- Xcode, hit `CMD+U` to execute tests. The single unit-test from the template fails as expected.
- xctool, run `xctool -project xcode503-xctest.xcodeproj -scheme xcode503-xctest test`
- AppCode, select the test case, choose `Run`

## Switch to SenTest

To be able to verify the differences I created another test project `xcode463-sentest` (not part of this repo) to compare its setup against the project of this repository. That reference project uses SenTest and works perfectly fine in all three tools. As you can see in 027f5524a9b5e8d572f3df5d65a205b74728c3e8 I

- copied Xcode 4's test case from the template over to the project of this Repository,
- changed the test target's bundle suffix to `octest`, and
- added the `SenTest.framework` to the build phase

to align these two project.

To avoid any irritations on any of the aforementioned tools, I also deleted the previous `XCTest` test case as you can see in d8f4ae5f9a6b086a0277f65995150a22e6e17859

I end up with a single `SenTest` test case on my project.

### Note
In your real-world scenario you would change your tests' base class from `XCTestCase` to `SenTestCase` and adjust the `XCT*` asserts to ´ST*` as needed.

### Results

Now, running this `SenTest` test on this project you'll see that

- Xcode works fine (it might be necessary to klick on the icon next to the method the first time, though)
- xctool works fine
- AppCode fails with an error: "Unable to attach test reporter to test framework or test framework quit unexpectedly"

## Patching `project.pbxproj` for AppCode 2.5.1

Looking carefully at the two `project.pbxproj` of the aforementioned Xcode 4 project (not part of this repo) and the one of this project I discovered a difference in the test target's `productType`. As you can see in 141cc6d70099fea67cd10e972242f708cfecb950 Xcode 5 puts a `com.apple.product-type.bundle.unit-test` into this value while Xcode 4 says `com.apple.product-type.bundle`. Change this value back to what Xcode 4 did in the past, makes tests run on

- Xcode,
- xctool, and
- AppCode

I will file a bug and update this project once there's an update.
