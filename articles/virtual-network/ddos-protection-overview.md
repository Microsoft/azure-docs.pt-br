---
title: "Visão geral de Proteção contra DDoS do Azure Standard | Microsoft Docs"
description: "Saiba mais sobre o serviço de Proteção contra DDoS do Azure."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 0130823b6a6f5a4883ad640c9bcefc89b82b2cd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ddos-protection-standard-overview"></a>Visão geral de Proteção contra DDoS do Azure Standard

>[!IMPORTANT]
>A Proteção contra DDoS do Azure Standard está atualmente em versão prévia. O suporte à Proteção contra DDoS Standard só é dado por um número limitado de recursos do Azure e em um número seleto de regiões. Você precisa [registrar-se para o serviço](http://aka.ms/ddosprotection) durante a versão prévia limitada para habilitar a Proteção contra DDoS Standard para a sua assinatura. Você é contatado pela equipe de DDoS do Azure após o registro para lhe orientar durante o processo de habilitação. A Proteção contra DDoS Standard está disponível nas regiões Leste dos EUA, Oeste dos EUA e Centro-oeste dos EUA. Durante a versão prévia, você não será cobrado pelo uso do serviço.

Ataques de DDoS (negação de serviço distribuído) são uma das maiores preocupações de disponibilidade e de segurança enfrentadas pelos clientes que estão movendo seus aplicativos para a nuvem. Um ataque de DDoS tenta esgotar os recursos de um aplicativo fazendo com que o aplicativo não fique disponível para usuários legítimos. Ataques de DDoS podem ser direcionadas a qualquer ponto de extremidade publicamente acessível pela Internet.

A Proteção contra DDoS do Azure, combinada com as melhores práticas de design de aplicativo, fornecem proteção contra esses ataques. Estas duas camadas de serviço são fornecidas: 

- **Proteção contra DDoS do Azure Básica** – já está automaticamente habilitada como parte da plataforma Azure, sem custo adicional. A mitigação em tempo real de ataques de nível de rede comuns e o monitoramento de tráfego Always On fornecem as mesmas defesas utilizadas por serviços online da Microsoft.  A escala inteira da rede global do Azure pode ser usada para distribuir e reduzir o tráfego de ataques entre regiões. 
- **Proteção contra DDoS do Azure Standard** – fornece recursos de mitigação adicional ajustados especificamente para os recursos da Rede Virtual. Ele é simples de habilitar e não requer nenhuma alteração ao aplicativo. As políticas de proteção são ajustadas por meio do monitoramento de tráfego dedicado e dos algoritmos de aprendizado de máquina e aplicadas a IPs públicos associados a recursos de Rede Virtual como instâncias do Load Balancer, do Gateway de Aplicativo e do Service Fabric.  A telemetria em tempo real está disponível por meio de exibições do Azure Monitor durante um ataque e para o histórico. Proteções de camada de aplicativo podem ser adicionadas por meio do [Firewall do Aplicativo Web do Gateway de Aplicativo](https://azure.microsoft.com/services/application-gateway/). 

![Proteção contra DDoS do Azure Standard](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

Para cenários de desenvolvimento e teste, você é convidado a experimentar a Proteção contra DDoS Standard e a usar estes recursos para fornecer comentários sobre suas experiências:
- [Proteção contra DDoS do Azure no Fórum do Microsoft Azure](https://feedback.azure.com/forums/905032-azure-ddos-protection). 
- [Proteção contra DDoS do Azure no Fórum do MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [Proteção contra DDoS do Azure no Stack Overflow](https://stackoverflow.com/tags/azure-ddos/info)

Para problemas de suporte, você pode [abrir um tíquete de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md).

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipos de ataques de DDoS mitigados pela Proteção contra DDoS Standard

A Proteção contra DDoS Standard pode mitigar estes tipos de ataques:

- **Ataques volumétricos** – a meta do ataque é inundar a camada de rede com uma quantidade significativa de tráfego aparentemente legítimo. Ele inclui inundações de UDP, inundações de amplificação e outras inundações de pacotes falsificados. A Proteção contra DDoS Standard mitiga esses ataques potenciais de vários gigabytes absorvendo-os e depurando-os, utilizando a escala de rede global do Azure automaticamente. 
- **Ataques de protocolo** – esses ataques renderizam um destino inacessível explorando uma vulnerabilidade na pilha de protocolos das camadas 3 e 4. Eles incluem ataques de inundação SYN, ataques de reflexão e outros ataques de protocolo. A Proteção contra DDoS Standard mitiga esses ataques diferenciando entre o tráfego mal-intencionado e o legítimo, interagindo com o cliente e bloqueando o tráfego mal-intencionado. 
- **Ataques de camada de aplicativo** – esses ataques são direcionados a pacotes de aplicativo Web para interromper a transmissão de dados entre os hosts. Eles incluem violações de protocolo HTTP, injeção de SQL, scripts entre sites e outros ataques de camada 7. Use o WAF do Gateway de Aplicativo com a Proteção contra DDoS Standard para fornecer proteção contra esses ataques. 

A Proteção contra DDoS Standard protege os recursos em uma rede virtual, incluindo IPs públicos associados com VMs, balanceadores de carga internos e gateways de aplicativo. Quando combinada com o SKU do WAF do Gateway de Aplicativo, a Proteção contra DDoS Standard pode fornecer uma capacidade de mitigação completa da L3 à L7.

## <a name="ddos-protection-standard-features"></a>Recursos da Proteção contra DDoS do Azure Standard

![Funcionalidade de DDoS](./media/ddos-protection-overview/ddos-overview-fig1.png)

Os recursos da Proteção contra DDoS do Azure Standard: 

- **Integração de plataforma nativa:** a Proteção contra DDoS Standard é nativamente integrada ao Azure e inclui configuração por meio do Portal do Azure e do PowerShell. A Proteção contra DDoS Standard compreende seus recursos e a respectiva configuração.
- **Monitoramento de tráfego Always On:** seus padrões de tráfego do aplicativo são monitorados 24x7, procurando indicadores de ataques DDoS. A mitigação é realizada quando as políticas de proteção são excedidas.
- **Proteção imediata:** a configuração simplificada protege de maneira imediata todos os recursos em uma rede virtual assim que a Proteção contra DDoS Standard é habilitada. Nenhuma intervenção ou definição do usuário é necessária: a Proteção contra DDoS Standard mitiga de maneira instantânea e automática o ataque assim que ele é detectado.
- **Ajuste adaptável:** a criação de perfil de tráfego inteligente aprende sobre o tráfego do seu aplicativo ao longo do tempo e seleciona e atualiza o perfil é o mais adequado para seu serviço. O perfil se ajusta conforme o tráfego é alterado ao longo do tempo.
- **Proteção da L3 à L7 com um Gateway de Aplicativo:** recursos do WAF do Gateway de Aplicativo fornecendo Proteção contra DDoS de pilha completa.
- **Escala de mitigação ampla:** mais de 60 tipos de ataques diferentes podem ser mitigados com capacidade global para proteger contra os maiores ataques de DDoS conhecidos. 
- **Métricas de ataque:** métricas resumidas de cada ataque são acessíveis por meio do Azure Monitor.
- **Alerta de ataque:** alertas podem ser configurados no início, durante e após a interrupção de um ataque, usando métricas de ataque internas. Os alertas se integram a seu software operacional como OMS, Splunk, Armazenamento do Azure, Email e Portal do Azure.
- **Garantia de custo:** créditos de serviço de expansão de aplicativo para ataques de DDoS documentados.

## <a name="ddos-protection-standard-mitigation"></a>Mitigação da Proteção contra DDoS do Azure Standard

O serviço de Proteção contra DDoS da Microsoft monitora a utilização de tráfego real e compara-a constantemente aos limites definidos na Política de DDoS. Quando esse limite de tráfego for excedido, a mitigação de DDoS será iniciada automaticamente. Quando o tráfego retorna para baixo do limite, a mitigação é removida.

Durante a mitigação, o tráfego para o recurso protegido é redirecionado pelo serviço de Proteção contra DDoS e diversas verificações são executadas. Geralmente, essas verificações desempenham a função a seguir:

- Assegurar que os pacotes estejam em conformidade com as especificações de Internet e não sejam malformados.
- Interagir com o cliente para determinar se ele é potencialmente um pacote adulterado (por exemplo: por Aut. SYN ou Cookie SYN, ou então removendo um pacote para que a fonte o retransmita).
- Estabeleça limites de taxa para pacotes se nenhum outro método de imposição puder ser executado.

Os blocos de Proteção contra DDoS atacam o tráfego e encaminham o tráfego restante para o destino pretendido. Em poucos minutos de detecção de ataque, você será notificado pelo uso de métricas no Azure Monitor. Configurando o registro em log na telemetria da Proteção contra DDoS Standard, você pode gravar os logs de opções disponíveis para análise futura. Atualmente, dados de métrica no Azure Monitor para a Proteção contra DDoS Standard são mantidos por 30 dias.

Não é aconselhável que clientes simulem seus próprios ataques de DDoS. Em vez disso, os clientes podem usar o canal de suporte para solicitar que uma simulação de ataque de DDoS seja realizada pela Rede do Azure. Um engenheiro entrará em contato com você para organizar os detalhes do ataque de DDoS (portas, protocolos, IPs de destino) e combinar um horário para agendar o teste.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como gerenciar a Proteção contra DDoS Standard usando o [Azure PowerShell](ddos-protection-manage-ps.md) ou o [Portal do Azure](ddos-protection-manage-portal.md).