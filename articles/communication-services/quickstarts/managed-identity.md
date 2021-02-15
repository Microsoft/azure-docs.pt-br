---
title: Usar identidades gerenciadas nos serviços de comunicação (.NET)
titleSuffix: An Azure Communication Services quickstart
description: Identidades gerenciadas permitem autorizar o acesso dos serviços de comunicação do Azure de aplicativos em execução em VMs do Azure, aplicativos de funções e outros recursos.
services: azure-communication-services
author: stefang931
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 12/04/2020
ms.author: gistefan
ms.reviewer: mikben
ms.openlocfilehash: 9fd8a17deeb49d836ff5902042bdb88696e29f31
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417015"
---
# <a name="use-managed-identities-net"></a>Usar identidades gerenciadas (.NET)

Introdução aos serviços de comunicação do Azure usando identidades gerenciadas no .NET. A administração dos serviços de comunicação e as bibliotecas de cliente SMS dão suporte à autenticação Azure Active Directory (Azure AD) com [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Este guia de início rápido mostra como autorizar o acesso às bibliotecas de cliente de administração e SMS de um ambiente do Azure que dá suporte a identidades gerenciadas. Ele também descreve como testar seu código em um ambiente de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

 - Uma conta do Azure com uma assinatura ativa. [Criar uma conta gratuitamente](https://azure.microsoft.com/free)
 - Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp).

## <a name="setting-up"></a>Ao configurar o

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Habilitar identidades gerenciadas em uma máquina virtual ou serviço de aplicativo

Identidades gerenciadas devem ser habilitadas nos recursos do Azure que você está autorizando. Para saber como habilitar identidades gerenciadas para Recursos do Azure, confira um dos seguintes artigos:

- [Azure portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [PowerShell do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo do Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de cliente Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [Serviços de aplicativos](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Atribuir funções do Azure com o portal do Azure

1. Navegue até o portal do Azure.
1. Navegue até o recurso serviço de comunicação do Azure.
1. Navegue até o controle de acesso (IAM) menu-> + Add-> adicionar atribuição de função.
1. Selecione a função "colaborador" (essa é a única função com suporte).
1. Selecione "identidade gerenciada atribuída pelo usuário" (ou uma "identidade gerenciada atribuída pelo sistema") e selecione a identidade desejada. Salve sua seleção.

![Função de identidade gerenciada](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Atribuir funções do Azure com o PowerShell

Para atribuir funções e permissões usando o PowerShell, consulte [Adicionar ou remover atribuições de função do Azure usando Azure PowerShell](../../../articles/role-based-access-control/role-assignments-powershell.md)

## <a name="add-managed-identity-to-your-communication-services-solution"></a>Adicionar identidade gerenciada à sua solução de serviços de comunicação

### <a name="install-the-client-library-packages"></a>Instalar os pacotes de biblioteca de cliente

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Configuration
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Usar os pacotes de biblioteca de cliente

Adicione as seguintes `using` diretivas ao seu código para usar as bibliotecas de cliente de armazenamento do Azure e identidade do Azure.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Configuration;
using Azure.Communication.Sms;
```

Os exemplos a seguir estão usando o [DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential). Essa credencial é adequada para ambientes de produção e desenvolvimento.

### <a name="create-an-identity-and-issue-a-token"></a>Criar uma identidade e emitir um token

O exemplo de código a seguir mostra como criar um objeto de cliente de serviço com tokens Azure Active Directory e, em seguida, usar o cliente para emitir um token para um novo usuário:

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndIssueTokenAsync(Uri resourceEdnpoint) 
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
     
          var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-azure-active-directory-tokens"></a>Enviar um SMS com tokens Azure Active Directory

O exemplo de código a seguir mostra como criar um objeto de cliente de serviço com tokens Azure Active Directory e, em seguida, usar o cliente para enviar uma mensagem SMS:

```csharp

     public async Task SendSmsAsync(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprender sobre autenticação](../concepts/authentication.md)

Você também pode querer:

- [Saiba mais sobre o controle de acesso baseado em função do Azure](../../../articles/role-based-access-control/index.yml)
- [Saiba mais sobre a biblioteca de identidades do Azure para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)
- [Criar tokens de acesso do usuário](../quickstarts/access-tokens.md)
- [Enviar uma mensagem SMS](../quickstarts/telephony-sms/send.md)
- [Saiba mais sobre SMS](../concepts/telephony-sms/concepts.md)
