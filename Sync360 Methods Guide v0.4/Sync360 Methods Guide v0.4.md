![](_page_0_Picture_0.jpeg)

ProServ

**Prepared By:**

AN

# Sync360 Script Methods Guide

v.0.4

This document and its supporting materials are proprietary and confidential. The information may not be disclosed to a third party without the express permission of HSO.

![](_page_1_Picture_1.jpeg)

#### **Contributors**

| Name              | Role | Company |
|-------------------|------|---------|
| Alexandra Petrova |      |         |
| Alexandra Petrova |      |         |
| Alexander Novikov |      |         |

#### **Revision History**

| <b>Version</b> | <b>Section Content Change</b>                                                                                                                     | <b>Date</b> |
|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------|-------------|
| 0.1            |                                                                                                                                                   | 03/23/2017  |
| 0.2            |                                                                                                                                                   | 06/08/2018  |
| 0.3            | Minor updates.                                                                                                                                    | 06/01/2022  |
| 0.4            | Added Appendix section and Additional Notes<br>$\bullet$<br>section.<br>Added a note regarding linking employee and<br>$\bullet$<br>user records. | 08/08/2024  |

![](_page_2_Picture_1.jpeg)

# **Table of Contents**

| Introduction                                                  | Details |
|---------------------------------------------------------------|---------|
| Sync360 methods working with connectors                       |         |
| Sync360 methods working with Microsoft Dynamics CRM connector |         |
| AssociateEntities                                             |         |
| DisassociateEntities                                          |         |
| AssociateUserRole                                             |         |
| DisassociateUserRole                                          |         |
| AddMembersTeam                                                |         |
| RemoveMembersTeam                                             |         |
| RunWorkflow                                                   |         |
| GetOptionSetValueLabel                                        |         |
| GetOptionSetValueCode                                         |         |
| GetEntityMetadata                                             |         |
| GetAllEntitiesMetadata                                        |         |
| GetAllEntitiesList                                            |         |
| AddListMember                                                 |         |
| RemoveListMember                                              |         |
| AddItemCampaignActivity                                       |         |
| AddItemCampaign                                               |         |
| AddPrivilegesRole                                             |         |
| AddSubstituteProduct                                          |         |
| DeleteById                                                    |         |
| SelectById                                                    |         |
| Merge15                                                       |         |
| RetrieveAuditDetailsRequest                                   |         |
| RetrieveRecordChangeHistoryRequest                            |         |
| SelectActivityParties                                         |         |
| GetEntityNameByObjectTypeCode                                 |         |
| GetObjectTypeCodeByEntityName                                 |         |
| ExecuteSendEmailRequest                                       |         |
| DeliverPromoteEmailRequest                                    |         |
| RunAction                                                     |         |
| ExecuteAction                                                 |         |
| Appendix                                                      |         |
| Additional Notes                                              |         |

![](_page_3_Picture_1.jpeg)

Note 1 ...................................

![](_page_4_Picture_0.jpeg)

![](_page_4_Picture_1.jpeg)

ProServ

<span id="page-4-0"></span>![](_page_4_Picture_2.jpeg)

This document describes methods of the Sync360 which work with CRM.

![](_page_5_Picture_1.jpeg)

# <span id="page-5-0"></span>SYNC360 METHODS WORKING WITH CONNECTORS

In the Sync360 each connector can have data related specific methods, which can be called using one of the two types of construction:

1. Using **@**:

#### **@[connectionname].[methodname]([parameters,…,])**

Where:

- **[connectionname]** is the name of the connection (for example, **crmTest**);
- **[methodname]** is the name of the method (for example, **DeleteById**);
- **[parameters]** is the list of parameters (for example, **'User1'**, **'1'**);

```
2. Using execute (two ways):
```

```
<execute in="[connectionname]" entity="" var="result">
```

<query>**[methodname]**</query>

<attr name="**[parameter name]**"> **[parameter value]**</attr>

```
<attr name="[parameter name]"> [parameter value]</attr>
</execute>
```

<execute in="**[ connectionname]**" entity="**[methodname]**" var="result"> <attr name="**[parameter name]**"> **[parameter value]** </attr>

<attr name="**[parameter name]**"> **[parameter value]**</attr>

</execute>

