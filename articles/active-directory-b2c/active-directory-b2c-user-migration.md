---
title: "Azure Active Directory B2C: abordagens de migração do usuário"
description: "Discuta conceitos centrais e avançados sobre a migração do usuário usando a API do Graph e, opcionalmente, políticas personalizadas do Azure AD B2C."
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/04/2017
ms.author: yoelh
ms.openlocfilehash: b102edd997e69fb3568780a42dfe93fc9a90e332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: migração do usuário
Quando você planeja migrar seu provedor de identidade para o Azure AD B2C, também é necessário migrar contas de usuários. Este artigo explica como migrar as contas de usuário existentes de qualquer provedor de identidade para o Azure AD B2C. Este artigo não se destina a ser prescritivo, mas a descrever dois entre vários métodos diferentes.  O desenvolvedor é responsável por escolher o mais adequado.

## <a name="user-migration-flows"></a>Fluxos de migração do usuário
O Azure AD B2C permite migrar usuários por meio da [API do Graph](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). O processo de migração do usuário se enquadra em dois fluxos:

* **Pré-migração** – esse fluxo é adequado quando você tem acesso às credenciais do usuário (nome de usuário e senha) em um texto não criptografado. Ou as credenciais são criptografadas, mas é possível descriptografá-las. O processo envolve: ler os usuários do provedor de identidade antigo e criar novas contas no diretório do Azure AD B2C.

* **Pré-migração e redefinição de senha** – esse fluxo é adequado quando a senha do usuário não está acessível. Por exemplo:
    * As senhas são armazenadas no formato HASH
    * As senhas são armazenadas em um provedor de identidade, ao qual você não tem acesso. Seu provedor de identidade antigo valida as credenciais do usuário chamando um serviço Web

Em ambos os fluxos, primeiro você executa o processo de __pré-migração__, lê os usuários do seu provedor de identidade antigo e cria novas contas no diretório do Azure AD B2C. Se você não tiver a senha, você criará a conta com a senha aleatória gerada. Você pede para os usuários alterem suas senhas. Ou, quando o usuário entra pela primeira vez, o B2C pede para ele redefinir a senha.

## <a name="password-policy"></a>Política de senha
A política de senha do Azure AD B2C (para contas locais) se baseia na política do Azure AD. As políticas de inscrição ou entrada e redefinição de senha do Azure AD B2C usam o nível de segurança de senha "forte" e não expiram as senhas. Leia a [Política de senha do Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) para obter detalhes.

Se as contas que você deseja migrar usarem um nível de segurança de senha menor do que o [nível de segurança de senha forte imposto pelo Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), você poderá desabilitar o requisito de senha forte. Para alterar a política de senha padrão, defina a propriedade `passwordPolicies` como `DisableStrongPassword`. Por exemplo, você pode modificar a solicitação de criação de usuário da seguinte maneira: 

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-using-graph-api-to-migrate-users"></a>Etapa 1: Usando a API do Graph para migrar usuários
Você cria a conta de usuário do Azure AD B2C por meio da API do Graph (com a senha ou com senha aleatória). Esta seção descreve o processo de como criar contas de usuário no diretório do Azure AD B2C usando a API do Graph.

### <a name="step-11-register-your-application-in-your-tenant"></a>Etapa 1.1 Registrar seu aplicativo em seu locatário
Para se comunicar com a API do Graph, você primeiro precisa ter a conta de serviço com privilégios administrativos. No Azure AD, você pode registrar um aplicativo e autenticá-lo para o Azure AD. As credenciais do aplicativo são: **ID do aplicativo** e **Segredo do Aplicativo**. O aplicativo atua em nome próprio e não como usuário, para chamar a API do Graph.

