---
title: "Limitação de solicitação avançada com o Gerenciamento de API do Azure"
description: "Saiba como criar e aplicar políticas flexíveis de limitação de cota e de taxa com o Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 356f98aec072a1295915ae0701a3e3cd793aba07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Limitação de solicitação avançada com o Gerenciamento de API do Azure
Ser capaz de limitar as solicitações de entrada é fundamental para o Gerenciamento de API do Azure. Ao controlar a taxa de solicitações ou as solicitações/dados totais transferidos, o Gerenciamento de API permite que os provedores de API protejam suas APIs contra o abuso e criem valor para diferentes níveis de produto de API.

## <a name="product-based-throttling"></a>Limitação baseada em produto
Até o momento, os recursos de limitação da taxa estavam restritos à assinatura de um produto específico (essencialmente uma chave), definida no portal do publicador de Gerenciamento de API. Isso é útil para o provedor de API aplicar limites aos desenvolvedores que se inscreveram para usar sua API, mas não ajuda, por exemplo, na limitação de usuários finais individuais da API. É possível que um único usuário do aplicativo do desenvolvedor consuma toda a cota, impedindo que outros clientes do desenvolvedor possam usar o aplicativo. Além disso, vários clientes que gerem um grande volume de solicitações podem limitar o acesso a usuários ocasionais.

## <a name="custom-key-based-throttling"></a>Limitação baseada em chave personalizada
As novas políticas [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) fornecem uma solução consideravelmente mais flexível para o controle de tráfego. Essas novas políticas permitem que você defina expressões a fim de identificar as chaves que serão usadas para acompanhar o tráfego. O modo como isso funciona será ilustrado com mais facilidade usando um exemplo. 

## <a name="ip-address-throttling"></a>Limitação de endereço IP
As políticas a seguir restringem um endereço IP de cliente individual para apenas 10 chamadas a cada minuto, com um total de 1.000.000 chamadas e 10.000 quilobytes de largura de banda por mês. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Se todos os clientes na Internet usassem um endereço IP exclusivo, essa poderia ser uma maneira eficaz de limitar o uso por usuário. No entanto, é bastante provável que vários usuários compartilhem um único endereço IP público, já que acessam a Internet por meio de um dispositivo NAT. Apesar disso, para as APIs que permitem o acesso não autenticado, o `IpAddress` pode ser a melhor opção.

## <a name="user-identity-throttling"></a>Limitação de identidade do usuário
Se um usuário final for autenticado, uma chave de limitação poderá ser gerada com base nas informações que identificam esse usuário exclusivamente.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Neste exemplo, extraímos o cabeçalho Autorização, o convertemos no objeto `JWT` e usamos o assunto do token para identificar o usuário e como a chave de limitação de taxa. Se a identidade do usuário for armazenada no `JWT` como uma das outras declarações, esse valor poderá ser usado em seu lugar.

## <a name="combined-policies"></a>Políticas combinadas
Embora as novas políticas de limitação forneçam mais controle do que as políticas de limitação existentes, ainda vale a pena combinar os dois recursos. Limitar por chave de assinatura do produto ([Limitar a taxa de chamada por assinatura](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e [Definir a cota de uso por assinatura](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) é uma ótima maneira de habilitar a monetização de uma API por meio da cobrança com base nos níveis de uso. O controle mais preciso proporcionado pela limitação de acordo com o usuário é complementar e impede que o comportamento de um usuário afete a experiência de outros. 

## <a name="client-driven-throttling"></a>Limitação controlada pelo cliente
Quando a chave de limitação é definida usando uma [expressão de política](https://msdn.microsoft.com/library/azure/dn910913.aspx), é o provedor de API que está escolhendo como a limitação é delimitada. No entanto, um desenvolvedor pode querer controlar como vai limitar seus próprios clientes. Isso pode ser habilitado pelo provedor da API introduzindo um cabeçalho personalizado para permitir que o aplicativo cliente do desenvolvedor comunique a chave para a API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Isso permite que o aplicativo cliente do desenvolvedor escolha como quer criar a chave de limitação de taxa. Com um pouco de criatividade, um desenvolvedor cliente pode criar suas próprias camadas de taxas, alocando conjuntos de chaves a usuários e promovendo a rotatividade do uso da chave.

## <a name="summary"></a>Resumo
O Gerenciamento de API do Azure fornece limitação de taxa e de cota para proteger e agregar valor ao serviço de sua API. As novas políticas de limitação com regras de escopo personalizadas permitem um controle mais preciso sobre essas políticas para permitir que seus clientes criem aplicativos ainda melhores. Os exemplos deste artigo demonstram o uso dessas novas políticas criando chaves de limitação de taxa com endereços IP do cliente, identidade do usuário e valores gerados pelo cliente. No entanto, há muitas outras partes da mensagem que podem ser usadas como agente do usuário, fragmentos de caminho de URL, tamanho da mensagem.

## <a name="next-steps"></a>Próximas etapas
Envie-nos seus comentários no thread de discussão deste tópico. Seria ótimo conhecer outros possíveis valores de chave que foram uma escolha lógica para os seus cenários.

## <a name="watch-a-video-overview-of-these-policies"></a>Assista a uma visão geral dessas políticas em vídeo
Para saber mais sobre as políticas de [limite de taxa por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e de [cota por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) abordadas neste artigo, assista ao vídeo a seguir.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Advanced-Request-Throttling-with-Azure-API-Management/player]
> 
> 