Where:

- **[connectionname]** is the name of the connection (for example, **crmProd**);
- **[methodname]** is the name of the method (for example, **DeleteById**);
- **[parameters]** is the list of parameters (for example, **'User1'**, **'1'**);

Most of the methods return data of certain types.

List of methods supported in execute operations:

- AddMembersTeam
- RemoveMembersTeam
- AssociateEntities
- DisassociateEntities
- Merge
- AssociateUserRole
- DisassociateUserRole
- GetOptionSetValueLabel
- GetOptionSetValueCode
- GetEntityMetadata
- RunWorkflow

![](_page_6_Picture_1.jpeg)

![](_page_6_Picture_2.jpeg)

- GetEntityNameByObjectTypeCode
- GetObjectTypeCodeByEntityName
- AddListMember
- RemoveListMember
- RunAction
- ExecuteAction

The attribute names for execute operations are the same as in the first variant (using @).

# <span id="page-7-0"></span>SYNC360 METHODS WORKING WITH MICROSOFT DYNAMICS CRM CONNECTOR

CRM Connector allows users to move data into and out of the Microsoft Dynamics CRM application. CRM Connector uses a web service that Microsoft provides to interact with their CRM system. The connector can be used to query, get, add, modify and delete the different types of records (entities). In this chapter we'll consider the methods of Sync360 working with CRM Connector.

### <span id="page-7-1"></span>AssociateEntities

Use the **AssociateEntities** method to create an association between the two records using many-tomany relationship. It returns true if the association for the two records is created, and it returns false if the association creation failed.

The method takes the following parameters:

- targetEntityName (string),
- targetEntityId (Guid),
- otherEntityName (string),
- otherEntityId (Guid),
- relationshipName (string)

Returns:

• bool (operation succeed?)

#### **Example:**

```
<set var="result">
      {@crm.AssociateEntities('contact', contactid, 'account', accountid,
      'contact customer account') }
```

</set>

In the example above the **AssociateEntities** method tries to create an association between the contact record and the account record using many-to-many relationship named **'contact\_customer\_account'**.

**NOTE:** In this example and hereafter, all the arguments, font color of which is red, must be initialized in advance.

### <span id="page-7-2"></span>DisassociateEntities

Use the **DisassociateEntities** method to remove an association between the two records through the existing many-to-many relationship. It returns true if the association for the two records is removed, and it returns false if the association removal failed.

The method takes the following parameters:

• targetEntityName (string),

![](_page_7_Picture_25.jpeg)

![](_page_8_Picture_1.jpeg)

![](_page_8_Picture_2.jpeg)

- targetEntityId (Guid),
- otherEntityName (string),
- otherEntityId (Guid),
- relationshipName (string)
- Returns:
  - bool (operation succeed?)

#### **Example:**

```
<set var=«result»> 
      {@crm.DisassociateEntities('contact', contactid, 'account', accountid,
      'contact customer account') }
```

</set>

In the example above the **DisassociateEntities** method tries to remove the association between the contact record and the account record using many-to-many relationship named **'contact\_customer\_account'**.

### <span id="page-8-0"></span>AssociateUserRole

The **AssociateUserRole** method allows you to add a new user role. It is a particular case of the **AssociateEntities** method, where the targetEntityName, the otherEntityName and the relationshipName are constant.

The method takes the following parameters:

- userId (Guid),
- roleId (Guid)

Returns:

• bool (operation succeed?)

#### **Example:**

```
<set var=«result»>
```

```
{@crm.AssociateUserRole(userid, roleid)}
```

 $\langle$ /set $\rangle$ 

### <span id="page-8-1"></span>DisassociateUserRole

The **DisassociateUserRole** method allows you to delete a user role. It is a particular case of the **DisassociateEntities** method, where the targetEntityName, the otherEntityName and the relationshipName are constant.

The method takes the following parameters:

- userId (Guid),
- roleId (Guid)

![](_page_9_Picture_1.jpeg)

![](_page_9_Picture_2.jpeg)

```
• bool (operation succeed?)
```

#### **Example:**

```
<set var=«result»> 
     {@crm.DisassociateUserRole(userid, roleid)}
\langle/set\rangle
```

### <span id="page-9-0"></span>AddMembersTeam

