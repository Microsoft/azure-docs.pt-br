---
title: Usar o gerenciamento de API do Azure com microservices implantados no serviço kubernetes do Azure | Microsoft Docs
description: Este artigo descreve as opções de implantação do gerenciamento de API com o AKS
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 36dfc8c906c52c6822e583db3a08c891306f7e78
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047925"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Usar o gerenciamento de API do Azure com microservices implantados no serviço kubernetes do Azure

Os microserviços são perfeitos para a criação de APIs. Com o AKs ( [serviço kubernetes do Azure](https://azure.microsoft.com/services/kubernetes-service/) ), você pode implantar e operar rapidamente uma [arquitetura baseada em microserviços](/azure/architecture/guide/architecture-styles/microservices) na nuvem. Em seguida, você pode aproveitar o [Gerenciamento de API do Azure](https://aka.ms/apimrocks) (gerenciamento de API) para publicar seus microserviços como APIs para consumo interno e externo. Este artigo descreve as opções de implantação do gerenciamento de API com o AKS. Ele assume o conhecimento básico do kubernetes, do gerenciamento de API e da rede do Azure. 

## <a name="background"></a>Segundo plano

Ao publicar os microserviços como APIs para consumo, pode ser desafiador gerenciar a comunicação entre os microserviço e os clientes que os consomem. Há uma infinidade de preocupações abrangentes, como autenticação, autorização, limitação, Caching, transformação e monitoramento. Essas preocupações são válidas independentemente se os microserviços são expostos a clientes internos ou externos. 

O padrão de [Gateway de API](/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) aborda essas preocupações. Um gateway de API serve como uma porta de front-end para os microserviços, dissocia clientes de seus microserviços, adiciona uma camada adicional de segurança e diminui a complexidade de seus microserviços removendo a carga de lidar com preocupações de corte cruzada. 

O [Gerenciamento de API do Azure](https://aka.ms/apimrocks) é uma solução completa para resolver suas necessidades de gateway de API. Você pode criar rapidamente um gateway consistente e moderno para seus microserviços e publicá-los como APIs. Como uma solução de gerenciamento de API de ciclo de vida completo, ela também fornece recursos adicionais, incluindo um portal de desenvolvedor de autoatendimento para descoberta de API, gerenciamento de ciclo de vida de API e análise de API.

Quando usados juntos, o AKS e o gerenciamento de API fornecem uma plataforma para implantar, publicar, proteger, monitorar e gerenciar suas APIs baseadas em microserviços. Neste artigo, veremos algumas opções de implantação do AKS em conjunto com o gerenciamento de API. 

## <a name="kubernetes-services-and-apis"></a>Serviços e APIs do kubernetes

Em um cluster kubernetes, os contêineres são implantados em [pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/), que são efêmeros e têm um ciclo de vida. Quando um nó de trabalho se tornar inativo, o pods em execução no nó será perdido. Portanto, o endereço IP de um pod pode ser alterado a qualquer momento. Não podemos depender dele para se comunicar com o pod. 

Para resolver esse problema, o kubernetes introduziu o conceito de [Serviços](https://kubernetes.io/docs/concepts/services-networking/service/). Um serviço kubernetes é uma camada de abstração que define um grupo lógico de pods e permite exposição de tráfego externo, balanceamento de carga e descoberta de serviço para esses pods. 

Quando estamos prontos para publicar nossos microserviços como APIs por meio do gerenciamento de API, precisamos pensar em como mapear nossos serviços em kubernetes para APIs no gerenciamento de API. Não há regras de conjunto. Depende de como você criou e particionou seus recursos de negócios ou domínios em microserviços no início. Por exemplo, se o pods por trás de um serviço for responsável por todas as operações em um determinado recurso (por exemplo, cliente), o serviço poderá ser mapeado para uma API. Se as operações em um recurso forem particionadas em vários microserviços (por exemplo, GetOrder, PlaceOrder), vários serviços poderão ser logicamente agregados em uma única API no gerenciamento de API (consulte Fig. 1). 

Os mapeamentos também podem evoluir. Como o gerenciamento de API cria uma fachada na frente dos microserviços, ele nos permite Refatorar e dimensionar os nossos microserviços ao longo do tempo. 

![Mapear serviços para APIs](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>Implantar o gerenciamento de API na frente do AKS

Há algumas opções de implantação do gerenciamento de API na frente de um cluster AKS. 

Embora um cluster AKS sempre seja implantado em uma VNet (rede virtual), uma instância de gerenciamento de API não precisa ser implantada em uma VNet. Quando o gerenciamento de API não reside na VNet do cluster, o cluster AKS precisa publicar pontos de extremidade públicos para o gerenciamento de API se conectar ao. Nesse caso, há a necessidade de proteger a conexão entre o gerenciamento de API e o AKS. Em outras palavras, precisamos garantir que o cluster só possa ser acessado exclusivamente por meio do gerenciamento de API. Vamos percorrer as opções. 

### <a name="option-1-expose-services-publicly"></a>Opção 1: expor serviços publicamente

Os serviços em um cluster AKs podem ser expostos publicamente usando [tipos de serviço](../aks/concepts-network.md) de NodePort, Balancer ou externalname. Nesse caso, os serviços podem ser acessados diretamente da Internet pública. Depois de implantar o gerenciamento de API na frente do cluster, precisamos garantir que todo o tráfego de entrada passe pelo gerenciamento de API aplicando a autenticação nos microserviços. Por exemplo, o gerenciamento de API pode incluir um token de acesso em cada solicitação feita ao cluster. Cada microserviço é responsável por validar o token antes de processar a solicitação. 


Essa pode ser a opção mais fácil de implantar o gerenciamento de API na frente do AKS, especialmente se você já tiver a lógica de autenticação implementada em seus microserviços. 

![Publicar serviços diretamente](./media/api-management-aks/direct.png)

Prós:
* Configuração fácil no lado do gerenciamento de API porque ele não precisa ser injetado na VNet do cluster
* Nenhuma alteração no lado do AKS se os serviços já estão expostos publicamente e se a lógica de autenticação já existe em microserviços

Contras:
* Possível risco de segurança devido à visibilidade pública dos pontos de extremidade de serviço
* Nenhum ponto de entrada única para tráfego de cluster de entrada
* Complica os microserviços com lógica de autenticação duplicada

### <a name="option-2-install-an-ingress-controller"></a>Opção 2: instalar um controlador de entrada

Embora a opção 1 possa ser mais fácil, ela tem desvantagens notáveis, conforme mencionado acima. Se uma instância de gerenciamento de API não residir na VNet do cluster, a mTLS (autenticação TLS mútua) é uma maneira robusta de garantir que o tráfego é seguro e confiável em ambas as direções entre uma instância de gerenciamento de API e um cluster AKS. 

A autenticação TLS mútua tem [suporte nativo](./api-management-howto-mutual-certificates.md) do gerenciamento de API e pode ser habilitada no kubernetes [instalando um controlador de entrada](../aks/ingress-own-tls.md) (Fig. 3). Como resultado, a autenticação será executada no controlador de entrada, o que simplifica os microserviços. Além disso, você pode adicionar os endereços IP do gerenciamento de API à lista de permissões por entrada para garantir que somente o gerenciamento de API tenha acesso ao cluster.  

 
![Publicar por meio de um controlador de entrada](./media/api-management-aks/ingress-controller.png)


Prós:
* Configuração fácil no lado do gerenciamento de API porque ele não precisa ser injetado na VNet do cluster e a mTLS tem suporte nativo
* Centraliza a proteção para tráfego de cluster de entrada na camada do controlador de entrada
* Reduz o risco de segurança ao minimizar os pontos de extremidade de cluster visíveis publicamente

Contras:
* Aumenta a complexidade da configuração de cluster devido ao trabalho extra para instalar, configurar e manter o controlador de entrada e gerenciar certificados usados para mTLS
* Risco de segurança devido à visibilidade pública dos pontos de extremidade do controlador de entrada


Quando você publica APIs por meio do Gerenciamento de API, é fácil e normal proteger o acesso a essas APIs usando chaves de assinatura. Os desenvolvedores que precisam consumir as APIs publicadas devem incluir uma chave de assinatura válida em solicitações HTTP ao fazer chamadas para essas APIs. Caso contrário, as chamadas serão imediatamente rejeitadas pelo gateway de Gerenciamento de API. Elas não são encaminhadas para os serviços de back-end.

É necessária uma assinatura para obter uma chave de assinatura para acessar APIs. A assinatura é essencialmente um contêiner nomeado para um par de chaves de assinatura. Os desenvolvedores que precisam consumir as APIs publicadas podem obter as assinaturas. E eles não precisam da aprovação dos editores das APIs. Os editores de API também podem criar assinaturas diretamente para os consumidores da API.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Opção 3: implantar o APIM dentro da VNet do cluster

Em alguns casos, os clientes com restrições regulatórias ou requisitos de segurança estritos podem encontrar a opção 1 e 2 soluções não viáveis devido a pontos de extremidade publicamente expostos. Em outros, o cluster AKS e os aplicativos que consomem os microserviços podem residir na mesma VNet, portanto, não há motivo para expor o cluster publicamente, pois todo o tráfego da API permanecerá na VNet. Para esses cenários, você pode implantar o gerenciamento de API na VNet do cluster. As [camadas do desenvolvedor de gerenciamento de API e Premium](https://aka.ms/apimpricing) oferecem suporte à implantação de VNet. 

Há dois modos de [implantar o gerenciamento de API em uma VNet](./api-management-using-with-vnet.md) – externo e interno. 

Se os consumidores de API não residirem na VNet do cluster, o modo externo (Fig. 4) deverá ser usado. Nesse modo, o gateway de gerenciamento de API é injetado na VNet do cluster, mas acessível pela Internet pública por meio de um balanceador externo de carga. Ele ajuda a ocultar o cluster completamente e, ao mesmo tempo, permitir que clientes externos consumam os microserviços. Além disso, você pode usar os recursos de rede do Azure, como NSG (grupos de segurança de rede) para restringir o tráfego de rede.

![Modo de VNet externa](./media/api-management-aks/vnet-external.png)

Se todos os consumidores de API residirem na VNet do cluster, o modo interno (Fig. 5) poderá ser usado. Nesse modo, o gateway de gerenciamento de API é injetado na VNET do cluster e acessível somente de dentro dessa VNet por meio de um balanceador de carga interno. Não é possível acessar o gateway de gerenciamento de API ou o cluster AKS da Internet pública. 

![Modo VNet interna](./media/api-management-aks/vnet-internal.png)

 Em ambos os casos, o cluster AKS não é visível publicamente. Em comparação com a opção 2, o controlador de entrada pode não ser necessário. Dependendo do cenário e da configuração, a autenticação ainda pode ser necessária entre o gerenciamento de API e seus microserviços. Por exemplo, se uma malha de serviço for adotada, ela sempre exigirá autenticação TLS mútua. 

Prós:
* A opção mais segura porque o cluster AKS não tem nenhum ponto de extremidade público
* Simplifica a configuração do cluster porque ele não tem nenhum ponto de extremidade público
* Capacidade de ocultar o gerenciamento de API e AKS dentro da VNet usando o modo interno
* Capacidade de controlar o tráfego de rede usando recursos de rede do Azure, como NSG (grupos de segurança de rede)

Contras:
* Aumenta a complexidade de implantar e configurar o gerenciamento de API para trabalhar dentro da VNet

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [os conceitos de rede para aplicativos no AKs](../aks/concepts-network.md)
* Saiba mais sobre [como usar o gerenciamento de API com redes virtuais](./api-management-using-with-vnet.md)
