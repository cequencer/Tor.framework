# Tor.framework

[![Carthage Compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)

Tor.framework is the easiest way to embed Tor in your iOS application. The API is *not* stable yet, and subject to change.

Currently, the framework compiles in static versions of `tor`, `libevent` and `openssl`:

|          |         |
|:-------- | -------:|
| tor      | 0.2.7.6 |
| libevent | 2.0.22  |
| OpenSSL  | 1.0.2f  |

## Requirements

- iOS 8.0 or later
- Xcode 7.0 or later

## Installation

Embedded frameworks require a minimum deployment target of iOS 8 or OS X Mavericks (10.9).

### Carthage

To integrate Tor into your Xcode project using Carthage, specify it in your `Cartfile`:

```ogdl
github "iCepa/Tor.framework" "e30f2a4974a1464ecb4c53a1fce000e4206633ed"
```

## Usage

Starting an instance of Tor involves using three classes: `TORThread`, `TORConfiguration` and `TORController`.

Here is an example of integrating Tor with `NSURLSession`:

```objc
TORConfiguration *configuration = [TORConfiguration new];
configuration.cookieAuthentication = YES;
configuration.dataDirectory = NSTemporaryDirectory();
configuration.controlSocket = [configuration.dataDirectory stringByAppendingPathComponent:@"control_port"];
configuration.arguments = @[@"--ignore-missing-torrc"];

TORThread *thread = [[TORThread alloc] initWithConfiguration:configuration];
[thread start];

NSString *cookiePath = [configuration.dataDirectory stringByAppendingPathComponent:@"control_auth_cookie"];
NSData *cookie = [NSData dataWithContentsOfFile:cookiePath];
TORController *controller = [[TORController alloc] initWithControlSocketPath:configuration.controlSocket];
[controller authenticateWithData:cookie completion:^(BOOL success, NSError *error) {
    if (!success)
        return;
    
    [controller getSessionConfiguration:^(NSURLSessionConfiguration *configuration) {
        NSURLSession *session = [NSURLSession sessionWithConfiguration:configuration];
        ...
    }];
}];
```
