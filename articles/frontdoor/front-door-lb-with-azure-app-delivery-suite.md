---
title: Azure Front Door Service – Balanceamento de carga com o pacote de entrega de aplicativos do Azure | Microsoft Docs
description: Este artigo ajuda você a saber mais sobre como o Azure recomenda realizar o balanceamento de carga com seu pacote de entrega de aplicativos
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 3d5c0ac068a6644f3499da6c3b642a4a04408370
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736474"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Balanceamento de carga com o pacote de entrega de aplicativos do Azure

## <a name="introduction"></a>Introdução
O Microsoft Azure fornece vários serviços globais e regionais para gerenciar a forma como o tráfego de rede é distribuído e como sua carga é balanceada: Gerenciador de Tráfego, Front Door Service, Gateway de Aplicativo e Load Balancer.  Junto com as muitas regiões e arquiteturas de zona do Azure, usar esses serviços juntos permite que você crie aplicativos de alto desempenho, robustos e dimensionáveis.

![Pacote de entrega de aplicativos ][1]
 
Esses serviços são divididos em duas categorias:
1. **Serviços de balanceamento de carga global** , como o Gerenciador de tráfego e a porta da frente distribuem o tráfego dos usuários finais em seu back-ends regionais, nuvens ou até mesmo seu híbrido serviços locais. O balanceamento de carga global roteia o tráfego para o back-end do serviço mais próximo e reage a alterações na confiabilidade ou no desempenho do serviço para manter o desempenho máximo sempre ativo para seus usuários. 
2. **Serviços de balanceamento de carga regionais**, como o Standard Load Balancer ou o Gateway de Aplicativo, fornecem a capacidade de distribuir tráfego em VNets (redes virtuais) em VMs (máquinas virtuais) ou em pontos de extremidade de serviço zonal dentro de uma região.

Combinar serviços regionais e globais em seu aplicativo fornece um alto desempenho confiável, de ponta a ponta e a maneira segura para rotear o tráfego de e para seus usuários seu IaaS, PaaS ou serviços locais. Na próxima seção, descrevemos cada um desses serviços.

## <a name="global-load-balancing"></a>Balanceamento de carga global
**O Gerenciador de Tráfego** fornece balanceamento de carga DNS global. Ele analisa as solicitações de entrada DNS e responde com um back-end íntegro e que esteja de acordo com a política de roteamento selecionada pelo cliente. Opções de métodos de roteamento são:
- Roteamento de desempenho para enviar o solicitante ao back-end mais próximo em termos de latência.
- Roteamento de prioridade para direcionar todo o tráfego para um back-end, com outros back-ends como backup.
- Roteamento ponderado round robin, que distribui o tráfego com base no peso atribuído a cada back-end.
- Roteamento geográfico para garantir que os solicitantes localizados em regiões geográficas específicas sejam direcionados para os back-ends mapeados para essas regiões (por exemplo, todas as solicitações da Espanha devem ser direcionadas para a região França Central do Azure)
- Roteamento da sub-rede que permite que você mapeie intervalos de endereço IP para back-ends para que solicitações provenientes deles sejam enviadas para o back-end especificado (por exemplo, todos os usuários que estão se conectando do intervalo de endereços IP da matriz corporativa devem obter conteúdo Web diferente daquele de usuários gerais)

O cliente se conecta diretamente ao back-end. O Gerenciador de Tráfego do Azure detecta quando um back-end não está íntegro e redireciona os clientes para outra instância íntegra. Consulte a documentação do [Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md) para saber mais sobre o serviço.

O **Azure Front Door Service** fornece um DSA (aceleração dinâmica de site) incluindo o balanceamento de carga global do HTTP.  Ele analisa as rotas de solicitações HTTP de entrada para o back-end/região de serviço mais próximo para o nome de host, caminho de URL e regras configuradas especificados.  
O Front Door encerra as solicitações HTTP na borda da rede da Microsoft e investiga ativamente para detectar alterações de latência ou integridade do aplicativo ou da infraestrutura.  O Front Door então sempre encaminha o tráfego para o back-end mais rápido e disponível (íntegro). Consulte os detalhes da [arquitetura de roteamento](front-door-routing-architecture.md) e os [métodos de roteamentos de tráfego](front-door-routing-methods.md) do Front Door para saber mais sobre o serviço.

## <a name="regional-load-balancing"></a>Balanceamento de carga regional
O Gateway de Aplicativo fornece o ADC (controlador de entrega de aplicativos) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para o aplicativo. Ele permite que os clientes otimizem a produtividade do web farm descarregando a terminação SSL com uso intensivo de CPU para o Gateway de Aplicativo. Outros recursos de roteamento de camada 7 incluem distribuição round robin do tráfego de entrada, afinidade de sessão, roteamento com base no caminho de URL e capacidade de hospedar vários sites com um único Gateway de Aplicativo baseado em cookie. O Gateway de Aplicativo pode ser configurado como um gateway voltado para a Internet, um gateway apenas interno ou uma combinação de ambos. O Gateway de Aplicativo é totalmente gerenciado pelo Azure, escalonável e altamente disponível. Ele fornece um conjunto avançado de recursos de log e diagnósticos para melhor capacidade de gerenciamento.
O Load Balancer é uma parte integral da pilha do Azure SDN e fornece serviços de balanceamento de carga de camada 4 de baixa latência e de alto desempenho para todos protocolos TCP e UDP. Ele gerencia conexões de entrada e saída. Você pode configurar pontos de extremidade públicos e internos com balanceamento de carga e definir regras para mapear as conexões de entrada para destinos de pool de back-end usando opções TCP e HTTP de investigação de integridade para gerenciar a disponibilidade do serviço.


