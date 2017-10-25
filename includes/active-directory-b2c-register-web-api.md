[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Para registrar sua API da Web, use as configurações especificadas na tabela.

![Exemplo de configurações de registro para nova API Web](./media/active-directory-b2c-register-web-api/b2c-new-web-api-settings.png)

| Configuração      | Valor de exemplo  | Descrição                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Nome** | API B2C da Contoso | Insira um **Nome** para o aplicativo que descreva sua API aos consumidores. | 
| **Incluir aplicativo Web/API Web** | Sim | Selecione **Sim** para uma API Web. |
| **Permitir fluxo implícito** | Sim | Escolha **Sim** se o seu aplicativo usar [Entrada do OpenID Connect](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md) |
| **URL de Resposta** | `https://localhost:44316/` | As URLs de Resposta são pontos de extremidade para onde o Azure AD B2C retorna quaisquer tokens que seus aplicativo solicitarem. Insira uma **URL de Resposta** [apropriada](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url). Neste exemplo, a sua API Web é local e escuta na porta 44316. |
| **URI da ID do Aplicativo** | api | O URI da ID do Aplicativo é o identificador usado para sua API Web. O URI do identificador completo, incluindo o domínio, é gerado para você. |

Clique em **Criar** para registrar seu aplicativo.

Seu aplicativo recém-registrado é exibido na lista de aplicativos para o locatário B2C. Selecione sua API Web na lista. O painel de propriedade da API é exibido.

![Propriedades da API Web](./media/active-directory-b2c-register-web-api/b2c-web-api-properties.png)

Anote o identificador global exclusivo **ID do Cliente do Aplicativo**. Use a ID no código do seu aplicativo.