# cocoapods-user-defined-build-types
![Latest Version](https://img.shields.io/badge/Latest_supported_CocoaPods-1.9.1-gray.svg)

Allow CocoaPods to mix dynamic/static libaries/frameworks.

This plugin allows for a Podfile to specify how each Pod (or multiple Pods) should be built (ex. *as a dynamic framework*).


## Installation
```Bash
$ gem install cocoapods-user-defined-build-types
```

## Usage
```C
plugin 'cocoapods-user-defined-build-types'

enable_user_defined_build_types!

target "CoffeeApp" do
    pod 'Alamofire'
    pod "SwiftyJSON", :build_type => :dynamic_framework
```
- Add `plugin 'cocoapods-user-defined-build-types'` to the top of your Podfile
- Add the `enable_user_defined_build_types!` directive to the top of your Podfile (removing `use_frameworks!` if present)
- Add a `build_type` option to one or more Pods to direct how they're built (ex. `:build_type => :dynamic_framework`)
- `pod install`

Based on this configuration, CocoaPods will build your project such that `Alamofire` stays a static library (CocoaPods default) and is part of the umbrella `Pods-CoffeeApp` static library generated by CocoaPods. `SwiftyJSON` will become a dynamic framework and dynamically linked in CoffeeApp.

| SUPPORTED BUILD TYPES |
| --- |
| `dynamic_library` |
| `dynamic_framework` |
| `static_library` |
| `static_framework` |

## Why
Cocoapod's `use_frameworks!` directive makes **all** integrated Pods build as dynamic frameworks.

This can cause issues with certain Pods. You may want some pods to be static libraries and a single Pod to a dynamic framework. CocoaPods currently does not support this. The `cocoapods-user-defined-build-types` plugin allows for build types to be changed on a Pod-by-Pod basis, otherwise defaulting to CocoaPods default build type (static library). 

This plugin was specifically built for React Native projects to be able to incorporate dynamic Swift Pods without needing to change other Pods.

## Verbose Logging
Having issues? Try enabling the plugin's verbose logging from the Podfile:
```C
plugin 'cocoapods-user-defined-build-types', {
  verbose: true
}

...
```

For even more detailed logging, the development flag can be set in your terminal env: `export CP_DEV=1`.

## How
By overriding `Pod::Podfile::TargetDefinition`'s `build_type` function (from cocoapods-core) to return the specifed linking (static/dynamic) and packing (library/framework), we can change how Cococpods builts specific dependencies. Currently in core, there is support for multiple build type but the use_frameworks! directive is the only way to enable framework builds, and it is an all-or-nothing approach.

## License
Available under the MIT license. See the LICENSE file for more info.
