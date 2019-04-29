---
title: Autenticação baseada em token (HTTP/2) para o APNS em Hubs de Notificação do Azure | Microsoft Docs
description: Este tópico explica como aproveitar a nova autenticação de token para o APNS
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 02/13/2019
ms.author: jowargo
ms.openlocfilehash: 890577c013a96fc06acf3b05881649ad8202a083
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60872283"
---
# <a name="token-based-http2-authentication-for-apns"></a>Autenticação baseada em token (HTTP/2) para o APNS

## <a name="overview"></a>Visão geral

Este artigo fornece detalhes sobre como usar o novo protocolo HTTP/2 do APNS com autenticação baseada em token.

Os principais benefícios de usar o novo protocolo incluem:

* A geração de token é relativamente sem complicações (em comparação com certificados)
* Não há mais dadas de expiração – você está no controle dos seus tokens de autenticação e sua revogação
* O conteúdo agora pode ser de até 4 KB
* Comentários síncronos
* Você está usando o protocolo mais recente da Apple – os certificados ainda usam o protocolo binário, que está marcado para substituição

O uso desse novo mecanismo pode ser feito em duas etapas, em alguns minutos:

1. Obtenha as informações necessárias do portal da Conta de Desenvolvedor da Apple
2. Configure seu hub de notificações com as novas informações

Os Hubs de Notificação agora estão configurados para usar o novo sistema de autenticação com APNS.

Observe que se você migrou usando as credenciais de certificado para APNS:

* as propriedades de token substituem o certificado em nosso sistema,
* mas o aplicativo continua a receber notificações perfeitamente.

## <a name="obtaining-authentication-information-from-apple"></a>Obtendo informações de autenticação da Apple

Para habilitar a autenticação baseada em token, você precisará das seguintes propriedades da Conta de Desenvolvedor da Apple:

### <a name="key-identifier"></a>Identificador de chave

O identificador de chave pode ser obtido na página "Chaves" em sua Conta de Desenvolvedor da Apple

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Identificador do aplicativo e nome do aplicativo

O nome do aplicativo está disponível por meio da página de IDs de Aplicativo na Conta de Desenvolvedor.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

O identificador do aplicativo está disponível por meio da página de detalhes da associação na Conta de Desenvolvedor.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>Token de autenticação

O token de autenticação pode ser baixado depois de gerar um token para seu aplicativo. Para obter detalhes sobre como gerar esse token, consulte a [documentação do Desenvolvedor da Apple](https://help.apple.com/xcode/mac/current/#/devdfd3d04a1).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Configurando seu hub de notificações para usar a autenticação baseada em token

### <a name="configure-via-the-azure-portal"></a>Configurar por meio do Portal do Azure

Para habilitar a autenticação baseada em token no portal, faça logon no Portal do Azure e vá para o painel Hub de Notificações > Serviços de Notificação > APNS.

Há uma nova propriedade – *Modo de Autenticação*. Selecionar Token permite que você atualize seu hub com todas as propriedades relevantes de token.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Insira as propriedades que você recuperou da sua conta de desenvolvedor da Apple
* Escolha o modo de aplicativo (Produção ou Área Restrita)
* Clique no botão **Salvar** para atualizar suas credenciais APNS

### <a name="configure-via-management-api-rest"></a>Configurar por meio da API de gerenciamento (REST)

Você pode usar nossas [APIs de gerenciamento](https://msdn.microsoft.com/library/azure/dn495827.aspx) para atualizar o hub de notificações para usar a autenticação baseada em token.
Dependendo se o aplicativo que você está configurando é um aplicativo de Área Restrita ou de Produção (especificado na sua Conta de Desenvolvedor da Apple), use um dos pontos de extremidade correspondentes:

* Ponto de extremidade da área restrita: [https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* Ponto de extremidade de produção: [https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> A autenticação baseada em token requer uma versão de API de: **04/2017 ou posterior**.

Aqui está um exemplo de uma solicitação PUT para atualizar um hub com autenticação baseada em token:

    ```text
    PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
      "Properties": {
        "ApnsCredential": {
          "Properties": {
            "KeyId": "<Your Key Id>",
            "Token": "<Your Authentication Token>",
            "AppName": "<Your Application Name>",
            "AppId": "<Your Application Id>",
            "Endpoint":"<Sandbox/Production Endpoint>"
          }
        }
      }
    ```

### <a name="configure-via-the-net-sdk"></a>Configurar por meio do SDK do .NET

Você pode configurar seu hub para usar a autenticação baseada em token usando nosso [SDK de cliente mais recente](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8).

Aqui está um exemplo de código que ilustra o uso correto:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR TOKEN HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="reverting-to-using-certificate-based-authentication"></a>Revertendo para usar a autenticação baseada em certificado

Você pode reverter a qualquer momento para usar autenticação baseada em certificado usando qualquer método anterior e passando o certificado em vez de as propriedades de token. Essa ação substitui as credenciais armazenadas anteriormente.