Primeiro, registre seu aplicativo de migração no Azure AD. Em seguida, crie a chave de aplicativo (Segredo do aplicativo) e defina o aplicativo com os privilégios corretos.

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Escolha seu locatário do Azure AD **B2C** selecionando sua conta no canto superior direito da página.
3. No painel esquerdo, clique em **Azure Active Directory** (não o Azure AD B2C). Talvez seja necessário selecionar **Mais Serviços** para localizá-la.
4. Selecione **Registros do Aplicativo**.
5. Clique em **Novo registro de aplicativo**.

    ![Novo registro de aplicativo](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
6. Siga os avisos e crie um novo aplicativo
    * Para **Nome**, use **B2CUserMigratioin** ou qualquer outro nome que desejar.
    * Para o **Tipo de aplicativo**, use **Aplicativo Web/API**.
    * Para **URL de logon**, use https://localhost (já que não é relevante para esse aplicativo).
    * Clique em **Criar**.
7. Depois que o aplicativo tiver sido criado, na lista de aplicativos, selecione o aplicativo recém-criado **B2CUserMigratioin**.
Selecione **Propriedades**, copie a **ID do aplicativo** e salve-a para mais tarde.

### <a name="step-12-create-application-secret"></a>Etapa 1.2 Criar segredo de aplicativo
8. Continuando no Aplicativo Registrado do portal do Azure. Clique em **Chaves** e adicione uma nova chave (também conhecida como o segredo do cliente). Além disso, copie a chave para mais tarde.

    ![ID e chaves do aplicativo](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>Etapa 1.3 Conceder permissão administrativa ao seu aplicativo
9. Continuando no **Aplicativo Registrado** do portal do Azure
10. Clique em **Permissões necessárias**.
11. Clique em **Microsoft Azure Active Directory**.
12. Em **Habilitar Acesso**, em **Permissões de Aplicativo**, selecione **Permissão de leitura e gravação de dados do diretório** e clique em **Salvar**.
13. Por fim, em **Permissões necessárias**, clique no botão **Conceder Permissões**.

    ![Permissões de aplicativo](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

Agora, você tem um aplicativo com permissão para criar, ler e atualizar usuários de seu locatário B2C.

### <a name="step-14-optional-environment-cleanup"></a>Etapa 1.4 Limpeza de ambiente [opcional]
A permissão de ler e gravar dados de diretório NÃO inclui a capacidade de excluir usuários. Se você quiser dar a seu aplicativo a capacidade de excluir usuários (para limpar seu ambiente), será preciso executar uma etapa extra. A etapa envolve a execução do PowerShell para definir permissões de __Administrador da Conta de Usuário__, caso contrário, você pode passar para a próxima seção.


> [!IMPORTANT]
> Você precisa usar uma conta de administrador de locatário B2C que seja **local** para o locatário B2C. Essas contas têm esta aparência: admin@contosob2c.onmicrosoft.com.

>[!NOTE]
> O script do PowerShell a seguir requer [Azure Active Directory PowerShell **Versão 2**](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).

No seguinte script do PowerShell:
* Conecte-se ao seu serviço online. Para fazer isso, execute o cmdlet `Connect-AzureAD` no prompt de comando do Windows PowerShell e forneça suas credenciais. 
* Use a **ID do Aplicativo** para atribuir o aplicativo à função de administrador da conta de usuário. Essas funções têm identificadores bem conhecidos, então tudo o que você precisa fazer é inserir sua **ID de Aplicativo** no script.

```PowerShell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Altere o valor `$AppId` com a **ID do aplicativo** do Azure AD

## <a name="step-2-pre-migration-application-sample"></a>Etapa 2: Exemplo de aplicativo pré-migração
Baixe o código de exemplo e execute-o. Você pode [baixar o código de exemplo como um arquivo .zip](http://www.github.com).

### <a name="step-21-edit-the-migration-data-file"></a>Etapa 2.1 Editar o arquivo de dados de migração
O aplicativo de exemplo usa o arquivo JSON que contém dados de usuários fictícios. Depois de executar com êxito o exemplo, altere o código para consumir os dados de seu próprio banco de dados. Ou exporte o perfil de usuários para um arquivo JSON e defina o aplicativo para usar esse arquivo.
Para editar o arquivo JSON, abra a solução do Visual Studio `AADB2C.UserMigration.sln`. No projeto `AADB2C.UserMigration`, abra o arquivo `UsersData.json`.


![Arquivo de dados do usuário](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Como você pode ver, o arquivo contém a lista de entidades do usuário. Cada entidade do usuário tem:
* email
* displayName
* firstName
* lastName
* senha (pode estar vazia)

> [!NOTE]
> No tempo de compilação, o Visual Studio copia o arquivo para o diretório `bin`.

### <a name="step-22-configure-the-application-settings"></a>Etapa 2.2 Definir as configurações do aplicativo
No projeto `AADB2C.UserMigration`, abra o arquivo App.config. Substitua as três configurações do aplicativo a seguir pelos seus próprios valores:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users data e.g. UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure Table string}" />
    
</appSettings>
```

> [!NOTE]
> * O uso de cadeia de conexão de Tabela do Azure é descrito posteriormente nas próximas seções
> * O nome do locatário do B2C é o domínio que você inseriu durante a criação do locatário e é exibido no portal do Azure. Ele geralmente termina com o sufixo `.onmicrosoft.com`, por exemplo, `contosob2c.onmicrosoft.com`.
>

### <a name="step-23-run-the-pre-migration-process"></a>Etapa 2.3 Executar o processo pré-migração
Clique com o botão direito do mouse na solução `AADB2C.UserMigration` e recompile o exemplo. Se tiver êxito, agora deverá você ter um arquivo executável `UserMigration.exe` localizado em `AADB2C.UserMigration\bin\Debug`. Para executar o processo de migração, use um dos seguintes parâmetros de linha de comando:

* Para **migrar usuários com senha**, use o comando `UserMigration.exe 1`.

* Para **migrar usuários com senha aleatória**, use o comando `UserMigration.exe 2`. Essa operação também cria a entidade de Tabela do Azure. Mais tarde, você configura a política para chamar o serviço de API REST. O serviço usa a Tabela do Azure para controlar e gerenciar o processo de migração.

![Demonstração do processo de migração](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Etapa 2.4 Verificar o processo pré-migração
Para verificar o resultado, no portal do Azure, abra **Azure AD B2C** e clique em **Usuários e Grupos**. Na caixa de pesquisa, digite um nome de exibição do usuário e veja o perfil do usuário. Como alternativa, você pode usar esse aplicativo de exemplo para recuperar o usuário por **endereço de email de entrada**. Para pesquisar um usuário pelo endereço de email de entrada, execute o seguinte comando

```Console
UserMigration.exe 3 {email address}
```

Você também pode salvar a saída no arquivo JSON, da seguinte maneira:
```Console
UserMigration.exe 3 {email address} >> UserProfile.json
```


Abra o arquivo UserProfile.json com seu editor JSON desejado. Com o Código do Visual Studio, você pode formatar o documento JSON usando `Shift+Alt+F` ou Formato de Documento no menu de contexto.

![Perfil do Usuário json](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

Você também pode recuperar o usuário por **nome de exibição** usando o comando `UserMigration.exe 4 <Display name>`

### <a name="step-25-optional-environment-cleanup"></a>Etapa 2.5 Limpeza de ambiente [opcional]
Se você desejar limpar seu locatário do Azure AD e remover os usuários do diretório do Azure AD, execute o comando `UserMigration.exe 5`

> [!NOTE]
> * Para limpar o seu locatário, configure as permissões de __Administrador da Conta de Usuário__ para seu aplicativo
> * O aplicativo de migração de exemplo limpa todos os usuários listados no arquivo JSON

### <a name="step-26-sing-in-with-migrated-users-with-password"></a>Etapa 2.6 Entrar com usuários migrados (com senha)
Depois de executar o processo de pré-migração com a senha dos usuários, as contas estão prontas para uso e os usuários podem entrar no seu aplicativo usando o Azure AD B2C. Se você não tiver acesso à senha dos usuários, continue para a próxima seção.

## <a name="step-3-password-reset"></a>Etapa 3: Redefinição de senha
No caso de você migrar usuários com senha aleatória, os usuários precisarão redefinir a senha. Para redefinir a senha:
* Enviar um email de boas-vindo com link para redefinição de senha
* [Opcional] Altere a política para lidar com o caso quando o usuário não redefinir a senha e tentar entrar. Na entrada, sua política verifica o status da migração. Se o usuário não tiver alterado a senha, será gerada a mensagem de erro amigável pedindo para o usuário clicar no link "Esqueceu sua senha?"

    > [!NOTE]
    > Para verificar e alterar o status de migração do usuário, você deve usar a política personalizada. Conclua as etapas no artigo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).
    >

### <a name="step-31-send-a-welcome-email-with-link-to-reset-password"></a>Etapa 3.1 Enviar um email de boas-vindo com link para redefinição de senha
Para obter o link para sua política de redefinição de senha:

1.  Abra **Configurações do Azure AD B2C** e abra as propriedades da política **Redefinir senha**.

2. Selecione seu aplicativo
    >[!NOTE]
    >
    >A **Executar Agora** exige que pelo menos um aplicativo esteja previamente registrado no locatário. Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.  

2.  Selecione **Executar agora** e verifique a política
3.  **Copie** a URL e envie-a aos seus usuários

    ![Definir logs de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-user-migration-status"></a>Etapa 4: [Opcional] Alterar sua política para verificar e definir o status de migração do usuário

Quando os usuários tentarem entrar sem redefinir a senha primeiro, sua política deve retornar a mensagem de erro amigável. Por exemplo: sua senha expirou, para redefini-la, clique no link de redefinição de senha.  Esta etapa opcional requer o uso do Azure AD B2C usando políticas personalizadas conforme descrito no artigo [Introdução a políticas personalizadas](active-directory-b2c-get-started-custom.md).

Nesta seção, você deve alterar a política para verificar o status de migração na entrada. Se o usuário não alterar a senha, será retornada a mensagem de erro HTTP 409 pedindo para o usuário clicar no link "Esqueceu sua senha?" . Para acompanhar a alteração da senha, você deve usar a Tabela do Azure. Execução do processo de pré-migração com parâmetro de linha de comando `2` cria a entidade de usuário na Tabela do Azure. Seu serviço:

* Ao entrar, a política do Azure AD B2C invoca o serviço Restful de migração, enviando um email como declarações de entrada. O serviço pesquisa o endereço de email na Tabela do Azure. Se existir, gere a mensagem de erro "Você deve alterar a senha".

* Na redefinição de senha, após o usuário alterar a senha com êxito, remova a entidade de tabela do Azure.


> [!NOTE]
>Usamos a Tabela do Azure para simplificar o exemplo. Você pode armazenar o status de migração em qualquer banco de dados ou como uma propriedade personalizada na conta do Azure AD B2C.

### <a name="41-application-settings"></a>4.1 Configurações do aplicativo
Para testar a API Restful de demonstração. Abra a solução do Visual Studio `AADB2C.UserMigration.sln` no Visual Studio. No projeto `AADB2C.UserMigration.API`, abra o arquivo App.config. Substitua as três configurações do aplicativo por seus próprios valores:

```XML
<appSettings>
    <add key="BlobStorageConnectionString" value="{The Azure Blob storage connection string"} />
</appSettings>
```

### <a name="step-42-deploy-your-web-application-to-azure-app-services"></a>Etapa 4.2: Implantar o aplicativo Web nos Serviços de Aplicativos do Azure
Publique seu aplicativo de API nos Serviços de Aplicativos do Azure. Para obter mais informações, consulte: [Implantar seu aplicativo no Serviço de Aplicativo do Azure](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy)

### <a name="step-43-add-technical-profile-and-technical-profile-validation-to-your-policy"></a>Etapa 4.3 Adicionar perfil técnico e validação de perfil técnico à sua política 
1.  Abra o arquivo de política de extensão (TrustFrameworkExtensions.xml) de seu diretório de trabalho. 
2. Localize a seção `<ClaimsProviders>`
3. Adicione o seguinte trecho XML no elemento `ClaimsProviders`
4. Altere o valor de `ServiceUrl` para apontar para a URL do ponto de extremidade 

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>

    <TechnicalProfile Id="LocalAccountSignIn">
        <DisplayName>Local account just in time migration</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
        </InputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="LocalAccountPasswordReset">
        <DisplayName>Local account just in time migration</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        </InputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>

<ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>

    <!-- This technical profile uses a validation technical profile to authenticate the user. -->
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="LocalAccountSignIn" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    <TechnicalProfile Id="LocalAccountWritePasswordUsingObjectId">
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="LocalAccountPasswordReset" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

O perfil técnico define uma declaração de entrada: `signInName` (enviar como email). Ao entrar, a declaração envia para seu ponto de extremidade Restful.

Depois de definir o perfil técnico para a sua API Restful, diga para a sua política do Azure AD B2C chamar esse perfil técnico. O trecho XML substitui `SelfAsserted-LocalAccountSignin-Email`, que é definido na política de base. O trecho XML também adiciona `ValidationTechnicalProfile` com ReferenceId apontando para o seu perfil técnico `LocalAccountUserMigration`. 

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Etapa 4.4 Carregar a política para o seu locatário
1.  No [Portal do Azure](https://portal.azure.com), mude para o [contexto do seu locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e clique em **Azure AD B2C**.
2.  Selecione **Estrutura de Experiência de Identidade**.
3.  Clique em **Todas as Políticas**.
4.  Selecione **Carregar Política**
5.  Marque a caixa **Substituir a política caso ela exista**.
6.  **Carregue** TrustFrameworkExtensions.xml e verifique se ele não falhou na validação
7.  Repita a última etapa e carregue o SignUpOrSignIn.xml

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Etapa 4.5 Testar a política personalizada usando Executar Agora
1.  Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.
2.  Abra a **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**.
3. Tente entrar com um dos usuários migrados e clique em **entrar**. Você verá a seguinte mensagem de erro proveniente da API REST.

    ![Definir logs de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshooting-your-rest-api"></a>Etapa 4.6 [Opcional] Solução de problemas na sua API REST
Você pode monitorar e ver informações de log quase em tempo real.

1. No menu de configurações do aplicativo Restful, role para baixo até a seção **Monitoramento** e clique em **Logs de Diagnóstico**. 
2. Habilitar o log de aplicativo (sistema de arquivos) 
3. Defina **Nível** como **Detalhado**
4. Clique em **Salvar**

    ![Definir logs de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. No menu de configurações, clique em **Fluxo de log**
6. Verifique a saída da API Restful.

Para obter mais informações, confira: [Logs de streaming e o Console](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-streaming-logs-and-console)

> [!IMPORTANT]
> Os __logs de diagnóstico__ devem ser usados somente durante desenvolvimento e teste. A saída API RESTful pode conter informações confidenciais que não devem ser expostas na produção.
>

## <a name="download-the-complete-policy-files"></a>Baixar os arquivos da política completa
Opcional: recomendamos a criação de seu cenário usando seus próprios arquivos de política personalizada após a conclusão do passo a passo Introdução às políticas personalizadas em vez de usar esses arquivos de exemplo.  [Arquivos de política de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration)