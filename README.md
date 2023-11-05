# flutter_in_app_purchase_with_revenue_cat

## Add library in pubspec.yaml

``
purchases_flutter: ^6.2.0
``

## Create project with RevenueCat

## CODE:

### Step - 1: Create api key constants

```dart

//TODO: add the Apple API key for your app from the RevenueCat dashboard: https://app.revenuecat.com
const appleApiKey = 'appl_api_key';

//TODO: add the Google API key for your app from the RevenueCat dashboard: https://app.revenuecat.com
const googleApiKey = 'googl_api_key';

//TODO: add the Amazon API key for your app from the RevenueCat dashboard: https://app.revenuecat.com
const amazonApiKey = 'amazon_api_key';

const entitlementKey = 'pro';

```

### Step - 2: Initialize StoreConfig in main function

```dart

class StoreConfig {
  final Store store;
  final String apiKey;
  static StoreConfig? _instance;

  factory StoreConfig({required Store store, required String apiKey}) {
    _instance ??= StoreConfig._internal(store, apiKey);
    return _instance!;
  }

  StoreConfig._internal(this.store, this.apiKey);

  static StoreConfig get instance {
    return _instance!;
  }

  static bool isForAppleStore() =>
      instance.store == Store.appStore
          || instance.store == Store.macAppStore;

  static bool isForGooglePlay() => instance.store == Store.playStore;

  static bool isForAmazonAppstore() =>
      instance.store == Store.amazon;
}

void main() {
  /// ...
  /// ...
  if (Platform.isIOS || Platform.isMacOS) {
    StoreConfig(
      store: Store.appStore,
      apiKey: appleApiKey,
    );
  } else if (Platform.isAndroid) {
    // Run the app passing --dart-define=AMAZON=true
    const useAmazon = bool.fromEnvironment("amazon");
    StoreConfig(
      store: useAmazon ? Store.amazon : Store.playStore,
      apiKey: useAmazon ? amazonApiKey : googleApiKey,
    );
  }

  ///...
  ///...
}

```

### Step - 3: Initialization PurchaseConfiguration

```dart
void main() async {
  /// ...
  /// ...
  WidgetsFlutterBinding.ensureInitialized();
  await _configureSDK();

  /// ...
  /// ...
}

Future<void> _configureSDK() async {
  await Purchases.setLogLevel(LogLevel.debug);

  PurchasesConfiguration configuration;
  if (StoreConfig.isForAmazonAppstore()) {
    configuration = AmazonConfiguration(StoreConfig.instance.apiKey);
  } else {
    configuration = PurchasesConfiguration(StoreConfig.instance.apiKey);
  }
  await Purchases.configure(configuration);

  await Purchases.enableAdServicesAttributionTokenCollection();
}
```

### Step - 4: 
