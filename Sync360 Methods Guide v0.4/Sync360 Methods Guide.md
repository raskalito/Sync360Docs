Introduction This document describes methods of the Sync360 which work with CRM.

# SYNC360 METHODS WORKING WITH CONNECTORS

In the Sync360 each connector can have data related specific methods, which can be called using one of the two types of construction:

1. Using **@**:

### **@[connectionname].[methodname]([parameters,…,])**

Where:

- **[connectionname]** is the name of the connection (for example, **crmTest**);
- **[methodname]** is the name of the method (for example, **DeleteById**);
- **[parameters]** is the list of parameters (for example, **'User1'**, **'1'**);

#### 2. Using **execute** (two ways):

```
<execute in="[connectionname]" entity="" var="result">
         <query>[methodname]</query>
         <attr name="[parameter name]"> [parameter value]</attr>
<attr name="[parameter name]"> [parameter value]</attr>
</execute>
```

```
<execute in="[connectionname]" entity="[methodname]" var="result">
         <attr name="[parameter name]"> [parameter value]</attr>
         <attr name="[parameter name]"> [parameter value]</attr>
</execute>
```

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
- GetEntityNameByObjectTypeCode
- GetObjectTypeCodeByEntityName
- AddListMember
- RemoveListMember
- RunAction

### • ExecuteAction

The attribute names for execute operations are the same as in the first variant (using @).

# SYNC360 METHODS WORKING WITH MICROSOFT DYNAMICS CRM CONNECTOR

CRM Connector allows users to move data into and out of the Microsoft Dynamics CRM application. CRM Connector uses a web service that Microsoft provides to interact with their CRM system. The connector can be used to query, get, add, modify and delete the different types of records (entities). In this chapter we'll consider the methods of Sync360 working with CRM Connector.

### AssociateEntities

Use the **AssociateEntities** method to create an association between the two records using many-tomany relationship. It returns true if the association for the two records is created, and it returns false if the association creation failed.

The method takes the following parameters:

- targetEntityName (string),
- targetEntityId (Guid),
- otherEntityName (string),
- otherEntityId (Guid),
- relationshipName (string)

Returns:

```
• bool (operation succeed?)
```

#### **Example:**

```
<set var="result">
{@crm.AssociateEntities('contact', contactid, 'account', accountid, 'contact_customer_account')}
</set>
```

In the example above the **AssociateEntities** method tries to create an association between the contact record and the account record using many-to-many relationship named **'contact\_customer\_account'**.

**NOTE:** In this example and hereafter, all the arguments, font color of which is red, must be initialized in advance.

### DisassociateEntities

Use the **DisassociateEntities** method to remove an association between the two records through the existing many-to-many relationship. It returns true if the association for the two records is removed, and it returns false if the association removal failed.

The method takes the following parameters:

- targetEntityName (string),
- targetEntityId (Guid),
- otherEntityName (string),
- otherEntityId (Guid),
- relationshipName (string)

Returns:

• bool (operation succeed?)

**Example:**

```
<set var="result">{@crm.DisassociateEntities('contact', contactid, 'account', accountid, 
'contact_customer_account')}</set>
```

In the example above the **DisassociateEntities** method tries to remove the association between the contact record and the account record using many-to-many relationship named **'contact\_customer\_account'**.

### AssociateUserRole

The **AssociateUserRole** method allows you to add a new user role. It is a particular case of the **AssociateEntities** method, where the targetEntityName, the otherEntityName and the relationshipName are constant.

The method takes the following parameters:

- userId (Guid),
- roleId (Guid)

Returns:

```
• bool (operation succeed?)
```

#### **Example:**

```
<set var="result">{@crm.AssociateUserRole(userid, roleid)}</set>
```

### DisassociateUserRole

The **DisassociateUserRole** method allows you to delete a user role. It is a particular case of the **DisassociateEntities** method, where the targetEntityName, the otherEntityName and the relationshipName are constant.

The method takes the following parameters:

- userId (Guid),
- roleId (Guid)

Returns:

• bool (operation succeed?)

#### **Example:**

<set var="result">{@crm.DisassociateUserRole(userid, roleid)}</set>

### AddMembersTeam

Use the **AddMembersTeam** method to add a new user to the team.

The method takes the following parameters:

- userId (Guid),
- teamId (Guid)

Returns:

```
• bool (operation succeed?)
```

#### **Example:**

```
<set var="result">{@crm.AddMembersTeam(userid, teamid)}</set>
```

### RemoveMembersTeam

Use the **RemoveMembersTeam** method to remove a certain user from the team.

The method takes the following parameters:

- userId (Guid),
- teamId (Guid)

Returns:

• bool (operation succeed?)

**Example:**

```
<set var="result">{@crm.RemoveMembersTeam(userid, teamid)}</set>
```