Use the **AddMembersTeam** method to add a new user to the team.

The method takes the following parameters:

- userId (Guid),
- teamId (Guid)

Returns:

• bool (operation succeed?)

#### **Example:**

```
<set var=«result»>
```

```
{@crm.AddMembersTeam(userid, teamid)}
```

```
</set>
```

### <span id="page-9-1"></span>RemoveMembersTeam

Use the **RemoveMembersTeam** method to remove a certain user from the team. The method takes the following parameters:

- userId (Guid),
- teamId (Guid)

Returns:

• bool (operation succeed?)

#### **Example:**

```
<set var=«result»>
```

```
{@crm.RemoveMembersTeam(userid, teamid)}
```

</set>

### <span id="page-9-2"></span>RunWorkflow

The **RunWorkflow** method is used to run a work flow with the defined name for a certain record. The method takes the following parameters:

- workflowName (string),
- entityId (Guid)

![](_page_10_Picture_0.jpeg)

![](_page_10_Picture_1.jpeg)

![](_page_10_Picture_2.jpeg)

```
• bool (operation succeed?)
```

#### **Example:**

<set>

```
{@crm.RunWorkflow('workflow1', entityid)}
\langle/set>
```

# <span id="page-10-0"></span>GetOptionSetValueLabel

The **GetOptionSetValueLabel** method allows you to get an option set value label for a certain record.

The method takes the following parameters:

- entityName (string),
- attributeName (string),
- value (int)

Returns:

• Label (string)

#### **Example:** <set>

```
{@crm.GetOptionSetValueLabel('record1', 'attribute1', 1)}
```

 $\langle$ /set $\rangle$ 

### <span id="page-10-1"></span>GetOptionSetValueCode

The **GetOptionSetValueCode** method allows you to get an option set value code for a certain record. The method takes the following parameters:

- entityName (string),
- attributeName (string),
- label (string)

Returns:

• value code (int) or null

#### **Example:**

<set>

```
{@crm.GetOptionSetValueCode('record1', 'attribute1', 'label1')}
```

 $\langle$ /set>

### <span id="page-10-2"></span>GetEntityMetadata

Use the **GetEntityMetadata** method to get metadata of the certain record.

The method takes the following parameters:

```
• entityName (string)
```

![](_page_11_Picture_1.jpeg)

ProServ

• one record's metadata (EntityMetadata)

See EntityMetadata class description here:

<https://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.entitymetadata.aspx> **Example:**

#### <set>

```
{@crm.GetEntityMetadata('record1')}
```

</set>

### <span id="page-11-0"></span>GetAllEntitiesMetadata

Use the **GetAllEntitiesMetadata** method to get metadata of the certain set of records. The method takes the following parameters:

• entities (params string[])

Returns:

• metadata of the set of records (List<EntityMetadata>)

See EntityMetadata class description here:

