---
title: Conectar dados de atividade do Azure ao Azure Sentinel | Microsoft Docs
description: Transmita os logs de atividades do Azure para o Sentinela do Azure com um único clique. O log de atividades registra e exibe eventos de nível de assinatura no Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 88f363406ec40dfa439a52ad351501d1c64dce95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564486"
---
# <a name="connect-data-from-azure-activity-log"></a>Conectar dados do log de atividades do Azure

Você pode transmitir logs do [log de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md) para o Azure Sentinel com um único clique. O log de atividades é um log de assinatura que registra e exibe eventos de nível de assinatura no Azure, de Azure Resource Manager dados operacionais a atualizações em eventos de integridade do serviço. Usando o log de atividades, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) executada nos recursos em sua assinatura. Você também pode aprender o status da operação e outras propriedades relevantes. O log de atividades não inclui operações de leitura (GET) ou operações para recursos que usam o modelo clássico/"RDFE". 

## <a name="prerequisites"></a>Pré-requisitos

- Seu usuário deve ter permissões de colaborador para o espaço de trabalho Log Analytics.
- Seu usuário deve ter permissões de leitor para qualquer assinatura cujos logs você deseja transmitir para o Azure Sentinel.

## <a name="set-up-the-azure-activity-connector"></a>Configurar o conector de atividades do Azure

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**. Na lista de conectores, clique em **atividade do Azure**e, em seguida, no botão **abrir página do conector** no canto inferior direito.

2. Na guia **instruções** , clique no link **Configurar Logs de atividades do Azure >** .

3. No painel **log de atividades do Azure** , selecione as assinaturas cujos logs você deseja transmitir para o Azure Sentinel. 

4. No painel assinatura que é aberto à direita, clique em **conectar**.

5. Para usar o esquema relevante no Log Analytics para alertas de atividade do Azure, digite `AzureActivity` na janela de consulta.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o log de atividades do Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a detectar ameaças com o Azure Sentinel, usando regras [internas](tutorial-detect-threats-built-in.md) ou [personalizadas](tutorial-detect-threats-custom.md) .