### RunWorkflow

The **RunWorkflow** method is used to run a work flow with the defined name for a certain record. The method takes the following parameters:

- workflowName (string),
- entityId (Guid)

Returns:

```
• bool (operation succeed?)
```

**Example:**

```
<set>{@crm.RunWorkflow('workflow1', entityid)}</set>
```

### GetOptionSetValueLabel

The **GetOptionSetValueLabel** method allows you to get an option set value label for a certain record. The method takes the following parameters:

- entityName (string),
- attributeName (string),
- value (int)

Returns:

• Label (string)

#### **Example:**

<set>{@crm.GetOptionSetValueLabel('record1', 'attribute1', 1)}</set>

### GetOptionSetValueCode

The **GetOptionSetValueCode** method allows you to get an option set value code for a certain record. The method takes the following parameters:

- entityName (string),
- attributeName (string),
- label (string)

Returns:

• value code (int) or null

#### **Example:**

```
<set var="optionSet">{@crm.GetOptionSetValueCode('record1', 'attribute1', 'label1')}</set>
```

### GetEntityMetadata

Use the **GetEntityMetadata** method to get metadata of the certain record.

The method takes the following parameters:

• entityName (string)

Returns:

• one record's metadata (EntityMetadata)

See EntityMetadata class description here:

<https://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.entitymetadata.aspx>

#### **Example:**

<set var="entityMeta">{@crm.GetEntityMetadata('record1')}</set>

### GetAllEntitiesMetadata

Use the **GetAllEntitiesMetadata** method to get metadata of the certain set of records.

The method takes the following parameters:

• entities (params string[])

Returns:

• metadata of the set of records (List<EntityMetadata>)

See EntityMetadata class description here:

