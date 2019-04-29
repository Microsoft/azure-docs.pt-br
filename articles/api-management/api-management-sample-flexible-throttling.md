---
title: Limitação de solicitação avançada com o Gerenciamento de API do Azure
description: Saiba como criar e aplicar políticas flexíveis de limitação de cota e de taxa com o Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 22c3987121e2ab3479274c89c359c679f5f1135e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61087112"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Limitação de solicitação avançada com o Gerenciamento de API do Azure
Ser capaz de limitar as solicitações de entrada é fundamental para o Gerenciamento de API do Azure. Ao controlar a taxa de solicitações ou as solicitações/dados totais transferidos, o Gerenciamento de API permite que os provedores de API protejam suas APIs contra o abuso e criem valor para diferentes níveis de produto de API.

## <a name="product-based-throttling"></a>Limitação com base no produto
Até o momento, os recursos de limitação da taxa estavam restritos à assinatura de um produto específico, definida no Portal do Azure. Isso é útil para o provedor de API aplicar limites aos desenvolvedores que se inscreveram para usar sua API, mas não ajuda, por exemplo, na limitação de usuários finais individuais da API. É possível que um único usuário do aplicativo do desenvolvedor consuma toda a cota, impedindo que outros clientes do desenvolvedor possam usar o aplicativo. Além disso, vários clientes que gerem um grande volume de solicitações podem limitar o acesso a usuários ocasionais.

## <a name="custom-key-based-throttling"></a>Limitação baseada em chave personalizada
As novas políticas [rate-limit-by-key](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) e [quota-by-key](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) fornecem uma solução mais flexível para o controle de tráfego. Essas novas políticas permitem que você defina expressões a fim de identificar as chaves que são usadas para acompanhar o tráfego. O modo como isso funciona será ilustrado com mais facilidade usando um exemplo. 

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

Este exemplo mostra como extrair o cabeçalho Autorização, o convertemos no objeto `JWT` e usamos o assunto do token para identificar o usuário e como a chave de limitação de taxa. Se a identidade do usuário for armazenada no `JWT` como uma das outras declarações, esse valor poderá ser usado em seu lugar.

## <a name="combined-policies"></a>Políticas combinadas
Embora as novas políticas de limitação forneçam mais controle do que as políticas de limitação existentes, ainda vale a pena combinar os dois recursos. Limitar por chave de assinatura do produto ([Limitar a taxa de chamada por assinatura](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) e [Definir a cota de uso por assinatura](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) é uma ótima maneira de habilitar a monetização de uma API por meio da cobrança com base nos níveis de uso. O controle mais preciso proporcionado pela limitação de acordo com o usuário é complementar e impede que o comportamento de um usuário afete a experiência de outros. 

## <a name="client-driven-throttling"></a>Limitação controlada pelo cliente
Quando a chave de limitação é definida usando uma [expressão de política](/azure/api-management/api-management-policy-expressions), é o provedor de API que está escolhendo como a limitação é delimitada. No entanto, um desenvolvedor pode querer controlar como vai limitar seus próprios clientes. Isso pode ser habilitado pelo provedor da API introduzindo um cabeçalho personalizado para permitir que o aplicativo cliente do desenvolvedor comunique a chave para a API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Isso permite que o aplicativo cliente do desenvolvedor escolha como quer criar a chave de limitação de taxa. Os desenvolvedores do cliente podem criar suas próprias camadas de taxas, alocando conjuntos de chaves a usuários e promovendo a rotatividade do uso da chave.

## <a name="summary"></a>Resumo
O Gerenciamento de API do Azure fornece limitação de taxa e de cota para proteger e agregar valor ao serviço de sua API. As novas políticas de limitação com regras de escopo personalizadas permitem um controle mais preciso sobre essas políticas para permitir que seus clientes criem aplicativos ainda melhores. Os exemplos deste artigo demonstram o uso dessas novas políticas criando chaves de limitação de taxa com endereços IP do cliente, identidade do usuário e valores gerados pelo cliente. No entanto, há muitas outras partes da mensagem que podem ser usadas como agente do usuário, fragmentos de caminho de URL, tamanho da mensagem.

## <a name="next-steps"></a>Próximas etapas
Envie-nos seus comentários no thread de discussão deste tópico. Seria ótimo conhecer outros possíveis valores de chave que foram uma escolha lógica para os seus cenários.

