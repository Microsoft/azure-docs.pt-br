Para habilitar a edição de perfil no aplicativo, você precisará criar uma política de edição de perfil. Essa política descreve as experiências pelas quais os consumidores passarão durante a edição do perfil e o conteúdo de tokens que o aplicativo receberá na conclusão bem-sucedida.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Na seção de políticas das configurações, selecione **Políticas de edição de perfil** e clique em **+ Adicionar**.

![Selecione as Políticas de edição de perfil e clique no botão Adicionar](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-policy.png)

Insira um **Nome** de política para referência de seu aplicativo. Por exemplo, insira: `SiPe`.

Clique em **Provedores de identidade** e marque **Entrada na Conta Local**. Opcionalmente, você também pode selecionar provedores de identidade social, se já configurado. Clique em **OK**.

![Selecione Inscrição na Conta Local como provedor de identidade e clique no botão OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-identity-providers.png)

Selecione **Atributos do perfil**. Escolha os atributos que o consumidor pode exibir e editar no perfil. Por exemplo, marque **País/Região**, **Nome de Exibição** e **CEP**. Clique em **OK**.

![Selecione alguns atributos e clique no botão OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-attributes.png)

Selecione **Declarações do aplicativo**. Escolha as declarações que você quer retornar nos tokens de autorização enviados ao aplicativo após uma experiência de edição de perfil bem-sucedida. Por exemplo, selecione **Nome de Exibição** e **CEP**.

![Selecione algumas declarações de aplicativo e clique no botão OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-application-claims.png)

Clique em **Criar** para adicionar a política. A política está listada como **B2C_1_SiPe**. O prefixo **B2C_1_** está anexado ao nome.

Abra a política selecionando **B2C_1_SiPe**. Verifique as configurações especificadas na tabela e, depois, clique em **Executar agora**.

![Selecione a política e execute-a](media/active-directory-b2c-create-profile-editing-policy/run-b2c-editing-policy.png)

| Configuração      | Valor  |
| ------------ | ------ |
| **Aplicativos** | Aplicativo B2C da Contoso |
| **Selecionar URL de resposta** | `https://localhost:44316/` |

Uma nova guia do navegador é aberta e você poderá verificar a experiência de edição de perfil do consumidor, conforme configurado.

> [!NOTE]
> Leva até um minuto para a criação de políticas e as atualizações entrem em vigor.
>