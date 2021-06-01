---
title: Beeline MF90 API

language_tabs: # must be one of https://git.io/vQNgJ
  - http
  # - shell
  # - javascript

toc_footers:
  # - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for ZTE Web UI Modem
  - name: keywords
    content: ZTE,Web,UI,modem,API,Documentation
  - name: google-site-verification
    content: msBeeCY7-smcfjFEqCkjTVrUUu1bTc1qaqBcyVmVvck
---

# Introduction

Welcome to the Beeline B10 API for ZTE MF90 MiFi! You can use this API to access and change device properties.

The API oriented around two endpoint, accepts raw request bodies, returns JSON in plain text format, and disregard standard HTTP response code, authentication, and verbs.

## Request and response formats

> Request

```http
POST /goform/goform_get_cmd_process?multi_data=1&isTest=false&cmd=modem_main_state%2Cpin_status%2Cloginfo%2Cnew_version_state%2Ccurrent_upgrade_state%2Cis_mandatory&_=1620080390191 HTTP/1.1
Host: 192.168.0.1
Accept: application/json, text/javascript, */*; q=0.01
Referer: http://192.168.0.1/index.html

cmd=modem_main_state,pin_status,loginfo,new_version_state,current_upgrade_state,is_mandatory
```

> Response

```http
HTTP/1.0 200 OK
Server: GoAhead-Webs
Pragma: no-cache
Cache-control: no-cache
Content-Type: text/plain

{"modem_main_state":"modem_init_complete","pin_status":"0","loginfo":"no","new_version_state":"0","current_upgrade_state":"","is_mandatory":""}
```

In general, the Beeline API uses HTTP POST requests with `x-www-form-urlencoded` arguments and JSON responses in `text/plain` format. 

API authentication is using preliminary request that create authenticated sessions for limited time.

<aside class="warning">
All request must contain header attribute <code>Referer</code> that linked to the device Web UI <code>index.html</code> file.
</aside>

### Multiple attributes

You can pass multiple object keys separated by comma into `cmd` parameter to retrieve multiple object in single request.

eg: `cmd=modem_main_state,pin_status,...`

<aside class="warning">
Passing to much object keys can lead to unresponsive device that required <b>force reboot</b>. 
It's advised to pass multiple object keys from the same group (eg: GSM & LTE Objects Group) to prevent said problem.
</aside>

### Form ID

When requesting a change to some properties using the endpoint `/goform/goform_set_cmd_process`, you required to pass an action **Form ID** as value for `goformId` parameter. 

eg: `goformId=<form_id>`

# Authentication

```http
POST /goform/goform_set_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html

goformId=LOGIN&password=<password>
```

> Response

```json
{
  "result":"0"
}
```

Beeline API didn't support any in-flight authorization, instead it relies on device sessions.

<aside class="notice">
The duration of authenticated session is still <b>unknown!</b> The workaround to that limitation you can schedule recurring AUTH requests or do authentication as preliminary for every request.
</aside>

### Form ID

`LOGIN`

### Parameters

Name|Default|Description
---|---|---
**`password`** | - | Your password in `base64` encoding

# GSM & LTE

<aside class="notice">
Any change to GSM & LTE properties must be done with device at <code>disconnected</code> state
</aside>

## GSM & LTE Object

> Object

```json
{
  "sim_imsi":"510103162079481",
  "hmcc": "510",
  "hmnc": "10",
  "hplmn": "51010",
  "rssi":"-65",
  "rscp":"-68",
  "lte_rsrp":"0",
  "network_type":"HSPA+",
  "signalbar": "5",
  "network_provider": "T-SEL",
  "spn_name_data": "00540045004C004B004F004D00530045004C",
  "simcard_roam": "Home"
}
```

This objects representing your GSM & LTE status.

