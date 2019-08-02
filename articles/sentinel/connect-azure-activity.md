---
title: Conectar-se a dados de atividade do Azure para visualização do Azure Sentinel | Microsoft Docs
description: Saiba como se conectar a dados de atividade do Azure para Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: e329c8efd9b0e89f5f5eae41952cda9a45a95969
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620664"
---
# <a name="connect-data-from-azure-activity-log"></a>Conectar-se a dados do log de atividades do Azure

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

É possível transmitir logs do [log de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md) em Azure Sentinel com um único clique. O log de atividades é um log de assinatura que fornece informações sobre eventos de nível de assinatura que ocorreram no Azure. Isso inclui um intervalo de dados, de dados operacionais do Azure Resource Manager para atualizações em eventos de Integridade do Serviço. Usando o log de atividades, você pode determinar o ' o que, quem e quando ' para qualquer operação (PUT, POST, DELETE) executada nos recursos em sua assinatura de gravação. Também é possível compreender o status da operação e outras propriedades relevantes. O log de atividades não inclui operações de leitura (GET) ou operações para recursos que usam o clássico / modelo "RDFE". 


## <a name="prerequisites"></a>Pré-requisitos

- Usuário com permissões de administrador de segurança ou de administrador global


## <a name="connect-to-azure-activity-log"></a>Conectar-se ao log de atividades do Azure

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no **log de atividades do Azure** lado a lado.

2. No painel de log de atividades do Azure, selecione as assinaturas que você deseja transmitir para o Azure Sentinel. 

3. Clique em **Conectar**.

4. Para usar o esquema relevante no Log Analytics para os alertas de atividade do Azure, pesquise **AzureActivity**.


 

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar-se o log de atividades do Azure para Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).
