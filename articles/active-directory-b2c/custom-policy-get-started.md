---
title: Introdução às políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba mais sobre como começar a usar as políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/28/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ae3865c1ee5e656fc4feda48de9ab67995619593
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96936362"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Introdução às políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

As [políticas personalizadas](custom-policy-overview.md) são arquivos de configuração que definem o comportamento do locatário do Azure AD B2C (Azure Active Directory B2C). Neste artigo, você criará uma política personalizada que dá suporte para inscrição ou entrada de conta local usando um endereço de email e uma senha. Você também pode preparar o ambiente para adicionar provedores de identidade.

## <a name="prerequisites"></a>Pré-requisitos

- Se ainda não tiver um, você precisará [criar um locatário do Azure AD B2C](tutorial-create-tenant.md) que esteja vinculado à sua assinatura do Azure.
- [Registre seu aplicativo](tutorial-register-applications.md) no locatário que você criou para que ele possa se comunicar com Azure AD B2C.
- Conclua as etapas em [Configurar a inscrição e entrada com a conta do Facebook](identity-provider-facebook.md) para configurar um aplicativo do Facebook. Embora não seja necessário um aplicativo do Facebook para o uso de políticas personalizadas, é usado neste tutorial para demonstrar a habilitação do logon social em uma política personalizada.

## <a name="add-signing-and-encryption-keys"></a>Adicionar chaves de criptografia e de assinatura

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, procure e selecione **Azure AD B2C**.
1. Na página Visão Geral, em **Políticas**, selecione **Identity Experience Framework**.

### <a name="create-the-signing-key"></a>Criar a chave de assinatura

1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha `Generate`.
1. Em **Nome**, insira `TokenSigningKeyContainer`. O prefixo `B2C_1A_` pode ser adicionado automaticamente.
1. Para **Tipo de chave**, selecione **RSA**.
1. Para **Uso de chave**, selecione **Assinatura**.
1. Selecione **Criar**.

### <a name="create-the-encryption-key"></a>Criar a chave de criptografia

1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha `Generate`.
1. Em **Nome**, insira `TokenEncryptionKeyContainer`. O prefixo `B2C_1A`_ pode ser adicionado automaticamente.
1. Para **Tipo de chave**, selecione **RSA**.
1. Para o **Uso da chave**, selecione **Criptografia**.
1. Selecione **Criar**.

### <a name="create-the-facebook-key"></a>Criar a chave do Facebook

Adicione o [Segredo do Aplicativo](identity-provider-facebook.md) do Facebook como uma chave de política. Você pode usar o Segredo do Aplicativo desse aplicativo criado como parte dos pré-requisitos deste artigo.

1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Para **Nome**, insira `FacebookSecret`. O prefixo `B2C_1A_` pode ser adicionado automaticamente.
1. Em **Segredo**, digite o *Segredo do Aplicativo* do Facebook de developers.facebook.com. Esse valor é o segredo, não a ID do aplicativo.
1. Para **Uso de chave**, selecione **Assinatura**.
1. Selecione **Criar**.

## <a name="register-identity-experience-framework-applications"></a>Registrar aplicativos do Identity Experience Framework

O Azure AD B2C exige o registro de dois aplicativos que são usados para a inscrição e entrada de usuários com contas locais: *IdentityExperienceFramework*, uma API Web, e *ProxyIdentityExperienceFramework*, um aplicativo nativo com permissão delegada para o aplicativo IdentityExperienceFramework. Os usuários podem se inscrever com um endereço de email ou nome de usuário e uma senha para acessar os aplicativos registrados no locatário, o que cria uma "conta local". As contas locais só existem em seu locatário do Azure AD B2C.

Você precisa registrar apenas uma vez esses dois aplicativos em seu locatário do Azure AD B2C.

### <a name="register-the-identityexperienceframework-application"></a>Registrar o aplicativo IdentityExperienceFramework

Para registrar um aplicativo em seu locatário do Azure AD B2C, você pode usar a experiência do **registros de aplicativo** .

