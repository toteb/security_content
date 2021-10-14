---
title: "Non Firefox Process Access Firefox Profile Dir"
excerpt: "Credentials from Web Browsers"
categories:
  - Endpoint
last_modified_at: 2021-09-15
toc: true
toc_label: ""
tags:
  - Anomaly
  - T1555.003
  - Credentials from Web Browsers
  - Credential Access
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
  - Endpoint
  - Exploitation
---



[Try in Splunk Security Cloud](https://www.splunk.com/en_us/cyber-security.html){: .btn .btn--success}

#### Description

This search is to detect an anomaly event of non-firefox process accessing the files in  profile folder. This folder contains all the sqlite database of the firefox browser related to users login, history, cookies and etc. Most of the RAT, trojan spy as well as FIN7 jssloader try to parse the those sqlite database to collect information on the compromised host. This SACL Event (4663) need to be enabled to tthe firefox profile directory to be eable to use this. Since you monitoring this access to the folder a noise coming from firefox need to be filter and also sqlite db browser and explorer .exe to make this detection more stable.

- **Type**: Anomaly
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud
- **Datamodel**: [Endpoint](https://docs.splunk.com/Documentation/CIM/latest/User/Endpoint)
- **Last Updated**: 2021-09-15
- **Author**: Teoderick Contreras, Splunk
- **ID**: e6fc13b0-1609-11ec-b533-acde48001122


#### ATT&CK

| ID          | Technique   | Tactic         |
| ----------- | ----------- | -------------- |
| [T1555.003](https://attack.mitre.org/techniques/T1555/003/) | Credentials from Web Browsers | Credential Access |


#### Search

```
`wineventlog_security` EventCode=4663 NOT (process_name IN ("*\\firefox.exe", "*\\explorer.exe", "*sql*")) Object_Name="*\\AppData\\Roaming\\Mozilla\\Firefox\\Profiles*" 
| stats count min(_time) as firstTime max(_time) as lastTime by Object_Name Object_Type process_name Access_Mask Accesses process_id EventCode dest user 
| `security_content_ctime(firstTime)` 
| `security_content_ctime(lastTime)` 
| `non_firefox_process_access_firefox_profile_dir_filter`
```

#### Associated Analytic Story
* [FIN7](/stories/fin7)


#### How To Implement
To successfully implement this search, you must ingest Windows Security Event logs and track event code 4663. For 4663, enable &#34;Audit Object Access&#34; in Group Policy. Then check the two boxes listed for both &#34;Success&#34; and &#34;Failure.&#34;

#### Required field
* _time
* Object_Name
* Object_Type
* process_name
* Access_Mask
* Accesses
* process_id
* EventCode
* dest
* user


#### Kill Chain Phase
* Exploitation


#### Known False Positives
other browser not listed related to firefox may catch by this rule.



#### RBA

| Risk Score  | Impact      | Confidence   | Message      |
| ----------- | ----------- |--------------|--------------|
| 35.0 | 50 | 70 | a non firefox browser process $process_name$ accessing $Object_Name$ |



#### Reference


#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [`replay.py`](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)

* [https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/malware/fin7/fin7_sacl/security.log](https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/malware/fin7/fin7_sacl/security.log)



[*source*](https://github.com/splunk/security_content/tree/develop/detections/endpoint/non_firefox_process_access_firefox_profile_dir.yml) \| *version*: **1**