## <a name="choosing-a-global-load-balancer"></a>Como escolher um balanceador de carga global
Ao escolher um balanceador de carga global entre o Gerenciador de Tráfego e o Azure Front Door para o roteamento global, você deve considerar o que é semelhante e o que é diferente entre os dois serviços.   Ambos os serviços fornecem
- **Múltipla redundância geográfica:** se uma região ficar inativa, o tráfego será roteado sem interrupção para a região mais próxima sem qualquer intervenção do proprietário do aplicativo.
- **Roteamento para a região mais próxima:** o tráfego será automaticamente roteado para a região mais próxima

</br>A tabela a seguir descreve as diferenças entre o Gerenciador de Tráfego e o Azure Front Door Service:</br>

| Gerenciador de Tráfego | Azure Front Door Service |
| --------------- | ------------------------ |
|**Qualquer protocolo:** uma vez que o Gerenciador de Tráfego funciona na camada do DNS, você pode rotear qualquer tipo de tráfego de rede: HTTP, TCP, UDP e outros. | **Aceleração de HTTP:** com o Front Door, o tráfego é transmitido por proxy na borda da rede da Microsoft.  Por isso, solicitações HTTP (S) obtêm melhorias de latência e produtividade reduzindo a latência para negociação de SSL e usando conexões quente do AFD para seu aplicativo.|
|**Roteamento local:** com o roteamento em uma camada do DNS, o tráfego sempre vai de ponto a ponto.  Roteamento de sua filial para seu datacenter local pode levar um caminho direto; mesmo em sua própria rede usando o Gerenciador de tráfego. | **Escalabilidade independente:** como o Front Door funciona com solicitação HTTP, solicitações para diferentes caminhos de URL podem ser roteadas para diferentes pools de serviço regionais/de back-end (microsserviços) com base em regras e na integridade de cada microsserviço de aplicativo.|
|**Formato de cobrança:** a cobrança com base no DNS é dimensionada de acordo com seus usuários e, para serviços com mais usuários, atinge um platô para reduzir o custo quando o uso é maior. |**Segurança embutida:** o Front Door habilita regras como limitação de taxa e ACL de IP para permitir que você proteja seus back-ends antes que o tráfego chegue ao aplicativo. 

</br>Devido aos benefícios de desempenho, capacidade de operação e segurança para cargas de trabalho HTTP com o Front Door, é recomendável que os clientes usem o Front Door para suas cargas de trabalho HTTP.    O Gerenciador de Tráfego e o Front Door podem ser usados em paralelo para atender a todo o tráfego para seu aplicativo. 

## <a name="building-with-azures-application-delivery-suite"></a>Como criar com o pacote de entrega de aplicativos do Azure 
É recomendável que todos os sites da Web, APIs e serviços sejam geograficamente redundantes e entreguem tráfego para seus usuários do local mais próximo (menor latência) sempre que possível.  Combinar os serviços do Gerenciador de Tráfego, do Front Door Service, do Gateway de Aplicativo e do Load Balancer permite que você crie redundância geográfica e zonal para maximizar a confiabilidade, a escala e o desempenho.

No diagrama a seguir, descrevemos um serviço de exemplo que usa uma combinação de todos esses serviços para criar um serviço Web global.   Neste caso, o arquiteto usou Gerenciador de Tráfego para rotear para back-ends global para entrega de arquivo e objeto, enquanto usou o Front Door para rotear globalmente caminhos de URL que correspondem ao padrão /store/* para o serviço migrado para o Serviço de Aplicativo ao rotear todas as outras solicitações para Gateways de Aplicativo regionais.

Na região, para o serviço de IaaS, o desenvolvedor de aplicativo decidiu que quaisquer URLs que correspondam o padrão /images/* sejam atendidas de um pool de VMs dedicadas diferentes do restante do Web farm.

Além disso, o pool de VMs padrão que atende o conteúdo dinâmico precisa se comunicar com um banco de dados de back-end hospedado em um cluster de alta disponibilidade. Toda a implantação é configurada por meio do Azure Resource Manager.

O diagrama a seguir mostra a arquitetura desse cenário:

![Arquitetura detalhada do pacote de entrega de aplicativos][2] 

> [!NOTE]
> Este exemplo é apenas uma das muitas configurações possíveis dos serviços de balanceamento de carga oferecidos pelo Azure. O Gateway de Aplicativo, o Front Door, o Load Balancer e o Gerenciador de Tráfego podem ser associados e combinados para melhor atender às necessidades de balanceamento de carga. Por exemplo, se o descarregamento de SSL ou o processamento de camada 7 não forem necessários, o balanceador de carga pode ser usado no lugar de Gateway de Aplicativo.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
