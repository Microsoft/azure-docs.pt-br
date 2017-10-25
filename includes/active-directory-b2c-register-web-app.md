[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Para registrar seu aplicativo Web, use as configurações especificadas na tabela.

![Exemplo de configurações de registro para nova aplicativo Web](./media/active-directory-b2c-register-web-app/b2c-new-app-settings.png)

| Configuração      | Valor de exemplo  | Descrição                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Nome** | Aplicativo B2C da Contoso | Insira um **Nome** para o aplicativo que descreva seu aplicativo aos consumidores. | 
| **Incluir aplicativo Web/API Web** | Sim | Selecione **Sim** para um aplicativo Web. |
| **Permitir fluxo implícito** | Sim | Escolha **Sim** se o seu aplicativo usar [Entrada do OpenID Connect](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md) |
| **URL de Resposta** | `https://localhost:44316` | As URLs de Resposta são pontos de extremidade para onde o Azure AD B2C retorna quaisquer tokens que seus aplicativo solicitarem. Insira uma **URL de Resposta** [apropriada](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url). Neste exemplo, seu aplicativo é local e escuta na porta 44316. |

Clique em **Criar** para registrar seu aplicativo.

Seu aplicativo recém-registrado é exibido na lista de aplicativos para o locatário B2C. Selecione o aplicativo Web na lista. O painel de propriedade do aplicativo Web é exibido.

![Propriedades do aplicativo Web](./media/active-directory-b2c-register-web-app/b2c-web-app-properties.png)

Anote o identificador global exclusivo **ID do Cliente do Aplicativo**. Use a ID no código do seu aplicativo.