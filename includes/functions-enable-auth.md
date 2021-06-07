---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2020
ms.author: glenga
ms.openlocfilehash: 380878fedaa2f7ea6160c3c4801c8dece6e1a9ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83648911"
---
#### <a name="enable-app-service-authenticationauthorization"></a>Habilitar a Autenticação/Autorização do Serviço de Aplicativo

A plataforma do Serviço de Aplicativo permite que você use o AAD (Azure Active Directory) e vários provedores de identidade de terceiros para autenticar clientes. Você pode usar essa estratégia para implementar regras de autorização personalizadas para suas funções e trabalhar com informações de usuário por meio do código da função. Para saber mais, consulte [Autenticação e autorização no Serviço de Aplicativo do Azure](../articles/app-service/overview-authentication-authorization.md) e [Trabalhando com identidades do cliente](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities).

#### <a name="use-azure-api-management-apim-to-authenticate-requests"></a>Use o APIM (Gerenciamento de API do Azure) para autenticar solicitações

O APIM fornece uma variedade de opções de segurança de API para solicitações de entrada. Para saber mais, confira [Políticas de autenticação do Gerenciamento de API](../articles/api-management/api-management-authentication-policies.md). Com o APIM em vigor, você pode configurar o aplicativo de funções para aceitar solicitações somente do endereço IP da sua instância do APIM. Para saber mais, confira [Restrições de endereço IP](../articles/azure-functions/ip-addresses.md#ip-address-restrictions).
