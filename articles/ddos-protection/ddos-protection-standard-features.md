---
title: Recursos da proteção contra DDoS do Azure
description: Conheça os recursos de proteção contra DDoS do Azure
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 602bb98f2cdc8a96874eba8dadfa33f3267d19ac
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746551"
---
# <a name="azure-ddos-protection-standard-features"></a>Recursos da Proteção contra DDoS do Azure Standard

As seções a seguir descrevem os principais recursos do serviço de Proteção contra DDoS do Azure Standard.

## <a name="always-on-traffic-monitoring"></a>Monitoramento de tráfego AlwaysOn

A Proteção contra DDoS Standard monitora a utilização de tráfego real e compara-a constantemente com os limites definidos na Política de DDoS. Quando esse limite de tráfego for excedido, a mitigação de DDoS será iniciada automaticamente. Quando o tráfego retorna abaixo dos limites, a mitigação é interrompida.

![Mitigação padrão da proteção contra DDoS do Azure](./media/ddos-protection-overview/mitigation.png)

Durante a mitigação, o tráfego enviado para o recurso protegido é redirecionado pelo serviço de proteção contra DDoS e várias verificações são executadas, como:

- Assegure que os pacotes estejam em conformidade com as especificações de Internet e não sejam malformados.
- Interagir com o cliente para determinar se o tráfego é potencialmente um pacote adulterado (por exemplo: Aut. SYN ou Cookie SYN ou removendo um pacote para que a fonte o retransmita).
- Estabelecer limites de taxa para pacotes se nenhum outro método de imposição puder ser executado.

A proteção contra DDoS descarta o tráfego de ataque e encaminha o tráfego restante para o destino pretendido. Poucos minutos após a detecção do ataque, você é notificado usando as métricas no Azure Monitor. Configurando o registro em log na telemetria da Proteção contra DDoS Standard, você pode gravar os logs de opções disponíveis para análise futura. Os dados de métrica no Azure Monitor para a Proteção contra DDoS Standard são mantidos por 30 dias.

## <a name="adaptive-real-time-tuning"></a>Ajuste de tempo real adaptável

O serviço da Proteção contra DDoS do Azure Básica ajuda a proteger os clientes e evitar impactos a outros clientes. Por exemplo, se um serviço está provisionado para um volume típico de tráfego de entrada legítimo menor do que a *taxa de gatilho* da política da Proteção contra DDoS de toda a infraestrutura, um ataques de DDoS contra os recursos do cliente em questão pode passar despercebido. Em geral, a complexidade dos ataques recentes (por exemplo, DDoS de vários vetores) e os comportamentos específicos do aplicativo de locatários são chamados para políticas de proteção personalizadas por cliente. O serviço realiza isso usando duas informações:

- Aprendizado automático de padrões de tráfego por cliente (por IP público) para a camada 3 e 4.

- Minimizando falsos positivos, considerando que a escala do Azure permite que absorva uma quantidade significativa de tráfego.

![Diagrama de como a Proteção contra DDoS Standard funciona, com a "Política de geração" circulada](./media/ddos-best-practices/image-5.png)

## <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Telemetria, monitoramento e alertas da Proteção contra DDoS

A proteção contra DDoS Standard expõe telemetria avançada por meio de [Azure monitor](../azure-monitor/overview.md). Você pode configurar alertas para qualquer uma das métricas do Azure Monitor que a Proteção contra DDoS utiliza. Você pode integrar o registro em log com Splunk (hubs de eventos do Azure), logs de Azure Monitor e armazenamento do Azure para análise avançada por meio da interface de diagnóstico de Azure Monitor.

### <a name="ddos-mitigation-policies"></a>Políticas de mitigação de DDoS

Na portal do Azure, selecione **monitorar**  >  **métricas**. No painel **Métricas**, selecione o grupo de recursos, o tipo de recurso do **Endereço IP Público** e o endereço IP público do Azure. As métricas de DDoS estão visíveis no painel de **Métricas disponíveis**.

A Proteção contra DDoS Standard aplica três políticas de mitigação ajustadas automaticamente (TCP SYN, TCP e UDP) em cada endereço IP público do recurso protegido, na rede virtual que tem o DDoS habilitado. Você pode exibir os limites da política selecionando os pacotes de entrada da métrica **para disparar a mitigação de DDoS**.

![Métricas disponíveis e gráfico de métricas](./media/ddos-best-practices/image-7.png)

Os limites da política são configurados automaticamente por meio da criação de perfil de tráfego de rede baseada em aprendizado de máquina. A mitigação de DDoS ocorre para um endereço IP sob ataque somente quando o limite da política for excedido.

### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Métrica para um endereço IP sob ataque de DDoS

Se o endereço IP público estiver sob ataque, o valor da métrica **Sob ataque de DDoS ou não** mudará para 1 conforme a Proteção contra DDoS executa a mitigação do tráfego do ataque.

![Gráfico e métrica "Sob ataque DDoS ou não"](./media/ddos-best-practices/image-8.png)

É recomendável configurar um alerta nessa métrica. Você será notificado quando houver uma mitigação de DDoS ativa executada no seu endereço IP público.

Para ver mais informações, consulte [Gerenciar a Proteção contra DDoS do Azure padrão usando o Portal do Azure](manage-ddos-protection.md).

## <a name="web-application-firewall-for-resource-attacks"></a>Firewall do aplicativo Web para ataques de recurso

Especificamente para ataques de recursos na camada de aplicativo, você deve configurar o WAF (Firewall do Aplicativo Web) para ajudar a proteger os aplicativos Web. O WAF inspeciona o tráfego de entrada da Web para bloquear injeções de SQL, scripts intersites, DDoS e outros ataques da camada 7. O Azure fornece o [WAF como um recurso do Gateway de Aplicativo](../web-application-firewall/ag/ag-overview.md) para proteção centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns. Há outras ofertas de WAF disponíveis de parceiros do Azure que podem ser mais adequadas às suas necessidades no [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Mesmo os firewalls do aplicativo Web estão suscetíveis a ataques de esgotamento volumétrico e de estado. Nós recomendamos habilitar a Proteção contra DDoS Standard em uma rede virtual WAF para ajudar a protegê-la contra ataques volumétricos e de protocolo. Para obter mais informações, consulte a seção [Arquiteturas de referência da Proteção contra DDoS](ddos-protection-reference-architectures.md).

## <a name="protection-planning"></a>Planejamento de proteção

Planejamento e preparação são cruciais para entender como será o desempenho de um sistema durante um ataque de DDoS. Criar um plano de resposta de gerenciamento de incidentes faz parte desse esforço.

Se tiver a Proteção contra DDoS Standard, certifique-se de que esteja habilitada na rede virtual dos pontos de extremidade voltados para a Internet. Configurar alertas de DDoS ajuda a manter um olhar constante sobre qualquer possível ataque à sua infraestrutura. 

Monitore seus aplicativos de forma independente. Entenda o comportamento normal do aplicativo. Prepare-se para agir caso o aplicativo não esteja se comportando conforme o esperado durante um ataque de DDoS. 

Saiba como seus serviços responderão a um ataque [testando por meio de simulações](test-through-simulations.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um plano de proteção contra DDoS](manage-ddos-protection.md).
