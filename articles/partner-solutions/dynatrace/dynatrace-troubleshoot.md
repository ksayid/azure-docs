---
title: Troubleshooting Azure Native Dynatrace Service
description: This article provides information about troubleshooting Dynatrace for Azure 

ms.topic: conceptual
ms.date: 02/02/2023

---

# Troubleshoot Azure Native Dynatrace Service

In this article, you learn how to contact support when working with an Azure Native Dynatrace Service resource. Before contacting support, see [Fix common errors](#fix-common-errors).

## Contact support

To contact support about the Azure Native Dynatrace Service, select **New Support request** in the left pane. Select the link to the Dynatrace support website.

:::image type="content" source="media/dynatrace-troubleshoot/dynatrace-support.png" alt-text="Screenshot showing new support request selected in resource menu.":::

## Fix common errors

This document contains information about troubleshooting your solutions that use Dynatrace.

### Marketplace purchase errors

[!INCLUDE [marketplace-purchase-errors](../includes/marketplace-purchase-errors.md)]
 
If those options don't solve the problem, contact [Dynatrace support](https://support.dynatrace.com/).

### Unable to create Dynatrace resource

- To set up the Azure Native Dynatrace Service, you must have **Owner** or **Contributor** access on the Azure subscription. Ensure you have the appropriate access before starting the setup.

- Create fails because Last Name is empty. The issue happens when the user info in Microsoft Entra ID is incomplete and doesn't contain Last Name. Contact your Azure tenant's global administrator to rectify the issue and try again.

### Logs not being emitted or limit reached issue

- Resource doesn't support sending logs. Only resource types with monitoring log categories can be configured to send logs.  For more information, see [supported categories](../../azure-monitor/essentials/resource-logs-categories.md).

- Limit of five diagnostic settings reached. This displays the message of Limit reached against the resource. Each Azure resource can have a maximum of five diagnostic settings. For more information, see [diagnostic settings](../../azure-monitor/essentials/diagnostic-settings.md?tabs=portal) You can go ahead and remove the other destinations to make sure each resource is sending data to at max five destinations.

- Export of Metrics data isn't supported currently by the partner solutions under Azure Monitor diagnostic settings. 

### Single sign-on errors

- **Single sign-on configuration indicates lack of permissions**
  - Occurs when the user that is trying to configure single sign-on doesn't have Manage users permissions for the Dynatrace account. For a description of how to configure this permission, see [here](https://www.dynatrace.com/support/help/shortlink/azure-native-integration#setup).
- **Unable to save single sign-on settings**
  - Error happens when there's another Enterprise app that is using the Dynatrace SAML identifier. To find which app is using it, select **Edit** on the Basic **SAML** configuration section. To resolve this issue, either disable the other app or use the other app as the Enterprise app to set up SAML SSO.

- **App not showing in Single sign-on settings page**
  - First, search for application ID. If no result is shown, check the SAML settings of the app. The grid only shows apps with correct SAML settings.

### Metrics checkbox disabled

- To collect metrics, you must have owner permission on the subscription. If you're a contributor, refer to the contributor guide mentioned in [Configure metrics and logs](dynatrace-create.md#configure-metrics-and-logs).

### Free trial errors

- **Unable to create another free trial resource on Azure**
  - During free trials, Dynatrace accounts can only have one environment. You can therefore create only one Dynatrace resource during the trial period.
- **My Dynatrace free trial resource is deleted**
  - With the free trial plan, your Dynatrace resource on Azure will get deleted after trial expiry. If you require more time, contact [sales@dynatrace.com](mailto:sales@dynatrace.com).

## Next steps

- Learn about [managing your instance](dynatrace-how-to-manage.md) of Dynatrace.
- Get started with Azure Native Dynatrace Service on

    > [!div class="nextstepaction"]
    > [Azure portal](https://portal.azure.com/#view/HubsExtension/BrowseResource/resourceType/Dynatrace.Observability%2Fmonitors)

    > [!div class="nextstepaction"]
    > [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/dynatrace.dynatrace_portal_integration?tab=Overview)
