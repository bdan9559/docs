# V2 Event

## Overview

It's always been possible to track events with the Branch SDK, including installs, opens, purchases, and more. Now we're introducing a new way to track events that make reporting and analytics a whole lot better and easier.

The new v2 events have better tracking and analytics options in the Branch dashboard, and the new event structure makes integration with third party analytics providers quicker and tighter.

We're standardizing the way you track "classes" of events. For example: all events related to a customer purchasing are bucketed into a "purchase" class of data items, and all events related to users interacting with your in-app content are linked to a "content" class of data items.

Below are examples of the kinds of events you'll likely be interested in tracking, and we'll show you the way to track through our SDKs.

## Prerequisites

With event tracking, it's important to track the objects related to the event occurring. Branch provides a class to describe your in-app content items  called the [Branch Universal Object](/pages/apps/ios/#create-content-reference). For any of the events tracked below, make sure to include the universal object(s) involved. For example, if you want to track when someone purchases 3 items, you'd want to create a Branch Universal Object per item, and pass them along when tracking the event.

Refer to the above document to set up Branch Universal Objects.

## Current Compatibility

There are a few products and features unsupported by this new method of tracking events. We clarify what's currently supported and what isn't below.

### Acceptance

Tracking these events will propagate to Ad Networks, like Criteo. For example, if you track the purchase event through Branch, this will map to Criteo's Purchase event.

These events will also have analytics, so you can understand their performance, using the new [People-Based Attribution](/pages/dashboard/people-based-attribution/).

### Limitations

As of now, any calls made through these SDK methods will **not** yet:

- Work with our [referrals system](/pages/viral/referrals/).

## Available Events

Use the table below to quickly find the event you want to track.