[https://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.entitymetadata.aspx\)](https://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.entitymetadata.aspx)

#### **Example:**

```
<set var="metadata">{@crm.GetAllEntitiesMetadata('record1', 'record2', 'record3', 'record4')}</set>
```

### GetAllEntitiesList

The **GetAllEntitiesList** method is used to get metadata of all of the records defined.

The method takes no parameters.

Returns:

• metadata of all records (List<EntityMetadata>)

See EntityMetadata class description here:

[https://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.entitymetadata.aspx\)](https://msdn.microsoft.com/en-us/library/microsoft.xrm.sdk.metadata.entitymetadata.aspx)

#### **Example:**

<set>{@crm.GetEntityMetadata()}</set>

## AddListMember

The **AddListMember** method allows you to add a member of team to the list. The method takes the following parameters:

- listId (Guid),
- memberId (Guid)

Returns:

• bool (operation succeed?)

#### **Example:**

```
<set>{@crm.AddListMember(listid, memberid)}</set>
```

### Removel istMember

The **RemoveListMember** method allows you to remove a member of team from the list. The method takes the following parameters:

- listId (Guid),
- memberId (Guid)

Returns:

```
• bool (operation succeed?)
```

#### **Example:**

<set>{@crm.RemoveListMember(listid, memberid)}</set>

# AddItemCampaignActivity

Add an item of the campaign activity using the **AddItemCampaignActivity** method.

The method takes the following parameters:

- campaignActivityId (Guid),
- itemId (Guid)
- entityName (string)

Returns:

```
• bool (operation succeed?)
```

#### **Example:**

<set>{@crm.AddItemCampaignActivity(campaignactivityid, itemid, 'entity1')}</set>

### AddItemCampaign

The **AddItemCampaign** method is used to add an item of the campaign.

The method takes the following parameters:

- campaignId (Guid),
- itemId (Guid)
- entityName (string)

**Example:**

<set>{@crm.AddItemCampaign(campaignid, itemid, 'record1')}</set>

### AddPrivilegesRole

The **AddPrivilegesRole** method allows you to add a privilege to the role.

The method takes the following parameters:

- roleId (Guid),
- privilegeId (Guid)

Returns:

• bool (operation succeed?)

#### **Example:**

<set>{@crm.AddPrivilegesRole(roleid, privilegeid)}</set>

### AddSubstituteProduct

The **AddSubstituteProduct** method allows you to add a substitute product.

The method takes the following parameters:

- productId (Guid),
- substituteId (Guid)

Returns:

• bool (operation succeed?)

#### **Example:**

<set>{@crm.AddSubstituteProduct(productid, productid)}</set>

### DeleteById

Use the **DeleteById** method to delete the record by its id.

The method takes the following parameters:

- entityName (string),
- id (Guid)

The method doesn't return value

#### **Example:**

<set>{@crm.DeleteById('record1', id)}</set>

### **SelectById**

Use the **SelectById** method to select the record by its id. The method takes the following parameters:

- entityName (string),
- id (Guid)
- columns (params string[])

Returns:

• record (dynamic)

#### **Example:**

```
<set>{@crm.SelectById('record1', id, 'column1', 'column2', 'column3')}</set>
```

### Merge

Use the **Merge** method to merge the duplicate records (records with the same name).

The method selects fields in the duplicate record that you want to merge into the master (the one you want to keep) record.

The method takes the following parameters:

- entityName (string)
- id1 (Guid)
- id2 (Guid)
- fieldsFromSubordinate (List<object>, null)

The method doesn't return value.

#### **Example:**

<set>{@crm.Merge('record1', id1, id2, fieldsFromSubordinate)}</set>

### RetrieveAuditDetailsRequest

The **RetrieveAuditDetailsRequest** method is used to retrieve the record's audit details.

The method takes the following parameters:

• id (Guid)

Returns:

• record's audit details (dynamic)

#### **Example:**

```
<set>{@crm.RetrieveAuditDetailsRequest(id)}</set>
```

### RetrieveRecordChangeHistoryRequest

The **RetrieveRecordChangeHistoryRequest** method is used to retrieve the record's change history.

The method takes the following parameters:

- entityName (string),
- entityId (Guid)

```
Returns:
```

• record's change history (dynamic)

#### **Example:**

```
<set>{@crm.RetrieveRecordChangeHistoryRequest('record1', id)}</set>
```

# SelectActivityParties

The **SelectActivityParties** method allows you to get the contents of an activity party type field and add it to a variable.

The method takes the following parameters:

- entityName (string),
- id (Guid),
- columns (params string[])

Returns:

• activity party type field (dynamic)

#### **Example:**

```
<set>{@crm.SelectActivityParties('record1', id, 'column1', 'column2', 'column3')}</set>
```

# GetEntityNameByObjectTypeCode

Use the **GetEntityNameByObjectTypeCode** method to select the record's name parties by its ObjectTypeCode.

## Header

*Italic text*

**Bold text**

`Inline code`

### Smaller Header

- Unordered list item 1
- Unordered list item 2

1. Ordered list item 1
2. Ordered list item 2

[Link](http://example.com)

`code block`

 $Inline math$ 

$$
Display math
$$

| Labels  | Values  |
|---------|---------|
| Label 1 | Value 1 |
| Label 2 | Value 2 |

The method takes the following parameters:

• objectTypeCode (string)

```
Returns:
```

• record's name (string)

#### **Example:**

```
<set>{@crm.GetEntityNameByObjectTypeCode('objectTypeCode')}</set>
```

# GetObjectTypeCodeByEntityName

Use the **GetObjectTypeCodeByEntityName** method to select the record's ObjectTypeCode parties by its name.

The method takes the following parameters:

• objectTypeCode (string)

Returns:

```
• record's ObjectTypeCode (int) or null
```

**Example:**

```
<set>{@crm.GetObjectTypeCodeByEntityName('record1')}</set>
```

### ExecuteSendEmailRequest

The **ExecuteSendEmailRequest** method allows you to execute the Send Email request.

The method takes the following parameters:

- emailld (Guid),
- trackingToken (string),
- issueSend (bool)

The method doesn't return value.

<set>{@crm.ExecuteSendEmailRequest (id, ' trackingToken', true)}</set>

### DeliverPromoteEmailRequest

The **DeliverPromoteEmailRequest** method returns Email Id of the delivered email.

The method takes the following parameters:

• parameters (Dictionary<string, object>)

Returns:

• Email Id (dynamic)

#### **Example:**

<set var="result">{@crm.DeliverPromoteEmailRequest(parameters)}</set>

### RunAction

The **RunAction** method is used to run an Action. The method has two overloads.

First of them takes the following parameters:

- actionName (string)
- entityName (string)
- entityId (Guid)
- properties (IDictionary<string, object>)

Second of them takes the following parameters:

- actionName (string)
- properties (IDictionary<string, object>)

Both overloads return:

• bool (operation succeed?)

#### **Examples:**

```
<set var="result">{@crm.RunAction('action Name', 'entity Name', id, properties )}</set>
<set var="result">{@crm.RunAction('action Name', properties )}</set>
```

### ExecuteAction

The **ExecuteAction** method is used to execute an Action. The method has two overloads.

First of them takes the following parameters:

- actionName (string)
- entityName (string)
- entityId (Guid)
- properties (IDictionary<string, object>)
- Second of them takes the following parameters:
  - actionName (string)
  - properties (IDictionary<string, object>)

Both overloads return values:

• Out Parameters of Action (IDictionary<string, object>)

#### **Examples:**

<set var="result">{@crm.ExecuteAction ('action Name', 'entity Name', id, properties )}</set> <set var="result">{@crm.ExecuteAction ('action Name', properties )}</set>