Object key | Description
--- | ---
**`sim_imsi`** | SIM Card [International mobile subscriber identity number](https://en.wikipedia.org/wiki/International_mobile_subscriber_identity). The first 3 digits represent the [mobile country code](https://en.wikipedia.org/wiki/Mobile_country_code) (MCC), which is followed by the [mobile network code](https://en.wikipedia.org/wiki/Mobile_network_code) (MNC), either 2-digit (European standard) or 3-digit (North American standard). The remaining digits are the [mobile subscription identification number](https://en.wikipedia.org/wiki/Mobile_subscription_identification_number) (MSIN) within the network's customer base, usually 9 to 10 digits long, depending on the length of the MNC.
**`hmcc`** | [Mobile country code](https://en.wikipedia.org/wiki/Mobile_country_code) (MCC) of the carrier.
**`hmnc`** | [Mobile network code](https://en.wikipedia.org/wiki/Mobile_network_code) (MNC) of the carrier.
**`hplmn`** | [Public land mobile network](https://en.wikipedia.org/wiki/Public_land_mobile_network) (MNC) code of the carrier.
**`rssi`** | [received signal strength indicator](https://en.wikipedia.org/wiki/Received_signal_strength_indication) (RSSI) is a measurement of the power present in a received radio signal in decibel-milliwatts (dBm).
**`rscp`** | [Received signal code power](https://en.wikipedia.org/wiki/Received_signal_code_power) (RSCP) is a measurement of the received power level in an UMTS/WCDMA cell network in decibel-milliwatts (dBm).
**`lte_rsrp`** | [Reference Signal Received Power](https://en.wikipedia.org/wiki/RSRP) (RSRP) is a measurement of the received power level in an LTE cell network in decibel-milliwatts (dBm). 
**`network_type`** | Currently connected network type. Available values are `GSM`, `GPRS`, `EDGE`, `UMTS`, `HSDPA`, `HSUPA`, `HSPA`, `HSPA+`, `DC`, `DC-HSPA`, `DC-HSPA+`, and `LTE`. This attribute will contain `limited_service` if the device can't establish with the GSM or LTE Network.
**`signalbar`** | Bar graph representation for signal strength.
**`network_provider`** | Shorter version of SIM card operator/carrier name.
**`spn_name_data`** | Advertised network/carrier name. Please see [Content Decoding](#decoding) to decode the value. 

## Retrieve GSM & LTE Properties

```http
POST /goform/goform_get_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html

cmd=<attributes>
```

> Response

```json
{
  "sim_imsi":"510103162079481",
  "hmcc": "510",
  ...
}
```

Retrieve current GSM & LTE info and settings

### Attributes

The `attributes` are the [object key](#gsm-amp-lte-object) that you want to retrieve it's value. 


## Change Network Type

```http
POST /goform/goform_set_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html

goformId=SET_BEARER_PREFERENCE&BearerPreference=Only_LTE
```

> Response

```json
{
  "result":"success",
}
```

The device had 3 network type which is 2G, 3G, and 4G network.

### Form ID

`SET_BEARER_PREFERENCE`

### Parameters

Name | Default | Descriptions
--- | --- | ---
**`BearerPreference`** | *`NETWORK_auto`* | Network Type. Available value are `NETWORK_auto`, `Only_LTE` *(4G)*, `Only_WCDMA` *(3G)*, or `Only_GSM` *(2G)*.

# PPP Connection 

For the device to be able making WAN/internet connection, you need to create or set default [Access Point Name](https://en.wikipedia.org/wiki/Access_Point_Name) (APN) profile.

## PPP Connection Profile Object

> Object

```json
{
  "APN_config0": "testv4($)test_apn($)manual($)*99#($)pap($)test_username($)test_password($)IP($)auto($)($)auto($)8.8.8.8($)8.8.8.8($)",
  ...,
  "APN_config19": "testv6($)($)manual($)*99#($)($)($)($)IPv6($)auto($)($)($)($)($)",
  "ipv6_APN_config0": "testv4($)($)manual($)*99#($)($)($)($)IP($)auto($)($)($)($)($)",
  ...,
  "ipv6_APN_config19": "testv6($)test_apn($)manual($)*99#($)chap($)test_username($)test_password($)IPv6($)auto($)($)manual($)2001:4860:4860::8888($)2001:4860:4860::8844($)",
  "m_profile_name": "testv4",
  "wan_dial": "*99#",
  "pdp_type": "IPv4",
  "pdp_select": "auto",
  "Current_index": "0",
  "wan_apn": "test_apn",
  "ppp_auth_mode": "pap",
  "ppp_username": "test_username",
  "ppp_passwd": "test_password",
  "dns_mode": "",
  "prefer_dns_manual": "",
  "standby_dns_manual": "",
  "ipv6_wan_apn": "test_apn",
  "ipv6_pdp_type": "IPv6",
  "ipv6_ppp_auth_mode": "chap",
  "ipv6_ppp_username": "test_username",
  "ipv6_ppp_passwd": "test_password",
  "ipv6_dns_mode": "manual",
  "ipv6_prefer_dns_manual": "2001:4860:4860::8888",
  "ipv6_standby_dns_manual": "2001:4860:4860::8844"
}
```

This objects representing your APN profile status

Object key | Descriptions
--- | ---
**`APN_config0` ~ `APN_config19`** | A dictionary of PPP profiles for IPv4 PDP that saved on device memory. This keys had correlation with `ipv6_APN_config*` with the same index. 
**`m_profile_name`** | Profile name from selected default PPP profile.
**`wan_dial`** | Dial number from selected default PPP Profile.
**`pdp_type`** | PDP type from selected default PPP Profile.
**`Current_index`** | Index of PPP profile that had been set as default.
**`wan_apn`** | Access point name for IPv4 PDP from selected default PPP Profile.
**`ppp_auth_mode`** | Authentication method from selected default PPP Profile.
**`ppp_username`** | PPP username from selected default PPP Profile.
**`ppp_passwd`** | PPP password from selected default PPP Profile.
**`dns_mode`** | DNS mode status from selected default PPP Profile.
**`prefer_dns_manual`** | Primary DNS from selected default PPP Profile.
**`standby_dns_manual`** | Fallback DNS from selected default PPP Profile.
**`ipv6_APN_config0` ~ `ipv6_APN_config19`** | A dictionary of PPP profiles for IPv6 PDP that saved on device memory. This keys had correlation with `APN_config*` with the same index. 
**`ipv6_wan_apn`** | Access point name for IPv6 PDP from selected default PPP Profile.
**`ipv6_ppp_auth_mode`** | Authentication method for IPv6 PDP from selected default PPP Profile.
**`ipv6_ppp_username`** | PPP username for IPv6 PDP from selected default PPP Profile.
**`ipv6_ppp_passwd`** | PPP password for IPv6 PDP from selected default PPP Profile.
**`ipv6_dns_mode`** | DNS mode status for IPv6 PDP from selected default PPP Profile.
**`ipv6_prefer_dns_manual`** | Primary DNS for IPv6 PDP from selected default PPP Profile.
**`ipv6_standby_dns_manual`** | Fallback DNS for IPv6 PDP from selected default PPP Profile.

### Parsing profile

The saved PPP connection profiles that stored in `APN_config` and `ipv6_APN_config` slots is concatenated from array using `($)` delimiter.

Here is an index table to identify key and value when parsing the profiles.

Index | Description
--- | ---
0 | Profile name.
1 | Access Point Name.
2 | Indicator of how this profile is created.
3 | PPP dial number.
4 | PPP Authentication method.
5 | PPP Username.
6 | PPP Password.
7 | PDP type.
8 | PDP Select?.
9 | DNS mode.
10 | Primary DNS.
11 | Fallback DNS.

## Retrieve PPP Properties

```http
POST /goform/goform_get_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html

cmd=<attributes>
```

> Response

```json
{
  "wan_dial":"*99#",
  "pdp_type": "IPv4",
  ...
}
```

Retrieve current and saved PPP properties

### Attributes

The `attributes` are the [object key](#ppp-connection-profile-object) that you want to retrieve it's value. 

## Create or Update PPP Profile

```http
POST /goform/goform_set_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html
Content-Length: 265

goformId=APN_PROC_EX&apn_action=save&apn_mode=manual&3&apn_select=manual&pdp_select=auto
profile_name=test1&wan_dial=*99%2&pdp_type=IP&index=7&wan_apn=test&ppp_auth_mode=pap&
ppp_username=test&ppp_passwd=test&dns_mode=auto
```

> Response

```json
{
  "result":"success"
}
```

In order to make a WAN connection, you must first create a PPP profile

### Form ID

`APN_PROC_EX`

### Actions

Name|Value|Required
---|---|---
**`apn_action`**|`save`|yes
**`apn_mode`**|`manual`|yes
**`pdp_select`**|`auto`|yes

### Parameters

Name|Default|Descriptions
---|---|---
**`profile_name`**|`new profile`|Profile name.
**`wan_dial`**|`*99#`|PPP dial number.
**`pdp_type`**|`IP`|PDP type, Available values are `IP` (IPv4), `IPv6` (IPv6), `IPv4v6` (IPv4 and IPv6).
**`index`**|`0`|Index of storage slot to store or update the profile. Available between `0` to `19`, 20 slot in total.
**`wan_apn`**|`internet`|The [access Point Name](https://en.wikipedia.org/wiki/Access_Point_Name) (APN) for your carrier.
**`ppp_auth_mode`**|`auto`|Authentication mode for PPP connection. Available values are `auto`, `pap`, and `chap`.
**`ppp_username`**|-|PPP username.
**`ppp_passwd`**|-|PPP password.
**`dns_mode`**|`auto`|Set DNS mode between `manual` or `auto`.
**`prefer_dns_manual`**|-|Primary DNS.
**`standby_dns_manual`**|-|Fallback DNS.

<aside class="notice">
IPv6 PPP Profile - in order to use <b>IPv6</b> or both <b>IPv4 and v6</b> you need to fill and pass these parameters
</aside>

Name|Default|Descriptions
---|---|---
**`ipv6_wan_apn`**|`internet`|The [access Point Name](https://en.wikipedia.org/wiki/Access_Point_Name) (APN) for your carrier.
**`ipv6_ppp_auth_mode`**|`auto`|Authentication mode for PPP connection. Available values are `auto`, `pap`, and `chap`.
**`ipv6_ppp_username`**|-|PPP username.
**`ipv6_ppp_passwd`**|-|PPP password.
**`ipv6_dns_mode`**|`auto`|Set DNS mode between `manual` or `auto`.
**`ipv6_prefer_dns_manual`**|-|Primary DNS.
**`ipv6_standby_dns_manual`**|-|Fallback DNS.

## Remove PPP Profile

```http
POST /goform/goform_set_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html
Content-Length: 265

goformId=APN_PROC_EX&apn_action=delete&index=7
```

> Response

```json
{
  "result":"success"
}
```

Remove PPP profile you didn't use.

### Form ID

`APN_PROC_EX`

### Actions

Name|Value|Required
---|---|---
**`apn_action`**|`delete`|yes

### Parameters

Name|Value|Required
---|---|---
**`index`**|-|Index of profile you want to remove from storage.

## Set Default Profile

```http
POST /goform/goform_set_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html
Content-Length: 265

isTest=false&goformId=APN_PROC_EX&apn_mode=manual&apn_action=set_default&set_default_flag=1&pdp_type=IP&index=6
```

> Response

```json
{
  "result":"success"
}
```

You set the default PPP profile so that your WAN connection will use said profile

### Form ID

`APN_PROC_EX`

### Actions

Name|Value|Required
---|---|---
**`apn_mode`**|`manual`|yes
**`set_default`**|`delete`|yes
**`set_default_flag`**|`1`|yes

### Parameters

Name|Default|Required
---|---|---
**`index`**|-|Index of profile you want to remove from storage.
**`pdp_type`**|`IP`|PDP type of selected profile. Available values are **IPv4** (`IP`), **IPv6** (`IPv6`), and **IPv4 *&* IPv6 combined** (`IPv4v6`)


# WAN Cellular Connection

## WAN Cellular Object

> Object

```json
{
    "connectionMode": "auto_dial",
    "autoConnectWhenRoaming": "off",
    "ppp_status":"ppp_connected",
    "realtime_rx_bytes": "1116739479",
    "realtime_rx_thrpt": "1580",
    "realtime_time": "63821",
    "realtime_tx_bytes": "99545339",
    "realtime_tx_thrpt": "3024",
    "monthly_rx_bytes": "23120021145",
    "monthly_time": "1062306",
    "monthly_tx_bytes": "2332584668"
}
```

This objects representing your WAN status

Object key | Descriptions
--- | ---
**`connectionMode`** | Current connection mode setting. 
**`autoConnectWhenRoaming`** | Status of auto connect the WAN even when roaming.
**`ppp_status`** | Current WAN connection status.
**`realtime_rx_bytes`** | Total data received since WAN connection started. Units are in Bytes.
**`realtime_rx_thrpt`** | Receive throughput speed. Units are in Bytes.
**`realtime_time`** | Total of seconds elapsed since WAN connection started.
**`realtime_tx_bytes`** | Total data transferred since WAN connection started. Units are in Bytes.
**`realtime_tx_thrpt`** | Transfer throughput speed. Units are in Bytes.
**`monthly_rx_bytes`** | Total of data received in current month. Units are in Bytes.
**`monthly_time`** | Total time since the month started. Units are in Unix Epoch Timestamp.
**`monthly_tx_bytes`** | Total of data transferred in current month.

## Retrieve WAN Properties

```http
POST /goform/goform_get_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html

cmd=<attributes>
```

> Response

```json
{
  "connectionMode":"auto_dial",
  "ppp_status":"ppp_connected",
  ...
}
```

Retrieve current WAN properties

### Attributes

The `attributes` are the [object key](#wan-cellular-object) that you want to retrieve it's value.

## Set WAN Connection Mode 

```http
POST /goform/goform_set_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html
Content-Length: 265

goformId=SET_CONNECTION_MODE&ConnectionMode=manual_dial&roam_setting_option=off
```

> Response

```json
{
  "result":"success"
}
```

You can set WAN connection mode to automatically or manually dialing. You can set auto dialing even when the device is roaming.

### Form ID

`SET_CONNECTION_MODE`

### Parameters

Name|Default|Required
---|---|---
**`ConnectionMode`**|`auto_dial`|Connection mode. Available values are `auto_dial` or `manual_dial`.
**`roam_setting_option`**|`off`|Enable auto dial when roaming. Available values are `on` or `off`.

## Make a WAN Connection

```http
POST /goform/goform_set_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html
Content-Length: 265

goformId=CONNECT_NETWORK
```

> Response

```json
{
  "result":"success"
}
```

Make a WAN connection through cellular. 
### Form ID

`CONNECT_NETWORK`
## Disconnect The WAN Connection

```http
POST /goform/goform_set_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html
Content-Length: 265

goformId=DISCONNECT_NETWORK
```

> Response

```json
{
  "result":"success"
}
```

Disconnect the cellular WAN connection. 
### Form ID

`DISCONNECT_NETWORK`

# Short Message Services

You can use this device to deliver and receive SMS.

<aside class="notice">
For country that only support sending SMS over GSM/WCDMA, you will unable to deliver message when your network is <code>Only_LTE</code> (4G Only). To change your network check <a href="#change-network-type">Change Network Type</a>
</aside>

## SMS Object

> Object

```json
{
  "messages": [
    {
      "id": "6",
      "number": "88441",
      "content": "004D0061007500200064006900620065006C0069006B0061006E0020006B0075006F0074006100200069006E007400650072006E006500740020006F006C00650068002000740065006D0061006E002F006B0065006C00750061007200670061003F000A00420061006C0061007300200053004D005300200069006E0069002000640067006E003A00200059004500530020003C006E006F006D006F0072002000740065006D0061006E002F006B0065006C00750061007200670061003E00200075006E00740075006B0020006D0069006E00740061002000700061006B0065007400200035003000300020004D0042002000520070002E003400720062002E0020000A0043006F006E0074006F0068003A0020005900450053002000300038003100320033003400350036003700380039",
      "tag": "1",
      "date": "21,05,28,14,31,29,+8",
      "draft_group_id": "",
      "received_all_concat_sms": "1",
      "concat_sms_total": "0",
      "concat_sms_received": "0"
    },
    {
      "id": "5",
      "number": "TELKOMSEL",
      "content": "004B0075006F0074006100200046006C00610073006800200041006E00640061002000740065006C00610068002000680061006200690073002E00200041006E0064006100200061006B0061006E002000640069006B0065006E0061006B0061006E0020007400610072006900660020006E006F006E002000700061006B006500740020006A0069006B0061002000730065006C00750072007500680020006B0075006F00740061002000740065006C00610068002000680061006200690073002E002000530069006C0061006B0061006E002000630065006B0020006B0075006F0074006100200069006E007400650072006E006500740020006C00610069006E006E007900610020006100740061007500200061006B007400690066006B0061006E0020006B0065006D00620061006C0069002000700061006B0065007400200041006E006400610020006400690020002A00330036003300230020006100740061007500200064006F0077006E006C006F006100640020004D007900540065006C006B006F006D00730065006C00200061007000700020006400690020007400730065006C002E006D0065002F007400730065006C",
      "tag": "1",
      "date": "21,05,28,14,31,29,+8",
      "draft_group_id": "",
      "received_all_concat_sms": "1",
      "concat_sms_total": "2",
      "concat_sms_received": "2"
    },
    {...}
  ],
  "sms_para_sca": "+6281100000",
  "sms_para_mem_store": "native",
  "sms_para_status_report": "0",
  "sms_para_validity_period": "255",
  "sms_nv_total": "100",
  "sms_sim_total": "40",
  "sms_nv_rev_total": "6",
  "sms_nv_send_total": "0",
  "sms_nv_draftbox_total": "0",
  "sms_sim_rev_total": "0",
  "sms_sim_send_total": "0",
  "sms_sim_draftbox_total": "0",
}
```

This objects representing your SMS properties.

Object key | Descriptions
--- | ---
**`messages`** | An array of message objects. 
**`messages.id`** | Message ID.
**`messages.number`** | The phone number (in [E.164](https://en.wikipedia.org/wiki/E.164) format), [Short-Code](https://en.wikipedia.org/wiki/Short_code), or [alphanumeric sender ID](https://www.twilio.com/docs/sms/send-messages#use-an-alphanumeric-sender-id) that initiated the message.
**`messages.content`** | The message content in [GSM-7](https://www.twilio.com/docs/glossary/what-is-gsm-7-character-encoding) or UCS-2/Unicode format.
**`messages.tag`** | Message status code.
**`messages.date`** | The date and time that the message was sent or received.
**`messages.draft_group_id`** | ???
**`messages.received_all_concat_sms`** | An indication that all message content are received.
**`messages.concat_sms_total`** | Total message concatenated to make the message content completed.
**`messages.concat_sms_received`** | Total message received to make the message content completed.
**`sms_para_sca`** | The [Short Message Service Center](https://en.wikipedia.org/wiki/Short_Message_service_center) (SMSC).
**`sms_para_mem_store`** | The location of message storage.
**`sms_para_status_report`** | SMS delivery report setting.
**`sms_para_validity_period`** | SMS validity period setting.
**`sms_nv_total`** | Device storage size.
**`sms_sim_total`** | SIM storage size.
**`sms_nv_rev_total`** | Total message received that stored in device memory.
**`sms_nv_send_total`** | Total message sent that stored in device memory.
**`sms_nv_draftbox_total`** | Total message draft that stored in device memory.
**`sms_sim_rev_total`** | Total message received that stored in SIM memory.
**`sms_sim_send_total`** | Total message sent that stored in SIM memory.
**`sms_sim_draftbox_total`** | Total message draft that stored in SIM memory.

## Message Status Code

The code that listed in `tag` attribute in `messages` object.

Value|Status
---|---
**`1`** | Unread received message.
**`0`** | Received message.
**`2`** | Message sent.
**`3`** | Message failed to be sent.
**`4`** | Draft.

## Retrieve Messages

```http
POST /goform/goform_get_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html
Content-Length: 265

cmd=sms_data_total&page=0&data_per_page=500&mem_store=1&tags=4&order_by=order+by+id+desc
```

> Response

```json
{
  "messages": [
    {
      "id": "22",
      "number": "88441",
      "content": "0059006F007500200068006100760065006E0027007400200072006500730070006F006E00640065006400200069006E002000740069006D00650020007700680065007400680065007200200079006F0075002000770061006E00740065006400200074006F002000730065006E006400200079006F007500720020006D00650073007300610067006500200074006F0020002B00360032003800320031003300310030003700390034003800310020002800290020006100730020006100200043006F006C006C00650063007400200044006100740061002E00200059006F007500720020006D006500730073006100670065002000770069006C006C0020006E006F0074002000620065002000640065006C006900760065007200650064002E",
      "tag": "0",
      "date": "21,05,28,20,31,30,+8",
      "draft_group_id": "",
      "received_all_concat_sms": "1",
      "concat_sms_total": "0",
      "concat_sms_received": "0"
    },
    {
      "id": "6",
      "number": "88441",
      "content": "004D0061007500200064006900620065006C0069006B0061006E0020006B0075006F0074006100200069006E007400650072006E006500740020006F006C00650068002000740065006D0061006E002F006B0065006C00750061007200670061003F000A00420061006C0061007300200053004D005300200069006E0069002000640067006E003A00200059004500530020003C006E006F006D006F0072002000740065006D0061006E002F006B0065006C00750061007200670061003E00200075006E00740075006B0020006D0069006E00740061002000700061006B0065007400200035003000300020004D0042002000520070002E003400720062002E0020000A0043006F006E0074006F0068003A0020005900450053002000300038003100320033003400350036003700380039",
      "tag": "0",
      "date": "21,05,28,14,31,29,+8",
      "draft_group_id": "",
      "received_all_concat_sms": "1",
      "concat_sms_total": "0",
      "concat_sms_received": "0"
    },
    {...}
  ]
}
```

Retrieve messages

### Attributes

`sms_data_total`

### Parameters

Name|Default|Descriptions
---|---|---
**`mem_store`**|`1`|Message storage filter. Available values are **SIM** (`0`) and **Device** (`1`).
**`tags`**|`10`|[Message status code](#message-status-code) filter. You can put value of `10` to retrieve all messages.
**`page`**|`0`|Page index.
**`data_per_page`**|`500`|The total of messages presented per pages.
**`order_by`**|`order+by+id+desc`|Sort the data by values. Available order are by **ID** (`order+by+id+asc` *or* `order+by+id+desc`) or **date** (`order+by+date+asc` *or* `order+by+date+desc`)

## Retrieve Messaging Properties

```http
POST /goform/goform_get_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html
Content-Length: 265

cmd=sms_capacity_info
```

> Response

```json
{
  "sms_nv_total": "100",
  "sms_sim_total": "40",
  "sms_nv_rev_total": "37",
  "sms_nv_send_total": "1",
  "sms_nv_draftbox_total": "2",
  "sms_sim_rev_total": "0",
  "sms_sim_send_total": "0",
  "sms_sim_draftbox_total": "0"
}
```

Retrieve the messaging properties

### Attributes

`sms_capacity_info` to retrieve message capacity info or  the [object key](#sms-object) that you want to retrieve it's value. 


## Send Message

```http
POST /goform/goform_set_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html
Content-Length: 265

goformId=SEND_SMS&notCallback=true&Number=+6285157458688&sms_time=21;06;01;11;46;56;+7&MessageBody=0074006500730074&ID=-1&encode_type=GSM7_default
```

> Response

```json
{
  "result": "success"
}
```

Send messages

### Form ID

`SEND_SMS` to send the messages.  
`SAVE_SMS` Save as draft.

### Parameters

Name|Default|Descriptions
---|---|---
`Number`|-|The recipient number in international format. eg: `+1854xxxxx`
`sms_time`|-|The time when message sent.
`MessageBody`|-|The message content in [GSM-7](https://www.twilio.com/docs/glossary/what-is-gsm-7-character-encoding) character encoding.
`ID`|`-1`|Auto increment ID.
`encode_type`|`GSM7_default`|Message content encoding. Available values are **GSM-7** (`GSM7_default`) or **UCS-2/Unicode** (`UNICODE`)

<aside class="warning">
Make sure to use appropriate <b><code>encode_type</code></b> with the message content you are sending, otherwise the message sent will not displayed properly in recipient device. Basic rule of thumb is to use available character in older brick <i>(dumb)</i> T9 phone.
</aside>

## Delete Message

```http
POST /goform/goform_set_cmd_process HTTP/1.1
Host: 192.168.0.1
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: http://192.168.0.1/index.html
Content-Length: 265

goformId=DELETE_SMS&notCallback=true&msg_id=1
```

> Response

```json
{
  "result": "success"
}
```

Delete the selected message

### Form ID

`DELETE_SMS`

### Parameters

Name|Default|Descriptions
---|---|---
`msg_id`|-|The message ID you want to delete.

## Messaging System Configuration

