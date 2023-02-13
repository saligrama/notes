# Web Content

* Modern websites rely on many different types of *third-party resources* to provide services to keep websites functional
    - Served by external parties, e.g. on `cnn.com` could have third-party resources from `doubleclick.com` or `google-analytics.com`
    - Could be anything from static images to JS libraries to analytics to etc

## Web tracking and analytics

* Many websites rely on user analytics to improve services
    - e.g. Google Analytics: appears on 70% of top websites
* Can see where users are connecting from, how long spent on page, devices used, etc.
    - Usually scoped to single request, but recently scope has been expanding (e.g. FullStory can get pretty invasive)
* How this works (web tracking): **cookies**
    - Difference between first-party auth cookies and third-party analytics cookies:
        - First-party cookies can only be sent to same origin as that which set it
        - Third-party cookies can be sent to another site
    - e.g. Google Analytics has some JS sent by CNN (or really any other GA client) servers, that send *Google* cookie to Google's servers, and then Google shares the info with CNN
        - Because Google cookie is the same across websites, this allows Google to track a user across sites
    - Alternatively, redirect chains can pass analytics state through GET query parameters; this can't be blocked using third-party cookie blockers
    - Cookie ghostwriting: `tracker.com/script.js` can set a cookie for both a first-party and an advertiser
* Third-party analytics with cookie syncing is enabled on 78% of modern websites
    - Mostly to sell ads

## The online advertising ecosystem

* Companies want to track you to be able to build profiles for *targeted advertising*
    - More targeted ads: more revenue possible from advertisers who may pay more for the ad spot
    - Useful for advertisers to know what people with browsing habits, properties, etc. are browsing on the web

### Publishers

* Publishers e.g. NYTimes, CNN, other websites often have ad space they want to make revenue off of
    - In some cases, publishers have explicit agreements with companies and can sell space that way

### Supply-side platforms

* If a publisher wants to place ad spot on open advertising market, they typically go through intermediary called Supply Side Platform (SSP)
    - e.g. Pubmatic, Rubicon Project, Verizon Media
* Aggregate information about the client (through Data Management Platform (DMP)) and participate in ad exchange
    - First-party data e.g. CRM data: can include data from customer behaviors, actions, purchases, or interest
    - Second-party data: statistics related to cookie pools on external publications and platforms
    - Third-party data: sourced from external providers and aggregated across sites; businesses sell third-party data

### Demand-side platforms

* On the other end: advertisers
* Advertisers contract with DSPs, which participate in real-time bidding, which is a real-time auction for ad space
    - e.g. Google DoubleClick, QuantCast, Criteo, Adform
    - Typically auctions clear in <100ms

### Ad exchanges

* Receive spots from supply-side and facilitate real-time bidding from demand-side based on properties of ad spot
    - e.g. Google DoubleClick, FB exchange, Pubmatic, Microsoft Advertising

### Real-time bidding

#### Waterfall bidding

* Publishers pre-define hierarchy of advertising networks they want to ask in order about any given ad spot
* Publishers set floor bid rate they need for ad spot
    - First network to fulfill floor wins spot, but floor price goes down w/ lower priority
* Issues:
    - Slow (serial computation)
    - Anti-competitive: Google had both SSP and DSP, so they often got first choice

#### Header bidding

* Every DSP offered auction at same time; DSPs are incentivized to provide true value for ad spot (theoretically)
    - Typically happens in 100-200ms
* Two options:
    - Client-side header bidding (happens in JS): makes page slower, but finer-grained access to cookies
    - Server-side header bidding (happens in SSP): can be faster, but requires cookie syncing; could make things slower

## Regulation: GDPR, CCPA

* Regulation since 2016 around privacy and tracking issues
    - General Data Protection Regulation (GDPR): in EU, for *data protection and privacy*
    - California Consumer Privacy Act (CCPA): in CA, for *customer protections*
* Both laws mandate rules for PII storage (e.g. IP addresses, cookies), how long they can be stored on server-side, etc.
* GDPR can be onerous; US based companies that don't care about shipping products to EU simply disable their websites in EU