[https://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.entitymetadata.aspx\)](https://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.entitymetadata.aspx)

#### **Example:**

<set>

```
{@crm.GetEntityMetadata('record1', 'record2', 'record3', 'record4')}
\langle/set>
```

### <span id="page-11-1"></span>GetAllEntitiesList

The **GetAllEntitiesList** method is used to get metadata of all of the records defined.

The method takes no parameters. Returns:

• metadata of all records (List<EntityMetadata>)

See EntityMetadata class description here:

[https://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.entitymetadata.aspx\)](https://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.entitymetadata.aspx) **Example:**

```
<set>
```

```
{@crm.GetEntityMetadata()}
```

 $\langle$ /set $\rangle$ 

### <span id="page-11-2"></span>AddListMember

The **AddListMember** method allows you to add a member of team to the list.

The method takes the following parameters:

• listId (Guid),

![](_page_12_Picture_1.jpeg)

![](_page_12_Picture_2.jpeg)

```
• memberId (Guid)
Returns:
   • bool (operation succeed?)
Example:
<set> 
     {@crm.AddListMember(listid, memberid)}
\langle/set>
```

### <span id="page-12-0"></span>RemoveListMember

The **RemoveListMember** method allows you to remove a member of team from the list. The method takes the following parameters:

- listId (Guid),
- memberId (Guid)

Returns:

• bool (operation succeed?)

#### **Example:**

<set>

```
{@crm.RemoveListMember(listid, memberid)}
```

 $\langle$ /set>

### <span id="page-12-1"></span>AddItemCampaignActivity

Add an item of the campaign activity using the **AddItemCampaignActivity** method. The method takes the following parameters:

- campaignActivityId (Guid),
- itemId (Guid)
- entityName (string)

Returns:

• bool (operation succeed?)

**Example:**

<set>

```
{@crm.AddItemCampaignActivity(campaignactivityid, itemid, 'entity1')}
```

 $\langle$ /set $\rangle$ 

### <span id="page-12-2"></span>AddItemCampaign

The **AddItemCampaign** method is used to add an item of the campaign.

The method takes the following parameters:

• campaignId (Guid),

![](_page_13_Picture_1.jpeg)

![](_page_13_Picture_2.jpeg)

- itemId (Guid)
- entityName (string)

#### **Example:**

#### <set>

```
{@crm.AddItemCampaign(campaignid, itemid, 'record1')}
\langle/set>
```

### <span id="page-13-0"></span>AddPrivilegesRole

The **AddPrivilegesRole** method allows you to add a privilege to the role. The method takes the following parameters:

- roleId (Guid),
- privilegeId (Guid)

Returns:

• bool (operation succeed?)

**Example:**

<set>

```
{@crm.AddPrivilegesRole(roleid, privilegeid)}
```

 $\langle$ /set $\rangle$ 

### <span id="page-13-1"></span>AddSubstituteProduct

The **AddSubstituteProduct** method allows you to add a substitute product.

The method takes the following parameters:

- productId (Guid),
- substituteId (Guid)

Returns:

```
• bool (operation succeed?)
```

#### **Example:**

<set>

```
{@crm.AddSubstituteProduct(productid, productid)}
```

 $\langle$ /set $\rangle$ 

### <span id="page-13-2"></span>DeleteById

Use the **DeleteById** method to delete the record by its id.

The method takes the following parameters:

- entityName (string),
- id (Guid)

The method doesn't return value

![](_page_14_Picture_1.jpeg)

#### **Example:**

```
<set>
```

```
{@crm.DeleteById('record1', id)}
```

```
\langle/set\rangle
```

### <span id="page-14-0"></span>**SelectById**

Use the **SelectById** method to select the record by its id. The method takes the following parameters:

- entityName (string),
- id (Guid)
- columns (params string[])

Returns:

• record (dynamic)

#### **Example:**

<set>

```
{@crm.SelectById('record1', id, 'column1', 'column2', 'column3')}
\langle/set>
```

### <span id="page-14-1"></span>Merge

Use the **Merge** method to merge the duplicate records (records with the same name).

The method selects fields in the duplicate record that you want to merge into the master (the one you want to keep) record.

The method takes the following parameters:

- entityName (string)
- id1 (Guid)
- id2 (Guid)
- fieldsFromSubordinate (List<object>, null)

The method doesn't return value.

**Example:**

<set>

```
{@crm.Merge('record1', id1, id2, fieldsFromSubordinate)}
```

 $\langle$ /set>

### <span id="page-14-2"></span>RetrieveAuditDetailsRequest

The **RetrieveAuditDetailsRequest** method is used to retrieve the record's audit details.

The method takes the following parameters:

• id (Guid)

![](_page_15_Picture_1.jpeg)

![](_page_15_Picture_2.jpeg)

Returns:

• record's audit details (dynamic)

#### **Example:**

<set>

```
{@crm.RetrieveAuditDetailsRequest(id)}
```

 $\langle$ /set>

### <span id="page-15-0"></span>RetrieveRecordChangeHistoryRequest

The **RetrieveRecordChangeHistoryRequest** method is used to retrieve the record's change history.

- The method takes the following parameters:
  - entityName (string),
  - entityId (Guid)

Returns:

• record's change history (dynamic)

#### **Example:**

<set>

```
{@crm.RetrieveRecordChangeHistoryRequest('record1', id)}
```

 $\langle$ /set $\rangle$ 

### <span id="page-15-1"></span>SelectActivityParties

The **SelectActivityParties** method allows you to get the contents of an activity party type field and add it to a variable.

The method takes the following parameters:

- entityName (string),
- id (Guid),
- columns (params string[])

Returns:

• activity party type field (dynamic)

#### **Example:**

<set>

```
{@crm.SelectActivityParties('record1', id, 'column1', 'column2', 'column3')}
```

 $\langle$ /set $\rangle$ 

### <span id="page-15-2"></span>GetEntityNameByObjectTypeCode

Use the **GetEntityNameByObjectTypeCode** method to select the record's name parties by its ObjectTypeCode.

The method takes the following parameters:

![](_page_16_Picture_1.jpeg)

![](_page_16_Picture_2.jpeg)

```
• objectTypeCode (string)
```

Returns:

• record's name (string)

#### **Example:**

<set>

```
{@crm.GetEntityNameByObjectTypeCode('objectTypeCode')}
```

 $\langle$ /set>

### <span id="page-16-0"></span>GetObjectTypeCodeByEntityName

Use the **GetObjectTypeCodeByEntityName** method to select the record's ObjectTypeCode parties by its name.

The method takes the following parameters:

• objectTypeCode (string)

Returns:

```
• record's ObjectTypeCode (int) or null
```

#### **Example:**

<set>

```
{@crm.GetObjectTypeCodeByEntityName('record1')}
```

 $\langle$ /set>

### <span id="page-16-1"></span>ExecuteSendEmailRequest

The **ExecuteSendEmailRequest** method allows you to execute the Send Email request.

The method takes the following parameters:

- emailld (Guid),
- trackingToken (string),
- issueSend (bool)

The method doesn't return value.

#### **Example:**

<set>

```
{@crm.ExecuteSendEmailRequest (id, ' trackingToken', true)}
```

 $\langle$ /set>

### <span id="page-16-2"></span>DeliverPromoteEmailRequest

The **DeliverPromoteEmailRequest** method returns Email Id of the delivered email.

The method takes the following parameters:

```
• parameters (Dictionary<string, object>)
```

![](_page_17_Picture_1.jpeg)

![](_page_17_Picture_2.jpeg)

```
• Email Id (dynamic)
```

#### **Example:**

```
<set var=«result»> 
     {@crm.DeliverPromoteEmailRequest(parameters)}
\langle/set\rangle
```

### <span id="page-17-0"></span>RunAction

The **RunAction** method is used to run an Action. The method has two overloads. First of them takes the following parameters:

- actionName (string)
- entityName (string)
- entityId (Guid)
- properties (IDictionary<string, object>)

Second of them takes the following parameters:

- actionName (string)
- properties (IDictionary<string, object>)

Both overloads return:

```
• bool (operation succeed?)
```

#### **Examples:**

```
<set var=«result»>
```

```
{@ crm.RunAction('action Name', 'entity Name', id, properties )}
```

 $\langle$ /set>

```
<set var=«result»>
```

{@ crm.RunAction('action Name', properties )}

 $\langle$ /set $\rangle$ 

### <span id="page-17-1"></span>ExecuteAction

The **ExecuteAction** method is used to execute an Action. The method has two overloads. First of them takes the following parameters:

- actionName (string)
- entityName (string)
- entityId (Guid)
- properties (IDictionary<string, object>)
- Second of them takes the following parameters:
  - actionName (string)
  - properties (IDictionary<string, object>)

Both overloads return values:

![](_page_18_Picture_1.jpeg)

```
• Out Parameters of Action (IDictionary<string, object>)
Examples:
<set var=«result»> 
     {@ crm.ExecuteAction ('action Name', 'entity Name', id, properties )}
\langle/set\rangle<set var=«result»> 
     {@ crm.ExecuteAction ('action Name', properties )}
     \langle/set\rangle\langle/set\rangle
```

![](_page_19_Picture_1.jpeg)

## <span id="page-19-0"></span>APPENDIX

### <span id="page-19-1"></span>Additional Notes

#### <span id="page-19-2"></span>Note 1

The standard sync360 script for linking employee and users has two steps in the logic:

1) Attempts to find user record by using match by Email address

2) Attempts to find user record by using match on Firstname + Lastname - If match was found using this method, then script updates Email address on Employee record from User.

Also, there is a plugin which automates the linkage, the plugin triggers on creation of User record and will work only if Employee were synced to Dynamics before user provisioning.