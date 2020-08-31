# App Tracking Transparency Guides

Starting in iOS 14, IDFA will be unavailable until an app calls the [App Tracking Transparency](https://developer.apple.com/documentation/apptrackingtransparency) framework to present the app-tracking authorization request to the end user. If an app does not present this request, the IDFA will automatically be zeroed out which may lead to a significant loss in ad revenue.

This guide outlines the changes needed for iOS 14 support.

## Prerequisites

Add [GMA](https://developers.google.com/admob/ios/download/) to Podfile.
Google Mobile Ads SDK 7.64.0 or higher

```bash
'Google-Mobile-Ads-SDK', '~> 7.64.0'
```

## Add info to Info.plist

```html
<key>NSUserTrackingUsageTitle</key>
<string>"$(PRODUCT_NAME)" would like permission to track you cross apps and website owned by other companies</string>
<key>NSUserTrackingUsageDescription</key>
<string>This identifier will be used to deliver personalized ads to you.</string>
```

<b>'NSUserTrackingUsageTitle'</b> and <b>'NSUserTrackingUsageDescription'</b> will be used for the tracking ads dialog and can its

## Usage

```swift
import AppTrackingTransparency
import AdSupport
import UIKit

class Tracking {
    static func requestIDFA(parent: UIViewController) {
        if #available(iOS 14, *) {
            ATTrackingManager.requestTrackingAuthorization(completionHandler: { status in
                switch status {
                case .denied:
                    print("Tracking denied. Show dialog setting now")
                    showDialogGoToTrackingSetting(parent: parent)
                case .notDetermined:
                    print("Tracking notDetermined")
                case .restricted:
                    print("Tracking restricted")
                case .authorized:
                    print("Tracking authorized")
                @unknown default:
                    break
                }
            })
        }
    }
    private static func showDialogGoToTrackingSetting(parent: UIViewController) {
        let title = Bundle.main.object(forInfoDictionaryKey: "NSUserTrackingUsageTitle") as? String ?? ""
        let message = Bundle.main.object(forInfoDictionaryKey: "NSUserTrackingUsageDescription") as? String ?? ""
        
        let alertVC = UIAlertController(title: title, message: message, preferredStyle: .alert)
        alertVC.addAction(UIAlertAction(title: "Remind later", style: .default, handler: { (alertController) -> Void in
            
        }))
        alertVC.addAction(UIAlertAction(title: "Go to setting", style: .default, handler: { (alertController) -> Void in
            UIApplication.shared.open(URL(string: UIApplication.openSettingsURLString)!, options: [:], completionHandler: nil)
        }))
        parent.present(alertVC, animated: true, completion: nil)
    }
}
```

Last call function in Main Viewcontroller
```swift
override func viewDidLoad() {
    super.viewDidLoad()
    Tracking.requestIDFA(parent: self)
}
```

## License
[MIT](https://choosealicense.com/licenses/mit/)
