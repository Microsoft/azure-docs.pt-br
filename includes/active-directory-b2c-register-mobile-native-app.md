[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Para registrar seu aplicativo móvel ou nativo, use as configurações especificadas na tabela.

![Exemplo de configurações de registro para o novo aplicativo móvel ou nativo](./media/active-directory-b2c-register-mobile-native-app/b2c-new-mobile-native-app-settings.png)

| Configuração      | Valor de exemplo  | Descrição                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Nome** | Aplicativo B2C da Contoso | Insira um **Nome** para o aplicativo que descreva seu aplicativo aos consumidores. |
| **Cliente nativo** | Sim | Selecione **Sim** para um aplicativo móvel ou nativo. |
| **URI de Redirecionamento Personalizado** | `com.onmicrosoft.contoso.appname://redirect/path` | Insira um URI de redirecionamento com um esquema personalizado. Escolha um [bom URI de redirecionamento](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-native-application-redirect-uri) e não inclua caracteres especiais, como sublinhados. |

Clique em **Criar** para registrar seu aplicativo.

Seu aplicativo recém-registrado é exibido na lista de aplicativos para o locatário B2C. Selecione seu aplicativo móvel ou nativo na lista. O painel de propriedade do aplicativo é exibido.

![Propriedades do aplicativo](./media/active-directory-b2c-register-mobile-native-app/b2c-mobile-native-app-properties.png)

Anote o identificador global exclusivo **ID do Cliente do Aplicativo**. Use a ID no código do seu aplicativo.

Se o aplicativo nativo chamar uma API da Web protegida pelo Azure AD B2C, execute estas etapas:
   1. Crie um segredo do aplicativo acessando a folha **Chaves** e clicando no botão **Gerar Chave**. Anote o valor da **Chave do Aplicativo**. Use o valor como o segredo do aplicativo no código do seu aplicativo.
   2. Clique em **Acesso à API**, clique em **Adicionar** e selecione sua API da Web e escopos (permissões).

> [!NOTE]
> Um **Segredo de Aplicativo** é uma credencial de segurança importante e deve ser protegido adequadamente.
> 
