# OpenVPN MCP Server for CloudConnexa

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache2.0-green.svg)](LICENSE)

This MCP server enables interaction with your CloudConnexa environment through standardized tools that can be used by any MCP client of your choice, such as [5ire](https://5ire.app), [Claude Desktop](https://claude.ai/download) and [Cursor](https://docs.cursor.com/context/model-context-protocol). It empowers AI assistants to manage your CloudConnexa tenant and fulfil requests made in natural language.

## Table of Contents

- [ Prerequisites](#-prerequisites)
- [ Setting up your OpenVPN MCP Server for CloudConnexa](#️-setting-up-your-openvpn-mcp-server-for-cloudconnexa)
- [Generate Your API Token](#1-generate-your-api-token)
- [Add the MCP Server to Your MCP Client](#5ire-app-setup)
 - [How to add GPT-5 model to 5ire App](#adding-gpt-5-model-to-5ire-app)
- [💬 Example Prompts](#-example-prompts)
- [🛠 Available Tools](#available-tools)
- [🧯 Troubleshooting](#troubleshooting)
- [🤝 Contributing](#contributing)
- [📄 License](#license)


## 🧰 Prerequisites

To use the OpenVPN MCP Server for CloudConnexa, you’ll need:

- [CloudConnexa account](https://openvpn.net)
- [Docker](https://docker.com):
  - Visit docker.com
  - Download Docker desktop for your operating system
  - Install Docker by following the instructions for your operating system
  - Open Docker and ensure it is running
  
- A supported MCP client (any of those):
  - [5ire](https://5ire.app) 
  - [Claude Desktop](https://claude.ai/download) (v1.9+)
  - [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview)
  - [Cursor](https://docs.cursor.com/context/model-context-protocol)
  - [VS Code](https://code.visualstudio.com/) with [Cline plugin](vscode:extension/saoudrizwan.claude-dev)
- (Optional): [GitHub CLI (gh)](https://cli.github.com) 


---

## Setting up your OpenVPN MCP Server for CloudConnexa

### 1. Generate Your API Token

Log in to [CloudConnexa](https://openvpn.net/cloud-vpn/) and go to **API & Logs** → **API** → **Create credentials**, fill in a name and click  **Create**
Copy and save your **Public API Client ID** and **Public API Client Secret**

### 2. In the root of the downloaded repository build the docker container:
`docker build -t mcp-server-public .`

### 3. Start the MCP server:
In **Terminal** run:
`bash start.sh`

Then download 5ire App and complete the MCP setup:

### 5ire App Setup

1. In 5ire App: **Tools → +Local button**
2. This will open Add New Tool modal, fill in the details:
- **openvpnmcp** as **Tool Key**
- **OpenVPN MCP** as **Name**
- **http://0.0.0.0:9999/sse** as **URL**
3. Click **Save** 
4. Switch the **toggle** to **ON** to turn on the MCP server
6. Head to **Workspace → Providers** and add your LLM Provider API key. We recommend using **GPT-5** from OpenAI for best results
7. Click "**New Chat**"
8. Select your desired LLM model and enter the prompt: **"Discover graphql_schema and tell me what tools are available?"**

That's it, your MCP server is now connected to 5ire App and you can ask your AI assistant to execute any of the available tools. Check out the example prompts section in this README for some suggestions.

_Setting up OpenVPN MCP Server in 5ire App_

---
### Adding GPT-5 model to 5ire App

GPT-5 has performed best during our tests, however it is not available by default in 5ire App. 
Here are brief instructions how to add it as an available model:
1. In 5ire App head to: **Workspace → Providers → OpenAI**  and click the "+ Model" button
2. Fill in **gpt-5** for **Name** and **Display Name**
3. Set **Context Window** to **400000** and Max Tokens to **16384** - 
4. Set **Input Price** to **$1.25** and **Output Price** to **$0**
5. Toggle **Tools** to **ON**
6. Click **Save**
7. Go to **New Chat** change temperature to 1.0 (GPT-5 requires >1) and adjust **Max Tokens** value

Those are some example values, you should adjust them in accordance with your own cost estimation of using GPT-5.

## 💬 Example Prompts

Here are some examples you can ask your AI assistant:

`"What are the available tools in graphql_schema"` - **This prompt is required to be run once in order for the MCP client to discover all available tools**
It will display all available MCP tools, then you can ask anything based on them. 

Here are some examples of queries:
```
“Which users are online right now?”
“What is my device posture policy?”
“Show DNS logs for yesterday”
“Show me a summary of my configuration”
```
Currently only read-only tools are available, but we are working on expanding the list of available tools.

---

## Available Tools

| Category                  | Tool                                       | Description                                                   |
|--------------------------|--------------------------------------------|---------------------------------------------------------------|
| **Hosts and related**        | `hosts`                                    | List all hosts.                                               |
|                          | `hostById(id)`                             | Get a host by ID.                                             |
|                          | `hostApplications(hostId)`                 | List applications attached to a host.                         |
|                          | `hostApplicationById(id)`                  | Get a specific host application.                              |
|                          | `hostServices(hostId)`                     | List IP-based services on a host.                             |
|                          | `hostServiceById(id)`                      | Get a host IP service by ID.                                  |
|                          | `hostConnectors(hostId)`                   | List connectors associated with a host.                       |
|                          | `hostConnectorsById(id)`                   | Get a specific host connector.                                |
| **Networks and related**     | `networks`                                 | List all networks.                                            |
|                          | `networkById(id)`                          | Get a network by ID.                                          |
|                          | `networkApplications(networkId)`          | List applications associated with a network.                  |
|                          | `networkApplicationById(id)`              | Get a specific network application.                           |
|                          | `networkServices(networkId)`              | List IP-based services on a network.                          |
|                          | `networkServiceById(id)`                  | Get a network IP service by ID.                               |
|                          | `networkRoutes(networkId)`                | List network routes (IPv4/IPv6).                              |
|                          | `networkRouteById(id)`                    | Get a network route by ID.                                    |
|                          | `networkConnectors(networkId)`            | List connectors for a network.                                |
|                          | `networkConnectorsById(id)`               | Get a specific network connector.                             |
| **Traffic & sessions**       | `visitedDomains(startHour, hoursBack=1)`  | Domain resolution stats for a time window.                    |
|                          | `sessions(startDate, endDate, ...)`       | Retrieve session records (active/historical).                 |
| **Users, groups, devices**   | `users`                                    | List users.                                                   |
|                          | `userById(id)`                             | Get a user by ID.                                             |
|                          | `userGroups`                               | List user groups.                                             |
|                          | `userGroupById(id)`                        | Get a user group by ID.                                       |
|                          | `devices(userId)`                          | List devices, optionally filtered by user.                    |
|                          | `deviceById(id, userId)`                   | Get a device by ID for a user.                                |
|                          | `vpnRegions`                               | List available VPN regions.                                   |
| **Access control**           | `accessGroups`                             | List access groups/policies.                                  |
|                          | `accessGroupById(id)`                      | Get an access group by ID.                                    |
|                          | `accessVisibilityEnabled`                 | Whether access visibility is enabled.                         |
| **Device posture**           | `devicePostures`                           | List device posture policies.                                 |
|                          | `devicePostureById(id)`                    | Get a device posture policy by ID.                            |
| **DNS and logging**          | `dnsLogUserResolutionsEnabled`            | Whether per-user DNS resolution logging is enabled.           |
|                          | `dnsRecords`                               | List custom DNS records.                                      |
|                          | `dnsRecordById(id)`                        | Get a DNS record by ID.                                       |
|                          | `dnsServerAddresses`                       | Get DNS server addresses configured.                          |
|                          | `dnsProxyEnabled`                          | Whether DNS proxying is enabled.                              |
|                          | `defaultDnsSuffix`                         | Default DNS suffix.                                           |
|                          | `dnsZones`                                 | List DNS zones.                                               |
| **Location contexts**        | `locationContexts`                         | List location context policies.                               |
|                          | `locationContextById(id)`                 | Get a location context by ID.                                 |
| **SCIM**                     | `scimCurrentUser`                          | Get SCIM token/config info for the current user.              |
|                          | `scimUsers(startIndex, count, filter)`    | SCIM-compliant user listing.                                  |
|                          | `scimUserById(id)`                         | Get a SCIM user by ID.                                        |
| **Organization settings**    | `topology`                                 | Current topology setting.                                     |
|                          | `defaultRegion`                            | Default VPN region.                                           |
|                          | `snat`                                     | Whether SNAT is enabled.                                      |
|                          | `subnet`                                   | Organization subnets (IPv4/IPv6).                             |
|                          | `domainRoutingSubnet`                      | Subnet used for domain routing.                               |
|                          | `clientOptions`                            | Client option flags.                                          |
|                          | `connectionTimeout`                        | Connection timeout (seconds).                                 |
|                          | `defaultConnectAuth`                       | Default connect authentication policy.                        |
|                          | `deviceAllowancePerUser`                  | Max devices per user.                                         |
|                          | `forceUpdateDeviceAllowance`              | Force device allowance update flag.                           |
|                          | `deviceEnforcementLevel`                  | Device compliance enforcement level.                          |
|                          | `profileDistribution`                     | Client/profile distribution method.                           |
|                          | `twoFactorAuthEnabled`                    | Whether 2FA is enabled.                                       |
|                          | `trustedDevicesAllowed`                   | Whether trusted devices are allowed.                          |
|                          | `ldapGroupMappings`                       | LDAP group-to-app mappings.                                   |
|                          | `samlGroupMappings`                       | SAML group-to-app mappings.                                   |


---

## Troubleshooting
### GPT-5 model is not available
- See the [How to add GPT-5 model to 5ire App](#adding-gpt-5-model-to-5ire-app) section

### The LLM model does not support this temperature

- GPT-5 model supports only temperature 1.0 and greater

### Context window size reached
- Just above the prompt in 5ire App you can change how many tokens should the context length be. If you often run into this error you can adjust this setting.

### LLM replies that it can not reach MCP tools
- Ensure that under Tools the MCP server has a green online status, if not then turn the toggle ON. If it's still not working check in the terminal that the server is running.

### LLM replies that it can not find any MCP tools
- Try running this prompt: "Discover graphql_schema" then try again.

### API credentials not working?

- Try generating a new pair of credentials from CloudConnexa -> API & Logs -> API

---

## Contributing

We welcome your contributions. Enhancements, bug reports, and documentation improvements are valued.

1. Fork this repository

2. Create a feature branch (e.g., git checkout -b feature/new-tool)

3. Submit a pull request for review

---

## License

This project is licensed under the [Apache 2.0](LICENSE).
