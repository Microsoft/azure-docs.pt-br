Para habilitar a entrada no aplicativo, você precisará criar uma política de entrada. Essa política descreve as experiências pelas quais os consumidores passarão durante a entrada e o conteúdo de tokens que o aplicativo receberá de entradas bem-sucedidas.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Na seção de políticas das configurações, selecione **Políticas de inscrição ou entrada** e clique em **+ Adicionar**.

![Selecione as políticas de inscrição ou entrada e clique no botão Adicionar](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-policy.png)

Insira um **Nome** de política para referência de seu aplicativo. Por exemplo, insira: `SiUpIn`.

Selecione **Provedores de identidade** e marque **Inscrição por email**. Opcionalmente, você também pode selecionar provedores de identidade social, se já configurado. Clique em **OK**.

![Selecione Inscrição por email como provedor de identidade e clique no botão OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-identity-providers.png)

Selecione **Atributos de inscrição**. Escolha os atributos que você deseja coletar do consumidor durante a inscrição. Por exemplo, marque **País/Região**, **Nome de Exibição** e **CEP**. Clique em **OK**.

![Selecione alguns atributos e clique no botão OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-attributes.png)

Selecione **Declarações do aplicativo**. Escolha as declarações que você deseja retornar nos tokens de autorização enviados ao aplicativo após uma experiência de inscrição ou entrada bem-sucedida. Por exemplo, selecione **Nome de Exibição**, **Provedor de Identidade**, **CEP**, **Novo Usuário** e **ID de Objeto do Usuário**.

![Selecione algumas declarações de aplicativo e clique no botão OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-application-claims.png)

Clique em **Criar** para adicionar a política. A política está listada como **B2C_1_SiUpIn**. O prefixo **B2C_1_** está anexado ao nome.

Abra a política selecionando **B2C_1_SiUpIn**. Verifique as configurações especificadas na tabela e, depois, clique em **Executar agora**.

![Selecione a política e execute-a](media/active-directory-b2c-create-sign-in-sign-up-policy/run-b2c-signup-signin-policy.png)

| Configuração      | Valor  |
| ------------ | ------ |
| **Aplicativos** | Aplicativo B2C da Contoso |
| **Selecionar URL de resposta** | `https://localhost:44316/` |

Uma nova guia do navegador é aberta e você poderá verificar a experiência de inscrição ou entrada do consumidor, conforme configurado.

> [!NOTE]
> Leva até um minuto para a criação de políticas e as atualizações entrem em vigor.
>