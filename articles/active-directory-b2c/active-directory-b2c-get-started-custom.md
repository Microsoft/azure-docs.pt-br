---
title: "Azure Active Directory B2C: introdução às políticas personalizadas | Microsoft Docs"
description: "Como começar a usar as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja;parahk;gsacavdm
ms.openlocfilehash: 4f14dbf4b66f10290cd4f98d56a005f97cc6a207
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Azure Active Directory B2C: introdução às políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Depois que você concluir as etapas descritas neste artigo, a política personalizada dará suporte à inscrição ou conexão da “conta local” usando um endereço de email e uma senha. Você também preparará o ambiente para adicionar provedores de identidade (como Facebook ou Azure Active Directory). Recomendamos que você conclua estas etapas antes de ler sobre outros usos da Estrutura de Experiência de Identidade do Azure AD (Active Directory) B2C.

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, verifique se você tem um locatário do Azure AD B2C, que é um contêiner para todos os seus usuários, aplicativos, políticas e muito mais. Se você ainda não tiver um, será necessário [criar um locatário do Azure AD B2C](active-directory-b2c-get-started.md). Recomendamos que todos os desenvolvedores concluam os passo a passos de política interna do Azure AD B2C e configurem seus aplicativos com políticas internas antes de continuar. Seus aplicativos funcionarão com dois tipos de políticas assim que você fizer uma simples alteração no nome da política para invocar a política personalizada.

>[!NOTE]
>Para acessar a edição da política personalizada você precisa de uma assinatura válida do Azure vinculada ao seu locatário. Se você não [vinculou seu locatário do Azure AD B2C a uma assinatura do Azure](active-directory-b2c-how-to-enable-billing.md), ou se sua assinatura do Azure está desabilitada, o botão Estrutura de Experiência de Identidade não estará disponível.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Adicionar chaves de criptografia e de assinatura ao seu locatário do B2C para serem usadas por políticas personalizadas

1. Abra a folha **Estrutura de Experiência de Identidade** em suas configurações de locatário do Azure AD B2C.
2. Selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário.
3. Crie a B2C_1A_TokenSigningKeyContainer, se não existir:<br>
    a. Selecione **Adicionar**. <br>
    b. Selecione **Gerar**.<br>
    c. Para o **Nome**, use `TokenSigningKeyContainer`. <br> 
    O prefixo `B2C_1A_` pode ser adicionado automaticamente.<br>
    d. Para o **Tipo de chave**, use **RSA**.<br>
    e. Para as **Datas**, use os padrões. <br>
    f. Para o **Uso da chave**, use **Assinatura**.<br>
    g. Selecione **Criar**.<br>
4. Crie a B2C_1A_TokenEncryptionKeyContainer, se não existir:<br>
 a. Selecione **Adicionar**.<br>
 b. Selecione **Gerar**.<br>
 c. Para o **Nome**, use `TokenEncryptionKeyContainer`. <br>
   O prefixo `B2C_1A`_ pode ser adicionado automaticamente.<br>
 d. Para o **Tipo de chave**, use **RSA**.<br>
 e. Para as **Datas**, use os padrões.<br>
 f. Para o **Uso da chave**, use **Criptografia**.<br>
 g. Selecione **Criar**.<br>
5. Crie o B2C_1A_FacebookSecret. <br>
Se você já tiver um segredo do aplicativo Facebook, adicione-o como uma chave de política para o seu locatário. Caso contrário, você deve criar a chave com um valor de espaço reservado para que suas políticas passem na validação.<br>
 a. Selecione **Adicionar**.<br>
 b. Para as **Opções**, use **Manual**.<br>
 c. Para o **Nome**, use `FacebookSecret`. <br>
 O prefixo `B2C_1A_` pode ser adicionado automaticamente.<br>
 d. Na caixa **Segredo**, insira seu FacebookSecret de developers.facebook.com ou `0` como um espaço reservado. *Esse não é a sua ID do aplicativo Facebook.* <br>
 e. Para o **Uso da chave**, use **Assinatura**. <br>
 f. Selecione **Criar** e confirme a criação.

## <a name="register-identity-experience-framework-applications"></a>Registrar aplicativos do Identity Experience Framework

O Azure AD B2C exige o registro de dois aplicativos adicionais que são usados pelo mecanismo para a inscrição e conexão de usuários.

>[!NOTE]
>Você deve criar dois aplicativos que permitam a entrada usando contas locais: IdentityExperienceFramework (um aplicativo Web) e ProxyIdentityExperienceFramework (um aplicativo nativo) com permissão delegada do aplicativo IdentityExperienceFramework. As contas locais só existem em seu locatário. Seus usuários entram com uma combinação exclusiva de endereço de email/senha para acessar seus aplicativos registrados por locatário.

