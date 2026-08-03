Banner swinlane Specs
TVO Swimlane using 'small banners'
Status for BEP: Analysis complete, ready for refinement.
The banner functionality is a versatile tool on the NTE2 Hub. Today only 'marketing' banners are 
supported : the swimlane holds a single broad banner that spans the width of the screen. This is now 
extended to support multiple banners on a swimlane and using smaller (narrower) banner images. 
A swimlane with TVO links will be a first practical use-case of this new functionality, but various other 
applications become possible. 
Generic Technical Solution proposal
The proposed solution re-uses the existing Banner Swimlane implementation with a small addition:
Swimlanes are created using Type=Banner and a technical name that refers to a banner Section in the showcase.xml. 
To determine the swimlane contents, the clients issue a BTA:getNte2Swimlane request with all relevant parameters. 
BEP (BRQH) uses the Section definition to request banners from TCMT. 
TCMT internal flow is out of scope.
Based on the section and other parameters a list of banners is returned to BEP. 
For each returned banner an action-when-selected (BannerActionLink), a banner image (ImageURL) and optional further parameters are delivered. NEW: 
response includes banner image dimensions. 
BEP translates the TCMT response into a BTAResponse towards the client, including the new banner image dimensions. 
The clients can use the new banner image dimensions to determine the swimlane appearance (details 
are client-specific)
either by choosing the best-fitting predefined UI design:
Classic UI design has placeholders with fixed dimensions (or rather: fixed aspect ratio) per item type. With this approach there can be 2 (or more) banner 
swimlane designs. The AR of the returned banners should correspond with one of the available designs so the client knows which layout to use. Nonperfect matches must be supported, which will however result in imperfect (cropped or stretched) banner visualisation . 
or by using dynamic layout: 
In this case the UI design prescribes only one dimension, expected to be the height of the banner swimlane. The client re-sizes the received banner 
images to match this swimlane height while preserving the AR. This means the width of each tile is variable.
Application for TVO-Banners swimlane
Based on the Banner Section, TCMT will contact TVO to determine the swimlane banners. TVO logic 
determines which tiles should be shown, based on the available offers and their subscription status for 
the account. It is possible to choose a different banner image or banner action for different subscription 
cases or device types. 
Details will be handled in the related TVO project. 
Impacted components
BTA client interface
The BTA "Banner" definition is extended with an optional <ImageSize> tag. 
Example for a "wide" banner: <ImageSize>1100x266</ImageSize>
Example for a "narrow" banner: <ImageSize>392x280</ImageSize>
The new tag is supported in BTA:getNte2Swimlane response but not in BTA:getShowCase response. 
BEP/BRQH
Accept the ImageSize information from the TCMT response (if present) and include it in BTA:
getNte2Swimlane response. 
V5/V6 clients
UI design
The design for a Banner Swimlane today only supports a single "wide" banner (1100x266). The 
existing clients will apply this design when a getNte2Swimlane response contains a banner. 
A second design for "small banner" swimlanes will be needed. 
As a preliminary solution the design for VOD Category swimlanes is re-used. This supports multiple 
tiles of 280x200 (not focused) / 372x266 (focused). All visual and UX aspects of this design are reused for the "small banner" swimlanes. Because this is an existing design with all required assets (like 
darkening gradient, focus glow, ...) it is a fast and cheap solution.
OPTION : we could also re-use other existing swimlane design e.g. the VOD Items swimlanes design 
with portrait posters 150x200 (not focused) / 200x266 (focused). 
This would not require much additional impact, only additional test effort. 
How does the client know which design to use?
A first indication is already in the swimlane definition (BTA:getNte2Hub response):
when the SL has an ItemDisplayLimit=1 it can be assumed to hold a "wide" banner.
when the ItemDisplayLimit > 1 it can be assumed to hold a list of "small" banners.
Confirmation comes with the BTA:getNte2Swimlane response
The response may contain only 1 banner, but this fact must not be used for deciding. Even a single banner can be "small".
The correct indicator is the banner ImageSize. 
For a "wide" banner SL, the aspect ratio (width / height) is in the range 4,00 - 4,20. Example: ImageSize=1100x266 AR=4,135
For a "small" banner SL, the aspect ratio (width / height) is in the range 1,30 - 1,50. Example: ImageSize=392x280 AR=1,40
(NOT selected option : for a "portrait" banner SL, the AR is in the range 0,70 - 0,80.)
In all cases, the returned images are fitted into the available space, which may include a limited amount of stretching (or cropping). 
The "small banners" design (AR= 1,3-1,5) is the default for out-of-range AR values. All returned banners will be shown, significantly deformed.
Obviously, it is the intention that this never occurs, but IF it happens it is considered better to show it in an ugly way (so it can be corrected asap) than to 
hide it (and remain unnoticed for a longer time). 
The client can ASSUME that all images in an SL are in the same AR-range. No mix of wide and small (and portrait) banners is supported. 
Banner support
Every tile in a "small banners" swimlane is a banner with a specific action behind it. All actions 
supported for "wide" banners are also possible on "small" (and portrait) banners. 
For the first practical use case of the TVO banner, the banners will link to a HTML page (including 
token-based parameter list). 
The small banner images must be retrieved from the banner poster server, according to that server's 
definition. 
For a "small banners" swimlane it is NOT required to support the Banner Viewed feedback 
mechanism. This is omitted to keep complexity (and cost) low.
The Banner Clicked feedback mechanism should be supported IF the banner metadata requests it - 
which will not be the case for the TVO-swimlane case. 
For "large" and "small" banner swimlanes alike, the swimlane cache must be invalidated when a poster 
is clicked so that a renewed banner swimlane is shown at next invocation.
TVI/TVO impacts
It is important that all the banners in a swimlane (Section) have an Aspect Ratio within the same
expected range. 
Migration aspects
During the client migration timeframe, older clients (not yet supporting multi-banner SL) and newer 
ones will co-exist.
The new banner field ImageSize should be ignored by older client versions and should not cause 
problems. 
Without precautions it may occur that older clients would show a multi-banner SL (TVO swimlane) 
using the single "wide" banner layout. 
This shall be avoided as follows
The new TVO swimlane will (at first) not be configured on the hub, only on the shop page. 
The V5/V6 client version that implements the Swimlane based shop must also include this multi-banner SL function. 
With this approach no older client will receive or use the TVO swimlane. 

