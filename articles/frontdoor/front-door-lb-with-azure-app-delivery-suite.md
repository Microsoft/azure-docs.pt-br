---
title: Azure front door-balanceamento de carga com o pacote de entrega de aplicativos do Azure | Microsoft Docs
description: Este artigo ajuda você a aprender como o Azure recomenda o balanceamento de carga com seu pacote de entrega de aplicativos
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 50e047325ad17710794b1640715ab1938373fe85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019456"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Balanceamento de carga com o pacote de entrega de aplicativos do Azure

## <a name="introduction"></a>Introdução
O Microsoft Azure fornece vários serviços globais e regionais para gerenciar como o tráfego de rede é distribuído e balanceado por carga: 

* Gateway de Aplicativo
* Front Door 
* Load Balancer  
* Gerenciador de Tráfego

Junto com várias regiões e arquitetura zonal do Azure, usar esses serviços juntos pode permitir que você crie aplicativos robustos, escalonáveis e de alto desempenho.

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="Pacote de entrega de aplicativos":::
 
Esses serviços são divididos em duas categorias:
1. Os **serviços de balanceamento de carga global** , como o Traffic Manager e a porta de front-end, distribuem o tráfego dos seus usuários finais entre os back-ends regionais, entre nuvens e até mesmo os serviços híbridos no local. O balanceamento de carga global roteia o tráfego para o back-end de serviço mais próximo e reage às alterações na confiabilidade do serviço para manter a disponibilidade sempre ativa e o alto desempenho para seus usuários. 
1. **Serviços de balanceamento de carga regionais** , como balanceadores de carga e gateways de aplicativo, fornecem a capacidade de distribuir o tráfego para VMs (máquinas virtuais) em uma rede virtual (VNETs) ou pontos de extremidade de serviço em uma região.

Quando você combina esses serviços globais e regionais, seu aplicativo se beneficiará do tráfego de ponta a ponta confiável e protegido que é enviado de seus usuários finais para seus serviços de IaaS, PaaS ou local. Na próxima seção, descrevemos cada um desses serviços.

## <a name="global-load-balancing"></a>Balanceamento de carga global
**O Gerenciador de Tráfego** fornece balanceamento de carga DNS global. Ele examina as solicitações DNS de entrada e responde com um back-end íntegro, seguindo a política de roteamento que o cliente selecionou. Opções de métodos de roteamento são:
- * * O roteamento de desempenho envia solicitações para o back-end mais próximo com a menor latência.
- O **Roteamento de prioridade** direciona todo o tráfego para um back-end, com outras back-ends como backup.
- O **Roteamento de rodízio ponderado** distribui o tráfego com base no peso atribuído a cada back-end.
- O **roteamento geográfico** garante que as solicitações que são originadas de regiões geográficas específicas sejam tratadas por back-ends mapeados para essas regiões. (Por exemplo, todas as solicitações da Espanha devem ser direcionadas para a região do Azure central da França)
- O **Roteamento de sub-rede** permite mapear intervalos de endereços IP para back-ends, de modo que as solicitações de entrada para esses IPS serão enviadas para o backend específico. (Por exemplo, todos os usuários que se conectam do intervalo de endereços IP da sede corporativa devem obter conteúdo da Web diferente dos usuários gerais)

O cliente se conecta diretamente ao back-end. O Gerenciador de Tráfego do Azure detecta quando um back-end não está íntegro e redireciona os clientes para outra instância íntegra. Consulte a documentação [do Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) para saber mais sobre o serviço.

A **porta frontal do Azure** fornece a aceleração de site dinâmico (DSA), incluindo balanceamento de carga http global.  Ele analisa as rotas de solicitações HTTP de entrada para o back-end/região de serviço mais próximo para o nome de host, caminho de URL e regras configuradas especificados.  
O Front Door encerra as solicitações HTTP na borda da rede da Microsoft e investiga ativamente para detectar alterações de latência ou integridade do aplicativo ou da infraestrutura.  O Front Door então sempre encaminha o tráfego para o back-end mais rápido e disponível (íntegro). Consulte os detalhes da [arquitetura de roteamento](front-door-routing-architecture.md) e os [métodos de roteamentos de tráfego](front-door-routing-methods.md) do Front Door para saber mais sobre o serviço.

## <a name="regional-load-balancing"></a>Balanceamento de carga regional
O Gateway de Aplicativo fornece o ADC (controlador de entrega de aplicativos) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para o aplicativo. Ele permite que os clientes otimizem a produtividade web farm descarregando a terminação TLS com uso intensivo de CPU para o gateway de aplicativo. Outros recursos adicionais de roteamento de camada 7 também incluem a distribuição Round Robin de tráfego de entrada, afinidade de sessão baseada em cookie, roteamento baseado em caminho de URL e a capacidade de hospedar vários sites por trás de um único gateway de aplicativo. O gateway de aplicativo pode ser configurado como um ponto de extremidade voltado para a Internet, um ponto de extremidade somente interno ou uma combinação de ambos. O gateway de aplicativo é totalmente gerenciado pelo Azure, fornecendo escalabilidade e alta disponibilidade. Ele fornece um conjunto avançado de recursos de log e diagnósticos para melhor capacidade de gerenciamento.

