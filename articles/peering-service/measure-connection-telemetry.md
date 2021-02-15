---
title: 'Serviço de Emparelhamento do Azure: Como medir a telemetria de conexão '
description: Neste tutorial, saiba como medir a telemetria de conexão.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service.
ms.openlocfilehash: abbe69ebbaed56ed416f85fafa7b77a1740fabe7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022489"
---
# <a name="tutorial-measure-peering-service-connection-telemetry"></a>Tutorial: Medir telemetria da conexão do Serviço de Emparelhamento

 Neste tutorial, você aprenderá a medir a telemetria para suas conexões de Serviço de Emparelhamento.
 
 A telemetria de conexão fornece informações coletadas para a conectividade entre o local do cliente e a rede da Microsoft. Neste artigo, você aprenderá a exibir o relatório de latência para uma conexão específica do Serviço de Emparelhamento do Azure. 

Para medir a telemetria de conexão de Serviço de Emparelhamento, você deve registrar uma conexão de Serviço de Emparelhamento no portal do Azure. Para saber como registrar uma conexão, confira [Registrar uma conexão de Serviço de Emparelhamento – portal do Azure](azure-portal.md).


## <a name="view-a-latency-report"></a>Exibir um relatório de latência

Para exibir um relatório de latência para uma conexão de Serviço de Emparelhamento específico, siga estas etapas.

1. Selecione **Todos os recursos** no painel esquerdo e selecione a conexão de Serviço de Emparelhamento. Em seguida, selecione **Abrir** em **Prefixos**. 

   ![Selecione a conexão do Serviço de Emparelhamento](./media/peering-service-measure/peering-service-measure-menu.png)

2. Uma página de relatório de latência para todos os prefixos associados à conexão de Serviço de Emparelhamento é exibida. 

      ![Página de relatório de latência](./media/peering-service-measure/peering-service-latency-report.png)

3. Por padrão, o relatório é atualizado para cada hora exibida nesta página. Para exibir o relatório de diferentes linhas do tempo, escolha a opção apropriada em **Mostrar dados para o último**. 

4. Para exibir eventos para um prefixo específico, selecione o nome do prefixo e selecione **Eventos de Prefixo** no painel esquerdo. Os eventos capturados são exibidos.


   ![Eventos de prefixo](./media/peering-service-measure/peering-service-prefix-event.png)

 Alguns dos possíveis eventos que são capturados na lista **Eventos de Prefixo** são mostrados aqui.

| **Eventos de prefixo** | **Tipo de evento**|**Justificativa**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |Informações|O comunicado de prefixo foi recebido|
| PrefixWithdrawalEvent|Aviso| A retirada de prefixo foi recebida |
| PrefixBackupRouteAnnouncementEvent |Informações|O comunicado de rota de backup de prefixo foi recebido |
| PrefixBackupRouteWithdrawalEvent|Aviso|A retirada de rota de backup de prefixo foi recebida |
| PrefixActivePath |Informações| Rota ativa do prefixo atual   |
| PrefixBackupPath | Informações|Rota de backup do prefixo atual   |
| PrefixOriginAsChangeEvent|Crítico| Prefixo exato recebido com um número de sistema autônomo de origem diferente (para a rota ativa)| 
| PrefixBackupRouteOriginAsChangeEvent  | Erro|Prefixo recebido com um número de sistema autônomo de origem diferente (para a rota de backup)  |

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a conexão do Serviço de Emparelhamento, confira [Conexão do Serviço de Emparelhamento](connection.md).
- Para saber mais sobre telemetria de conexão do Serviço de Emparelhamento, confira [Telemetria de conexão do Serviço de Emparelhamento](connection-telemetry.md).