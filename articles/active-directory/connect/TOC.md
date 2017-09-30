# 概觀
## [何謂 Azure AD Connect](active-directory-aadconnect.md)
## [何謂 Azure AD Connect 同步處理？](active-directory-aadconnectsync-whatis.md)
### [使用者和連絡人](active-directory-aadconnectsync-understanding-users-and-contacts.md)
### [架構](active-directory-aadconnectsync-understanding-architecture.md)
### [宣告式佈建](active-directory-aadconnectsync-understanding-declarative-provisioning.md)
#### [宣告式佈建運算式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
### [預設組態](active-directory-aadconnectsync-understanding-default-configuration.md)
## [何謂 Azure AD Connect 和同盟？](active-directory-aadconnectfed-whatis.md)


# 開始使用
## [必要條件](active-directory-aadconnect-prerequisites.md)
## [安裝 Azure AD Connect](active-directory-aadconnect-select-installation.md)
### [快速設定](active-directory-aadconnect-get-started-express.md)
### [自訂設定](active-directory-aadconnect-get-started-custom.md)
### [從 DirSync 升級](active-directory-aadconnect-dirsync-upgrade-get-started.md)
### [從舊版升級](active-directory-aadconnect-upgrade-previous-version.md)
### [使用現有的 ADSync 資料庫安裝](active-directory-aadconnect-existing-database.md)

# 作法
## 規劃和設計
### [設計概念](active-directory-aadconnect-design-concepts.md)
### [Azure AD Connect 的拓撲](active-directory-aadconnect-topologies.md)
### [Azure 中的 Active Directory Federation Services](active-directory-aadconnect-azure-adfs.md)
### [執行個體的特殊考量](active-directory-aadconnect-instances.md)
### [適用於已經有 Azure AD 時](active-directory-aadconnect-existing-tenant.md)
## [管理 Azure AD Connect](active-directory-aadconnect-whats-next.md)
### [O365 和 Azure AD 更新憑證](active-directory-aadconnect-o365-certs.md)
### [更新適用於 Active Directory 同盟服務 (AD FS) 伺服器陣列的 SSL 憑證](active-directory-aadconnectfed-ssl-update.md)
### [啟用裝置回寫](active-directory-aadconnect-feature-device-writeback.md)
### [使用者單一登入選項](active-directory-aadconnect-user-signin.md)
#### [順暢單一登入](active-directory-aadconnect-sso.md)
##### [快速入門](active-directory-aadconnect-sso-quick-start.md)
##### [運作方式](active-directory-aadconnect-sso-how-it-works.md)
##### [常見問題集](active-directory-aadconnect-sso-faq.md)
##### [疑難排解](active-directory-aadconnect-troubleshoot-sso.md)
#### [傳遞驗證](active-directory-aadconnect-pass-through-authentication.md)
##### [快速入門](active-directory-aadconnect-pass-through-authentication-quick-start.md)
##### [目前限制](active-directory-aadconnect-pass-through-authentication-current-limitations.md)
##### [運作方式](active-directory-aadconnect-pass-through-authentication-how-it-works.md)
##### [升級預覽代理程式](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md)
##### [智慧鎖定](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)
##### [常見問題集](active-directory-aadconnect-pass-through-authentication-faq.md)
##### [疑難排解](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)
##### [深入探討安全性](active-directory-aadconnect-pta-security-deep-dive.md)
### [對同盟的多網域支援](active-directory-aadconnect-multiple-domains.md)
### [自動升級](active-directory-aadconnect-feature-automatic-upgrade.md)
### [使用 SAML 2.0 識別提供者 (IdP) 來進行單一登入](active-directory-aadconnect-federation-saml-idp.md)



## 管理 Azure AD Connect 同步處理
### [防止意外刪除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
### [密碼同步處理](active-directory-aadconnectsync-implement-password-synchronization.md)
### [Azure AD 服務帳戶](active-directory-aadconnectsync-howto-azureadaccount.md)
### [安裝精靈](active-directory-aadconnectsync-installation-wizard.md)
### [變更預設組態](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)
### [設定篩選](active-directory-aadconnectsync-configure-filtering.md)
### [排程器](active-directory-aadconnectsync-feature-scheduler.md)
### [目錄延伸模組](active-directory-aadconnectsync-feature-directory-extensions.md)

### [變更 Azure AD 同步服務帳戶密碼](active-directory-aadconnectsync-change-serviceacct-pass.md)
### [變更 AD DS 帳戶密碼](active-directory-aadconnectsync-change-addsacct-pass.md)
### [啟用 AD 資源回收筒](active-directory-aadconnectsync-recycle-bin.md)

### [同步處理服務管理員](active-directory-aadconnectsync-service-manager-ui.md)
#### [作業](active-directory-aadconnectsync-service-manager-ui-operations.md)
#### [連接器](active-directory-aadconnectsync-service-manager-ui-connectors.md)
#### [Metaverse 設計工具](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)
#### [Metaverse 搜尋](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)


## 管理同盟服務
### [管理和自訂](active-directory-aadconnect-federation-management.md)
### [將多個 Azure AD 執行個體與單一 AD FS 執行個體同盟](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)


## 疑難排解
### [連線能力](active-directory-aadconnect-troubleshoot-connectivity.md)
### [同步處理期間的錯誤](active-directory-aadconnect-troubleshoot-sync-errors.md)
### [物件未同步處理](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)
### [密碼同步處理](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)
### [userCertificate 所造成的 LargeObject 錯誤](active-directory-aadconnectsync-largeobjecterror-usercertificate.md)
### [如何從 LocalDB 10-GB 的限制復原](active-directory-aadconnect-recover-from-localdb-10gb-limit.md)

# 參考
## [程式碼範例](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory)
## [身分識別同步處理和重複屬性恢復功能](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
## [混合式身分識別所需的連接埠和通訊協定](active-directory-aadconnect-ports.md)
## [預覽中的功能](active-directory-aadconnect-feature-preview.md)
## [版本歷程記錄](active-directory-aadconnect-version-history.md)
## [帳戶和權限](active-directory-aadconnect-accounts-permissions.md)

## Azure AD Connect 同步處理
### [將屬性同步處理至 Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md)
### [連接器版本發行歷程記錄](active-directory-aadconnectsync-connector-version-history.md)
### [函式參考](active-directory-aadconnectsync-functions-reference.md)
### [作業工作和考量](active-directory-aadconnectsync-operations.md)
### [Azure AD 同盟相容性清單](active-directory-aadconnect-federation-compatibility.md)
### [技術概念](active-directory-aadconnectsync-technical-concepts.md)
### [服務功能](active-directory-aadconnectsyncservice-features.md)




# 相關參考
## [在雲端中監視內部部署身分識別基礎結構和同步處理服務。](../connect-health/active-directory-aadconnect-health.md)
## [混合式身分識別設計指南](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)


# 資源
## [Azure 藍圖](https://azure.microsoft.com/roadmap/?category=security-identity)
##[Azure AD Connect 常見問題集](active-directory-aadconnect-faq.md)
##[DirSync 淘汰](active-directory-aadconnect-dirsync-deprecated.md)
## [定價計算機](https://azure.microsoft.com/pricing/calculator/)

