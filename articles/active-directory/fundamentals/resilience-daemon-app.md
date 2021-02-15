---
title: Aumentar a resiliência de autenticação e autorização em aplicativos daemon que você desenvolve
titleSuffix: Microsoft identity platform
description: Diretrizes para aumentar a resiliência de autenticação e autorização no aplicativo daemon usando a plataforma de identidade da Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: a7b8f893026bb96c8d768d2e6d07d0240ecb81fa
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724834"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>Aumentar a resiliência de autenticação e autorização em aplicativos daemon que você desenvolve

Este artigo fornece orientação sobre como os desenvolvedores podem usar a plataforma de identidade da Microsoft e Azure Active Directory para aumentar a resiliência dos aplicativos daemon. Isso inclui processos em segundo plano, serviços, aplicativos de servidor para servidor e aplicativos sem usuários.

![Um aplicativo daemon fazendo uma chamada para o Microsoft Identity](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>Usar identidades gerenciadas para recursos do Azure

Os desenvolvedores que criam aplicativos daemon em Microsoft Azure podem usar [identidades gerenciadas para recursos do Azure](../managed-identities-azure-resources/overview.md). Identidades gerenciadas eliminam a necessidade de os desenvolvedores gerenciarem segredos e credenciais. O recurso melhora a resiliência ao evitar erros relacionados à expiração do certificado, erros de rotação ou confiança. Ele também tem vários recursos internos destinados especificamente para aumentar a resiliência.

Identidades gerenciadas usam tokens de acesso de longa duração e informações da identidade da Microsoft para adquirir novos tokens de forma proativa em uma grande janela de tempo antes que o token existente expire. Seu aplicativo pode continuar a ser executado durante a tentativa de adquirir um novo token.

Identidades gerenciadas também usam pontos de extremidade regionais para melhorar o desempenho e a resiliência contra falhas fora de região. O uso de um ponto de extremidade regional ajuda a manter todo o tráfego dentro de uma área geográfica. Por exemplo, se o recurso do Azure estiver em WestUS2, todo o tráfego, incluindo o tráfego gerado pelo Microsoft Identity, deverá permanecer no WestUS2. Isso elimina possíveis pontos de falha ao consolidar as dependências do seu serviço.

## <a name="use-the-microsoft-authentication-library"></a>Usar a biblioteca de autenticação da Microsoft

Os desenvolvedores de aplicativos daemon que não usam identidades gerenciadas podem usar a [MSAL (biblioteca de autenticação da Microsoft)](../develop/msal-overview.md), o que torna a implementação de autenticação e autorização simples e usa automaticamente as práticas recomendadas para resiliência. O MSAL tornará o processo de fornecer as credenciais de cliente necessárias com mais facilidade. Por exemplo, seu aplicativo não precisa implementar a criação e assinatura de declarações de token Web JSON ao usar credenciais baseadas em certificado.

### <a name="use-microsoftidentityweb-for-net-developers"></a>Usar Microsoft. Identity. Web para desenvolvedores .NET

Os desenvolvedores que criam aplicativos daemon no ASP.NET Core podem usar a biblioteca [Microsoft. Identity. Web](../develop/microsoft-identity-web.md) . Essa biblioteca é criada com base em MSAL para tornar a implementação da autorização ainda mais fácil para aplicativos ASP.NET Core. Ele inclui várias estratégias de [cache de token distribuídas](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache) para aplicativos distribuídos que podem ser executados em várias regiões.

## <a name="cache-and-store-tokens"></a>Cache e armazene tokens

Se você não estiver usando o MSAL para implementar a autenticação e a autorização, poderá implementar algumas práticas recomendadas para armazenar em cache e armazenamento de tokens. O MSAL implementa e segue essas práticas recomendadas automaticamente.

Um aplicativo adquire tokens de um provedor de identidade para autorizar o aplicativo a chamar APIs protegidas. Quando seu aplicativo recebe tokens, a resposta que contém os tokens também contém uma propriedade "Expires \_ in" que informa ao aplicativo quanto tempo armazenar em cache e reutilizar o token. É importante que os aplicativos usem a propriedade "expire \_ em" para determinar o ciclo de vida do token. O aplicativo nunca deve tentar decodificar um token de acesso de API. O uso do token armazenado em cache impede o tráfego desnecessário entre seu aplicativo e a identidade da Microsoft. Seu usuário pode permanecer conectado ao seu aplicativo durante o tempo de vida do token.

## <a name="properly-handle-service-responses"></a>Lidar corretamente com as respostas do serviço

Por fim, embora os aplicativos devam lidar com todas as respostas de erro, há algumas respostas que podem afetar a resiliência. Se seu aplicativo receber um código de resposta HTTP 429, muitas solicitações, a identidade da Microsoft estará limitando suas solicitações. Se seu aplicativo continuar a fazer muitas solicitações, ele continuará a ser limitado, impedindo que seu aplicativo receba tokens. Seu aplicativo não deve tentar adquirir um token novamente até depois do tempo, em segundos, no campo de resposta "tentar novamente após" aprovado. Receber uma resposta 429 geralmente é uma indicação de que o aplicativo não está armazenando em cache e reutilizando tokens corretamente. Os desenvolvedores devem examinar como os tokens são armazenados em cache e reutilizados no aplicativo.

Quando um aplicativo recebe um código de resposta HTTP 5xx, o aplicativo não deve entrar em um loop de repetição rápido. Quando presente, o aplicativo deve respeitar o mesmo tratamento de "tentar novamente após" como faz para uma resposta de 429. Se nenhum cabeçalho "tentar novamente após" for fornecido pela resposta, é recomendável implementar uma nova tentativa de retirada exponencial com a primeira tentativa, pelo menos, 5 segundos após a resposta.

Quando uma solicitação atinge o tempo limite, os aplicativos não devem tentar de imediato imediatamente. Implemente uma nova tentativa de retirada exponencial com a primeira tentativa, pelo menos, 5 segundos após a resposta.

## <a name="next-steps"></a>Próximas etapas

- [Crie resiliência em aplicativos que conectam usuários](resilience-client-app.md)
- [Crie resiliência em sua infraestrutura de gerenciamento de identidade e acesso](resilience-in-infrastructure.md)
- [Crie resiliência em seus sistemas CIAM](resilience-b2c.md)