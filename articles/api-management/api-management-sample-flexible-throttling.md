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
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 36b21196207f65975dae950f43ec0c7094991dad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100362022"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Limitação de solicitação avançada com o Gerenciamento de API do Azure
Ser capaz de limitar as solicitações de entrada é fundamental para o Gerenciamento de API do Azure. Ao controlar a taxa de solicitações ou as solicitações/dados totais transferidos, o Gerenciamento de API permite que os provedores de API protejam suas APIs contra o abuso e criem valor para diferentes níveis de produto de API.

## <a name="rate-limits-and-quotas"></a>Limites de taxa e cotas
Os limites de taxa e as cotas são usados para finalidades diferentes.

### <a name="rate-limits"></a>Limites de taxa
Os limites de taxa geralmente são usados para proteger contra picos de volume curtos e intensos. Por exemplo, se você souber que seu serviço de back-end tem um afunilamento em seu banco de dados com um alto volume de chamadas, você pode definir uma `rate-limit-by-key` política para não permitir o alto volume de chamadas usando essa configuração.

### <a name="quotas"></a>Cotas
As cotas geralmente são usadas para controlar as taxas de chamada em um período de tempo maior. Por exemplo, eles podem definir o número total de chamadas que um determinado assinante pode fazer dentro de um determinado mês. Para monetização sua API, as cotas também podem ser definidas de forma diferente para assinaturas baseadas em camadas. Por exemplo, uma assinatura de camada básica pode não ser capaz de fazer mais de 10.000 chamadas por mês, mas uma camada Premium pode ir até 100 milhões chamadas por mês.

No gerenciamento de API do Azure, os limites de taxa normalmente são propagados mais rapidamente entre os nós para proteção contra picos. Por outro lado, as informações de cota de uso são usadas em um prazo maior e, portanto, sua implementação é diferente.

> [!CAUTION]
> Devido à natureza distribuída da arquitetura de limitação, a limitação de taxa nunca é completamente precisa. A diferença entre o número configurado e o real de solicitações permitidas varia de acordo com o volume e a taxa de solicitação, a latência de back-end e outros fatores.

## <a name="product-based-throttling"></a>Limitação com base no produto
Os recursos de limitação de taxa que têm o escopo de uma determinada assinatura são úteis para o provedor de API aplicar limites aos desenvolvedores que se inscreveram para usar sua API. No entanto, ele não ajuda, por exemplo, na limitação de usuários finais individuais da API. É possível que um único usuário do aplicativo do desenvolvedor consuma toda a cota e, em seguida, impeça que outros clientes do desenvolvedor possam usar o aplicativo. Além disso, vários clientes que gerem um grande volume de solicitações podem limitar o acesso a usuários ocasionais.

## <a name="custom-key-based-throttling"></a>Limitação baseada em chave personalizada

> [!NOTE]
> As `rate-limit-by-key` `quota-by-key` políticas e não estão disponíveis no nível de consumo do gerenciamento de API do Azure. 

As políticas [taxa-limite-por-chave](./api-management-access-restriction-policies.md#LimitCallRateByKey) e [cota por chave](./api-management-access-restriction-policies.md#SetUsageQuotaByKey) fornecem uma solução mais flexível para o controle de tráfego. Essas políticas permitem que você defina expressões para identificar as chaves que são usadas para rastrear o uso do tráfego. O modo como isso funciona será ilustrado com mais facilidade usando um exemplo. 

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
Embora as políticas de limitação baseadas no usuário forneçam mais controle do que as políticas de limitação baseadas em assinatura, ainda há um valor que combina os dois recursos. Limitar por chave de assinatura do produto ([Limitar a taxa de chamada por assinatura](./api-management-access-restriction-policies.md#LimitCallRate) e [Definir a cota de uso por assinatura](./api-management-access-restriction-policies.md#SetUsageQuota)) é uma ótima maneira de habilitar a monetização de uma API por meio da cobrança com base nos níveis de uso. O controle mais preciso proporcionado pela limitação de acordo com o usuário é complementar e impede que o comportamento de um usuário afete a experiência de outros. 

## <a name="client-driven-throttling"></a>Limitação controlada pelo cliente
Quando a chave de limitação é definida usando uma [expressão de política](./api-management-policy-expressions.md), é o provedor de API que está escolhendo como a limitação é delimitada. No entanto, um desenvolvedor pode querer controlar como vai limitar seus próprios clientes. Isso pode ser habilitado pelo provedor da API introduzindo um cabeçalho personalizado para permitir que o aplicativo cliente do desenvolvedor comunique a chave para a API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Isso permite que o aplicativo cliente do desenvolvedor escolha como quer criar a chave de limitação de taxa. Os desenvolvedores do cliente podem criar suas próprias camadas de taxas, alocando conjuntos de chaves a usuários e promovendo a rotatividade do uso da chave.

## <a name="summary"></a>Resumo
O Gerenciamento de API do Azure fornece limitação de taxa e de cota para proteger e agregar valor ao serviço de sua API. As novas políticas de limitação com regras de escopo personalizadas permitem um controle mais preciso sobre essas políticas para permitir que seus clientes criem aplicativos ainda melhores. Os exemplos deste artigo demonstram o uso dessas novas políticas criando chaves de limitação de taxa com endereços IP do cliente, identidade do usuário e valores gerados pelo cliente. No entanto, há muitas outras partes da mensagem que podem ser usadas como agente do usuário, fragmentos de caminho de URL, tamanho da mensagem.

## <a name="next-steps"></a>Próximas etapas
Envie-nos seus comentários como um problema do GitHub para este tópico. Seria ótimo conhecer outros possíveis valores de chave que foram uma escolha lógica para os seus cenários.
