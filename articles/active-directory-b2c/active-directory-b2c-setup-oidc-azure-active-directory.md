---
title: Configurar entrada-para uma organização do Active Directory do Azure - Azure Active Directory B2C
description: Configurar assinatura para uma organização do Active Directory do Azure específica no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b867a7f9ffeab3d243c8c094830aa0984cffd04a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654209"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurar assinatura para uma organização do Active Directory do Azure específica no Azure Active Directory B2C

>[!NOTE]
> Esse recurso está em uma versão prévia. Não use o recurso em ambientes de produção.

Para usar um Azure Active Directory (Azure AD) como um [provedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure AD B2C, é preciso criar um aplicativo que o represente. Este artigo mostra como habilitar a entrada de usuários a partir de uma organização específica do Azure AD usando um fluxo de usuário no Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Criar um aplicativo Azure AD

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD, que não é o mesmo que o seu locatário do Azure Active Directory B2C.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se que você estiver usando o diretório que contém o seu locatário do AD do Azure. Selecione o **filtro de diretório e assinatura** no menu superior e escolha o diretório que contém o seu locatário do AD do Azure. Isso não é o mesmo locatário que seu locatário do Azure AD B2C.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
4. Selecione **Novo registro**.
5. Insira um nome para seu aplicativo. Por exemplo, `Azure AD B2C App`.
6. Aceite a seleção de **contas neste diretório organizacional apenas** para este aplicativo.
7. Para o **URI de redirecionamento**, aceite o valor de **Web**e digite a seguinte URL em letras minúsculas, onde `your-B2C-tenant-name` é substituído pelo nome do seu locatário do Azure AD B2C. Por exemplo `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Agora todas as URLs devem estar usando [b2clogin.com](b2clogin.md).

8. Clique em **Registrar**. Cópia de **ID do aplicativo (cliente)** a ser usado posteriormente.
9. Selecione **certificados e segredos** no menu de aplicativo e, em seguida, selecione **novo segredo do cliente**.
10. Insira um nome para o segredo do cliente. Por exemplo, `Azure AD B2C App Secret`.
11. Selecione o período de validade. Para este aplicativo, aceite a seleção de **em 1 ano**.
12. Selecione **adicionar** e copie o valor do novo segredo do cliente que é exibido para ser usado mais tarde.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurar o Azure AD como um provedor de identidade

1. Verifique se que você estiver usando o diretório que contém o locatário do Azure AD B2C. Selecione o **filtro de diretório e assinatura** no menu superior e escolha o diretório que contém o seu locatário do Azure AD B2C.
2. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
3. Escolha **Provedores de identidade** e escolha **Adicionar**.
4. Insira um **Nome**. Por exemplo, insira: `Contoso Azure AD`.
5. Selecione **Tipo de provedor de identidade**, **Open ID Connect (versão prévia)** e, em seguida, clique em **OK**.
6. Selecione **configurar este provedor de identidade**
7. Para a **URL dos metadados**, insira a seguinte URL, substituindo `your-AD-tenant-domain` pelo nome de domínio do locatário do Azure AD. Por exemplo `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. Para **ID do cliente**, insira a ID do aplicativo que você registrou anteriormente e para **segredo do cliente**, insira o segredo do cliente que você registrou anteriormente.
9. Opcionalmente, digite um valor para **Domain_hint**. Por exemplo, `ContosoAD`. Esse é o valor a ser usado ao fazer referência a esse provedor de identidade usando *domain_hint* na solicitação.
10. Clique em **OK**.
11. Selecione **Mapear declarações do provedor de identidade** e defina as seguintes declarações:

    - Para **ID de usuário**, digite `oid`.
    - Para **Nome de exibição**, digite `name`.
    - Para **Nome**, digite `given_name`.
    - Para **Sobrenome**, digite `family_name`.
    - Para **Email**, digite `unique_name`.

12. Clique em **OK** e, em seguida, em **Criar** para salvar sua configuração.
