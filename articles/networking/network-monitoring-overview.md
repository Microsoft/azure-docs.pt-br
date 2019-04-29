---
title: Sobre o monitoramento de rede no Azure Monitor registra em log | Microsoft Docs
description: Visão geral de soluções de monitoramento de rede, incluindo NPM, para gerenciar redes em nuvens, locais e ambientes híbridos.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 42fb5d69a1f32d669ad5191e342b3f2f880b8c98
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564739"
---
# <a name="network-monitoring-solutions"></a>Soluções de monitoramento de rede 

Azure oferece diversas soluções para monitorar seus ativos de rede. Azure tem soluções e utilidades para monitorar a conectividade de rede, a integridade de circuitos ExpressRoute e analisar o tráfego de rede na nuvem.

## <a name="network-performance-monitor-npm"></a>Monitor de Desempenho de Rede (NPM)

Monitor de Desempenho de Rede (NPM) é um conjunto de recursos, cada um deles é voltado para o monitoramento da integridade de sua rede, a conectividade de rede para seus aplicativos e fornece informações sobre o desempenho da rede. NPM é baseado em nuvem e fornece uma solução de monitoramento de rede híbrida que monitora a conectividade entre:
 
* Implantações na nuvem e pontos locais
* Vários data centers e filiais
* Microsserviços/aplicativos multiníveis críticos à missão
* Aplicativos baseado em web e locais de usuários (HTTP/HTTPs) 

O Monitor de Desempenho, Monitor do ExpressRoute e Monitor da Conectividade de Serviço estão monitorando os recursos dentro do NPM e são descritos abaixo.

## <a name="performance-monitor"></a>Monitorar Desempenho

Monitor de Desempenho é parte do NPM e está monitorando a rede para ambientes locais, híbridos e na nuvem. Você pode monitorar a conectividade de rede em escritórios e filiais remotas, locais de armazenamento, data centers e nuvens. Você pode detectar problemas de rede antes de seus usuários reclamarem. As principais vantagens são:

* Monitorar perda e latência entre várias sub-redes e definir alertas
* Monitorar todos os caminhos (incluindo caminhos redundantes) na rede
* Solucionar problemas de rede transitórios e pontuais, que são difíceis de replicar
* Determinar um segmento específico na rede, que é responsável pelo desempenho degradado
* Monitorar a integridade da rede, sem a necessidade de SNMP

![Mapa de topologia NPM](./media/network-monitoring-overview/npm-topology-map.png) 

Para obter mais informações, consulte os seguintes artigos:

* [Configurar uma solução de Monitor de desempenho de rede nos logs do Azure Monitor](../azure-monitor/insights/network-performance-monitor.md) 
* [Casos de uso](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  Atualizações de produto: [Fevereiro de 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [de agosto de 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute Monitor

NPM para o ExpressRoute oferece monitoramento abrangente do ExpressRoute para conexões de emparelhamento da Microsoft e conexões de emparelhamento privadas do Azure. Você pode monitorar desempenho e conectividade E2E entre as filiais e o Azure por meio do ExpressRoute. Os principais recursos são:

* Detecção automática de circuitos ER associados à sua assinatura
* Detecção da topologia de rede do local para seus aplicativos de nuvem
* Planejamento de capacidade, análise de utilização de largura de banda
* Monitoramento e alertas em caminhos primários e secundários
* Monitoramento de conectividade para serviços do Azure como Office 365, Dynamics 365,... via ExpressRoute
* Detectar degradação de conectividade para VNets

![Mapa geográfico mostrando o tráfego entre as regiões](./media/network-monitoring-overview/expressroute-topology-map.png) 

Para obter mais informações, consulte os seguintes artigos:

* [Configurar o Monitor de Desempenho de Rede para ExpressRoute](../expressroute/how-to-npm.md)
* [Postagem no blog](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Monitor de Conectividade de Serviço

Com o monitoramento da Conectividade de Serviço, você pode testar a acessibilidade de aplicativos e detectar gargalos de desempenho entre locais, redes de transporte e data centers de nuvem/privada.

* Monitorar a conectividade de rede de ponta a ponta para aplicativos
* Correlacionar a entrega de aplicativos com o desempenho da rede, detectar o local exato de degradação ao longo do caminho entre o usuário e o aplicativo
* Testar a acessibilidade de aplicativo de vários locais do usuário em todo o mundo
* Determinar a rede latência e perda de pacotes para a linha de negócios e aplicativos SaaS
* Determinar os pontos de acesso na rede, que podem estar causando o desempenho insatisfatório do aplicativo
* Monitorar o alcance a aplicativos do Office 365, usando testes internos para o Microsoft Office 365, Dynamics 365, Skype for Business e outros serviços da Microsoft

Para obter mais informações, consulte os seguintes artigos:

* [Configurar o Monitor de Desempenho de Rede para monitorar pontos de extremidade do serviço](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Postagem no blog](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Análise de Tráfego
A Análise de Tráfego é uma solução baseada em nuvem, que oferece visibilidade sobre atividade de usuário e aplicativo nas redes em nuvem. Os logs de fluxo do NSG são analisados para fornecer informações sobre:

* Fluxos de tráfego em suas redes entre o Azure e a Internet, regiões de nuvem pública, VNETs e sub-redes
* Aplicativos e protocolos em sua rede, sem a necessidade de sniffers (farejadores) ou dispositivos coletores de fluxo dedicados
* Principais talkers, aplicativos chatty, conversas de VM na nuvem, pontos de acesso de tráfego
* Origens e destinos do tráfego entre VNETs, inter-relacionamentos entre serviços críticos de negócios e aplicativos
* Segurança – tráfego malicioso, portas abertas para a Internet, aplicativos ou VMs que tentam acessar a Internet…
* Utilização de capacidade – ajuda a eliminar problemas de provisionamento excessivo ou subutilização, monitorando as tendências de utilização de gateways de VPN e outros serviços

A Análise de Tráfego fornece informações acionáveis que ajudam a auditar a atividade de rede de sua organização, proteger aplicativos e dados, otimizar o desempenho da carga de trabalho e permanecer em conformidade.

![Mapa geográfico mostrando o tráfego entre as regiões](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Links relacionados:
* [Postagem no blog](https://aka.ms/trafficanalytics), [Documentação](https://aka.ms/trafficanalyticsdocs), [Perguntas frequentes](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>Análise de DNS
Compilada para Administradores de DNS, essa solução coleta, analisa e correlaciona logs de DNS para fornecer insights relacionados à segurança, operações e ao desempenho.  Algumas das capacidades são:

* Identificar clientes que tentam resolver domínios mal-intencionados
* Identificar registros de recursos obsoletos
* Visibilidade em nomes de domínio consultados com frequência e clientes DNS loquazes
* Visibilidade na carga da solicitação nos servidores DNS
* Monitoramento de falhas no registro DNS dinâmico

![Painel de Análise de DNS](./media/network-monitoring-overview/dns-analytics-overview.png) 

Links relacionados:
* [Postagem no blog](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [Documentação](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Diversos

* [Novos preços](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