### <a name="create-the-identityexperienceframework-application"></a>Criar o aplicativo IdentityExperienceFramework

1. No [Portal do Azure](https://portal.azure.com), alterne para o [contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
2. Abra a folha do **Azure Active Directory** (não a folha do **Azure AD B2C**). Talvez seja necessário selecionar **Mais Serviços** para localizá-la.
3. Selecione **Registros do Aplicativo**.
4. Selecione **Novo registro de aplicativo**.
   * Para o **Nome**, use `IdentityExperienceFramework`.
   * Para o **Tipo de aplicativo**, use **Aplicativo Web/API**.
   * Para a **URL de Entrada**, use `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, em que `yourtenant` é o nome de domínio do seu locatário do Azure AD B2C.
5. Selecione **Criar**.
6. Depois de concluir, selecione o aplicativo recém-criado **IdentityExperienceFramework**.<br>
   * Selecione **Propriedades**.<br>
   * Copie a ID do aplicativo e salve-a para mais tarde.

### <a name="create-the-proxyidentityexperienceframework-application"></a>Criar o aplicativo ProxyIdentityExperienceFramework

1. Selecione **Registros do Aplicativo**.
1. Selecione **Novo registro de aplicativo**.
   * Para o **Nome**, use `ProxyIdentityExperienceFramework`.
   * Para o **Tipo de aplicativo**, use **Nativo**.
   * Para a **URI de Redirecionamento**, use `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, em que `yourtenant` é o seu locatário do Azure AD B2C.
1. Selecione **Criar**.
1. Depois de ele ter sido criado, selecione o aplicativo **ProxyIdentityExperienceFramework**.<br>
   * Selecione **Propriedades**. <br>
   * Copie a ID do aplicativo e salve-a para mais tarde.
1. Selecione **Permissões necessárias**.
1. Selecione **Adicionar**.
1. Selecione **Selecionar uma API**.
1. Pesquise o nome IdentityExperienceFramework. Selecione **IdentityExperienceFramework** nos resultados e, em seguida, clique em **Selecionar**.
1. Marque a caixa de seleção ao lado de **Acessar IdentityExperienceFramework** e, em seguida, clique em **Selecionar**.
1. Selecione **Concluído**.
1. Selecione **Conceder Permissões** e, em seguida, confirme selecionando **Sim**.

## <a name="download-starter-pack-and-modify-policies"></a>Baixar o pacote de início e modificar políticas

Políticas personalizadas são um conjunto de arquivos XML que precisam ser carregados no locatário do Azure AD B2C. Fornecemos starter packs para você começar a trabalhar rapidamente. Cada pacote de início da lista a seguir contém o menor número de perfis de técnicos e jornadas do usuário necessárias para alcançar os cenários descritos:
 * LocalAccounts. Habilita o uso somente de contas locais.
 * SocialAccounts. Habilita o uso somente de contas sociais (ou federadas).
 * **SocialAndLocalAccounts**. Usaremos esse arquivo no passo a passo.
 * SocialAndLocalAccountsWithMFA. As opções de Autenticação Multifator, local e social estão incluídas aqui.

Cada pacote de início contém:

* O [arquivo base](active-directory-b2c-overview-custom.md#policy-files) da política. São necessárias algumas modificações na base.
* O [arquivo de extensão](active-directory-b2c-overview-custom.md#policy-files) da política.  Esse arquivo é o local em que a maioria das alterações de configuração é feita.
* [Arquivos de terceira parte confiável](active-directory-b2c-overview-custom.md#policy-files) são os arquivos específicos de tarefa, chamados pelo aplicativo.

>[!NOTE]
>Caso seu editor de XML dê suporte à validação, valide os arquivos em relação ao esquema XML TrustFrameworkPolicy_0.3.0.0.xsd, localizado no diretório raiz do pacote de início. A validação de esquema XML identifica erros antes do upload.

 Vamos começar:

1. Baixe o active-directory-b2c-custom-policy-starterpack do GitHub. [Baixe o arquivo .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou execute

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Abra a pasta SocialAndLocalAccounts.  O arquivo base (TrustFrameworkBase.xml) dessa pasta contém o conteúdo necessário para contas locais e sociais/corporativas. O conteúdo social não interfere nas etapas para ativação das contas locais.
3. Abra o TrustFrameworkBase.xml. Se você precisar de um editor de XML, [experimente o Visual Studio Code](https://code.visualstudio.com/download), um editor de plataforma cruzada leve.
4. No elemento raiz `TrustFrameworkPolicy`, atualize os atributos `TenantId` e `PublicPolicyUri`, substituindo `yourtenant.onmicrosoft.com` pelo nome de domínio do seu locatário do Azure AD B2C:
   ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
   >[!NOTE]
   >`PolicyId` é o nome da política que você vê no portal e o nome pelo qual este arquivo de política é referenciado por outros arquivos de política.

5. Salve o arquivo.
6. Abra o TrustFrameworkExtensions.xml. Faça as mesmas duas alterações, substituindo `yourtenant.onmicrosoft.com` pelo seu locatário do Azure AD B2C. Faça a mesma substituição no elemento `<TenantId>` para um total de três alterações. Salve o arquivo.
7. Abra o SignUpOrSignIn.xml. Faça as mesmas alterações, substituindo `yourtenant.onmicrosoft.com` pelo seu locatário do Azure AD B2C em três locais. Salve o arquivo.
8. Abra a os arquivos de redefinição de senha e edição de perfil. Faça as mesmas alterações substituindo `yourtenant.onmicrosoft.com` pelo seu locatário do Azure AD B2C em três locais em cada arquivo. Salve os arquivos.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Adicionar as IDs do Aplicativo à política personalizada
Adicione as IDs de aplicativo ao arquivo de extensões (`TrustFrameworkExtensions.xml`):

1. No arquivo de extensões (TrustFrameworkExtensions.xml), localize o elemento `<TechnicalProfile Id="login-NonInteractive">`.
2. Substitua ambas as instâncias de `IdentityExperienceFrameworkAppId` pela ID do aplicativo do aplicativo Identity Experience Framework criado anteriormente. Aqui está um exemplo:

   ```xml
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. Substitua ambas as instâncias de `ProxyIdentityExperienceFrameworkAppId` pela ID do aplicativo do aplicativo Estrutura de Experiência de Identidade de Proxy que você criou anteriormente.
4. Salve o arquivo de extensões.

## <a name="upload-the-policies-to-your-tenant"></a>Carregar as políticas no locatário

1. No [Portal do Azure](https://portal.azure.com), alterne para o [contexto do seu locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e abra a folha do **Azure AD B2C**.
2. Selecione **Estrutura de Experiência de Identidade**.
3. Selecione **Carregar Política**.

    >[!WARNING]
    >Os arquivos da política personalizada devem ser carregados na seguinte ordem:

1. Carregue o TrustFrameworkBase.xml.
2. Carregue o TrustFrameworkExtensions.xml.
3. Carregue o SignUpOrSignin.xml.
4. Carregue os outros arquivos de política.

Quando um arquivo é carregado, o nome do arquivo de política é precedido por `B2C_1A_`.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testar a política personalizada usando a opção Executar Agora

1. Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.

   >[!NOTE]
   >A **Executar Agora** exige que pelo menos um aplicativo esteja previamente registrado no locatário. Os aplicativos devem ser registrados no locatário do B2C, seja pelo uso da seleção de menu **Aplicativos** no Azure AD B2C ou pelo uso da Estrutura de Experiência de Identidade para invocar tanto as políticas internas quanto as personalizadas. Somente um registro por aplicativo é necessário.<br><br>
   Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C.  

2. Abra a B2C_1A_signup_signin, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**.

3. Você deverá conseguir se inscrever usando um endereço de email.

4. Entre com a mesma conta para confirmar que você tem a configuração correta.

>[!NOTE]
>Uma causa comum de falha de entrada é um aplicativo IdentityExperienceFramework configurado incorretamente.


## <a name="next-steps"></a>Próximas etapas

### <a name="add-facebook-as-an-identity-provider"></a>Adicionar o Facebook como um provedor de identidade
Para configurar o Facebook:
1. [Configurar um aplicativo do Facebook em developers.facebook.com](active-directory-b2c-setup-fb-app.md).
2. [Adicionar o segredo do aplicativo Facebook ao seu locatário do Azure AD B2C](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. No arquivo de política TrustFrameworkExtensions, substitua o valor de `client_id` pela ID do aplicativo Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. Carregue o arquivo de política de TrustFrameworkExtensions.xml em seu locatário.
5. Teste usando **Executar Agora** ou invocando a política diretamente do seu aplicativo registrado.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Adicionar o Azure Active Directory como um provedor de identidade
O arquivo base usado neste guia de introdução já contém uma parte do conteúdo de que você precisa para adicionar outros provedores de identidade. Para obter informações sobre como configurar conexões, consulte o artigo [Azure Active Directory B2C: entrar usando contas do Azure AD](active-directory-b2c-setup-aad-custom.md).

Para obter uma visão geral sobre políticas personalizadas no Azure AD B2C que usam a Estrutura de Experiência de Identidade, consulte o artigo [Azure Active Directory B2C: políticas personalizadas](active-directory-b2c-overview-custom.md). 
