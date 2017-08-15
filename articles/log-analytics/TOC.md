# 概觀
## [什麼是 Log Analytics？](log-analytics-overview.md)
## [資料安全性](log-analytics-security.md)

# 開始使用
## [註冊 Log Analytics](log-analytics-get-started.md)
## [管理存取權](log-analytics-manage-access.md)
## [使用狀況資料](log-analytics-usage.md)
## [Log Analytics 常見問題集](log-analytics-faq.md)
## [服務提供者](log-analytics-service-providers.md)

# 作法
## 收集資料
### 連接的來源
#### [Windows 代理程式](log-analytics-windows-agents.md)
#### [Linux 代理程式](log-analytics-agent-linux.md)
#### [Azure 虛擬機器](log-analytics-azure-vm-extension.md)
#### [Azure 資源](log-analytics-azure-storage.md)
#### [Operations Manager](log-analytics-om-agents.md)
#### [組態管理員](log-analytics-sccm.md)
#### [OMS 閘道](log-analytics-oms-gateway.md)
### 資料來源
#### [資料來源概觀](log-analytics-data-sources.md)
#### [Windows 事件](log-analytics-data-sources-windows-events.md)
#### [自訂 JSON 資料](log-analytics-data-sources-json.md)
#### [已收集的效能資料](log-analytics-data-sources-collectd.md)
#### [Nagios 和 Zabbix 的警示](log-analytics-data-sources-alerts-nagios-zabbix.md)
#### [Syslog](log-analytics-data-sources-syslog.md)
#### [效能計數器](log-analytics-data-sources-performance-counters.md)
#### [Linux 應用程式的效能](log-analytics-data-sources-linux-applications.md)
#### [IIS 記錄檔](log-analytics-data-sources-iis-logs.md)
#### [自訂的記錄檔](log-analytics-data-sources-custom-logs.md)
#### [自訂欄位](log-analytics-custom-fields.md)

## 查詢資料
### [記錄檔搜尋概觀](log-analytics-log-searches.md)
### [搜尋參考](log-analytics-search-reference.md)
#### [規則運算式](log-analytics-log-searches-regex.md)
### [搜尋結果中採取行動](log-analytics-log-search-takeaction.md)
### [電腦群組](log-analytics-computer-groups.md)

## 記錄搜尋
### [升級您的工作區](log-analytics-log-search-upgrade.md)
### [常見問題集](log-analytics-log-search-faq.md)
### [記錄檔搜尋概觀](log-analytics-log-search-new.md)
### [記錄搜尋入口網站](log-analytics-log-search-portals.md)
#### [使用入口網站記錄搜尋](log-analytics-log-search-log-search-portal.md)
### [從舊版語言轉換](log-analytics-log-search-transition.md)
### [流程連接器](log-analytics-flow-tutorial.md)

## 分析資料
### [儀表板](log-analytics-dashboards.md)
### [檢視設計工具](log-analytics-view-designer.md)
### [Power BI](log-analytics-powerbi.md)
## 建立警示
### [了解警示](log-analytics-alerts.md)
### [警示動作](log-analytics-alerts-actions.md)
### 建立警示規則
#### [OMS 入口網站](log-analytics-alerts-creating.md)
#### [REST API](log-analytics-api-alerts.md)
#### [Resource Manager 範本](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
### [Webhook 動作範例](log-analytics-alerts-webhooks.md)
### [警示管理解決方案](log-analytics-solution-alert-management.md)
## 使用解決方案
### [解決方案概觀](log-analytics-add-solutions.md)
### [目標解決方案](../operations-management-suite/operations-management-suite-solution-targeting.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [活動 Log Analytics](log-analytics-activity.md)
### [AD 評估](log-analytics-ad-assessment.md)
### [AD 複寫狀態](log-analytics-ad-replication-status.md)
### [代理程式健全狀況](../operations-management-suite/oms-solution-agenthealth.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [警示管理](log-analytics-solution-alert-management.md)
### [Application Insights Connector](log-analytics-app-insights-connector.md)
### [Azure SQL 分析](log-analytics-azure-sql.md)
### [Azure Web Apps 分析](log-analytics-azure-web-apps-analytics.md)
### [容量和效能](log-analytics-capacity.md)
### [變更追蹤](log-analytics-change-tracking.md)
### [容器](log-analytics-containers.md)
### [DNS 分析](log-analytics-dns.md)
### [IT 服務管理連接器](log-analytics-itsmc-overview.md)
#### [IT 服務管理連線](log-analytics-itsmc-connections.md)
### [金鑰保存庫](log-analytics-azure-key-vault.md)
### Logic Apps B2B 訊息
#### [Logic Apps B2B 訊息解決方案](../logic-apps/logic-apps-track-b2b-messages-omsportal.md?toc=%2fazure%2flog-analytics%2ftoc.json)
#### [Logic Apps B2B 自訂追蹤結構描述](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [惡意程式碼評估](log-analytics-malware.md)
### [網路分析](log-analytics-azure-networking-analytics.md)
### [網路效能監視器](log-analytics-network-performance-monitor.md)
### [Office 365](../operations-management-suite/oms-solution-office-365.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [SCOM 評估](log-analytics-scom-assessment.md)
### [安全性稽核](../operations-management-suite/oms-security-getting-started.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [Service Fabric](log-analytics-service-fabric.md)
### [服務對應](../operations-management-suite/operations-management-suite-service-map.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [SQL 評估](log-analytics-sql-assessment.md)
### [Surface Hub](log-analytics-surface-hubs.md)
### [更新管理](../operations-management-suite/oms-solution-update-management.md)
### [VMware](log-analytics-vmware.md)
### Windows 分析
#### [更新合規性](https://technet.microsoft.com/itpro/windows/manage/update-compliance-get-started)
#### [升級整備程度](https://technet.microsoft.com/itpro/windows/deploy/upgrade-readiness-get-started)
### [連線資料](log-analytics-wire-data.md)
## 開發
### [資料收集器 API](log-analytics-data-collector-api.md)
### [PowerShell Cmdlet](log-analytics-powershell-workspace-configuration.md)
### [Resource Manager 範本](log-analytics-template-workspace-configuration.md)
### [記錄檔搜尋 API](log-analytics-log-search-api.md)
#### [Python](log-analytics-log-search-api-python.md)
### [警示 API](log-analytics-api-alerts.md)

# 參考
## [PowerShell](/powershell/module/azurerm.operationalinsights)
## [REST](/rest/api/loganalytics)

# 資源
## [Azure 藍圖](https://azure.microsoft.com/roadmap/)
## [價格](https://azure.microsoft.com/pricing/details/log-analytics/)
## [定價計算機](https://azure.microsoft.com/pricing/calculator/)
## [服務更新](https://azure.microsoft.com/updates/?product=log-analytics)
## [Windows 分析](https://www.microsoft.com/en-us/WindowsForBusiness/windows-analytics)
