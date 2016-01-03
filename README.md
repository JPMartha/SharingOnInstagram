# EggsBenedict [![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)

__EggsBenedict__ is a library for sharing picture on Instagram in Swift.

![Options Menu](https://github.com/JPMartha/EggsBenedict/wiki/images/EggsBenedict01.png)
![Instagram app](https://github.com/JPMartha/EggsBenedict/wiki/images/EggsBenedict02.png)


This library is following Instagram's sharing flow.

> __Instagram's documentation__

> - [Document Interaction](https://www.instagram.com/developer/mobile-sharing/iphone-hooks/#document-interaction)

If the custom URL `instagram://` can be opened direct users on the iOS device, the flow is as follows.

1. Save temporary file named  `jpmarthaeggsbenedict` (JPEG format) in "tmp/" directory using the filename extension `.ig` or `.igo`.
2. Display an options menu for copying to Instagram.
3. If users tap the "Copy to Instagram" icon, open Instagram app with its filter screen.

  > The image is preloaded and sized appropriately for Instagram. For best results, Instagram prefers opening a JPEG that is 640px by 640px square. If the image is larger, it will be resized dynamically.

#### _\- By the way, why was it named "EggsBenedict"?_

The reason is because I like Eggs Benedict 😋

## Availability

- Swift 2.1
- Xcode 7.2
- iOS 8.0 and later

## Adding EggsBenedict.framework to your project

This library can be used with [Carthage](https://github.com/Carthage/Carthage).

If you don't install Carthage, please install it.

1. Create a [Cartfile](https://github.com/Carthage/Carthage/blob/master/Documentation/Artifacts.md#cartfile) and add `github "JPMartha/EggsBenedict" ~> 0.9.3`.
2. Run `carthage update --platform iOS`.
3. On your application targets’ “Build Phases” settings tab, in the “Link Binary With Libraries” section, click the “+” icon and add `EggsBenedict.framework` from the Carthage/Build folder on disk.
4. On your application targets’ “Build Phases” settings tab, click the “+” icon and choose “New Run Script Phase”. Create a Run Script with the following contents: 
  ```
  /usr/local/bin/carthage copy-frameworks
  ```
  and add the paths to EggsBenedict.framework:
  ```
  $(SRCROOT)/Carthage/Build/iOS/EggsBenedict.framework`
  ```
  
  This script works around an [App Store submission bug](http://www.openradar.me/radar?id=6409498411401216) triggered by universal binaries and ensures that necessary bitcode-related files are copied when archiving.

## Getting started

1. On your application Info.plist, add `LSApplicationQueriesSchemes` key.

  Key                                           |Type    |Value
  ------------------------------------|--------|-----------
  LSApplicationQueriesSchemes | Array | instagram

2. Create an instance of `SharingFlow` class with `SharingFlowType` enumeration.

  ```swift
  let sharingFlow = SharingFlow(type: .IGOExclusivegram)
  ```
  
  #### SharingFlowType enumeration

  According to the [Instagram's documentation](https://www.instagram.com/developer/mobile-sharing/iphone-hooks/#document-interaction), you can use two ways in Instagram's sharing flow.

  - `IGPhoto`
  
    Show Instagram plus any other public/jpeg-conforming apps in the application list.

  - `IGOExclusivegram` (preferred)
  
    Show only Instagram in the application list. (Actually, some apps are shown.)

3. Call `sendImage` method with two parameters.

  ```swift
  sharingFlow.sendImage(imageView.image, view: view) { (result) -> Void in
      // Handling Errors
  }
  ```
  
    - Handling Errors Example
    
    ```swift
    switch result {
    case .Success:
        print("Success!")
    case let .Failure(error as SharingFlowError):
        print(error.debugDescription)
    case let .Failure(error as NSError):
        print(error.debugDescription)
    case let .Failure(error):
        print(error)
    }
    ```
  
  #### Parameters
  
  - image: `UIImage!`
  
    The image for sending to Instagram app.
    
  - view: `UIView!`
  
    The view from which to display the options menu.
    
  - completion: `((result: Result<ErrorType>) -> Void)?`
  
    The block to execute after the sending image finishes. You may specify nil for this parameter.

## Remove temporary image

To remove temporary image in "tmp/" directory, call `removeTemporaryImage` method of the created instance.

```swift
do {
    try sharingFlow.removeTemporaryImage()
} catch {
    // Handling Errors
}
```

  - Handling Errors Example
  
  ```swift
  do {
      try sharingFlow.removeTemporaryImage()
  } catch let sharingFlowError as SharingFlowError {
      print("Error: \(sharingFlowError.debugDescription)")
  } catch let error as NSError {
      print("Error: \(error.debugDescription)")
  }
  ```

## License

__EggsBenedict__ is released under the [MIT License](LICENSE).
