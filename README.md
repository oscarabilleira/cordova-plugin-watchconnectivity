# Cordova Watch Connectivity Plugin

Simple plugin that establishes iOS Watch Connectivity session with Watch OS 2 and helps exchange of messages between an iPhone hybrid application and its iWatch application.

## Installation


####vPrep

Install npm 'xcode' package because our hooks need it: npm install -g xcode


### XCode

File > New > Target > Apple Watch: Language ObjC, Select Glance and Notifications > Finish > Activate




### With cordova-cli

If you are using [cordova-cli](https://github.com/apache/cordova-cli), install
with:

    cordova plugin add https://github.com/gori78/cordova-plugin-watchconnectivity.git
     cordova prepare
     
     



### With plugman

With a plain [plugman](https://github.com/apache/cordova-plugman), you should be able to install with something like:

    plugman --platform <ios> --project <directory> --plugin https://github.com/DVenkatesh/cordova-plugin-watchconnectivity.git
    
    
    
 ###  XCode

Set CFBundleVersion (Bundle Version) and CFBundleShortVersionString (Bundle versions string, short) of all targets to the same value (use XCode's search feature and change all 3 .plist values)

At this point your builds should succeed

    

## Use from Javascript
Edit `www/js/index.js` and add the following code inside `onDeviceReady`
```js
    var didReceiveMessage = function(message){
        var obj = JSON.parse(message);
        alert(obj.message);
    }
    var msgSendSuccess = function() {
        alert("Message send success");
    }
    var failure = function() {
        alert("Error");
    }
    var success = function() {
        sswc.messageReceiver(didReceiveMessage, failure);
        sswc.sendMessage("Message from phone", msgSendSuccess, failure);
    }
    sswc.init(success, failure);
```
## Use from iWatch extension
### Objective-C
```objective-c
// Setup and activate session in awakeWithContext or willActivate
if ([WCSession isSupported]) {
    WCSession *session = [WCSession defaultSession];
    session.delegate = self;
    [session activateSession];
}
// Implement didReceiveMessage WatchConnectivity handler/callback to receive incoming messages
- (void)session:(WCSession *)session didReceiveMessage:(NSDictionary<NSString *, id> *)message replyHandler:(void(^)(NSDictionary<NSString *, id> *replyMessage))replyHandler {
    NSString *message = [message objectForKey:@"message"];
    NSLog(@"%@",message);
    [self sendMessage:@"Message from iWatch"];
}
// Send message
- (void)sendMessage:(NSString*)message {
    NSDictionary *messageDictionary = [[NSDictionary alloc] initWithObjects:@[message] forKeys:@[@"message"]];
    [[WCSession defaultSession] sendMessage:messageDictionary
                               replyHandler:^(NSDictionary *reply) {
                                   NSLog(@"Send message success");
                               }
                               errorHandler:^(NSError *error) {
                                   NSLog(@"Send message failed");
                               }
     ];
}
```
### Swift
```swift
// Setup and activate session in awakeWithContext or willActivate
if WCSession.isSupported() {
    let session = WCSession.defaultSession()
    session.delegate = self
    session.activateSession()
}
// Implement didReceiveMessage WatchConnectivity handler/callback to receive incoming messages
func session(session: WCSession, didReceiveMessage message: [String : AnyObject], replyHandler: ([String : AnyObject]) -> Void) {
    let message = message["message"] as? String
    print(message)
    self.sendMessage("Message from iWatch")
}
// Send message
func sendMessage:(message: String) -> Void{
    let message = ["message": message]
    WCSession.defaultSession()!.sendMessage(["message": message], 
                                replyHandler: { (response) -> Void in
                                    print("Send message success")
                                }, errorHandler: { (error) -> Void in
                                    print("Send message failed")
                                })
     
}
```

## Credits
Written by [Venkatesh D](https://www.linkedin.com/in/dvenkateshd) and [Vagish M M](http:///)

## More Info
TODO: The plugin is very simple and short without much error handling. This is developed for an immediate need and shall be upgraded to support other platforms with error handling and improved design. 

For more information on setting up Cordova see [the documentation](http://cordova.apache.org/docs/en/4.0.0/guide_cli_index.md.html#The%20Command-Line%20Interface)

For more info on plugins see the [Plugin Development Guide](http://cordova.apache.org/docs/en/4.0.0/guide_hybrid_plugins_index.md.html#Plugin%20Development%20Guide)
