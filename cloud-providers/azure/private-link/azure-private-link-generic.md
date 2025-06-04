---
layout: sidebar
title: Generic Azure Private Link Setup
description: Secure Connection between Azure Virtual Network and Snowflake VNet in Azure, not covering managed services like Azure Data Factory and Synapse.
---

## When To Use Private Link
- When industry standards demand a near completely private connection (ie. healthcare)
- Security requirements demand the vast majority of your traffic needs to take place only over Azure fiber via Microsoft Backbone.

## Overview
> **Note:** Private Link is a Business Critical Edition only feature.
[Azure Private Link](https://learn.microsoft.com/en-us/azure/private-link/private-link-overview) provides private connectivity to Snowflake by ensuring that access to snowflake is through a private IP address.

Traffic can only occur from the customer virtual network (VNet) to the Snowflake VNet using the [Microsoft Backbone](https://learn.microsoft.com/en-us/azure/networking/microsoft-global-network) and avoids the public Internet.

### Private Link Diagram
![azure_private_link_diagram](/media/azure/private-link/azure-privatelink.png)

## Pre Setup Checklist
> **NOTE:** This process does not support authorizing a managed Private Endpoint from Azure Data Factory, Synapse, or other managed services. To review more about that sort of connection, see [here](/cloud-providers/azure/azure-private-link-adf-synapse.html).

- [ ] In Azure at the subnet level, optionally enable a network policy for the Private Endpoint.

- [ ] Verify that the TCP ports 443 and 80 allow traffic to 0.0.0.0 in the network security group of the Private Endpoint network card.

    >  For help with the port configuration, contact your internal Azure administrator.
- [ ] Use [ARM VNets](https://learn.microsoft.com/en-us/azure/azure-resource-manager/)
- [ ] Use IPv4 TCP traffic only
