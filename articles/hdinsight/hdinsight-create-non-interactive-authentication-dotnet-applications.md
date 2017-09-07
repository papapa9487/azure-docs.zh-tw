---
title: "建立非互動式驗證 .NET HDInsight 應用程式 - Azure | Microsoft Docs"
description: "了解如何建立非互動式驗證 .NET HDInsight 應用程式。"
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 7821a9e60e60ff01cff06db2a6f216a260c1c41a
ms.contentlocale: zh-tw
ms.lasthandoff: 06/09/2017

---
# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>建立非互動式驗證 .NET HDInsight 應用程式
您可以使用應用程式本身的身分識別 (非互動式) 或使用應用程式的登入使用者的身分識別 (互動式)，執行 .NET Azure HDInsight 應用程式。 如需互動式應用程式的範例，請參閱[連接至 Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight)。 本文將說明如何建立非互動式驗證 .NET 應用程式，來連接到 Azure 及管理 HDInsight。

從非互動式 .NET 應用程式，您需要︰

* 您的 Azure 訂用帳戶租用戶識別碼 (又稱為目錄識別碼)。 請參閱[取得租用戶識別碼](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id)。
* Azure Active Directory 應用程式用戶端識別碼。 請參閱[建立 Azure Active Directory 應用程式](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)以及[取得應用程式識別碼](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)
* Azure Active Directory 應用程式祕密金鑰。 請參閱[取得應用程式驗證金鑰](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)

## <a name="prerequisites"></a>必要條件
* HDInsight 叢集。 請參閱[入門教學課程](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)。



## <a name="assign-azure-ad-application-to-role"></a>將 Azure AD 應用程式指派給角色
您必須將應用程式指派給某個 [角色](../active-directory/role-based-access-built-in-roles.md) ，以便授與它執行動作的權限。 您可以針對訂用帳戶、資源群組或資源的層級設定範圍。 較低的範圍層級會繼承較高層級的權限 (舉例來說，為資源群組的讀取者角色新增應用程式，代表該角色可以讀取資源群組及其所包含的任何資源)。 在本教學課程中，您將在資源群組層級設定範圍。 如需詳細資訊，請參閱[使用角色指派來管理 Azure 訂用帳戶資源的存取權](../active-directory/role-based-access-control-configure.md)

**將擁有者角色新增至 Azure AD 應用程式**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左側面板上的 [資源群組] 。
3. 按一下資源群組，它包含您稍後在本教學課程中要在其中執行 Hive 查詢的 HDInsight 叢集。 如果有太多的資源群組，您可以使用篩選器。
4. 從資源群組功能表中，按一下 [存取控制 (IAM)]。
5. 從 [使用者] 刀鋒視窗中，按一下 [新增]。
6. 依照指示以將 [擁有者] 角色新增至您在上一個程序中建立的 Azure AD 應用程式。 當您成功完成此作業時，您會看到 [使用者] 刀鋒視窗中列出的應用程式具有 [擁有者] 角色。

## <a name="develop-hdinsight-client-application"></a>開發 HDInsight 用戶端應用程式

1. 建立 C# 主控台應用程式。
2. 新增以下 Nuget 套件：

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. 使用下列程式碼範例：

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.HDInsight;
        
        namespace CreateHDICluster
        {
            internal class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
        
                private static Guid SubscriptionId = new Guid("<Enter Your Azure Subscription ID>");
                private static string tenantID = "<Enter Your Tenant ID (A.K.A. Directory ID)>";
                private static string applicationID = "<Enter Your Application ID>";
                private static string secretKey = "<Enter the Application Secret Key>";
        
                private static void Main(string[] args)
                {
                    var key = new SecureString();
                    foreach (char c in secretKey) { key.AppendChar(c); }

                    var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
        
                    var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                    resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        
                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
        
                    var results = _hdiManagementClient.Clusters.List();
                    foreach (var name in results.Clusters)
                    {
                        Console.WriteLine("Cluster Name: " + name.Name);
                        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                        Console.WriteLine("\t Cluster location: " + name.Location);
                        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                    }
                    Console.WriteLine("Press Enter to continue");
                    Console.ReadLine();
                }

                /// Get the access token for a service principal and provided key                
                public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
                {
                    var authFactory = new AuthenticationFactory();
                    var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                    var accessToken =
                        authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
        
                    return new TokenCloudCredentials(accessToken);
                }
        
                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
            }
        }

## <a name="next-steps"></a>後續步驟
* [使用入口網站建立 Azure Active Directory 應用程式和服務主體](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [使用 Azure Resource Manager 驗證服務主體](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)

