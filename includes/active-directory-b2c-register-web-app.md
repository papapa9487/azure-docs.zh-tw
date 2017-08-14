[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

若要註冊 Web 應用程式，請使用表格中所指定的設定。

![新 Web 應用程式的範例註冊設定](./media/active-directory-b2c-register-web-app/b2c-new-app-settings.png)

| 設定      | 範例值  | 說明                                        |
| ------------ | ------- | -------------------------------------------------- |
| **名稱** | Contoso B2C 應用程式 | 輸入應用程式的 [名稱]，此名稱可為取用者說明您的應用程式。 | 
| **包含 Web 應用程式 / Web API** | 是 | 針對 Web 應用程式選取 [是]。 |
| **允許隱含流程** | 是 | 如果您的應用程式使用 [OpenID Connect 登入](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md)，請選取 [是] |
| **回覆 URL** | `https://localhost:44316` | 回覆 URL 是 Azure AD B2C 傳回您應用程式要求之任何權杖的所在端點。 輸入[適當的](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url) [回覆 URL]。 在此範例中，您的應用程式位於本機並在通訊埠 44316 上進行接聽。 |

按一下 [建立]  以註冊您的應用程式。

新註冊的應用程式會顯示在 B2C 租用戶的應用程式清單中。 從清單中選取 Web 應用程式。 Web 應用程式的 [屬性] 窗格隨即顯示。

![Web 應用程式屬性](./media/active-directory-b2c-register-web-app/b2c-web-app-properties.png)

請記下全域唯一的 [應用程式用戶端識別碼]。 您可在您的應用程式程式碼中使用此識別碼。