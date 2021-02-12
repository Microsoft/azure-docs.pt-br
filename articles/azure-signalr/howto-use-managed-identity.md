---
title: Identidades gerenciadas no serviço de Signaler do Azure
description: Saiba como as identidades gerenciadas funcionam no serviço do Azure Signalr e como usar uma identidade gerenciada em cenários sem servidor.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: dee15977318eda7bcd0b1950286bb33f621221dd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731577"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Identidades gerenciadas para o serviço de Signaler do Azure

Este artigo mostra como criar uma identidade gerenciada para o serviço de sinalizador do Azure e como usá-lo em cenários sem servidor.

> [!Important] 
> O serviço de Signaler do Azure pode dar suporte a apenas uma identidade gerenciada. Isso significa que você pode adicionar uma identidade atribuída pelo sistema ou uma identidade atribuída pelo usuário. 

## <a name="add-a-system-assigned-identity"></a>Adicionar uma identidade atribuída pelo sistema

Para configurar uma identidade gerenciada no portal do Azure, primeiro você criará uma instância do serviço de Signaler do Azure e, em seguida, habilitará o recurso.

1. Crie uma instância do serviço de Signaler do Azure no portal como faria normalmente. Navegue até ele no Portal.

2. Selecionar **Identidade**.

4. Na guia **atribuído pelo sistema** , alterne o **status** para **ativado**. Clique em **Salvar**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Adicionar uma identidade atribuída pelo sistema no portal":::

## <a name="add-a-user-assigned-identity"></a>Adicionar uma identidade atribuída pelo usuário

A criação de uma instância do serviço de Signaler do Azure com uma identidade atribuída pelo usuário requer que você crie a identidade e, em seguida, adicione seu identificador de recurso ao serviço.

1. Crie um recurso de identidade gerenciado atribuído pelo usuário de acordo com [estas instruções](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Crie uma instância do serviço de Signaler do Azure no portal como faria normalmente. Navegue até ele no Portal.

3. Selecionar **Identidade**.

4. Na guia **atribuído pelo usuário** , selecione **Adicionar**.

5. Pesquise a identidade que você criou anteriormente e a selecione. Selecione **Adicionar**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Adicionar uma identidade atribuída pelo usuário no portal":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>Usar uma identidade gerenciada em cenários sem servidor

O serviço de Signaler do Azure é um serviço totalmente gerenciado, portanto, você não pode usar uma identidade gerenciada para obter tokens manualmente. Em vez disso, o serviço de sinalizador do Azure usa a identidade gerenciada que você definiu para obter um token de acesso. Em seguida, o serviço define o token de acesso em um `Authorization` cabeçalho em uma solicitação upstream em cenários sem servidor.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Habilitar a autenticação de identidade gerenciada em configurações de upstream

1. Adicione uma identidade atribuída pelo sistema ou uma identidade atribuída pelo usuário.

2. Adicione uma configuração upstream e clique em qualquer asterisco para entrar em uma página detalhada, conforme mostrado abaixo.
    :::image type="content" source="media/signalr-howto-use-managed-identity/pre-msi-settings.png" alt-text="pré-MSI-configuração":::
    
    :::image type="content" source="media/signalr-howto-use-managed-identity/msi-settings.png" alt-text="MSI-configuração":::

3. Nas configurações de autenticação de identidade gerenciadas, para **recurso**, você pode especificar o recurso de destino. O recurso se tornará uma `aud` declaração no token de acesso obtido, que pode ser usado como parte da validação em seus pontos de extremidade upstream. O recurso pode ser um dos seguintes:
    - Vazio
    - ID do aplicativo (cliente) da entidade de serviço
    - URI da ID do aplicativo da entidade de serviço
    - [ID de recurso de um serviço do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Se você validar um token de acesso por conta própria em seu serviço, poderá escolher qualquer um dos formatos de recurso. Apenas certifique-se de que o valor do **recurso** nas configurações de **autenticação** e a validação são consistentes. Se você usar o controle de acesso baseado em função do Azure (RBAC do Azure) para um plano de dados, deverá usar o recurso que o provedor de serviços solicita.

### <a name="validate-access-tokens"></a>Validar tokens de acesso

O token no `Authorization` cabeçalho é um [token de acesso da plataforma de identidade da Microsoft](../active-directory/develop/access-tokens.md#validating-tokens).

Para validar tokens de acesso, seu aplicativo também deve validar o público e os tokens de assinatura. Estes precisam ser validados em relação aos valores no documento de descoberta OpenID. Por exemplo, consulte a [versão independente de locatário do documento](https://login.microsoftonline.com/common/.well-known/openid-configuration).

O middleware Azure Active Directory (Azure AD) tem recursos internos para validar tokens de acesso. Você pode navegar pelos nossos [exemplos](../active-directory/develop/sample-v2-code.md) para encontrar um no idioma de sua escolha.

Fornecemos bibliotecas e exemplos de código que mostram como manipular a validação de token. Há também várias bibliotecas de parceiros de software livre disponíveis para validação JWT (JSON Web token). Há pelo menos uma opção para quase todas as plataformas e idiomas. Para obter mais informações sobre as bibliotecas de autenticação do Azure AD e exemplos de código, consulte [bibliotecas de autenticação da plataforma de identidade da Microsoft](../active-directory/develop/reference-v2-libraries.md).

#### <a name="authentication-in-function-app"></a>Autenticação no Aplicativo de funções

Definir a validação de token de acesso no Aplicativo de funções é fácil e eficiente sem que o código funcione.

1. Na página **autenticação/autorização** , alterne a **autenticação do serviço de aplicativo** para **ativado**.

2. Selecione **fazer logon com Azure Active Directory** em **ação a ser tomada quando a solicitação não for autenticada**.

3. No provedor de autenticação, clique em into **Azure Active Directory**

4. Na nova página. Selecione **expressa** e **criar novo aplicativo do AD** e clique em **OK** :::image type="content" source="media/signalr-howto-use-managed-identity/function-aad.png" alt-text="função AAD":::

5. Navegue até o serviço de sinalização e siga [as etapas](howto-use-managed-identity.md#add-a-system-assigned-identity) para adicionar uma identidade atribuída pelo sistema ou uma identidade atribuída pelo usuário.

6. Entre em **configurações de upstream** no serviço signalr e escolha **usar identidade gerenciada** e **selecione de aplicativos existentes**. Selecione o aplicativo que você criou anteriormente.

Depois dessas configurações, a Aplicativo de funções rejeitará as solicitações sem um token de acesso no cabeçalho.

## <a name="use-a-managed-identity-for-key-vault-reference"></a>Usar uma identidade gerenciada para referência de Key Vault

O serviço signalr pode acessar Key Vault para obter o segredo usando a identidade gerenciada.

1. Adicione uma identidade atribuída pelo sistema ou uma identidade atribuída pelo usuário para o serviço de Signaler do Azure.

2. Conceda permissão de leitura de segredo para a identidade gerenciada nas políticas de acesso no Key Vault. Consulte [atribuir uma política de acesso de Key Vault usando o portal do Azure](../key-vault/general/assign-access-policy-portal.md)

Atualmente, esse recurso pode ser usado nos seguintes cenários:

- [Segredo de referência no padrão de URL upstream](./concept-upstream.md#key-vault-secret-reference-in-url-template-settings)


## <a name="next-steps"></a>Próximas etapas

- [Desenvolvimento de funções do Azure e a configuração com o serviço do Azure SignalR](signalr-concept-serverless-development-config.md)