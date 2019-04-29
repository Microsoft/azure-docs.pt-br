---
title: Azure Service Fabric com visão geral de Gerenciamento de API | Microsoft Docs
description: Este artigo é uma introdução ao uso de Gerenciamento de API do Azure como um gateway para aplicativos do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 0dac2730bcc13b979de6a8faaaa53c0aaf15e902
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621790"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric com visão geral de Gerenciamento de API do Azure

Os aplicativos em nuvem geralmente precisam de um gateway front-end para fornecer um ponto de entrada único para usuários, dispositivos ou outros aplicativos. No Service Fabric, um gateway pode ser qualquer serviço sem estado, como um [aplicativo ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), ou outro serviço projetado para entrada de tráfego, como [Hubs de Evento](https://docs.microsoft.com/azure/event-hubs/), [Hub IoT](https://docs.microsoft.com/azure/iot-hub/) ou [Gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/).

Este artigo é uma introdução ao uso de Gerenciamento de API do Azure como um gateway para aplicativos do Service Fabric. O Gerenciamento de API integra-se diretamente com o Service Fabric, permitindo que APIs sejam publicadas com um conjunto de regras de roteamento avançado para serviços de back-end do Service Fabric . 

## <a name="availability"></a>Disponibilidade

> [!IMPORTANT]
> Este recurso está disponível nas camadas **Premium** e **Desenvolvedor** do Gerenciamento de API, devido ao suporte de rede virtual necessário.

## <a name="architecture"></a>Arquitetura

Uma arquitetura comum do Serviço Fabric utiliza um aplicativo Web de página única que realiza chamadas HTTP para serviços de back-end que expõem APIs de HTTP. O [aplicativo de exemplo de introdução do Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) mostra um exemplo dessa arquitetura.

Nesse cenário, um serviço Web sem estado serve como o gateway no aplicativo do Serviço Fabric. Essa abordagem exige que você grave um serviço Web que pode usar um proxy nas solicitações HTTP para serviços de back-end, conforme mostrado no diagrama a seguir:

![Service Fabric com visão geral da topologia do Gerenciamento de API do Azure][sf-web-app-stateless-gateway]

À medida que os aplicativos crescem em termos de complexidade, assim também ocorre com os gateways que devem apresentar uma API diante de inúmeros serviços de back-end. O Gerenciamento de API do Azure é projetado para gerenciar APIs complexas com regras de roteamento, controle de acesso, limitação de taxa, monitoramento, registro de eventos e cache de resposta com um mínimo de trabalho da parte do usuário. O Gerenciamento de API do Azure oferece suporte para descoberta de serviço do Service Fabric, resolução de partição e seleção de réplicas para rotear de forma inteligente as solicitações de rotas diretamente para serviços de back-end no Service Fabric, de modo que você não precise gravar seu próprio gateway de API sem estado. 

Nesse cenário, a interface do usuário da Web ainda é atendida através de um serviço Web, enquanto as chamadas da API HTTP são gerenciadas e roteadas por meio do Gerenciamento de API do Azure, conforme mostrado no diagrama a seguir:

![Service Fabric com visão geral da topologia do Gerenciamento de API do Azure][sf-apim-web-app]

## <a name="application-scenarios"></a>Cenários de aplicativos

Os serviços em Service Fabric podem ser sem estado ou com estado e podem ser particionados usando um dos três esquemas: singleton, intervalo int-64 e nomeado. A resolução do ponto de extremidade de serviço requer a identificação de uma partição específica de uma instância de serviço específica. Ao resolver um ponto de extremidade de um serviço, tanto o nome da instância de serviço (por exemplo, `fabric:/myapp/myservice`) quanto a partição específica do serviço devem ser especificados, exceto no caso da partição singleton.

O Gerenciamento de API do Azure pode ser usado com qualquer combinação de serviços sem estado, serviços com estado e qualquer esquema de particionamento.

## <a name="send-traffic-to-a-stateless-service"></a>Enviar tráfego para um serviço sem estado

No caso mais simples, o tráfego é encaminhado para uma instância de serviço sem estado. Para fazer isso, uma operação de Gerenciamento de API contém uma política de processamento de entrada com um back-end do Service Fabric que mapeia para uma instância de serviço sem estado específica no back-end do Service Fabric. As solicitações enviadas para esse serviço são enviadas para uma réplica aleatória da instância de serviço sem estado.

#### <a name="example"></a>Exemplo
No cenário a seguir, um aplicativo Serviço Fabric contém um serviço sem estado nomeado `fabric:/app/fooservice`, que expõe uma API HTTP interna. O nome da instância de serviço é bem conhecido e pode ser codificado diretamente na política de processamento de entrada do Gerenciamento de API. 

![Service Fabric com visão geral da topologia do Gerenciamento de API do Azure][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Enviar tráfego para um serviço com estado

Semelhante ao cenário do serviço sem estado, o tráfego pode ser encaminhado para uma instância de serviço com estado. Nesse caso, uma operação de Gerenciamento de API contém uma política de processamento de entrada com um back-end de Service Fabric que mapeia uma solicitação para uma partição específica de uma instância de serviço *com estado* específica. A partição para mapear cada solicitação é computada através de um método lambda usando alguma entrada da solicitação HTTP de entrada, como um valor no caminho da URL. A política pode ser configurada para enviar solicitações somente para a réplica primária ou para uma réplica aleatória para operações de leitura.

#### <a name="example"></a>Exemplo

No cenário a seguir, um aplicativo do Serviço Fabric contém um serviço com estado particionado nomeado `fabric:/app/userservice` que expõe uma API HTTP interna. O nome da instância de serviço é bem conhecido e pode ser codificado diretamente na política de processamento de entrada do Gerenciamento de API.  

O serviço é particionado usando o esquema de partição Int64 com duas partições e um intervalo de chave que abrange `Int64.MinValue` a `Int64.MaxValue`. A política de back-end computa uma chave de partição dentro desse intervalo convertendo o valor `id` fornecido no caminho de solicitação da URL para um inteiro de 64-BIT, embora qualquer algoritmo possa ser utilizado aqui para computar a chave de partição. 

![Service Fabric com visão geral da topologia do Gerenciamento de API do Azure][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Enviar tráfego para múltiplos serviços sem estado

Em cenários mais avançados, é possível definir uma operação de Gerenciamento de API que mapeia solicitações para mais de uma instância de serviço. Neste caso, cada operação contém uma política que mapeia solicitações para uma instância de serviço específica com base nos valores da solicitação HTTP de entrada, como o caminho da URL ou cadeia de caracteres de consulta e, no caso de serviços com estado, uma partição dentro da instância de serviço. 

Para fazer isso, uma operação de Gerenciamento de API contém uma política de processamento de entrada com um back-end do Service Fabric que mapeia para uma instância de serviço sem estado no back-end do Service Fabric com base nos valores recuperados da solicitação HTTP de entrada. As solicitações para uma instância de serviço são enviadas para uma réplica aleatória da instância de serviço.

#### <a name="example"></a>Exemplo

Nesse exemplo, uma nova instância de serviço sem estado é criada para cada usuário de um aplicativo com um nome dinamicamente gerado utilizando a seguinte fórmula:
 
- `fabric:/app/users/<username>`

  Cada serviço tem um nome exclusivo, mas os nomes não são conhecidos antecipadamente porque os serviços são criados em resposta à entrada de administrador ou usuário e, portanto, não podem ser codificados em políticas do APIM ou regras de roteamento. Em vez disso, o nome do serviço para o qual enviar uma solicitação é gerado na definição de política de back-end a partir do valor `name` fornecido no caminho de solicitação da URL. Por exemplo: 

  - Uma solicitação para `/api/users/foo` é roteada para a instância de serviço `fabric:/app/users/foo`
  - Uma solicitação para `/api/users/bar` é roteada para a instância de serviço `fabric:/app/users/bar`

![Service Fabric com visão geral da topologia do Gerenciamento de API do Azure][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Enviar tráfego para múltiplos serviços com estado

Semelhante ao exemplo de serviço sem estado, uma operação de Gerenciamento de API pode mapear solicitações para mais de uma instância de serviço **com estado**, caso em que também será necessário executar a resolução de partição para cada instância de serviço com estado.

Para fazer isso, uma operação de Gerenciamento de API contém uma política de processamento de entrada com um back-end de Service Fabric que mapeia para uma instância de serviço com estado no back-end do Service Fabric com base nos valores recuperados da solicitação HTTP de entrada. Além de mapear uma solicitação para instância de serviço específica, a solicitação também pode ser mapeada para uma partição específica dentro da instância de serviço e, opcionalmente, para a réplica primária ou uma réplica secundária aleatória dentro da partição.

#### <a name="example"></a>Exemplo

Nesse exemplo, uma nova instância de serviço com estado é criada para cada usuário do aplicativo com um nome dinamicamente gerado utilizando a seguinte fórmula:
 
- `fabric:/app/users/<username>`

  Cada serviço tem um nome exclusivo, mas os nomes não são conhecidos antecipadamente porque os serviços são criados em resposta à entrada de administrador ou usuário e, portanto, não podem ser codificados em políticas do APIM ou regras de roteamento. Em vez disso, o nome do serviço para o qual enviar uma solicitação é gerado na definição de política de back-end a partir do valor `name` fornecido no caminho de solicitação da URL. Por exemplo: 

  - Uma solicitação para `/api/users/foo` é roteada para a instância de serviço `fabric:/app/users/foo`
  - Uma solicitação para `/api/users/bar` é roteada para a instância de serviço `fabric:/app/users/bar`

Cada instância de serviço também é particionada utilizando o esquema de partição Int64 com duas partições e um intervalo de chave que abrange `Int64.MinValue` a `Int64.MaxValue`. A política de back-end computa uma chave de partição dentro desse intervalo convertendo o valor `id` fornecido no caminho de solicitação da URL para um inteiro de 64-BIT, embora qualquer algoritmo possa ser utilizado aqui para computar a chave de partição. 

![Service Fabric com visão geral da topologia do Gerenciamento de API do Azure][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Próximas etapas

Siga o [tutorial](service-fabric-tutorial-deploy-api-management.md) para configurar seu primeiro cluster do Service Fabric com o Gerenciamento de API e criar um fluxo de solicitações por meio do Gerenciamento de API para seus serviços.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png