1. Escolha **Registros de aplicativo** e **Novo registro**.
1. Para **Nome**, insira `IdentityExperienceFramework`.
1. Em **Tipos de contas com suporte**, selecione **Contas somente neste diretório organizacional**.
1. Em **URI de redirecionamento**, selecione **Web** e digite `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, em que `your-tenant-name` é o nome de domínio do locatário do Azure AD B2C.
1. Em **Permissões**, marque a caixa de seleção *Dar consentimento do administrador às permissões OpenID e offline_access*.
1. Selecione **Registrar**.
1. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

Em seguida, exponha a API adicionando um escopo:

1. No menu à esquerda, em **gerenciar**, selecione **expor uma API**.
1. Selecione **Adicionar um escopo** e **Salvar e continuar** para aceitar o URI da ID do aplicativo padrão.
1. Insira os valores a seguir para criar um escopo que permita a execução da política personalizada em seu locatário Azure AD B2C:
    * **Nome do escopo**: `user_impersonation`
    * **Nome de exibição de consentimento do administrador**: `Access IdentityExperienceFramework`
    * **Descrição do consentimento do administrador:** `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Selecione **Adicionar escopo**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrar o aplicativo ProxyIdentityExperienceFramework

1. Escolha **Registros de aplicativo** e **Novo registro**.
1. Para **Nome**, insira `ProxyIdentityExperienceFramework`.
1. Em **Tipos de contas com suporte**, selecione **Contas somente neste diretório organizacional**.
1. Em **URI de Redirecionamento**, use a lista suspensa para selecionar **Cliente Público/Nativo (móvel e área de trabalho)** .
1. No **URI de redirecionamento**, insira `myapp://auth`.
1. Em **Permissões**, marque a caixa de seleção *Dar consentimento do administrador às permissões OpenID e offline_access*.
1. Selecione **Registrar**.
1. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

Em seguida, especifique que o aplicativo deve ser tratado como um cliente público:

1. No menu à esquerda, em **Gerenciar**, selecione **Autenticação**.
1. Em **Configurações avançadas**, habilite **Tratar aplicativo como um cliente público** (selecione **Sim**). Verifique se **"allowPublicClient": true** está definido no manifesto do aplicativo. 
1. Clique em **Salvar**.

Agora conceda permissões ao escopo da API que você expôs anteriormente no registro de *IdentityExperienceFramework*:

1. No menu à esquerda, em **gerenciar**, selecione **permissões de API**.
1. Em **Permissões Configuradas**, selecione **Adicionar uma permissão**.
1. Selecione a guia **Minhas APIs** e o aplicativo **IdentityExperienceFramework**.
1. Em **Permissão**, selecione o escopo **user_impersonation** que você definiu anteriormente.
1. Selecione **Adicionar Permissões**. Conforme as instruções, aguarde alguns minutos antes de seguir para a próxima etapa.
1. Selecione **Fornecer o consentimento do administrador para (nome do seu locatário)** .
1. Selecione a conta de administrador conectada no momento ou entre com uma conta no seu locatário do Azure AD B2C que tenha recebido, pelo menos, a função *Administrador de aplicativos de nuvem*.
1. Selecione **Aceitar**.
1. Selecione **Atualizar** e, em seguida, verifique se "concedido para..." aparece em **status** para os escopos-offline_access, openid e user_impersonation. Pode levar alguns minutos para que as permissões sejam propagadas.

* * *

## <a name="custom-policy-starter-pack"></a>Pacote de início de política personalizada

As políticas personalizadas são um conjunto de arquivos XML que você carrega em seu locatário do Azure AD B2C para definir perfis técnicos e percursos do usuário. Fornecemos pacotes de início com várias políticas predefinidas para ajudá-lo a começar rapidamente. Cada um desses pacotes de início contém o menor número de perfis de técnicos e percurso do usuário necessários para alcançar os cenários descritos:

- **LocalAccounts** – permite o uso apenas de contas locais.
- **SocialAccounts** – permite o uso apenas de contas sociais (ou federadas).
- **SocialAndLocalAccounts** – permite o uso de contas locais e de contas sociais.
- **SocialAndLocalAccountsWithMFA** – permite opções de autenticação locais, sociais e multifator.

Cada pacote de início contém:

- **Arquivo base** – são necessárias poucas modificações na base. Exemplo: *TrustFrameworkBase.xml*
- **Arquivo de extensão** – é nesse arquivo que se faz a maioria das alterações de configuração. Exemplo: *TrustFrameworkExtensions.xml*
- **Arquivos de terceira parte confiável** – são os arquivos específicos de tarefa chamados pelo aplicativo. Exemplos: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

