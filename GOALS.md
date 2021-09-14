Goals and use cases
====================

This document is a collection of use cases and design principles that a web platform feature for measuring and reporting ad conversions should support.

## Privacy

Any conversion measurement API will be built around joining impression level information with conversion information. If this information channel is not carefully controlled, this API could be used to share identity across sites. To maintain good privacy, we need to ensure that the information in a report does not reveal much more information about a given user than the publisher / advertiser already knew without the API (i.e. the unjoined data).

Since the browser has control over this channel, limits can be tuned to give good privacy and utility.

## First party and third party ads

This API should be able to support conversion measurement on ads in first party and third party contexts. The vast majority of the web advertising ecosystem relies on third parties for their ads, and ideally a solution would accommodate them.

Restricting to first party ads could lead to perverse incentives for third parties to opt-out of isolating themselves using primitives like cross-domain iframes.

## Few site updates

Ideally, most publishers and advertisers will not need to update their sites much to take advantage of this API. Ad tech providers and ad creative authors can change their code to do it under the hood.

Lots of conversion tags today rely on `<img>` "pixels", so a conversion registration mechanism that relied on JavaScript would force advertisers to make updates. Additionally, nearly all ad tech companies fall back to `<img>` tags if JavaScript is disabled, or partner with existing publishers using legacy `<img>` tags.

Examples: [Google](https://support.google.com/admanager/answer/2499318), [Appnexus](https://wiki.appnexus.com/display/api/Conversion+Pixel+Service), [Facebook](https://developers.facebook.com/docs/facebook-pixel/implementation#base-code).

## Third party reporting

Most publishers and advertisers do not have the server-side infrastructure required to log and measure conversions. Instead, they have third party ad tech companies do it for them. For a conversion API to be broadly used, it should allow for this use-case.

This goal is purely for ergonomics. It shouldn’t change the underlying privacy properties of the API assuming publishers / advertisers would forward reports to their ad tech companies anyway on the server-side.

Of course, it should not be possible for untrusted third parties to receive conversion reports without publisher / advertiser permission. This could potentially be addressed via a [Feature Policy](https://w3c.github.io/webappsec-feature-policy/) delegation of permission.

## Declarative / Non-script based

All else being equal, it is beneficial to avoid the need for more third-party JavaScript running on pages.


## Optimization and Reporting

The API may support:
* **Optimization** use cases: answer "How can I generate the maximum number of conversions?" and similar questions

and

* **Reporting** use cases: answer "What is my return on investment?" and similar questions

One approach to support these in a privacy-preserving way is to give access to different types of insights via two types of reports that can be sent to an advertiser or a third-party adtech provider. These two types of reports would be used simultaneously and be complementary.


### Event-level reports

Event-level data is data that identifies a single unique event, as opposed to aggregated data. This kind of data is essential for training machine learning models used to optimize ad selection, since success / failure needs to propagate to the individual inference that chose the ad in the first place.

Event-level reports would be suited to:
* **Optimization** use cases. In particular, they would be used to optimize for ad placement, since a unique ID for the ad side can be made available in the reports. Event-level reports could provide training data for machine learning models.
* Coarse **reporting** use cases where very little information, a few bits only, is needed about the conversion. Encoding of granular conversion-side data, such as a specific price or conversion time, would thus not be supported in event-level reports.
* **Fraud detection** use cases. The data in some reports would be useful for ad fraud detection and analysis, by allowing to understand patterns that can be used to identify spammy or invalid activity. (With coarser click or view data, fraudsters can more easily hide in the crowd)

### Aggregate reports

Aggregate reports would offer more detailed conversion data, and more flexibility for joining click/view data and conversion data.

Aggregate reports would be best suited for **reporting** use cases.

Using aggregate reports for **optimization** (for example, to optimize for a purchase value, which wouldn't not supported by event-level reports because the conversion data is too coarse) may be possible.

## Conversion data

Here are some legitimate use cases of conversion data:

-   Conversion label (sign-up vs purchase)
-   Conversion value ($10 purchase vs. $1000 purchase)
-   Conversion delay (conversion time – click/view time)
-   Lifetime value (sum of all purchase values for a given user)
-   Conversion basket (the list of items and quantity purchased)
-   New / existing customer (whether the customer was existing or new, for the purpose of optimizing for customer acquisition)

Some of these use cases may not be supported by this API, depending on their informational needs.








