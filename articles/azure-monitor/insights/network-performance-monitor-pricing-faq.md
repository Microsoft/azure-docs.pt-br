---
title: Perguntas frequentes sobre preços para o Monitor de Desempenho de Rede do Azure | Microsoft Docs
description: Perguntas frequentes - Monitor de Desempenho de Rede do Azure
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: agummadi
ms.openlocfilehash: 68e84d8ac82d5cdd52bf92df858730315514c17c
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612849"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Alterações de preços para o Monitor de Desempenho de Rede do Azure

Ouvimos seus comentários e introduzimos recentemente uma [nova experiência de preços](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) para vários serviços de monitoramento no Azure. Este artigo captura as alterações de preços relacionadas ao [Monitor de Desempenho de Rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) do Azure em qualquer formato de pergunta e resposta fácil de ler.

O Monitor de Desempenho de Rede consiste em três componentes:
* [Monitorar Desempenho](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitor de Ponto de Extremidade de Serviço](https://docs.microsoft.com/azure/networking/network-monitoring-overview)
* [ExpressRoute Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

As seções a seguir explicam as alterações de preços para os componentes do NPM.

## <a name="performance-monitor"></a>Monitorar Desempenho

**Como o uso do Monitor de Desempenho era cobrado no modelo antigo?**

A cobrança do NPM era baseada no uso e consumo de dois componentes:
* **Nós**: Todas as transações sintéticas se originam e terminam nos nós. Os nós também são conhecidos como agentes ou Microsoft Monitoring Agents.
* **Data**: Os resultados de vários testes de rede são armazenados no espaço de trabalho do Log Analytics.

No modelo antigo, a cobrança era calculada com base no número de nós e no volume de dados gerados. 

**Como o uso do Monitor de Desempenho é cobrado no novo modelo?**

O recurso de Monitor de Desempenho no NPM agora é cobrado com base em uma combinação de: 

* links de sub-rede monitorados
* Volume de dados

**O que é um link de sub-rede?**

O Monitor de Desempenho monitora a conectividade entre dois ou mais locais na rede. A conexão entre um grupo de nós ou agentes em uma sub-rede e um grupo de nós em outra sub-rede é chamado de link de sub-rede.

**Tenho duas sub-redes (A e B) e vários agentes em cada sub-rede. O Monitor de Desempenho monitora a conectividade de todos os agentes na sub-rede A para todos os agentes na sub-rede B. Serei cobrado com base no número de conexões entre as sub-redes?**

Nº Para fins de cobrança, todas as conexões da sub-rede A para a sub-rede B são agrupadas em um link de sub-rede. Você será cobrado por uma única conexão. O Monitor de Desempenho continua monitorando a conectividade entre vários agentes em cada sub-rede.

**Quais são os custos de monitoramento de um link de sub-rede?**

Para o custo de monitoramento de um link de sub-rede único para o mês inteiro, consulte a seção [Ping de Malha](https://azure.microsoft.com/pricing/details/network-watcher/).

**Quais são as cobranças para os dados que o Monitor de Desempenho gera?**

A cobrança por ingestão (upload de dados ao espaço de trabalho do Log Analytics no Azure Monitor, processamento e indexação) está disponível na [página de preços](https://azure.microsoft.com/pricing/details/log-analytics/) para Log Analytics, na seção ingestão de dados. A cobrança por retenção de dados (ou seja, os dados retidos por opção do cliente, além do primeiro mês) também está disponível na [página de preços](https://azure.microsoft.com/pricing/details/log-analytics/), na seção Retenção de Dados.


## <a name="expressroute-monitor"></a>ExpressRoute Monitor

**Quais são as cobranças pelo uso do ExpressRoute Monitor?**

As cobranças pelo ExpressRoute Monitor são feitas com base no volume de dados gerados durante o monitoramento. Para mais informações, consulte “Quais são as cobranças para os dados que o Monitor de Desempenho gera?”

**Eu uso o Monitor do ExpressRoute para monitorar vários circuitos do ExpressRoute. Sou cobrado com base no número de circuitos que está sendo monitorado?**

Você não será cobrado com base no número de circuitos ou no tipo de emparelhamento (por exemplo, emparelhamento privado, emparelhamento da Microsoft). Você é cobrado com base no volume de dados, conforme explicado anteriormente.

**O que é o volume de dados gerado quando o ExpressRoute monitora um único circuito?**

O volume de dados gerados por mês, quando o ExpressRoute monitora uma conexão de emparelhamento privado de monitoramento é conforme segue:

|Percentil      |Dados/mês (MB)|
| :---:          |           ---:|
|50<sup>º</sup> |            192|
|60<sup>º</sup> |            256|
|70<sup>º</sup> |            360|
|80<sup>º</sup> |            498|
|90<sup>º</sup> |            870|
|95<sup>º</sup> |           1560|


De acordo com esta tabela, os clientes do 50º percentil pagam por 192 MB de dados. A USD 2,30/GB para o primeiro mês, o custo incorrido para monitoramento de um circuito é de USD 0,43 (= 192 * 2,30 / 1024).

**Quais são alguns dos motivos para as variações do volume de dados?**

O volume de dados de monitoramento gerados depende de vários fatores, como:
* Número de agentes. A precisão de isolamento de falhas aumenta com o aumento do número de agentes.
* Número de saltos na rede.
* Número de caminhos entre a origem e o destino.

Os clientes nos percentis mais altos (na tabela anterior) geralmente monitoram seus circuitos a partir de várias posições de vantagem em sua rede local. Vários agentes também são posicionados mais profundamente na rede, mais distante do roteador de borda de provedor de serviços. Os agentes geralmente são colocados em vários sites de usuário, branches e racks nos data centers.

## <a name="service-endpoint-monitor"></a>Monitor de Ponto de Extremidade de Serviço

**Quais são as cobranças pelo uso do Monitor de Ponto de Extremidade de Serviço?**

As cobranças pelo uso do Monitor de Ponto de Extremidade de Serviço são calculadas com base em:
* Número de conexões
* Volume de dados

**O que é uma conexão?**

Uma conexão é um teste de acessibilidade para um ponto de extremidade (serviço de rede ou URL) de um único agente para o mês inteiro. Por exemplo, o monitoramento de uma conexão para bing.com de três agentes constitui três conexões.

**Quais são os custos para o Monitor de Ponto de Extremidade de Serviço?**

Consulte a seção [Monitoramento de conexão](https://azure.microsoft.com/pricing/details/network-watcher/) para o custo de monitoramento de um ponto de extremidade para o mês inteiro. A cobrança dos dados está disponível na [página de preços](https://azure.microsoft.com/pricing/details/log-analytics/) de Log Analytics, na seção Ingestão de Dados.

## <a name="references"></a>Referências

[Perguntas frequentes sobre o preço do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/): A seção de perguntas frequentes traz informações sobre a camada gratuita, por preços de nós e outros detalhes de preços.

