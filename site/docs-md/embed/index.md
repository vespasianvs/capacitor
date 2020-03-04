# Embedding Capacitor into Existing Native Apps

Capacitor was built from the ground up to support being embedded directly into existing iOS and Android native apps.

Many teams are exploring adding web content to an existing native app for developer and feature velocity reasons, or to transition their app over to web technology over time.

## Concepts

When Capacitor is embedded in an existing native app, it becomes a `ViewController` (iOS) or `View` (Android) that manages an embedded WebView while providing a web-to-native bridge to expose native functionality to the Web View.

That means you can add new screens or views in your app that display this managed Capacitor view, which will then load
web content in that screen with full access to your Capacitor plugins.

Additionally, multiple instances of Capacitor can run in parallel, each displaying different content.

## iOS Embedding

### Installation

To embed Capacitor in an existing native app, first install the `@capacitor/core` npm package and copy the `native-bridge.js` file over, then install the Capacitor pod.

```
npm install @capacitor/core
mkdir MyApp/public/
cp node_modules/\@capacitor/core/native-bridge.js MyApp/public/
```

The `native-bridge.js` file is the "glue" that helps the WebView communicate with the native layer.

Next, install the CocoaPod for Capacitor:

```ruby
use_modular_headers!
use_frameworks!

target 'CapacitorEmbedTest' do
  pod 'Capacitor', '~> VERSION'
end
```

Where `VERSION` is the latest version of Capacitor.

Next, ceate a stub JSON file in your app project folder named capacitor.config.json. It should have the contents "{}" without quotes.

Finally, create an empty file named `config.xml` in the app project folder.

### Create index.html

For the content you'd like to load, make sure it exists in the project. The default path is `public/index.html`, so if you would like to use that, then create and add to your project a folder called `public` and then an HTML file that will be the root of your capacitor app in `index.html`. Make sure to add these to your main app project in your app workspace.

### Open Xcode Workspace and Add Files

To run your app in Xcode, make sure to open the Xcode _xcworkspace_ file, not the _xcodeproj_ file to correctly use the installed Pod.

Make sure to Add the `capacitor.config.json`, `config.xml`, and `public/index.html` files to the main app sub-project.

### Usage

Then, to use a Capacitor View Controller to display web content, use `CAPBridgeViewController` anywhere you want to have a Capacitor-powered Web View, for example as the view controller for a tab in `UITabBarController`.

### Configuration

Chances are, you'll want to display different content for different instances of each Capacitor view. To do this,
set `startDir` on the `CAPBridgeViewController` instance, either manually or using `User Defined Runtime Attributes` in
the storyboard tool.

`startDir` is relative to the `public` resource folder, so a value of `tab1` will load `public/tab1/index.html` as the first
page.


## Android Embedding

Capacitor can be installed into any existing native Android app. There are two ways to use Capacitor in this way: as an Activity or as a Fragment.

The Activity approach is good for full screen experiences, while the Fragment is perfect for use cases like bottom navigation and having a separate Capacitor instance for each tab.

### Installation

The installation process for Capacitor involves installing a node module to grab the current version of `native-bridge.js`, which Capacitor users to communicate with the native layer, along with installing the native Android `capacitor-android` library in your app.

```
npm install @capacitor/core
mkdir MyApp/public/
cp node_modules/\@capacitor/core/native-bridge.js MyApp/public/
```

In your root `build.gradle` file, under `allprojects`, add the following maven repository:

```gradle
allprojects {
  maven {
      url  "https://dl.bintray.com/ionic-team/capacitor" 
  }
}
```

Next, in the `build.gradle` file for your app (note: this is not the same gradle file as above), add the `capacitor-android` dependency:

```gradle
dependencies {
  implementation 'ionic-team:capacitor-android:VERSION'
}
```

Where `VERSION` is the version you wish to install.


### Usage

To embed Capacitor in your existing Android app, you can either start a new fullscreen Capacitor activity or embed Capacitor as a Fragment.

#### Activity usage

To start a fullscreen Capacitor activity at any moment in the app (such as a button click), call `startActivity()` with com.getcapacitor.BridgeActivity`.

We recommend this use case for apps that are okay start a new fullscreen activity. However, most apps will likely prefer using the `Fragment` approach (see below) which embeds the Capacitor instance in the existing Activity.

#### Fragment usage
