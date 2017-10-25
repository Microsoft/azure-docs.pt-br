---
title: "Integridade preditiva do veículo e hábitos de condução - Azure | Microsoft Docs"
description: "Use os recursos do Cortana Intelligence para obter informações preditivas em tempo real sobre a integridade do veículo e hábitos de condução."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: af8b3d5bf891c93c30a05c5f02d86639a466dde5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Manual da solução de análise de telemetria do veículo
Este **menu** fornece links para os capítulos deste manual. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Visão geral
Os supercomputadores foram retirados do laboratório e agora estão estacionados em nossa garagem! Esses automóveis de ponta contêm uma infinidade de sensores, dando-lhes a capacidade de acompanhar e monitorar milhões de eventos por segundo. Esperamos que até 2020, a maioria desses carros esteja conectada à Internet. Imagine-se explorando essa riqueza de dados para fornecer maior segurança, confiabilidade e uma experiência melhor de condução! A Microsoft tornou esse sonho uma realidade por meio do Cortana Intelligence.

O Cortana Intelligence da Microsoft é um pacote de análise avançada e de Big Data totalmente gerenciado que o habilita a transformar seus dados em ação inteligente. Gostaríamos de apresentar o Modelo de Solução de Análise de Telemetria do Veículo do Cortana Intelligence. Esta solução demonstra como concessionárias, fabricantes de automóveis e seguradoras podem usar os recursos do Cortana Intelligence para obter informações preditivas em tempo real sobre a integridade do veículo e hábitos de condução. 

A solução é implementada como um [padrão de arquitetura lambda](https://en.wikipedia.org/wiki/Lambda_architecture) , mostrando o potencial completo da plataforma do Cortana Intelligence para processamento em tempo real e em lote. A solução: 

* fornece um simulador de Telemática do Veículo
* aproveita os Hubs De eventos para a ingestão de milhões de eventos de telemetria simulados do veículo no Azure 
* usa o Stream Analytics para obter informações em tempo real sobre a integridade do veículo
* mantém os dados em um armazenamento de longo prazo para análise em e lote mais detalhada. 
* aproveita o Machine Learning para a detecção de anomalias em tempo real e o processamento em lote para obter informações preditivas.
* aproveita o HDInsight para transformar dados em escala e o Data Factory para lidar com a orquestração, agendamento, gerenciamento de recursos e monitoramento do pipeline de processamento em lote 
* fornece essa solução a um painel avançado para os dados em tempo real e as visualizações da análise preditiva usando Power BI

## <a name="architecture"></a>Arquitetura
![Diagrama de arquitetura da solução](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*Figura 1 – Arquitetura da Solução de Análise da Telemetria do Veículo*

Essa solução inclui os seguintes **componentes do Cortana Intelligence** e apresenta sua integração de ponta a ponta:

* **Hubs de Eventos** para a ingestão de milhões de eventos de telemetria do veículo no Azure.
* **Stream Analytics** para obter informações em tempo real sobre a integridade do veículo e persistir esses dados no armazenamento de longo prazo para uma análise de lote mais avançada.
* **Machine Learning** para detecção de anomalias em tempo real e processamento em lote para obter previsões.
* **HDInsight** é utilizado para transformar dados em escala
* **Data Factory** lida com a orquestração, o agendamento, o gerenciamento de recursos e o monitoramento do pipeline de processamento em lote.
* **PowerBI** fornece essa solução a um painel avançado para os dados em tempo real e as visualizações da análise preditiva.

Essa solução acessa duas **fontes de dados**diferentes: 

* **Sinais de veículo e diagnóstico simulados**: um simulador de telemática do veículo emite informações de diagnóstico e sinais que correspondem ao estado do veículo e ao padrão de condução em um determinado momento. 
* **Catálogo do veículo**: um conjunto de dados de referência que contém um VIN para o mapeamento do modelo.

