---
title: "Como adicionar uma origem do evento do Hub de Eventos ao ambiente de Análise de Séries Temporais do Azure | Microsoft Docs"
description: "Este tutorial aborda como adicionar uma origem do evento que está conectada a um Hub de Eventos ao ambiente de Análise de Séries Temporais"
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
ms.openlocfilehash: f6a993b3858cfb94dd9795f5e55f15fa6ec7dcb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-an-event-hub-event-source"></a>Como adicionar uma origem do evento do Hub de Eventos

Este tutorial explica como usar o portal do Azure para adicionar uma origem do evento que lê de um Hub de Eventos ao ambiente de Análise de Séries Temporais.

## <a name="prerequisites"></a>Pré-requisitos

Você criou um Hub de Eventos e está gravando eventos nele. Para saber mais sobre Hubs de Eventos, visite <https://azure.microsoft.com/services/event-hubs/>

> [Grupos de Consumidores] Cada origem do evento da Análise de Séries Temporais precisa ter seu próprio grupo de consumidores dedicado que não esteja compartilhado com outros consumidores. Se vários leitores consumirem eventos do mesmo grupo de consumidores, provavelmente, todos os leitores verão falhas. Observe que também há um limite de 20 grupos de consumidores por Hub de Eventos. Para obter detalhes, confira o [Guia de programação dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md).

## <a name="choose-an-import-option"></a>Escolher uma opção de importação

As configurações para a origem do evento podem ser inseridas manualmente ou um hub de eventos pode ser selecionado nos hubs de eventos que estão disponíveis para você.
No seletor **Opção de Importação**, escolha uma das seguintes opções:

* Fornecer configurações de Hub de Eventos manualmente
* Usar o Hub de Eventos nas assinaturas disponíveis

### <a name="select-an-available-event-hub"></a>Selecionar um Hub de Eventos disponível

A seguinte tabela explica cada opção na guia Nova Origem do Evento com sua descrição ao selecionar um Hub de Eventos disponível como uma origem do evento:

| Nome da Propriedade | Descrição |
| --- | --- |
| Nome da origem do evento | O nome da sua origem do evento. Esse nome deve ser exclusivo no ambiente de Análise de Séries Temporais.
| Fonte | Escolha **Hub de Eventos** para criar uma origem do evento do Hub de Eventos.
| ID da assinatura | Selecione a assinatura na qual esse hub de eventos foi criado.
| Namespace do Barramento de Serviço | Selecione o namespace do Barramento de Serviço que contém o Hub de Eventos.
| Nome do Hub de Eventos | Selecione o nome do Hub de Eventos.
| Nome da política do hub de eventos | Selecione a política de acesso compartilhada, que pode ser criada na guia Configurar o Hub de Eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **leitura**.
| Grupo de consumidores do hub de eventos | O Grupo de Consumidores para ler eventos do Hub de Eventos. É altamente recomendável usar um grupo de consumidores dedicado para a origem do evento.

### <a name="provide-event-hub-settings-manually"></a>Fornecer configurações de Hub de Eventos manualmente

A seguinte tabela explica cada propriedade na guia Nova Origem do Evento com sua descrição ao inserir configurações manualmente:

| Nome da Propriedade | Descrição |
| --- | --- |
| Nome da origem do evento | O nome da sua origem do evento. Esse nome deve ser exclusivo no ambiente de Análise de Séries Temporais.
| Fonte | Escolha **Hub de Eventos** para criar uma origem do evento do Hub de Eventos.
| ID da assinatura | A assinatura na qual esse hub de eventos foi criado.
| Grupo de recursos | A assinatura na qual esse hub de eventos foi criado.
| Namespace do Barramento de Serviço | Um namespace Barramento de Serviço é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo Hub de Eventos, você também criou um namespace Barramento de Serviço.
| Nome do Hub de Eventos | O nome do seu Hub de Eventos. Quando você criou o hub de eventos, também deu a ele um nome específico
| Nome da política do hub de eventos | A política de acesso compartilhada, que pode ser criada na guia Configurar o Hub de Eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **leitura**.
| Chave de política do hub de eventos | A chave de Acesso Compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. Digite aqui a chave primária ou secundária.
| Grupo de consumidores do hub de eventos | O Grupo de Consumidores para ler eventos do Hub de Eventos. É altamente recomendável usar um grupo de consumidores dedicado para a origem do evento.

## <a name="next-steps"></a>Próximas etapas

1. Adicionar uma política de acesso a dados ao seu ambiente [Definir políticas de acesso a dados](time-series-insights-data-access.md)
1. Acessar seu ambiente no [Portal de Análise de Séries Temporais](https://insights.timeseries.azure.com)