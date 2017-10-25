---
title: "Como adicionar uma origem do evento do Hub IoT ao ambiente de Análise de Séries Temporais do Azure | Microsoft Docs"
description: "Este tutorial aborda como adicionar uma origem do evento que está conectada a um Hub IoT ao ambiente de Análise de Séries Temporais"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.openlocfilehash: 3b98728ced81256d05b1bed2c92fc66c5ca61b98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-an-iot-hub-event-source"></a>Como adicionar uma origem do evento do Hub IoT

Este tutorial explica como usar o Portal do Azure para adicionar uma origem do evento que lê de um Hub IoT ao ambiente de Análise de Séries Temporais.

## <a name="prerequisites"></a>Pré-requisitos

Você criou um Hub IoT e está gravando eventos nele. Para saber mais sobre Hubs IoT, visite <https://azure.microsoft.com/services/iot-hub/>

> [Grupos de Consumidores] Cada origem do evento da Análise de Séries Temporais precisa ter seu próprio grupo de consumidores dedicado que não esteja compartilhado com outros consumidores. Se vários leitores consumirem eventos do mesmo grupo de consumidores, provavelmente, todos os leitores verão falhas. Para obter detalhes, confira o [guia de desenvolvedor do Hub IoT](../iot-hub/iot-hub-devguide.md).

## <a name="choose-an-import-option"></a>Escolher uma opção de importação

As configurações para a origem do evento podem ser inseridas manualmente ou um Hub IoT pode ser selecionado nos Hubs IoTs que estão disponíveis para você.
No seletor **Opção de Importação**, escolha uma das seguintes opções:

* Fornecer configurações de Hub IoT manualmente
* Usar o Hub IoT nas assinaturas disponíveis

### <a name="select-an-available-iot-hub"></a>Selecionar um Hub IoT disponível

A seguinte tabela explica cada opção na guia Nova Origem do Evento com sua descrição ao selecionar um Hub IoT disponível como uma origem do evento:

| Nome da Propriedade | Descrição |
| --- | --- |
| Nome da origem do evento | O nome da sua origem do evento. Esse nome deve ser exclusivo no ambiente de Análise de Séries Temporais.
| Fonte | Escolha **Hub IoT** para criar uma origem do evento do Hub IoT.
| ID da assinatura | Selecione a assinatura na qual esse Hub IoT foi criado.
| Nome do Hub IoT | Selecione o nome do Hub IoT.
| Nome da política do Hub IoT | Selecione a política de acesso compartilhado, que pode ser encontrada na guia de configurações do Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **conexão de serviço**.
| Grupo de consumidores do Hub IoT | O Grupo de Consumidores para ler eventos do Hub IoT. É altamente recomendável usar um grupo de consumidores dedicado para a origem do evento.

### <a name="provide-iot-hub-settings-manually"></a>Fornecer configurações de Hub IoT manualmente

A seguinte tabela explica cada propriedade na guia Nova Origem do Evento com sua descrição ao inserir configurações manualmente:

| Nome da Propriedade | Descrição |
| --- | --- |
| Nome da origem do evento | O nome da sua origem do evento. Esse nome deve ser exclusivo no ambiente de Análise de Séries Temporais.
| Fonte | Escolha **Hub IoT** para criar uma origem do evento do Hub IoT.
| ID da assinatura | A assinatura na qual esse Hub IoT foi criado.
| Grupo de recursos | A assinatura na qual esse Hub IoT foi criado.
| Nome do Hub IoT | O nome do seu Hub IoT. Quando você criou o Hub IoT, também deu a ele um nome específico
| Nome da política do Hub IoT | A política de acesso compartilhado, que pode ser criada na guia de configurações do Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **conexão de serviço**.
| Chave de política do Hub IoT | A chave de Acesso Compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. Digite aqui a chave primária ou secundária.
| Grupo de consumidores do Hub IoT | O Grupo de Consumidores para ler eventos do Hub IoT. É altamente recomendável usar um grupo de consumidores dedicado para a origem do evento.

## <a name="next-steps"></a>Próximas etapas

1. Adicionar uma política de acesso a dados ao seu ambiente [Definir políticas de acesso a dados](time-series-insights-data-access.md)
1. Acessar seu ambiente no [Portal de Análise de Séries Temporais](https://insights.timeseries.azure.com)