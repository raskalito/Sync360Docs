|  |  |
| --- | --- |
| ![A picture containing icon  Description automatically generated](data:image/png;base64...)  Prepared By:  AN |  |

Sync360 Script Methods Guide

v.0.4

This document and its supporting materials are proprietary and confidential.

The information may not be disclosed to a third party without the express permission of HSO.

Contributors

| **Name** | **Role** | **Company** |
| --- | --- | --- |
| Alexandra Petrova |  |  |
| Alexandra Petrova |  |  |
| Alexander Novikov |  |  |

Revision History

| **Version** | **Section** | **Content Change** | **Date** |
| --- | --- | --- | --- |
| 0.1 |  |  | 03/23/2017 |
| 0.2 |  |  | 06/08/2018 |
| 0.3 |  | Minor updates. | 06/01/2022 |
| 0.4 |  | * Added Appendix section and Additional Notes section. * Added a note regarding linking employee and user records. | 08/08/2024 |

Table of Contents

[Introduction 5](#_Toc174012774)

[Sync360 methods working with connectors 6](#_Toc174012775)

[Sync360 methods working with Microsoft Dynamics CRM connector 8](#_Toc174012776)

[AssociateEntities 8](#_Toc174012777)

[DisassociateEntities 8](#_Toc174012778)

[AssociateUserRole 9](#_Toc174012779)

[DisassociateUserRole 9](#_Toc174012780)

[AddMembersTeam 10](#_Toc174012781)

[RemoveMembersTeam 10](#_Toc174012782)

[RunWorkflow 10](#_Toc174012783)

[GetOptionSetValueLabel 11](#_Toc174012784)

[GetOptionSetValueCode 11](#_Toc174012785)

[GetEntityMetadata 11](#_Toc174012786)

[GetAllEntitiesMetadata 12](#_Toc174012787)

[GetAllEntitiesList 12](#_Toc174012788)

[AddListMember 12](#_Toc174012789)

[RemoveListMember 13](#_Toc174012790)

[AddItemCampaignActivity 13](#_Toc174012791)

[AddItemCampaign 13](#_Toc174012792)

[AddPrivilegesRole 14](#_Toc174012793)

[AddSubstituteProduct 14](#_Toc174012794)

[DeleteById 14](#_Toc174012795)

[SelectById 15](#_Toc174012796)

[Merge 15](#_Toc174012797)

[RetrieveAuditDetailsRequest 15](#_Toc174012798)

[RetrieveRecordChangeHistoryRequest 16](#_Toc174012799)

[SelectActivityParties 16](#_Toc174012800)

[GetEntityNameByObjectTypeCode 16](#_Toc174012801)

[GetObjectTypeCodeByEntityName 17](#_Toc174012802)

[ExecuteSendEmailRequest 17](#_Toc174012803)

[DeliverPromoteEmailRequest 17](#_Toc174012804)

[RunAction 18](#_Toc174012805)

[ExecuteAction 18](#_Toc174012806)

[Appendix 20](#_Toc174012807)

[Additional Notes 20](#_Toc174012808)

[Note 1 20](#_Toc174012809)

Introduction

This document describes methods of the Sync360 which work with CRM.

Sync360 methods working with connectors

In the Sync360 each connector can have data related specific methods, which can be called using one of the two types of construction:

1. Using **@**:

**@[connectionname].[methodname]([parameters,…,])**

Where:

* **[connectionname]** is the name of the connection (for example, **crmTest**);
* **[methodname]** is the name of the method (for example, **DeleteById**);
* **[parameters]** is the list of parameters (for example, **‘User1’**, **‘1’**);

1. Using **execute** (two ways):

<execute in="**[connectionname]**" entity="" var="result">

<query>**[methodname]**</query>

<attr name="**[parameter name]**"> **[parameter value]**</attr>

<attr name="**[parameter name]**"> **[parameter value]**</attr>

</execute>

<execute in="**[ connectionname]**" entity="**[methodname]**" var="result">

<attr name="**[parameter name]**"> **[parameter value]**</attr>

<attr name="**[parameter name]**"> **[parameter value]**</attr>

</execute>

Where:

* **[connectionname]** is the name of the connection (for example, **crmProd**);
* **[methodname]** is the name of the method (for example, **DeleteById**);
* **[parameters]** is the list of parameters (for example, **‘User1’**, **‘1’**);

Most of the methods return data of certain types.

List of methods supported in execute operations:

* AddMembersTeam
* RemoveMembersTeam
* AssociateEntities
* DisassociateEntities
* Merge
* AssociateUserRole
* DisassociateUserRole
* GetOptionSetValueLabel
* GetOptionSetValueCode
* GetEntityMetadata
* RunWorkflow
* GetEntityNameByObjectTypeCode
* GetObjectTypeCodeByEntityName
* AddListMember
* RemoveListMember
* RunAction
* ExecuteAction

The attribute names for execute operations are the same as in the first variant (using @).

Sync360 methods working with Microsoft Dynamics CRM connector

CRM Connector allows users to move data into and out of the Microsoft Dynamics CRM application. CRM Connector uses a web service that Microsoft provides to interact with their CRM system. The connector can be used to query, get, add, modify and delete the different types of records (entities).

In this chapter we’ll consider the methods of Sync360 working with CRM Connector.

AssociateEntities

Use the **AssociateEntities** method to create an association between the two records using many-to-many relationship. It returns true if the association for the two records is created, and it returns false if the association creation failed.

The method takes the following parameters:

* targetEntityName (string),
* targetEntityId (Guid),
* otherEntityName (string),
* otherEntityId (Guid),
* relationshipName (string)

Returns:

* bool (operation succeed?)

**Example:**

<set var="result">

{@crm.AssociateEntities('contact', contactid, 'account', accountid, 'contact\_customer\_account')}

</set>

In the example above the **AssociateEntities** method tries to create an association between the contact record and the account record using many-to-many relationship named **‘contact\_customer\_account’**.

NOTE: In this example and hereafter, all the arguments, font color of which is red, must be initialized in advance.

DisassociateEntities

Use the **DisassociateEntities** method to remove an association between the two records through the existing many-to-many relationship. It returns true if the association for the two records is removed, and it returns false if the association removal failed.

The method takes the following parameters:

* targetEntityName (string),
* targetEntityId (Guid),
* otherEntityName (string),
* otherEntityId (Guid),
* relationshipName (string)

Returns:

* bool (operation succeed?)

**Example:**

<set var=«result»>

{@crm.DisassociateEntities('contact', contactid, 'account', accountid, 'contact\_customer\_account')}

</set>

In the example above the **DisassociateEntities** method tries to remove the association between the contact record and the account record using many-to-many relationship named **‘contact\_customer\_account’**.

AssociateUserRole

The **AssociateUserRole** method allows you to add a new user role. It is a particular case of the **AssociateEntities** method, where the targetEntityName, the otherEntityName and the relationshipName are constant.

The method takes the following parameters:

* userId (Guid),
* roleId (Guid)

Returns:

* bool (operation succeed?)

**Example:**

<set var=«result»>

{@crm.AssociateUserRole(userid, roleid)}

</set>

DisassociateUserRole

The **DisassociateUserRole** method allows you to delete a user role. It is a particular case of the **DisassociateEntities** method, where the targetEntityName, the otherEntityName and the relationshipName are constant.

The method takes the following parameters:

* userId (Guid),
* roleId (Guid)

Returns:

* bool (operation succeed?)

**Example:**

<set var=«result»>

{@crm.DisassociateUserRole(userid, roleid)}

</set>

AddMembersTeam

Use the **AddMembersTeam** method to add a new user to the team.

The method takes the following parameters:

* userId (Guid),
* teamId (Guid)

Returns:

* bool (operation succeed?)

**Example:**

<set var=«result»>

{@crm.AddMembersTeam(userid, teamid)}

</set>

RemoveMembersTeam

Use the **RemoveMembersTeam** method to remove a certain user from the team.

The method takes the following parameters:

* userId (Guid),
* teamId (Guid)

Returns:

* bool (operation succeed?)

**Example:**

<set var=«result»>

{@crm.RemoveMembersTeam(userid, teamid)}

</set>

RunWorkflow

The **RunWorkflow** method is used to run a work flow with the defined name for a certain record.

The method takes the following parameters:

* workflowName (string),
* entityId (Guid)

Returns:

* bool (operation succeed?)

**Example:**

<set>

{@crm.RunWorkflow('workflow1', entityid)}

</set>

GetOptionSetValueLabel

The **GetOptionSetValueLabel** method allows you to get an option set value label for a certain record.

The method takes the following parameters:

* entityName (string),
* attributeName (string),
* value (int)

Returns:

* Label (string)

**Example:**

<set>

{@crm.GetOptionSetValueLabel('record1', 'attribute1', 1)}

</set>

GetOptionSetValueCode

The **GetOptionSetValueCode** method allows you to get an option set value code for a certain record.

The method takes the following parameters:

* entityName (string),
* attributeName (string),
* label (string)

Returns:

* value code (int) or null

**Example:**

<set>

{@crm.GetOptionSetValueCode('record1', 'attribute1', 'label1')}

</set>

GetEntityMetadata

Use the **GetEntityMetadata** method to get metadata of the certain record.

The method takes the following parameters:

* entityName (string)

Returns:

* one record’s metadata (EntityMetadata)

See EntityMetadata class description here:

<https://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.entitymetadata.aspx>

**Example:**

<set>

{@crm.GetEntityMetadata('record1')}

</set>

GetAllEntitiesMetadata

Use the **GetAllEntitiesMetadata** method to get metadata of the certain set of records.

The method takes the following parameters:

* entities (params string[])

Returns:

* metadata of the set of records (List<EntityMetadata>)

See EntityMetadata class description here:

<https://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.entitymetadata.aspx>)

**Example:**

<set>

{@crm.GetEntityMetadata('record1', 'record2', 'record3', 'record4')}

</set>

GetAllEntitiesList

The **GetAllEntitiesList** method is used to get metadata of all of the records defined.

The method takes no parameters.

Returns:

* metadata of all records (List<EntityMetadata>)

See EntityMetadata class description here:

<https://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.entitymetadata.aspx>)

**Example:**

<set>

{@crm.GetEntityMetadata()}

</set>

AddListMember

The **AddListMember** method allows you to add a member of team to the list.

The method takes the following parameters:

* listId (Guid),
* memberId (Guid)

Returns:

* bool (operation succeed?)

**Example:**

<set>

{@crm.AddListMember(listid, memberid)}

</set>

RemoveListMember

The **RemoveListMember** method allows you to remove a member of team from the list.

The method takes the following parameters:

* listId (Guid),
* memberId (Guid)

Returns:

* bool (operation succeed?)

**Example:**

<set>

{@crm.RemoveListMember(listid, memberid)}

</set>

AddItemCampaignActivity

Add an item of the campaign activity using the **AddItemCampaignActivity** method.

The method takes the following parameters:

* campaignActivityId (Guid),
* itemId (Guid)
* entityName (string)

Returns:

* bool (operation succeed?)

**Example:**

<set>

{@crm.AddItemCampaignActivity(campaignactivityid, itemid, 'entity1')}

</set>

AddItemCampaign

The **AddItemCampaign** method is used to add an item of the campaign.

The method takes the following parameters:

* campaignId (Guid),
* itemId (Guid)
* entityName (string)

**Example:**

<set>

{@crm.AddItemCampaign(campaignid, itemid, 'record1')}

</set>

AddPrivilegesRole

The **AddPrivilegesRole** method allows you to add a privilege to the role.

The method takes the following parameters:

* roleId (Guid),
* privilegeId (Guid)

Returns:

* bool (operation succeed?)

**Example:**

<set>

{@crm.AddPrivilegesRole(roleid, privilegeid)}

</set>

AddSubstituteProduct

The **AddSubstituteProduct** method allows you to add a substitute product.

The method takes the following parameters:

* productId (Guid),
* substituteId (Guid)

Returns:

* bool (operation succeed?)

**Example:**

<set>

{@crm.AddSubstituteProduct(productid, productid)}

</set>

DeleteById

Use the **DeleteById** method to delete the record by its id.

The method takes the following parameters:

* entityName (string),
* id (Guid)

The method doesn’t return value

**Example:**

<set>

{@crm.DeleteById('record1', id)}

</set>

SelectById

Use the **SelectById** method to select the record by its id.

The method takes the following parameters:

* entityName (string),
* id (Guid)
* columns (params string[])

Returns:

* record (dynamic)

**Example:**

<set>

{@crm.SelectById('record1', id, 'column1', 'column2', 'column3')}

</set>

Merge

Use the **Merge** method to merge the duplicate records (records with the same name).

The method selects fields in the duplicate record that you want to merge into the master (the one you want to keep) record.

The method takes the following parameters:

* entityName (string)
* id1 (Guid)
* id2 (Guid)
* fieldsFromSubordinate (List<object>, null)

The method doesn’t return value.

**Example:**

<set>

{@crm.Merge('record1', id1, id2, fieldsFromSubordinate)}

</set>

RetrieveAuditDetailsRequest

The **RetrieveAuditDetailsRequest** method is used to retrieve the record’s audit details.

The method takes the following parameters:

* id (Guid)

Returns:

• record’s audit details (dynamic)

**Example:**

<set>

{@crm.RetrieveAuditDetailsRequest(id)}

</set>

RetrieveRecordChangeHistoryRequest

The **RetrieveRecordChangeHistoryRequest** method is used to retrieve the record’s change history.

The method takes the following parameters:

* entityName (string),
* entityId (Guid)

Returns:

* record’s change history (dynamic)

**Example:**

<set>

{@crm.RetrieveRecordChangeHistoryRequest('record1', id)}

</set>

SelectActivityParties

The **SelectActivityParties** method allows you to get the contents of an activity party type field and add it to a variable.

The method takes the following parameters:

* entityName (string),
* id (Guid),
* columns (params string[])

Returns:

* activity party type field (dynamic)

**Example:**

<set>

{@crm.SelectActivityParties('record1', id, 'column1', 'column2', 'column3')}

</set>

GetEntityNameByObjectTypeCode

Use the **GetEntityNameByObjectTypeCode** method to select the record’s name parties by its ObjectTypeCode.

The method takes the following parameters:

* objectTypeCode (string)

Returns:

* record’s name (string)

**Example:**

<set>

{@crm.GetEntityNameByObjectTypeCode('objectTypeCode')}

</set>

GetObjectTypeCodeByEntityName

Use the **GetObjectTypeCodeByEntityName** method to select the record’s ObjectTypeCode parties by its name.

The method takes the following parameters:

* objectTypeCode (string)

Returns:

* record’s ObjectTypeCode (int) or null

**Example:**

<set>

{@crm.GetObjectTypeCodeByEntityName('record1')}

</set>

ExecuteSendEmailRequest

The **ExecuteSendEmailRequest** method allows you to execute the Send Email request.

The method takes the following parameters:

* emailId (Guid),
* trackingToken (string),
* issueSend (bool)

The method doesn’t return value.

**Example:**

<set>

{@crm.ExecuteSendEmailRequest (id, ' trackingToken', true)}

</set>

DeliverPromoteEmailRequest

The **DeliverPromoteEmailRequest** method returns Email Id of the delivered email.

The method takes the following parameters:

* parameters (Dictionary<string, object>)

Returns:

* Email Id (dynamic)

**Example:**

<set var=«result»>

{@crm.DeliverPromoteEmailRequest(parameters)}

</set>

RunAction

The **RunAction** method is used to run an Action. The method has two overloads.

First of them takes the following parameters:

* actionName (string)
* entityName (string)
* entityId (Guid)
* properties (IDictionary<string, object>)

Second of them takes the following parameters:

* actionName (string)
* properties (IDictionary<string, object>)

Both overloads return:

* bool (operation succeed?)

**Examples:**

<set var=«result»>

{@ crm.RunAction(’action Name’, ‘entity Name’, id, properties )}

</set>

<set var=«result»>

{@ crm.RunAction(’action Name’, properties )}

</set>

ExecuteAction

The **ExecuteAction** method is used to execute an Action. The method has two overloads.

First of them takes the following parameters:

* actionName (string)
* entityName (string)
* entityId (Guid)
* properties (IDictionary<string, object>)

Second of them takes the following parameters:

* actionName (string)
* properties (IDictionary<string, object>)

Both overloads return values:

* Out Parameters of Action (IDictionary<string, object>)

**Examples:**

<set var=«result»>

{@ crm.ExecuteAction (’action Name’, ‘entity Name’, id, properties )}

</set>

<set var=«result»>

{@ crm.ExecuteAction (’action Name’, properties )}

</set>

</set>

Appendix

Additional Notes

Note 1

The standard sync360 script for linking employee and users has two steps in the logic:

1) Attempts to find user record by using match by Email address

2) Attempts to find user record by using match on Firstname + Lastname - If match was found using this method, then script updates Email address on Employee record from User.

Also, there is a plugin which automates the linkage, the plugin triggers on creation of User record and will work only if Employee were synced to Dynamics before user provisioning.
