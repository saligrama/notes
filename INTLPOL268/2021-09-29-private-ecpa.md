# ECPA Continued

## ECPA liability for private actors

* Does WiFi "sniffing" violate the Wiretap Act?
    - Statute: Not unlawful to intercept an electronic communication when readily accessible to general public
        - This is to prevent penalizing radio hobbyists for listening in on radio frequencies
    - Court: No! *In re Innovatio IP Ventures, LLC Patent Litig.*, 886 F. Supp. 2d 888 (N.D. III 2012)
        - Innovatio, a patent troll, used a "packet capture adapter" intercepted packets sent over WiFi to fish for IP violations on public WiFi networks
            - Capture included content, not just metadata
        - Court ruled this is not unlawful since it falls inside private interception exception
            - Opinion held that sniffing public unencrypted WiFi comms is easy and cheap, so these comms are "readily accessible to the general public"
    - Court: Maybe! *Joffe v. Google Inc.*, 746 F.3d 920 (9th Cir. 2013)
        - Google Street View cars mapping out available WiFi networks also obtained payload data
        - Court held that payload data transmitted over Wi-Fi is not a "radio communication": defined a distinction between "radio" and "radio EM spectrum"
        - Court deleted original opinion's "readily accessible" analysis
* Does setting third-party cookies violate the Wiretap Act?
    - Court: No! *In re Google Inc. Cookie Placement Consumer Privacy Litigation.*, 806 F.3d 125 (3d Cir. 2015)
        - Plaintiffs alleged that Google et al. placed tracking cookies on web browsers against browser cookie blocking settings
        - Cookies set through invisible `iframe`s
        - However: court ruled in Google's favor
    - Court: Yes! *In re Facebook, Inc. Internet Tracking Litigation* 956 F.3d 589 (9th Cir. 2020)
        - Court acknowledged disagreement with *Google Cookie* in Facebook tracking case

## Other laws about cookies and consent
* Europe: GDPRA, ePrivacy Directive
* California: CCPA, CA Privacy Rights Act (CPRA)
* Other states: VA Consumer Data Protection Act, WA Privacy Act