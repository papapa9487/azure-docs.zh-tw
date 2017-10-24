以下是 Azure Active Directory 服務的使用條件約束和其他服務限制。

| 類別 | 限制 |
| --- | --- |
| 目錄 |單一使用者最多只能與 20 個 Azure Active Directory 目錄相關聯。<br />可能組合的範例︰ <ul> <li>單一使用者建立 20 個目錄。</li><li>單一使用者新增 20 個目錄作為成員。</li><li>單一使用者建立 10 個目錄，之後由其他使用者加入到 10 個不同的目錄。</li></ul> |
| 物件 |<ul><li>在免費版的 Azure Active Directory 中，最多可以在單一目錄中建立 500,000 個物件。</li><li>非系統管理員的使用者最多可以建立 250 個物件。</li></ul> |
| 結構描述延伸模組 |<ul><li>字串類型延伸模組最多可有 256 個字元。 </li><li>二進位類型延伸模組受限於 256 個位元組。</li><li>任何單一物件均可寫入 100 個延伸模組值 (所有類型和所有應用程式皆可)。</li><li>只能使用「字串」類型或「二進位」類型單一值屬性來擴充 “User”、“Group”、“TenantDetail”、“Device”、“Application” 和 “ServicePrincipal” 實體。</li><li>結構描述延伸模組僅適用於圖形 API 版本 1.21 預覽。 應用程式必須取得寫入權限才能登錄延伸模組。</li></ul> |
| 應用程式 |最多 100 個使用者可以成為單一應用程式的擁有者。 |
| 群組 |<ul><li>最多 100 個使用者可以成為單一群組的擁有者。</li><li>任何數目的物件都可以是 Azure Active Directory 中的單一群組的成員。</li><li>可以使用 Azure Active Directory 目錄同步處理 (DirSync) 從您的內部部署 Active Directory 同步至 Azure Active Directory 群組中的成員數目限制為 15K 個成員。</li><li>可以使用 Azure AD Connect 從您的內部部署 Active Directory 同步至 Azure Active Directory 群組中的成員數目限制為 50K 個成員。</li></ul> |
| 存取面板 |<ul><li>對於 Azure AD Premium 或 Enterprise Mobility Suite 使用者已指派授權，每位使用者在存取面板中可以看到的應用程式數目沒有限制。</li><li>對於 Azure Active Directory 免費版或 Azure AD Basic 版的使用者已指派授權，每位使用者在存取面板中最多可以看到 10 個應用程式磚 (例如：Box、Salesforce 或 Dropbox)。 此限制不適用於系統管理員帳戶。</li></ul> |
| 報告 | 在任何報告中，最多可以檢視或下載 1000 個資料列。 任何其他資料會遭到截斷。 |
| 管理單位 | 物件可以是有不超過 30 個管理單位的成員。 |