Os balanceadores de carga são parte integrante da pilha SDN do Azure, que fornece serviços de balanceamento de carga de camada 4 de alto desempenho e de baixa latência para todos os protocolos UDP e TCP. Você pode configurar pontos de extremidade de balanceamento de carga públicos ou internos definindo regras que mapeiam conexões de entrada para pools de back-end. Com o monitoramento de investigação de integridade usando TCP ou HTTPS, ele pode ajudá-lo a gerenciar sua disponibilidade de serviço.

## <a name="choosing-a-global-load-balancer"></a>Como escolher um balanceador de carga global
Ao escolher um balanceador de carga global entre o Gerenciador de Tráfego e o Azure Front Door para o roteamento global, você deve considerar o que é semelhante e o que é diferente entre os dois serviços.   Ambos os serviços fornecem
- **Redundância de várias regiões geográficas:** Se uma região sair do serviço, o tráfego roteará diretamente para a região mais próxima sem qualquer intervenção do proprietário do aplicativo.
- **Roteamento para a região mais próxima:** automaticamente o tráfego é roteado para a região mais próxima

</br>A tabela a seguir descreve as diferenças entre o Gerenciador de tráfego e a porta frontal do Azure:</br>

| Gerenciador de Tráfego | Porta da frente do Azure |
| --------------- | ------------------------ |
|**Qualquer protocolo:** Como o Gerenciador de tráfego funciona na camada DNS, você pode rotear qualquer tipo de tráfego de rede; HTTP, TCP, UDP e assim por diante. | **Aceleração de http:** Com a porta frontal, o tráfego é proxy na borda da rede da Microsoft. As solicitações HTTP/S verão as melhorias de latência e taxa de transferência, que reduzem a latência da negociação TLS.|
|**Roteamento local:** Com o roteamento em uma camada DNS, o tráfego sempre passa do ponto a ponto.  O roteamento de sua filial para seu datacenter local pode usar um caminho direto; mesmo em sua própria rede usando o Gerenciador de tráfego. | **Escalabilidade independente:** Como a porta frontal funciona com a solicitação HTTP, as solicitações para caminhos de URL diferentes podem ser roteadas para diferentes pools de serviço de back-end/regionais (microservices) com base em regras e a integridade de cada microserviço de aplicativo.|
|**Formato de cobrança:** a cobrança com base no DNS é dimensionada com seus usuários e para serviços com mais usuários, atingindo um platô para reduzir o custo de um uso mais alto. |**Segurança embutida:** o Front Door habilita regras como limitação de taxa e ACL de IP para permitir que você proteja seu back-ends antes que o tráfego chegue a seu aplicativo. 

</br>Recomendamos que os clientes usem a porta de front-end para sua carga de trabalho HTTP devido aos benefícios de desempenho, de operabilidade e de segurança que o HTTP trabalha com a porta frontal. O Gerenciador de Tráfego e o Front Door podem ser usados em paralelo para atender a todo o tráfego para seu aplicativo. 

## <a name="building-with-azures-application-delivery-suite"></a>Como criar com o pacote de entrega de aplicativos do Azure 
Recomendamos que todos os sites, APIs, serviços sejam geograficamente redundantes para que ele possa entregar o tráfego para seus usuários a partir do local mais próximo sempre que possível.  A combinação de vários serviços de balanceamento de carga permite que você crie redundância geográfica e zonal para maximizar a confiabilidade e o desempenho.

No diagrama a seguir, descrevemos uma arquitetura de exemplo que usa uma combinação de todos esses serviços para criar um serviço Web global. O arquiteto usou o Gerenciador de tráfego para rotear o tráfego para back-ends globais para entrega de arquivos e objetos. Ao usar a porta frontal, para rotear globalmente caminhos de URL que correspondam ao padrão/Store/* para o serviço que eles migraram para o serviço de aplicativo. Por fim, roteando todas as outras solicitações para gateways de aplicativo regionais.

Em cada região do serviço IaaS, o desenvolvedor do aplicativo decidiu que todas as URLs que correspondem ao padrão/images/* serão servidas de um pool de VMs dedicado. Esse pool de VMs é diferente do restante do web farm.

Além disso, o pool de VMs padrão que atende o conteúdo dinâmico precisa se comunicar com um banco de dados de back-end hospedado em um cluster de alta disponibilidade. Toda a implantação é configurada por meio de Azure Resource Manager.

O diagrama a seguir mostra a arquitetura desse cenário:

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="Arquitetura detalhada do pacote de entrega de aplicativos":::

> [!NOTE]
> Este exemplo é apenas uma das muitas configurações possíveis dos serviços de balanceamento de carga oferecidos pelo Azure. O Gateway de Aplicativo, o Front Door, o Load Balancer e o Gerenciador de Tráfego podem ser associados e combinados para melhor atender às necessidades de balanceamento de carga. Por exemplo, se o descarregamento de TLS/SSL ou o processamento de camada 7 não for necessário, Load Balancer poderá ser usado no lugar do gateway de aplicativo.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
