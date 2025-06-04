---
layout: sidebar
title: Azure Private Link for Data Factory & Synapse 
description: Secure Connection between Azure Virtual Network and Snowflake VNet in Azure, specifically for Data Factory and Synapse.
---

## When To Use Private Link
- When industry standards demand a near completely private connection (ie. healthcare)
- Security requirements demand the vast majority of your traffic needs to take place only over Azure fiber via Microsoft Backbone.

## Overview
> **Note:** Private Link is a Business Critical Edition only feature.

[Azure Private Link](https://learn.microsoft.com/en-us/azure/private-link/private-link-overview) provides private connectivity to Snowflake by ensuring that access to snowflake is through a private IP address.

Traffic can only occur from the customer virtual network (VNet) to the Snowflake VNet using the [Microsoft Backbone](https://learn.microsoft.com/en-us/azure/networking/microsoft-global-network) and avoids the public Internet.

This specific article describes how to set up a Managed Private Endpoint from Azure Data Factory or Azure Synapse to Snowflake.

## Pre Setup Checklist
- [ ] &nbsp; As the function `SYSTEM$GET_PRIVATELINK_CONFIG()` currently only returns the PrivateLink alias, you must [open a support case to Snowflake Support](https://community.snowflake.com/s/article/How-To-Submit-a-Support-Case-in-Snowflake-Lodge) and reference [this Knowledge Article](https://community.snowflake.com/s/article/How-to-set-up-a-managed-private-endpoint-from-Azure-Data-Factory-or-Synapse-to-Snowflake) to request **Snowflake's endpoint service resource ID** of the Azure region of your Snowflake account.

<br>

- [ ] &nbsp; In your Snowflake account, run the following command:
    > `use role accountadmin;  `  
    >  
    > `select key, value::varchar from table(flatten(input=>parse_json(SYSTEM$GET_PRIVATELINK_CONFIG())));`

    Retrieve these items from the output:
    - privatelink-account-url
    - regionless-privatelink-account-url
    - privatelink_oscp-url

<br>

- [ ] &nbsp; Create a managed private endpoint from ADF/Synapse Studio with the resource ID recieved in step 1 and add the FQDN values found in step 2.
    ![adf_new_private_endpoint](/media/azure/private-link/adf_new_private_endpoint.png)
    ![adf_private_link_service](/media/azure/private-link/adf_private_link_service.jpeg)

<br>

> **_IMPORTANT:_** Make sure to add all of the above Snowflake endpoints (account locator, regionless account name, OCSP endpoint) under 'Fully qualified domain names with their privatelink hostnames, as demonstrated in the screenshot.

<br>

The connection should now be in **Pending** status.
![managed_private_endpoints_pending](/media/azure/private-link/managed_private_endpoints_pending.jpeg)

<br>

### For Azure Data Factory
- [ ] &nbsp; Click on your Managed Private Endpoint name, and retrieve the **Managed Private Endpoint Resource ID**
    ![managed_private_endpoint_resource_id](/media/azure/private-link/adf_managed_private_endpoint.jpeg)

### For Azure Synapse
- [ ] &nbsp; Retrieve the **Synapse Analytics workspace name** and the **Managed Private Endpoint name**

## Connection Setup Steps
- [ ] &nbsp; Contact Snowflake Technical Support, provide either the ADF MPE resource Id or Synapse workspace and MPE names from above.
- [ ] &nbsp; Once Snowflake Support confirms the connection is Approved,  you should see your managed private endpoint connection as Approved in your Synapse/ADF workspace.
    ![success_image](/media/azure/private-link/success_manage_private_endpoints.jpeg)
## Test Privatelink connectivity to Snowflake from the MPE in ADF.
    
- [ ] &nbsp; Select Integration runtimes and click the AutoResolveIntegrationRuntime.
  ![test_adf_connection](/media/azure/private-link/test_adf_connection.jpeg)

<br>

- [ ] &nbsp; In the window pop up, navigate to Virtual Network and set Interactive authoring to Enabled, then click apply.<br>
![integration_runtime](/media/azure/private-link/integration_runtime.jpeg)

<br>

- [ ] &nbsp; From the left hand side menu, select linked services then click New.
![linked_services](/media/azure/private-link/linked_services.jpeg)

<br>

- [ ] &nbsp; In the new linked service window, look for Snowflake, select the icon and hit Continue.
![new_linked_service](/media/azure/private-link/new_linked_service.jpeg)

<br>

It may take a couple of minutes for Interactive Authoring to show enabled. It should look like this:
<br>
![interactive_authoring](/media/azure/private-link/interactive_authoring.jpeg)

<br>

- [ ] &nbsp; Fill in the information relevant to the Snowlake account
    - To retrieve the account-identifier, use the `system$allowlist_privatelink()` system function, and extract the SNOWFLAKE_DEPLOYMENT value.
    - The account name value will have this format: `<account-locator>.<region-id>.privatelink`
    - It is important to include the keyword .privatelink at the end of the account identifier otherwise the connection will not go through the privatelink.
    ![new_linked_service_setup](/media/azure/private-link/new_linked_service_setup.jpeg)

<br>

- [ ] &nbsp; Click Test Connection, if successful, you will see this message:
    ![successful_connection](/media/azure/private-link/successful_connection.jpeg)


