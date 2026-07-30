Target Ad Live for STB conf

Table of Contents
Introduction
NTVBE base solution
Server locations
Target Ad proxy TAP
Target Ad Server
Target Ad CDN
HW version information
TA consent
Channel enabling
Impact
AVS console
UI builder
urlprov
3
3
3
3
3
3
4
4
4
4
4
4
4
Introduction
Target Ad Live (for STB)
On BEP the necessary information is provided to the clietns to allow them to perform TA-live functionality
without further interaction with the TV backend
To maintain a similar solution when clients are connected to AVS backend similar information needs to be
provided to the STB clients
NTVBE base solution
For NTVBE Target Ad Live the same approach as on BEP is chosen
Below are the adapted methods to retrieve the different inforamtion elements needed for TA live. Client
processing shall be the same as when connected to BEP
Server locations
To enable the client to know where to send request for target advertisement, the address of these servers
need to be provided towards those clients.
For this purpose the following server addresses will be provided
SAP channel for Target advertisements
TAP server address 
request ad replacement
TAS server address
request daily ad list
CDN address for downloading ads in unicast mode
Target Ad proxy TAP
Server Name = TAP
Server Type = TAP
TAP Server Address
TAP Server port number
request randomization interval (in milliseconds) = randomization value for unicast based on marker
download
VAST Request Randomization = randomization to send VAST request after detecting SCTE marker
Ad duration Tolerance = max time value difference between SCTE marker time and duration of
replacement ad
Target Ad Server
Server Name
Server Type = TAS
TAS Server Address
TAS Server port number
AdRetrievalInterval = periodicity for retrieving list of ads
RequestRandomInterval = randomization to retrieve list after boot
Target Ad CDN
3 / 4
Server Name
Target Ad Live (for STB)
External IP Address: IPV4/IPV6 or DNS name as which the CDN server is visible towards the clients on
the external network (egress side of the CDN).
External Port: port number through which the file can be fetched (default 80)
External Protocol: http, https (default http)
TAPublishingPath: path on the external CDN from which the target ads can be loaded.
HW version information
this is included in the config/app response
TADTVEnabled:  true
TAStorageSize: 1024
TAPreDownloadPercentage: 80
TargetAdDownLoadBandwidth :150
TargetAdCutOffBandwidth
this is a new parameter: in case the totalAssignedBandwidth (for the line) from the get
USER/DEVICES response is below this threshold then no support for TA live will be provided
TA consent
Consent for target ad live is contained in
"consentFlag": "Target_Adv_Enabled" 
which can be retrieved via /AGL/1.6/R/ENG/ANDROIDSTB/PXS/CUSUSER/CONSENTS
Channel enabling
a Channel is enabled for TA live in case isTargetADVEnabled": true,
No distinction to be made between live and Pause live T
TargetADVInactivity will be provided on channel.extendedMetadata level (no need to provide it on asset
level)
Impact
AVS console
create server definitions
UI builder
enhance STB configuration with HW information 
urlprov
include isTargetADVEnabledin and TargetADVInactivity in urlprov (values taken from BEP)
4 / 4
