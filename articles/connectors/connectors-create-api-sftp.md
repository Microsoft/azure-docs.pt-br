---
title: "Saiba como usar o conector SFTP em seus aplicativos lógicos | Microsoft Docs"
description: "Crie aplicativos lógicos com o serviço de Aplicativo do Azure. Conecte-se à API do SFTP para enviar e receber arquivos. Você pode executar várias operações, como criar, atualizar, obter ou excluir arquivos."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 31253d8daee1581167a96a20ba8ad529a04b3e92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-sftp-connector"></a>Introdução ao conector de SFTP
Use o conector de SFTP de modo a acessar uma conta SFTP para enviar e receber arquivos. Você pode executar várias operações, como criar, atualizar, obter ou excluir arquivos.  

Para usar [qualquer conector](apis-list.md), primeiro é preciso criar um aplicativo lógico. Você pode começar [criando um aplicativo lógico agora mesmo](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-sftp"></a>Conectar-se ao SFTP
Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma *conexão* com o serviço. Uma [conexão](connectors-overview.md) fornece uma conectividade entre um aplicativo lógico e outro serviço.  

### <a name="create-a-connection-to-sftp"></a>Criar uma conexão com o SFTP
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>Usar um gatilho de SFTP
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Neste exemplo, o gatilho **SFTP – quando um arquivo é adicionado ou modificado** é usado para iniciar um fluxo de trabalho do aplicativo lógico quando um arquivo é adicionado ou modificado em um servidor SFTP. Você também adiciona uma condição que verifica o conteúdo do arquivo novo ou modificado e toma uma decisão para extrair o arquivo se seu conteúdo indicar que ele deve ser extraído antes do uso. Por fim, adicione uma ação para extrair o conteúdo de um arquivo e colocar o conteúdo extraído em uma pasta no servidor SFTP. 

Em um exemplo corporativo, você pode usar esse gatilho para monitorar uma pasta SFTP para novos arquivos que representam pedidos de clientes.  Você pode usar uma ação do conector SFTP, como **Obter conteúdo do arquivo**, para obter o conteúdo do pedido para ter mais processamento e armazenamento em um banco de dados de pedidos.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Adicionar uma condição
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>Usar uma ação de SFTP
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Veja os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/sftpconnector/).

## <a name="more-connectors"></a>Mais conectores
Volte para a [Lista de APIs](apis-list.md).