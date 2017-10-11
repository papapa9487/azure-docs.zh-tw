### <a name="troubleshoot-azure-diagnostics"></a>針對 Azure 診斷進行疑難排解

如果您收到下列錯誤訊息，則未註冊 Microsoft.insights 資源提供者︰

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

若要註冊資源提供者，請在 Azure 入口網站中執行下列步驟：

1.  在左側的導覽窗格中，按一下 [訂用帳戶]。
2.  選取錯誤訊息中識別的訂用帳戶
3.  按一下 [資源提供者]
4.  尋找 Microsoft.insights 提供者
5.  按一下 [註冊] 連結

![註冊 microsoft.insights 資源提供者](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

註冊 Microsoft.insights 資源提供者後，請重試設定診斷。


在 PowerShell 中，如果您收到下列錯誤訊息，則需要更新 PowerShell 版本︰

`Set-AzureRmDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

使用[開始使用 Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) 一文中的指示，將 PowerShell 版本更新至 2016 年 11 月 (v2.3.0) 或更新版本。
