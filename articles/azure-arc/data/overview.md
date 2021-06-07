---
title: O que são serviços de dados habilitados para Azure Arc
description: Apresenta os serviços de dados habilitados para Azure Arc
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: overview
ms.openlocfilehash: ef16a4c0b6ea40fb4934307916f1fe79ccea72f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97609132"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>O que são os serviços de dados habilitados para Azure Arc (versão prévia)?

O Azure Arc possibilita a execução de serviços de dados do Azure no local, na borda e em nuvens públicas usando o Kubernetes e a infraestrutura de sua escolha.

Atualmente, os seguintes serviços de dados habilitados para Azure Arc estão disponíveis em versão prévia:

- Instância Gerenciada de SQL
- Hiperescala do PostgreSQL

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>Sempre atual

Os serviços de dados habilitados para Azure Arc, como a instância gerenciada de SQL e a Hiperescala do PostgreSQL habilitadas para Azure Arc recebem atualizações com frequência, incluindo patches de manutenção e novos recursos, de modo semelhante à experiência no Azure. As atualizações do Registro de Contêiner da Microsoft são fornecidas a você, e as cadências de implantação são definidas de acordo com as suas políticas. Dessa forma, os bancos de dados locais podem permanecer atualizados, garantindo que você mantenha o controle. Como os serviços de dados habilitados para Azure Arc são um serviço de assinatura, você deixará de enfrentar situações de fim do suporte para seus bancos de dados.

## <a name="elastic-scale"></a>Escala elástica

A elasticidade semelhante à nuvem no local permite que você aumente ou diminua seus bancos de dados de maneira dinâmica como ocorre no Azure, com base na capacidade disponível da sua infraestrutura. Essa funcionalidade pode atender a cenários de intermitência que têm necessidades voláteis, incluindo cenários que exigem a ingestão e a consulta de dados em tempo real, em qualquer escala, com um tempo de resposta de subsegundos. Além disso, você pode escalar horizontalmente instâncias de banco de dados usando a opção de implantação exclusiva de hiperescala da Hiperescala do Banco de Dados do Azure para PostgreSQL. Essa funcionalidade fornece às cargas de trabalho de dados um aumento extra na otimização da capacidade, usando leituras e gravações de *expansão* exclusivas.

## <a name="self-service-provisioning"></a>Provisionamento de autoatendimento

O Azure Arc também oferece outros benefícios de nuvem, como implantação rápida e automação em escala. Graças à orquestração baseada em Kubernetes, você pode implantar um banco de dados em segundos usando ferramentas de GUI ou CLI.

## <a name="unified-management"></a>Gerenciamento unificado

Usando ferramentas já conhecidas, como o portal do Azure, o Azure Data Studio e a [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)], agora você pode obter uma exibição unificada de todos os seus ativos de dados implantados com o Azure Arc. Você pode ver e gerenciar uma variedade de bancos de dados relacionais no seu ambiente e no Azure, além de obter logs e telemetria das APIs do Kubernetes para analisar a integridade e a capacidade da infraestrutura subjacente. Além de ter a análise de logs e o monitoramento de desempenho localizados, agora você pode aproveitar o Azure Monitor para obter insights operacionais abrangentes em toda a sua propriedade.

## <a name="disconnected-scenario-support"></a>Suporte a cenários desconectados

Muitos dos serviços, como provisionamento de autoatendimento, backup/restauração automatizados e monitoramento, podem ser executados no local na sua infraestrutura com ou sem uma conexão direta com o Azure. A conexão direta com o Azure revela mais opções de integração a outros serviços do Azure, como o Azure Monitor, e a capacidade de usar as APIs do portal do Azure e do Azure Resource Manager em qualquer lugar do mundo para gerenciar seus serviços de dados habilitados para Azure Arc.

## <a name="next-steps"></a>Próximas etapas

> **Quer apenas experimentar as novidades?**  
> Obtenha uma introdução rápida com o artigo [Começar a usar o Azure Arc rapidamente](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) no AKS (Serviço de Kubernetes do Azure), no AWS EKS (Elastic Kubernetes Service), no GKE (Google Cloud Kubernetes Engine) ou em uma VM do Azure.

[Instalar as ferramentas de cliente](install-client-tools.md)

[Criar o controlador de dados do Azure Arc](create-data-controller.md) (é necessária a instalação das ferramentas de cliente primeiro)

[Criar uma instância gerenciada de SQL do Azure no Azure Arc](create-sql-managed-instance.md) (é necessária a criação de um controlador de dados do Azure Arc primeiro)

[Criar um grupo de servidores de Hiperescala do Banco de Dados do Azure para PostgreSQL no Azure Arc](create-postgresql-hyperscale-server-group.md) (é necessária a criação de um controlador de dados do Azure Arc primeiro)
