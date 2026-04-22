# Core Tables — Data Model Reference

> **Environment**: Dataverse (D365 Customer Service + Field Service)  
> **Generated**: 2026-04-22  
> **Publisher prefix (custom)**: `bshcs_`

---

## Table of Contents

1. [contact — Person / Customer](#1-contact--person--customer)
2. [account — Customer / Company](#2-account--customer--company)
3. [msdyn_customerasset — Customer Asset](#3-msdyn_customerasset--customer-asset)
4. [incident — Case / Service Request](#4-incident--case--service-request)
5. [bshcs_billingfunctionallocation — Billing Functional Location](#5-bshcs_billingfunctionallocation--billing-functional-location)
6. [msdyn_functionallocation — Functional Location](#6-msdyn_functionallocation--functional-location)
7. [Relationship Diagram](#7-relationship-diagram)

---

## 1. `contact` — Person / Customer

> **Type**: OOB Core table | **Ownership**: User  
> **Description**: Person with whom a business unit has a relationship, such as customer, supplier, and colleague.

| Column | Type | Required | Notes |
|--------|------|----------|-------|
| `contactid` | GUID | PK | Primary key |
| `lastname` | NVARCHAR(50) | ✅ NOT NULL | |
| `firstname` | NVARCHAR(50) | | |
| `middlename` | NVARCHAR(50) | | |
| `fullname` | NVARCHAR(160) | | Computed |
| `salutation` | NVARCHAR(100) | | |
| `suffix` | NVARCHAR(10) | | |
| `jobtitle` | NVARCHAR(100) | | |
| `department` | NVARCHAR(100) | | |
| `emailaddress1` | EMAIL(100) | | |
| `emailaddress2` | EMAIL(100) | | |
| `emailaddress3` | EMAIL(100) | | |
| `telephone1` | PHONE(50) | | Business phone |
| `telephone2` | PHONE(50) | | |
| `telephone3` | PHONE(50) | | |
| `mobilephone` | PHONE(50) | | |
| `fax` | NVARCHAR(50) | | |
| `websiteurl` | URL(200) | | |
| `birthdate` | DATE ONLY | | |
| `anniversary` | DATE ONLY | | |
| `gendercode` | CHOICE | | Male (1), Female (2) |
| `familystatuscode` | CHOICE | | Single (1), Married (2), Divorced (3), Widowed (4) |
| `description` | MULTILINE TEXT | | |
| `accountid` | LOOKUP → **account** | | Parent company |
| `parentcustomerid` | CUSTOMER → account, contact | | |
| `parentcontactid` | LOOKUP → **contact** | | Hierarchical parent contact |
| `masterid` | LOOKUP → **contact** | | After merge |
| `merged` | BIT | | |
| `preferredcontactmethodcode` | CHOICE | | Any/Email/Phone/Fax/Mail |
| `donotbulkemail` | BIT | | |
| `donotemail` | BIT | | |
| `donotfax` | BIT | | |
| `donotphone` | BIT | | |
| `donotpostalmail` | BIT | | |
| `donotbulkpostalmail` | BIT | | |
| `slaid` | LOOKUP → sla | | |
| `slainvokedid` | LOOKUP → sla | | |
| `defaultpricelevelid` | LOOKUP → pricelevel | | |
| `originatingleadid` | LOOKUP → lead | | |
| `preferredequipmentid` | LOOKUP → equipment | | |
| `preferredserviceid` | LOOKUP → service | | |
| `preferredsystemuserid` | LOOKUP → systemuser | | |
| `transactioncurrencyid` | LOOKUP → transactioncurrency | | |
| `msdyn_gdproptout` | BIT | | GDPR opt-out |
| `msdyn_isminor` | BIT | | GDPR — minor |
| `msdyn_isminorwithparentalconsent` | BIT | | GDPR |
| `msdyn_contactkpiid` | LOOKUP → msdyn_contactkpiitem | | |
| `msdyn_segmentid` | LOOKUP → msdyn_segment | | |
| `msdyn_primarytimezone` | TIMEZONE(INT) | | |
| `msdyn_orgchangestatus` | CHOICE | | No Feedback (0), Not at Company (1), Ignore (2) |
| `msdyn_decisioninfluencetag` | CHOICE | | Decision maker/Influencer/Blocker/Unknown |
| `adx_identity_username` | NVARCHAR(100) | | Power Pages portal |
| `adx_identity_emailaddress1confirmed` | BIT | | Portal |
| `adx_identity_lastsuccessfullogin` | DATETIME | | Portal |
| `adx_identity_lockoutenabled` | BIT | | Portal |
| `adx_identity_logonenabled` | BIT | | Portal |
| `adx_identity_twofactorenabled` | BIT | | Portal |
| `adx_identity_mobilephoneconfirmed` | BIT | | Portal |
| `statecode` | STATE(INT) | | Active (0), Inactive (1) |
| `statuscode` | STATUS(INT) | | Active (1), Inactive (2) |
| `ownerid` | OWNER | | |
| `owningbusinessunit` | LOOKUP → businessunit | | |
| `createdby` | LOOKUP → systemuser | | |
| `createdon` | DATETIME | | |
| `modifiedby` | LOOKUP → systemuser | | |
| `modifiedon` | DATETIME | | |
| `versionnumber` | BIGINT | | Row version |

### Address Blocks (contact)

Three address blocks: `address1_*`, `address2_*`, `address3_*` — each containing:

| Sub-field | Type |
|-----------|------|
| `_addressid` | GUID |
| `_addresstypecode` | CHOICE (Bill To / Ship To / Primary / Other) |
| `_name` | NVARCHAR(200) |
| `_line1`, `_line2`, `_line3` | NVARCHAR(250) |
| `_city` | NVARCHAR(80) |
| `_stateorprovince` | NVARCHAR(50) |
| `_postalcode` | NVARCHAR(20) |
| `_country` | NVARCHAR(80) |
| `_county` | NVARCHAR(50) |
| `_telephone1/2/3` | PHONE(50) |
| `_fax` | NVARCHAR(50) |
| `_latitude` / `_longitude` | FLOAT |
| `_composite` | MULTILINE TEXT |

---

## 2. `account` — Customer / Company

> **Type**: OOB + Custom extensions (`bshcs_`) | **Ownership**: User  
> **Description**: Business that represents a customer or potential customer.

| Column | Type | Required | Notes |
|--------|------|----------|-------|
| `accountid` | GUID | PK | Primary key |
| `name` | NVARCHAR(160) | ✅ NOT NULL | Company/account name |
| `accountnumber` | NVARCHAR(20) | | Auto or manual |
| `primarycontactid` | LOOKUP → **contact** | | |
| `parentaccountid` | LOOKUP → **account** | | Account hierarchy |
| `masterid` | LOOKUP → **account** | | After merge |
| `merged` | BIT | | |
| `emailaddress1/2/3` | EMAIL(100) | | |
| `telephone1/2/3` | PHONE(50) | | |
| `fax` | NVARCHAR(50) | | |
| `websiteurl` | URL(200) | | |
| `description` | MULTILINE TEXT | | |
| `accountcategorycode` | CHOICE | | Preferred Customer (1), Standard (2) |
| `customertypecode` | CHOICE | | Customer/Competitor/Partner/Prospect/etc. |
| `industrycode` | CHOICE | | Industry classification |
| `ownershipcode` | CHOICE | | Public/Private/Subsidiary/Other |
| `numberofemployees` | INT | | |
| `revenue` | MONEY | | |
| `creditlimit` | MONEY | | |
| `creditonhold` | BIT | | |
| `paymenttermscode` | CHOICE | | Net 30, 2% 10 Net 30, Net 45, Net 60 |
| `preferredcontactmethodcode` | CHOICE | ✅ NOT NULL | Any/Email/Phone/Fax/Mail |
| `slaid` | LOOKUP → sla | | |
| `slainvokedid` | LOOKUP → sla | | |
| `defaultpricelevelid` | LOOKUP → pricelevel | | |
| `territoryid` | LOOKUP → territory | | |
| `originatingleadid` | LOOKUP → lead | | |
| `transactioncurrencyid` | LOOKUP → transactioncurrency | | |
| **`bshcs_accounttype`** | CHOICE | ✅ NOT NULL | **Individual (563910000), Corporate (563910001)** |
| `bshcs_firstname` | NVARCHAR(100) | | Individual — first name |
| `bshcs_lastname` | NVARCHAR(100) | | Individual — last name |
| `bshcs_corpname` | NVARCHAR(100) | | Corporate — primary name |
| `bshcs_corpname2` | NVARCHAR(100) | | Corporate — secondary name |
| `bshcs_taxnumber` | NVARCHAR(100) | | Tax / fiscal ID |
| **`bshcs_mainaddressid`** | LOOKUP → **msdyn_functionallocation** | | Main address as functional location |
| `bshcs_originatingbusinessunitid` | LOOKUP → businessunit | | |
| `msdyn_billingaccount` | LOOKUP → **account** | | Billing account reference |
| `msdyn_salestaxcode` | LOOKUP → msdyn_taxcode | | |
| `msdyn_taxexempt` | BIT | | |
| `msdyn_taxexemptnumber` | NVARCHAR(20) | | |
| `msdyn_serviceterritory` | LOOKUP → territory | | Field Service territory |
| `msdyn_preferredresource` | LOOKUP → bookableresource | | Field Service |
| `msdyn_workhourtemplate` | LOOKUP → msdyn_workhourtemplate | | |
| `msdyn_travelchargetype` | CHOICE | | Hourly/Mileage/Fixed/None |
| `msdyn_travelcharge` | MONEY | | |
| `msdyn_workorderinstructions` | MULTILINE TEXT | | |
| `msdyn_gdproptout` | BIT | | |
| `msdyn_segmentid` | LOOKUP → msdyn_segment | | |
| `msdyn_primarytimezone` | TIMEZONE(INT) | | |
| `statecode` | STATE(INT) | | Active (0), Inactive (1) |
| `statuscode` | STATUS(INT) | | Active (1), Inactive (2) |
| `ownerid` | OWNER | | |
| `owningbusinessunit` | LOOKUP → businessunit | | |
| `createdby` | LOOKUP → systemuser | | |
| `createdon` | DATETIME | | |
| `modifiedby` | LOOKUP → systemuser | | |
| `modifiedon` | DATETIME | | |
| `versionnumber` | BIGINT | | |

### Address Blocks (account)

Two address blocks: `address1_*`, `address2_*` — same structure as contact address blocks.

---

## 3. `msdyn_customerasset` — Customer Asset

> **Type**: Field Service OOB + Custom extensions (`bshcs_`) | **Ownership**: User  
> **Description**: Represents a physical asset installed at a customer location.

| Column | Type | Required | Notes |
|--------|------|----------|-------|
| `msdyn_customerassetid` | GUID | PK | Primary key |
| `msdyn_name` | NVARCHAR(100) | | Asset name |
| **`msdyn_account`** | LOOKUP → **account** | ✅ NOT NULL | Owner account |
| **`msdyn_product`** | LOOKUP → product | ✅ NOT NULL | Product/model |
| `msdyn_parentasset` | LOOKUP → **msdyn_customerasset** | | Asset hierarchy — parent |
| `msdyn_masterasset` | LOOKUP → **msdyn_customerasset** | | Master asset (after exchange/merge) |
| **`msdyn_functionallocation`** | LOOKUP → **msdyn_functionallocation** | | Installation location |
| `msdyn_customerassetcategory` | LOOKUP → msdyn_customerassetcategory | | Category classification |
| `msdyn_assettag` | NVARCHAR(100) | | Physical tag / barcode |
| `msdyn_deviceid` | NVARCHAR(100) | | IoT device ID |
| `msdyn_registrationstatus` | CHOICE | | Unknown (192350000), Unregistered (192350001), In Progress (192350002), Registered (192350003), Error (192350004) |
| `msdyn_alert` | BIT | | IoT alert active |
| `msdyn_alertcount` | INT | | |
| `msdyn_lastalerttime` | DATETIME | | |
| `msdyn_lastcommandsent` | LOOKUP → msdyn_iotdevicecommand | | |
| `msdyn_lastcommandsenttime` | DATE ONLY | | |
| `msdyn_latitude` | FLOAT | | GPS |
| `msdyn_longitude` | FLOAT | | GPS |
| `msdyn_manufacturingdate` | DATE ONLY | | |
| `msdyn_workorderproduct` | LOOKUP → msdyn_workorderproduct | | Associated work order product |
| **`bshcs_installationdate`** | DATE ONLY | | Date installed at location |
| `bshcs_purchasedate` | DATE ONLY | | |
| `bshcs_purchasedprice` | MONEY | | |
| `bshcs_manufacturingdate` | NVARCHAR(100) | | Manufacturing date (text) |
| `bshcs_warrantystartdate` | DATE ONLY | | Warranty start |
| `bshcs_fdnumber` | INT | | FD reference number |
| `bshcs_consecutivenumber` | INT | | Consecutive number |
| `transactioncurrencyid` | LOOKUP → transactioncurrency | | |
| `statecode` | STATE(INT) | | Active (0), Inactive (1) |
| `statuscode` | STATUS(INT) | | Active (1), Created (563910001), Verified (563910003), Cancelled (563910002), Exchanged (563910004), Refurbished (563910005), Scrapped (563910006), Inactive (2) |
| `ownerid` | OWNER | | |
| `owningbusinessunit` | LOOKUP → businessunit | | |
| `createdby` | LOOKUP → systemuser | | |
| `createdon` | DATETIME | | |
| `modifiedby` | LOOKUP → systemuser | | |
| `modifiedon` | DATETIME | | |
| `versionnumber` | BIGINT | | |

### Asset Lifecycle Status Values

| Status | Code | Meaning |
|--------|------|---------|
| Active | 1 | In active use |
| Created | 563910001 | Registered but not yet verified |
| Verified | 563910003 | Verified and operational |
| Cancelled | 563910002 | Cancelled before activation |
| Exchanged | 563910004 | Replaced by another asset |
| Refurbished | 563910005 | Sent for refurbishment |
| Scrapped | 563910006 | End of life |
| Inactive | 2 | Deactivated |

---

## 4. `incident` — Case / Service Request

> **Type**: Customer Service OOB + Custom extensions (`bshcs_`) | **Ownership**: User  
> **Description**: Service request case — repairs, complaints, and work orders.

| Column | Type | Required | Notes |
|--------|------|----------|-------|
| `incidentid` | GUID | PK | Primary key |
| `ticketnumber` | NVARCHAR(100) | | Auto-generated case number |
| `title` | NVARCHAR(200) | ✅ NOT NULL | Case title |
| `customerid` | CUSTOMER → account, contact | | Primary customer |
| `accountid` | LOOKUP → **account** | | |
| `contactid` | LOOKUP → **contact** | | |
| `primarycontactid` | LOOKUP → **contact** | | |
| `responsiblecontactid` | LOOKUP → **contact** | | |
| **`casetypecode`** | CHOICE | ✅ NOT NULL | **Repair (1), Complaint (2)** |
| `caseorigincode` | CHOICE | | Phone (1), Email (2), Web (3), Facebook (2483), Twitter (3986), IoT (700610000) |
| `prioritycode` | CHOICE | | High (1), Normal (2), Low (3) |
| `severitycode` | CHOICE | | Default Value (1) |
| `customersatisfactioncode` | CHOICE | | Very Satisfied (5) → Very Dissatisfied (1) |
| `description` | MULTILINE TEXT | | |
| `emailaddress` | EMAIL(100) | | |
| `productserialnumber` | NVARCHAR(100) | | |
| `productid` | LOOKUP → product | | |
| `parentcaseid` | LOOKUP → **incident** | | Case hierarchy |
| `existingcase` | LOOKUP → **incident** | | |
| `masterid` | LOOKUP → **incident** | | After merge |
| `merged` | BIT | | |
| `isescalated` | BIT | | |
| `escalatedon` | DATETIME | | |
| `followupby` | DATE ONLY | | |
| `entitlementid` | LOOKUP → entitlement | | |
| `slaid` | LOOKUP → sla | | |
| `slainvokedid` | LOOKUP → sla | | |
| `firstresponsebykpiid` | LOOKUP → slakpiinstance | | |
| `resolvebykpiid` | LOOKUP → slakpiinstance | | |
| `firstresponseslastatus` | CHOICE | | In Progress / Nearing Noncompliance / Succeeded / Noncompliant |
| `resolvebyslastatus` | CHOICE | | In Progress / Nearing Noncompliance / Succeeded / Noncompliant |
| `resolveby` | DATETIME | | SLA resolve by |
| `kbarticleid` | LOOKUP → kbarticle | | |
| `subjectid` | LOOKUP → subject | | |
| `contractid` | LOOKUP → contract | | |
| `contractdetailid` | LOOKUP → contractdetail | | |
| `contractservicelevelcode` | CHOICE | | Gold (1), Silver (2), Bronze (3) |
| `servicestage` | CHOICE | | Identify (0), Research (1), Resolve (2) |
| `socialprofileid` | LOOKUP → socialprofile | | |
| **`msdyn_incidenttype`** | LOOKUP → msdyn_incidenttype | ✅ NOT NULL | Incident/work order type |
| `msdyn_functionallocation` | LOOKUP → **msdyn_functionallocation** | | Service location |
| `msdyn_iotalert` | LOOKUP → msdyn_iotalert | | IoT alert origin |
| `msdyn_casesentiment` | CHOICE | | N/A to Very positive (AI-driven) |
| `msdyn_copilotengaged` | BIT | | Copilot used on this case |
| `msdyn_aiagentstatus` | LOOKUP → msdyn_aiagentstatus | | |
| **`bshcs_customerassetid`** | LOOKUP → **msdyn_customerasset** | ✅ NOT NULL | Asset linked to case |
| `bshcs_caller` | CUSTOMER → account, contact | | Who raised the call |
| `bshcs_duedate` | DATE ONLY | | |
| `bshcs_onsitecontactname` | NVARCHAR(100) | | On-site contact |
| `bshcs_onsitecontactemail` | EMAIL(100) | | |
| `bshcs_onsitecontactphone` | PHONE(100) | | |
| `bshcs_firstaidprovidedon` | DATETIME | | First aid timestamp |
| `bshcs_firstaidautoclose` | BIT | | Auto-close after first aid |
| `bshcs_repairbatchcase` | LOOKUP → bshcs_repairbatch | | Batch repair reference |
| `bshcs_qt_json` | MULTILINE TEXT | | Questionnaire payload (JSON) |
| `transactioncurrencyid` | LOOKUP → transactioncurrency | | |
| `statecode` | STATE(INT) | | **Active (0), Resolved (1), Cancelled (2)** |
| `statuscode` | STATUS(INT) | | See table below |
| `ownerid` | OWNER | | |
| `owningbusinessunit` | LOOKUP → businessunit | | |
| `createdby` | LOOKUP → systemuser | | |
| `createdon` | DATETIME | | |
| `modifiedby` | LOOKUP → systemuser | | |
| `modifiedon` | DATETIME | | |
| `versionnumber` | BIGINT | | |

### Case Status Values

| Status | Code | State |
|--------|------|-------|
| New | 563910009 | Active |
| In Progress | 1 | Active |
| On Hold | 2 | Active |
| On hold - Customer | 563910001 | Active |
| On hold - Internal | 563910003 | Active |
| Waiting for Details | 3 | Active |
| Researching | 4 | Active |
| First Aid given | 563910002 | Active |
| First Aid Successful | 563910005 | Active |
| WO In Progress | 563910004 | Active |
| WO Completed | 563910006 | Active |
| WO Cancellation | 563910007 | Active |
| Convert to Sales | 563910008 | Active |
| Problem Solved | 5 | Resolved |
| Information Provided | 1000 | Resolved |
| First Aid Successful | 563910005 | Resolved |
| Cancelled | 6 | Cancelled |
| Cancellation due to customer call | 563910010 | Cancelled |
| Merged | 2000 | Cancelled |

---

## 5. `bshcs_billingfunctionallocation` — Billing Functional Location

> **Type**: Custom table (`bshcs_`) | **Ownership**: User  
> **Description**: Junction table assigning billing partner roles to functional locations per account. Models the SAP partner function pattern.

| Column | Type | Required | Notes |
|--------|------|----------|-------|
| `bshcs_billingfunctionallocationid` | GUID | PK | Primary key |
| `bshcs_name` | NVARCHAR(850) | | Record name (computed) |
| **`bshcs_accountid`** | LOOKUP → **account** | ✅ NOT NULL | Account with the billing role |
| **`bshcs_functionallocationid`** | LOOKUP → **msdyn_functionallocation** | ✅ NOT NULL | Location assigned the role |
| **`bshcs_partnerfunction`** | CHOICE | ✅ NOT NULL | **Bill-to (0), Sold-to (1), Payer (2)** |
| `statecode` | STATE(INT) | | Active (0), Inactive (1) |
| `statuscode` | STATUS(INT) | | Active (1), Inactive (2) |
| `ownerid` | OWNER | | |
| `owningbusinessunit` | LOOKUP → businessunit | | |
| `createdby` | LOOKUP → systemuser | | |
| `createdon` | DATETIME | | |
| `modifiedby` | LOOKUP → systemuser | | |
| `modifiedon` | DATETIME | | |
| `versionnumber` | BIGINT | | |

### Partner Function Values

| Value | Code | Description |
|-------|------|-------------|
| Bill-to | 0 | Party to whom invoice is sent |
| Sold-to | 1 | Party who places the order |
| Payer | 2 | Party who settles the invoice |

> ⚠️ **Design note**: This table implements a many-to-many relationship between `account` and `msdyn_functionallocation` with a typed role. One functional location can have multiple billing roles assigned to different accounts.

---

## 6. `msdyn_functionallocation` — Functional Location

> **Type**: Field Service OOB + Custom extensions (`bshcs_`) | **Ownership**: User  
> **Description**: Hierarchical physical location (e.g., building, floor, unit) where assets are installed and services are performed.

| Column | Type | Required | Notes |
|--------|------|----------|-------|
| `msdyn_functionallocationid` | GUID | PK | Primary key |
| `msdyn_name` | NVARCHAR(60) | ✅ NOT NULL | Location name |
| `msdyn_shortname` | NVARCHAR(100) | | Short label |
| `msdyn_parentfunctionallocation` | LOOKUP → **msdyn_functionallocation** | | Hierarchy — parent location |
| `msdyn_functionallocationtype` | LOOKUP → msdyn_functionallocationtype | | Type classification |
| `msdyn_sequence` | INT | | Display/sort order within parent |
| `msdyn_locationopendate` | DATE ONLY | | Date location became active |
| `msdyn_costcenter` | NVARCHAR(100) | | Cost center code |
| `msdyn_emailaddress` | EMAIL(100) | | |
| `msdyn_primarytimezone` | TIMEZONE(INT) | | |
| `msdyn_addressname` | NVARCHAR(250) | | Address label |
| `msdyn_address1` | NVARCHAR(250) | | Street address line 1 |
| `msdyn_address2` | NVARCHAR(250) | | Street address line 2 |
| `msdyn_address3` | NVARCHAR(250) | | Street address line 3 |
| `msdyn_city` | NVARCHAR(80) | | |
| `msdyn_stateorprovince` | NVARCHAR(50) | | |
| `msdyn_postalcode` | NVARCHAR(20) | | |
| `msdyn_country` | NVARCHAR(80) | | |
| `msdyn_latitude` | FLOAT | | GPS |
| `msdyn_longitude` | FLOAT | | GPS |
| **`bshcs_accountid`** | LOOKUP → **account** | | Owner / responsible account |
| `bshcs_housenumber` | NVARCHAR(100) | | House/building number |
| `bshcs_flatapartment` | NVARCHAR(100) | | Flat or apartment identifier |
| `bshcs_addresscomposite` | MULTILINE TEXT | | Computed full address |
| `statecode` | STATE(INT) | | Active (0), Inactive (1) |
| `statuscode` | STATUS(INT) | | Active (1), Inactive (2) |
| `ownerid` | OWNER | | |
| `owningbusinessunit` | LOOKUP → businessunit | | |
| `createdby` | LOOKUP → systemuser | | |
| `createdon` | DATETIME | | |
| `modifiedby` | LOOKUP → systemuser | | |
| `modifiedon` | DATETIME | | |
| `versionnumber` | BIGINT | | |

---

## 7. Relationship Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                          account                                    │
│  bshcs_accounttype: Individual | Corporate                          │
│  bshcs_mainaddressid ──────────────────────────────────────────────►│
└───────────────────────┬──────────────────────────────────────┬──────┘
                        │ primarycontactid                     │ bshcs_accountid / msdyn_billingaccount
                        ▼                                      ▼
               ┌─────────────────┐              ┌─────────────────────────────┐
               │    contact      │              │   msdyn_functionallocation  │
               │                 │              │   (hierarchy via parent)    │
               └─────────────────┘              └──────────┬──────────────────┘
                                                           │         ▲
                                        msdyn_functionallocation     │ parentfunctionallocation (self)
                                                           │
                     ┌─────────────────────────────────────┤
                     │                                     │
                     ▼                                     ▼
        ┌────────────────────────┐       ┌──────────────────────────────────┐
        │   msdyn_customerasset  │       │  bshcs_billingfunctionallocation │
        │   msdyn_account ──────►│       │  bshcs_accountid ───────────────►│
        │   msdyn_product        │       │  bshcs_functionallocationid ────►│
        │   msdyn_functionalloc  │       │  bshcs_partnerfunction:          │
        │   (parent/master self) │       │    Bill-to | Sold-to | Payer     │
        └──────────┬─────────────┘       └──────────────────────────────────┘
                   │ bshcs_customerassetid
                   ▼
        ┌──────────────────────┐
        │       incident       │
        │  customerid ────────►│ account / contact
        │  msdyn_incidenttype  │
        │  msdyn_functionalloc ├──► msdyn_functionallocation
        │  bshcs_caller        │
        └──────────────────────┘
```

### Key Relationships Summary

| From | Column | To | Cardinality | Notes |
|------|--------|----|-------------|-------|
| `contact` | `accountid` | `account` | N:1 | Contact belongs to company |
| `account` | `primarycontactid` | `contact` | 1:1 | Main contact |
| `account` | `bshcs_mainaddressid` | `msdyn_functionallocation` | N:1 | Address modelled as FL |
| `account` | `msdyn_billingaccount` | `account` | N:1 | Separate billing account |
| `account` | `parentaccountid` | `account` | N:1 (self) | Account hierarchy |
| `msdyn_functionallocation` | `msdyn_parentfunctionallocation` | `msdyn_functionallocation` | N:1 (self) | Location hierarchy |
| `msdyn_functionallocation` | `bshcs_accountid` | `account` | N:1 | Owner account |
| `msdyn_customerasset` | `msdyn_account` | `account` | N:1 | **Required** |
| `msdyn_customerasset` | `msdyn_product` | `product` | N:1 | **Required** |
| `msdyn_customerasset` | `msdyn_functionallocation` | `msdyn_functionallocation` | N:1 | Installation location |
| `msdyn_customerasset` | `msdyn_parentasset` | `msdyn_customerasset` | N:1 (self) | Asset hierarchy |
| `bshcs_billingfunctionallocation` | `bshcs_accountid` | `account` | N:1 | **Required** |
| `bshcs_billingfunctionallocation` | `bshcs_functionallocationid` | `msdyn_functionallocation` | N:1 | **Required** |
| `incident` | `bshcs_customerassetid` | `msdyn_customerasset` | N:1 | **Required** |
| `incident` | `customerid` | `account` / `contact` | N:1 | Polymorphic |
| `incident` | `msdyn_functionallocation` | `msdyn_functionallocation` | N:1 | Service location |
| `incident` | `parentcaseid` | `incident` | N:1 (self) | Case hierarchy |

---

*Generated by GitHub Copilot CLI — Solution Architect Agent*