Neste artigo, você editará os arquivos XML de políticas personalizadas no pacote de início de **SocialAndLocalAccounts**. Se precisar de um editor de XML, experimente o [Visual Studio Code](https://code.visualstudio.com/download), um editor leve de multiplataforma.

### <a name="get-the-starter-pack"></a>Obter o pacote de início

Obtenha os pacotes de início de políticas personalizadas no GitHub. Em seguida, atualize os arquivos XML no pacote de início de SocialAndLocalAccounts com nome de seu locatário do Azure AD B2C.

1. [Baixar os arquivos .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou clonar o repositório:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Em todos os arquivos do diretório **SocialAndLocalAccounts**, substitua a cadeia de caracteres `yourtenant` pelo nome do seu locatário do Azure AD B2C.

    Por exemplo, se o nome do seu locatário B2C for *contosotenant*, todas as instâncias de `yourtenant.onmicrosoft.com` se tornarão `contosotenant.onmicrosoft.com`.

### <a name="add-application-ids-to-the-custom-policy"></a>Adicionar IDs de aplicativo à política personalizada

Adicione as IDs de aplicativo ao arquivo de extensões *TrustFrameworkExtensions.xml*.

1. Abra `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** e encontre o elemento `<TechnicalProfile Id="login-NonInteractive">`.
1. Substitua ambas as instâncias de `IdentityExperienceFrameworkAppId` pela ID do aplicativo IdentityExperienceFramework criado anteriormente.
1. Substitua ambas as instâncias de `ProxyIdentityExperienceFrameworkAppId` pela ID do aplicativo ProxyIdentityExperienceFramework criado anteriormente.
1. Salve o arquivo.

## <a name="upload-the-policies"></a>Fazer upload das políticas

1. Selecione o item de menu **Identity Experience Framework** em seu locatário B2C no portal do Azure.
1. Selecione **Carregar política personalizada**.
1. Nesta ordem, carregue os arquivos de política:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

À medida que você carrega os arquivos, o Azure adiciona o prefixo `B2C_1A_` a cada um.

> [!TIP]
> Caso seu editor de XML dê suporte à validação, valide os arquivos em relação ao esquema XML `TrustFrameworkPolicy_0.3.0.0.xsd`, localizado no diretório raiz do pacote de início. A validação de esquema XML identifica erros antes do upload.

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Na página **Políticas personalizadas**, selecione **B2C_1A_signup_signin**.
1. Para **Selecionar aplicativo** na página de visão geral da política personalizada, selecione o aplicativo Web chamado *webapp1* que você registrou anteriormente.
1. Verifique se a **URL de resposta** é `https://jwt.ms`.
1. Selecione **Executar Agora**.
1. Crie uma conta usando um endereço de email.
1. Selecione novamente **Executar agora**.
1. Entre com a mesma conta para confirmar que você tem a configuração correta.

## <a name="add-facebook-as-an-identity-provider"></a>Adicionar o Facebook como um provedor de identidade

Como mencionado em [Pré-requisitos](#prerequisites), o Facebook *não* é necessário para usar políticas personalizadas, mas é usado aqui para demonstrar como você pode habilitar o logon social federado em uma política personalizada.

1. No arquivo `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** , substitua o valor de `client_id` pela ID do aplicativo do Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Faça upload do arquivo *TrustFrameworkExtensions.xml* no seu locatário.
1. Na página **Políticas personalizadas**, selecione **B2C_1A_signup_signin**.
1. Selecione **Executar agora** e o Facebook para entrar no Facebook e testar a política personalizada.

## <a name="next-steps"></a>Próximas etapas

Em seguida, tente adicionar o Azure AD (Azure Active Directory) como provedor de identidade. O arquivo base usado neste guia de introdução já contém uma parte do conteúdo de que você precisa para adicionar outros provedores de identidade como o Azure AD. Para obter informações sobre como configurar o Azure AD como um provedor de identidade, consulte [Configurar inscrição e entrada com uma conta de Azure Active Directory usando Active Directory B2C políticas personalizadas](identity-provider-azure-ad-single-tenant.md). 

Visite nossa [Galeria de parceiros](partner-gallery.md) para saber mais sobre como implementar a integração de ISVs usando políticas personalizadas. 