| Event Name | Event Category | iOS | Android | API
| :-: | :-: | :-: | :-: | :-:
| Add To Cart | [Commerce Event](#track-commerce-events) | BranchStandardEventAddToCart | BRANCH_STANDARD_EVENT.ADD_TO_CART | ADD_TO_CART
| Add To Wishlist | [Commerce Event](#track-commerce-events) | BranchStandardEventAddToWishlist | BRANCH_STANDARD_EVENT.ADD_TO_WISHLIST | ADD_TO_WISHLIST
| View Cart | [Commerce Event](#track-commerce-events) | BranchStandardEventViewCart | BRANCH_STANDARD_EVENT.VIEW_CART | VIEW_CART
| Initiate Purchase | [Commerce Event](#track-commerce-events) | BranchStandardEventInitiatePurchase | BRANCH_STANDARD_EVENT.INITIATE_PURCHASE | INITIATE_PURCHASE
| Add Payment Info | [Commerce Event](#track-commerce-events) | BranchStandardEventAddPaymentInfo | BRANCH_STANDARD_EVENT.ADD_PAYMENT_INFO | ADD_PAYMENT_INFO
| Purchase | [Commerce Event](#track-commerce-events) | BranchStandardEventPurchase | BRANCH_STANDARD_EVENT.PURCHASE | PURCHASE
| Spend Credits | [Commerce Event](#track-commerce-events) | BranchStandardEventSpendCredits | BRANCH_STANDARD_EVENT.SPEND_CREDITS | SPEND_CREDITS
| Search | [Content Event](#track-content-events) | BranchStandardEventSearch | BRANCH_STANDARD_EVENT.SEARCH | SEARCH
| View Item | [Content Event](#track-content-events) | BranchStandardEventViewItem | BRANCH_STANDARD_EVENT.VIEW_ITEM | VIEW_ITEM
| View Items | [Content Event](#track-content-events) | BranchStandardEventViewItems | BRANCH_STANDARD_EVENT.VIEW_ITEMS | VIEW_ITEMS
| Rate | [Content Event](#track-content-events) | BranchStandardEventRate | BRANCH_STANDARD_EVENT.RATE | RATE
| Share | [Content Event](#track-content-events) | BranchStandardEventShare | BRANCH_STANDARD_EVENT.SHARE | SHARE
| Complete Registration | [Lifecycle Event](#track-lifecycle-events) | BranchStandardEventCompleteRegistration | BRANCH_STANDARD_EVENT.COMPLETE_REGISTRATION | COMPLETE_REGISTRATION
| Complete Tutorial | [Lifecycle Event](#track-lifecycle-events) | BranchStandardEventCompleteTutorial | BRANCH_STANDARD_EVENT.COMPLETE_TUTORIAL | COMPLETE_TUTORIAL
| Achieve Level | [Lifecycle Event](#track-lifecycle-events) | BranchStandardEventAchieveLevel | BRANCH_STANDARD_EVENT.ACHIEVE_LEVEL | ACHIEVE_LEVEL
| Unlock Achievement | [Lifecycle Event](#track-lifecycle-events) | BranchStandardEventUnlockAchievement | BRANCH_STANDARD_EVENT.UNLOCK_ACHIEVEMENT | UNLOCK_ACHIEVEMENT

## Track Commerce Events

Commerce events describe events that relate to a customer interacting with your products and converting by purchasing. These are events like adding payment information, purchasing, viewing products, etc. If you have enabled Branch Universal Ads, these events will automatically map to certain Ad Partners. Start by creating a Branch Universal Object for each product that is associated with the event you're tracking.

From there, add the Branch universal object to the tracked event, and use the right predefined constant. For example, the code snippet below is to track when a user adds to cart, but simply replace that constant with another constant to track a different event.

**A note on currency and exchange rates:**
If you track commerce events without a currency, we assume they are USD. If you track commerce events with a currency other than USD, we will convert the `revenue` specified to USD, using a recent exchange rate.

!!! warning "Third Party Integrations"
    We strongly recommend settings a currency code as it is required for third party integrations, including Facebook and Google Ads.

This allows you to easily visualize revenue on the Dashboard, across many countries and currencies, because all units are USD. The exchange rate is pulled from [openexchangerates.org](https://openexchangerates.org) regularly, and is generally within an hour of the realtime exchange rate. If you view raw Branch events via either Webhooks or Exports, you can see the exchange rate used.

### iOS

- *Swift*

    ```swift
    // Create a BranchUniversalObject with your content data:
    let branchUniversalObject = BranchUniversalObject.init()

    // ...add data to the branchUniversalObject as needed...
    branchUniversalObject.canonicalIdentifier = "item/12345"
    branchUniversalObject.canonicalUrl        = "https://branch.io/item/12345"
    branchUniversalObject.title               = "My Item Title"

    branchUniversalObject.contentMetadata.contentSchema     = .commerceProduct
    branchUniversalObject.contentMetadata.quantity          = 1
    branchUniversalObject.contentMetadata.price             = 23.20
    branchUniversalObject.contentMetadata.currency          = .USD
    branchUniversalObject.contentMetadata.sku               = "1994320302"
    branchUniversalObject.contentMetadata.productName       = "my_product_name1"
    branchUniversalObject.contentMetadata.productBrand      = "my_prod_Brand1"
    branchUniversalObject.contentMetadata.productCategory   = .apparel
    branchUniversalObject.contentMetadata.productVariant    = "XL"
    branchUniversalObject.contentMetadata.condition         = .new

    // Create a BranchEvent:
    let event = BranchEvent.standardEvent(.purchase)

    // Add the BranchUniversalObjects with the content:
    event.contentItems     = [ branchUniversalObject ]

    // Add relevant event data:
    event.transactionID    = "12344555"
    event.currency         = .USD
    event.revenue          = 1.5
    event.shipping         = 10.2
    event.tax              = 12.3
    event.coupon           = "test_coupon"
    event.affiliation      = "test_affiliation"
    event.eventDescription = "Event_description"
    event.searchQuery      = "item 123"
    event.customData       = [
        "Custom_Event_Property_Key1": "Custom_Event_Property_val1",
        "Custom_Event_Property_Key2": "Custom_Event_Property_val2"
    ]
    event.logEvent() // Log the event.
    ```

- *Objective-C*

    ```objc
    // Create a BranchUniversalObject with your content data:
    BranchUniversalObject *branchUniversalObject = [BranchUniversalObject new];

    // ...add data to the branchUniversalObject as needed...
    branchUniversalObject.canonicalIdentifier = @"item/12345";
    branchUniversalObject.canonicalUrl        = @"https://branch.io/item/12345";
    branchUniversalObject.title               = @"My Item Title";

    branchUniversalObject.contentMetadata.contentSchema     = BranchContentSchemaCommerceProduct;
    branchUniversalObject.contentMetadata.quantity          = @"1";
    branchUniversalObject.contentMetadata.price             = 23.20;
    branchUniversalObject.contentMetadata.currency          = BNCCurrencyUSD;
    branchUniversalObject.contentMetadata.sku               = @"1994320302";
    branchUniversalObject.contentMetadata.productName       = @"my_product_name1";
    branchUniversalObject.contentMetadata.productBrand      = @"my_prod_Brand1";
    branchUniversalObject.contentMetadata.productCategory   = BNCProductCategoryApparel;
    branchUniversalObject.contentMetadata.productVariant    = @"XL";
    branchUniversalObject.contentMetadata.condition         = @"NEW";

    // Create an event and add the BranchUniversalObject to it.
    BranchEvent *event     = [BranchEvent standardEvent:BranchStandardEventAddToCart];

    // Add the BranchUniversalObjects with the content:
    event.contentItems     = (id) @[ branchUniversalObject ];

    // Add relevant event data:
    event.transactionID    = @"12344555";
    event.currency         = BNCCurrencyUSD;
    event.revenue          = [NSDecimalNumber decimalNumberWithString:@"1.5"];
    event.shipping         = [NSDecimalNumber decimalNumberWithString:@"10.2"];
    event.tax              = [NSDecimalNumber decimalNumberWithString:@"12.3"];
    event.coupon           = @"test_coupon";
    event.affiliation      = @"test_affiliation";
    event.eventDescription = @"Event_description";
    event.searchQuery      = @"item 123";
    event.customData       = (NSMutableDictionary*) @{
        @"Custom_Event_Property_Key1": @"Custom_Event_Property_val1",
        @"Custom_Event_Property_Key2": @"Custom_Event_Property_val2"
    };
    [event logEvent];
    ```

### Android

```java
BranchUniversalObject buo = new BranchUniversalObject()
	.setCanonicalIdentifier("myprod/1234")
	.setCanonicalUrl("https://test_canonical_url")
	.setTitle("test_title")
	.setContentMetadata(
	    new ContentMetadata()
		.addCustomMetadata("custom_metadata_key1", "custom_metadata_val1")
		.addCustomMetadata("custom_metadata_key1", "custom_metadata_val1")
		.addImageCaptions("image_caption_1", "image_caption2", "image_caption3")
		.setAddress("Street_Name", "test city", "test_state", "test_country", "test_postal_code")
		.setRating(5.2, 6.0, 5)
		.setLocation(-151.67, -124.0)
		.setPrice(10.0, CurrencyType.USD)
		.setProductBrand("test_prod_brand")
		.setProductCategory(ProductCategory.APPAREL_AND_ACCESSORIES)
		.setProductName("test_prod_name")
		.setProductCondition(ContentMetadata.CONDITION.EXCELLENT)
		.setProductVariant("test_prod_variant")
		.setQuantity(1.5)
		.setSku("test_sku")
		.setContentSchema(BranchContentSchema.COMMERCE_PRODUCT))
	    .addKeyWord("keyword1")
	    .addKeyWord("keyword2");

new BranchEvent(BRANCH_STANDARD_EVENT.ADD_TO_CART)
        .setAffiliation("test_affiliation")
        .setCoupon("Coupon Code")
        .setCurrency(CurrencyType.USD)
        .setDescription("Customer added item to cart")
        .setShipping(0.0)
        .setTax(9.75)
        .setRevenue(1.5)
        .setSearchQuery("Test Search query")
        .addCustomDataProperty("Custom_Event_Property_Key1", "Custom_Event_Property_val1")
        .addCustomDataProperty("Custom_Event_Property_Key2", "Custom_Event_Property_val2")
        .addContentItems(buo)
        .logEvent(context);
```

### HTTP API

```bash
curl -vvv -d '{
  "name": "PURCHASE",
  "user_data": {
    "os": "Android",
    "os_version": 25,
    "environment": "FULL_APP",
    "aaid": "abcdabcd-0123-0123-00f0-000000000000",
    "android_id": "a12300000000",
    "limit_ad_tracking": false,
    "developer_identity": "user123",
    "country": "US",
    "language": "en",
    "local_ip": "192.168.1.2",
    "brand": "LGE",
    "app_version": "1.0.0",
    "model": "Nexus 5X",
    "screen_dpi": 420,
    "screen_height": 1794,
    "screen_width": 1080
  },
  "custom_data": {
    "purchase_loc": "Palo Alto",
    "store_pickup": "unavailable"
  },
  "event_data": {
    "transaction_id": "trans_Id_1232343434",
    "currency": "USD",
    "revenue": 180.2,
    "shipping": 10.5,
    "tax": 13.5,
    "coupon": "promo-1234",
    "affiliation": "high_fi",
    "description": "Preferred purchase"
  },
  "content_items": [
    {
      "$content_schema": "COMMERCE_PRODUCT",
      "$og_title": "Nike Shoe",
      "$og_description": "Start loving your steps",
      "$og_image_url": "http://example.com/img1.jpg",
      "$canonical_identifier": "nike/1234",
      "$publicly_indexable": false,
      "$price": 101.2,
      "$locally_indexable": true,
      "$quantity": 1,
      "$sku": "1101123445",
      "$product_name": "Runner",
      "$product_brand": "Nike",
      "$product_category": "Sporting Goods",
      "$product_variant": "XL",
      "$rating_average": 4.2,
      "$rating_count": 5,
      "$rating_max": 2.2,
      "$creation_timestamp": 1499892854966,
      "$exp_date": 1499892854966,
      "$keywords": [
        "sneakers",
        "shoes"
      ],
      "$address_street": "230 South LaSalle Street",
      "$address_city": "Chicago",
      "$address_region": "IL",
      "$address_country": "US",
      "$address_postal_code": "60604",
      "$latitude": 12.07,
      "$longitude": -97.5,
      "$image_captions": [
        "my_img_caption1",
        "my_img_caption_2"
      ],
      "$condition": "NEW",
      "$custom_fields": "{\"foo1\":\"bar1\",\"foo2\":\"bar2\"}"
    },
    {
      "$og_title": "Nike Woolen Sox",
      "$canonical_identifier": "nike/5324",
      "$og_description": "Fine combed woolen sox for those who love your foot",
      "$publicly_indexable": false,
      "$price": 80.2,
      "$locally_indexable": true,
      "$quantity": 5,
      "$sku": "110112467",
      "$product_name": "Woolen Sox",
      "$product_brand": "Nike",
      "$product_category": "Apparel & Accessories",
      "$product_variant": "Xl",
      "$rating_average": 3.3,
      "$rating_count": 5,
       "$rating_max": 2.8,
      "$creation_timestamp": 1499892854966
     }
   ],
   "metadata": {},
   "branch_key": "key_test_hdcBLUy1xZ1JD0tKg7qrLcgirFmPPVJc"
 }' https://api2.branch.io/v2/event/standard
```

See [full API docs here](https://github.com/BranchMetrics/branch-deep-linking-public-api#logging-commerce-events).

## Track Content Events

Content events are events that occur when a user engages with your in-app content. For example, if you have in-app articles, you would want to track events related to when users search, view content, rate the content, and share. This can apply to a wide variety of in-app content, such as blog posts, music, video, pictures, and e-commerce catalogue items.

### iOS

- *Swift*

    ```swift
    let event = BranchEvent.standardEvent(.search)
    event.eventDescription = "Product Search"
    event.searchQuery = "user search query terms for product xyz"
    event.customData["Custom_Event_Property_Key1"] = "Custom_Event_Property_val1"
    event.logEvent()
    ```

- *Objective-C*

    ```obj-c
    BranchEvent *event = [BranchEvent standardEvent:BranchStandardEventSearch];
    event.eventDescription = @"Product Search";
    event.searchQuery = @"user search query terms for product xyz";
    event.customData[@"Custom_Event_Property_Key1"] = @"Custom_Event_Property_val1";
    [event logEvent];
    ```

### Android

```Java
 new BranchEvent(BRANCH_STANDARD_EVENT.SEARCH)
    .setDescription("Product Search")
    .setSearchQuery("product name")
    .addCustomDataProperty("Custom_Event_Property_Key1", "Custom_Event_Property_val1")
    .logEvent(context);
```

### HTTP API

```
curl -vvv -d '{
  "name": "VIEW_ITEMS",
  "user_data": {
    "os": "Android",
    "os_version": 25,
    "environment": "FULL_APP",
    "aaid": "abcdabcd-0123-0123-00f0-000000000000",
    "android_id": "a12300000000",
    "limit_ad_tracking": false,
    "developer_identity": "user123",
    "country": "US",
    "language": "en",
    "local_ip": "192.168.1.2",
    "brand": "LGE",
    "app_version": "1.0.0",
    "model": "Nexus 5X",
    "screen_dpi": 420,
    "screen_height": 1794,
    "screen_width": 1080
  },
  "custom_data": {
    "purchase_loc": "Palo Alto",
    "store_pickup": "unavailable"
  },
  "event_data": {
    "search_query": "red sneakers",
    "description": "Preferred purchase"
  },
  "content_items": [
    {
      "$content_schema": "COMMERCE_PRODUCT",
      "$og_title": "Nike Shoe",
      "$og_description": "Start loving your steps",
      "$og_image_url": "http://example.com/img1.jpg",
      "$canonical_identifier": "nike/1234",
      "$publicly_indexable": false,
      "$price": 101.2,
      "$locally_indexable": true,
      "$sku": "1101123445",
      "$product_name": "Runner",
      "$product_brand": "Nike",
      "$product_category": "Sporting Goods",
      "$product_variant": "XL",
      "$rating_average": 4.2,
      "$rating_count": 5,
      "$rating_max": 2.2,
      "$creation_timestamp": 1499892854966,
      "$exp_date": 1499892854966,
      "$keywords": [
        "sneakers",
        "shoes"
      ],
      "$address_street": "230 South LaSalle Street",
      "$address_city": "Chicago",
      "$address_region": "IL",
      "$address_country": "US",
      "$address_postal_code": "60604",
      "$latitude": 12.07,
      "$longitude": -97.5,
      "$image_captions": [
        "my_img_caption1",
        "my_img_caption_2"
      ],
      "$condition": "NEW",
      "$custom_fields": "{\"foo1\":\"bar1\",\"foo2\":\"bar2\"}"
    },
    {
      "$og_title": "Nike Woolen Sox",
      "$canonical_identifier": "nike/5324",
      "$og_description": "Fine combed woolen sox for those who love your foot",
      "$publicly_indexable": false,
      "$price": 80.2,
      "$locally_indexable": true,
      "$sku": "110112467",
      "$product_name": "Woolen Sox",
      "$product_brand": "Nike",
      "$product_category": "Apparel & Accessories",
      "$product_variant": "Xl",
      "$rating_average": 3.3,
      "$rating_count": 5,
      "$rating_max": 2.8,
      "$creation_timestamp": 1499892854966
    }
  ],
  "metadata": {},
  "branch_key": "key_test_hdcBLUy1xZ1JD0tKg7qrLcgirFmPPVJc"
}' https://api2.branch.io/v2/event/standard
```

See [full API docs here](https://github.com/BranchMetrics/branch-deep-linking-public-api#logging-content-events).

## Track Lifecycle Events

Lifecycle events can be described as events a user takes in your app to continue progressing. These events can apply to game apps, as well as non game apps, for when a user completes a user profile, registration or tutorial.

### iOS

- *Swift*

    ```swift
    let event = BranchEvent.standardEvent(.completeRegistration)
    event.transactionID = "tx1234"
    event.eventDescription = "User completed registration."
    event.customData["registrationID"] = "12345"
    event.logEvent()
    ```

- *Objective-C*

    ```obj-c
    BranchEvent *event = [BranchEvent standardEvent:BranchStandardEventCompleteRegistration];
    event.transactionID = @"tx1234";
    event.eventDescription = @"User completed registration.";
    event.customData[@"registrationID"] = @"12345";
    [event logEvent];
    ```

### Android

```java
new BranchEvent(BRANCH_STANDARD_EVENT.COMPLETE_REGISTRATION)
    .setTransactionID("tx1234")
    .setDescription("User created an account")
    .addCustomDataProperty("registrationID", "12345")
    .logEvent(context);
```

### HTTP

```
curl -vvv -d '{
  "name": "COMPLETE_REGISTRATION",
  "user_data": {
    "os": "Android",
    "os_version": 25,
    "environment": "FULL_APP",
    "aaid": "abcdabcd-0123-0123-00f0-000000000000",
    "android_id": "a12300000000",
    "limit_ad_tracking": false,
    "developer_identity": "user123",
    "country": "US",
    "language": "en",
    "local_ip": "192.168.1.2",
    "brand": "LGE",
    "app_version": "1.0.0",
    "model": "Nexus 5X",
    "screen_dpi": 420,
    "screen_height": 1794,
    "screen_width": 1080
  },
  "custom_data": {
    "foo": "bar"
  },
  "event_data": {
    "description": "Preferred purchase"
  },
  "metadata": {},
  "branch_key": "key_test_hdcBLUy1xZ1JD0tKg7qrLcgirFmPPVJc"
}' https://api2.branch.io/v2/event/standard
```

See [full API docs here](https://github.com/BranchMetrics/branch-deep-linking-public-api#logging-user-lifecycle-events).

## Track Custom Events

If you want to track an event that isn't a predefined event, simply do the following:

!!! warning "Custom Event Names"
    We strongly recommend using custom event names that contain no more than 40 characters, contain only letters, numbers, hyphens, spaces and underscores, and do not start with a hyphen. Facebook will not accept events that violate these rules, and if you enable the Facebook integration, Branch may sanitize your event names to pass validation.

### iOS

- *Swift*

    ```swift
    let event = BranchEvent.customEvent(withName: “User_Scanned_Item”)
	event.customData[“Custom_Event_Property_Key1”] = “Custom_Event_Property_val1"
	event.customData[“Custom_Event_Property_Key2”] = “Custom_Event_Property_val2"
	event.logEvent()
    ```

- *Objective-C*

    ```obj-c
    BranchEvent *event = [BranchEvent customEventWithName:@"User_Scanned_Item"]];
    event.customData[@“Custom_Event_Property_Key1”] = @“Custom_Event_Property_val1";
    event.customData[@“Custom_Event_Property_Key2”] = @“Custom_Event_Property_val2";
	[event logEvent];
    ```

### Android

```Java
new BranchEvent("Some Custom Event")
    .addCustomDataProperty("Custom_Event_Property_Key11", "Custom_Event_Property_val11")
    .addCustomDataProperty("Custom_Event_Property_Key22", "Custom_Event_Property_val22")
    .logEvent(MainActivity.this);
}
```

### HTTP API

```
curl -vvv -d '{
  "name": "picture swiped",
  "user_data": {
    "os": "Android",
    "os_version": 25,
    "environment": "FULL_APP",
    "aaid": "abcdabcd-0123-0123-00f0-000000000000",
    "android_id": "a12300000000",
    "limit_ad_tracking": false,
    "developer_identity": "user123",
    "country": "US",
    "language": "en",
    "local_ip": "192.168.1.2",
    "brand": "LGE",
    "app_version": "1.0.0",
    "model": "Nexus 5X",
    "screen_dpi": 420,
    "screen_height": 1794,
    "screen_width": 1080
  },
  "custom_data": {
    "foo": "bar"
  },
  "metadata": {},
  "branch_key": "key_test_hdcBLUy1xZ1JD0tKg7qrLcgirFmPPVJc"
}' https://api2.branch.io/v2/event/custom
```

See [full API docs here](https://github.com/BranchMetrics/branch-deep-linking-public-api#logging-custom-events).

## Testing v2/event

In order to test whether v2/events are being received on Branch's backend, check out [Liveview](pages/exports/pba-liveview). Be sure you have opted into the new [People-Based Attribution](/pages/dashboard/people-based-attribution/).
