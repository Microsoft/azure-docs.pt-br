---
title: "Alertas de Métrica Quase em Tempo Real no Azure Monitor | Microsoft Docs"
description: "Os alertas de métrica quase em tempo real permitem monitorar as métricas de recurso do Azure com uma frequência de até 1 minuto."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: aeeb6c2fb87e6c19991ef243ee7230f4e8f4e251
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>Alertas de Métrica Quase em Tempo Real (Versão Prévia)
Agora, o Azure Monitor dá suporte a um novo tipo de alertas de métrica chamado Alertas de Métrica Quase em Tempo Real (Versão Prévia). Esse recurso está atualmente em visualização pública.
Esses alertas diferem dos alertas de métrica comuns de algumas maneiras

- **Melhor Latência** – os alertas de métrica quase em tempo real podem monitorar as alterações nos valores de métrica com uma frequência de até 1 minuto.
- **Mais controle sobre as condições de métrica** – os alertas de métrica quase em tempo real permitem aos usuários definir regras de alerta mais avançadas. Os alertas agora dão suporte ao monitoramento dos valores máximo, mínimo, média e total das métricas.
- **Monitoramento combinado de várias métricas** – os alertas de métrica quase em tempo real podem monitorar várias métricas (atualmente duas) com uma única regra. O alerta é disparado se ambas as métricas violam seus respectivos limites para o período especificado.
- **Sistema de notificação modular** – os alertas de métrica quase em tempo real usam [grupos de ação](monitoring-action-groups.md). Essa funcionalidade permite aos usuários criar ações de forma modular e reutilizá-las para muitas regras de alerta.

> [!NOTE]
> Atualmente, o recurso de alertas de métrica quase em tempo real está em visualização pública. A funcionalidade e a experiência do usuário está sujeita a alterações.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Para quais recursos é possível criar alertas de métrica quase em tempo real?
Lista completa dos tipos de recursos com suporte nos alertas de métrica quase em tempo real:

* Microsoft.ApiManagement/service
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Sql/servers/elasticpools
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.Timeseriesinsights
* Microsoft.CognitiveServices/accounts


## <a name="create-a-near-real-time-metric-alert"></a>Criar um alerta de métrica quase em tempo real
Atualmente, os alertas de métrica quase em tempo real podem ser criados apenas por meio do portal do Azure. O suporte à configuração de alertas de métrica quase em tempo real por meio do PowerShell, da CLI (interface de linha de comando) e da API REST do Azure Monitor estará disponível em breve.

1. No [Portal](https://portal.azure.com/), localize o recurso no qual você está interessado em monitor e selecione-o. Esse recurso deve ser um dos tipos de recursos listados na [seção anterior](#what-resources-can-i-create-near-real-time-metric-alerts-for). Também faça o mesmo para todos os tipos de recursos com suporte de forma centralizada em Monitorar>Alertas.

2. Selecione **Alertas** ou **Regras de alerta** na seção MONITORAMENTO. O texto e o ícone podem variar um pouco para recursos diferentes.
   ![Monitoramento](./media/insights-alerts-portal/AlertRulesButton.png)

3. Clique no comando **Adicionar alerta de métricas quase em tempo real (versão prévia)**. Se o comando estiver esmaecido, verifique se o recurso está selecionado no filtro.

    ![Botão Adicionar Alerta de Métricas Quase em Tempo Real](./media/monitoring-near-real-time-metric-alerts/AddNRTAlertButton.png)

4. Dê um **Nome** para o alerta de regra e escolha uma **Descrição**, que também mostre os emails de notificação.
5. Selecione a **Métrica** que você deseja monitorar e, em seguida, escolha um valor de **Condição**, **Agregação de Tempo** e **Limite** para a métrica. Opcionalmente, selecione outra **Métrica** que você deseja monitorar e, em seguida, escolha um valor de **Condição**, **Agregação de Tempo** e **Limite** para a segunda métrica. 

    ![Adicionar Alerta de Métricas Quase em Tempo Real1](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert1.png) ![Adicionar Alerta de Métricas Quase em Tempo Real2](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert2.png)
6. Escolha o **Período** durante o qual as regras de métrica devem ser atendidas antes que o alerta seja disparado. Portanto, por exemplo, se você usar o período “Acima do últimos 5 minutos” e o alerta procurar por CPU acima de 80% (NetworkIn acima de 500 MB), o alerta será disparado quando a CPU estiver consistentemente acima de 80% por 5 minutos. Após o primeiro gatilho, ele disparará novamente quando a CPU permanecer abaixo de 80% durante cinco minutos. O alerta é avaliado de acordo com a **Frequência de Avaliação**


6. Selecione uma **Gravidade** apropriada na lista suspensa.

7. Especifique se deseja usar um **Grupo de Ação** novo ou existente.

8. Se você optar por criar um **Novo** Grupo de Ação, dê ao grupo de ação um nome e um nome curto, especifique as ações (SMS, Email, Webhook) e preencha os respectivos detalhes.


8. Selecione **OK** ao concluir a criação do alerta.   

Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

## <a name="managing-near-real-time-metric-alerts"></a>Gerenciando alertas de métrica quase em tempo real
Depois de criar um alerta, você poderá selecioná-lo e:

* Exibir um gráfico mostrando o limite de métrica e os valores reais do dia anterior.
* Editar ou exclui-lo.
* **Desabilitar** ou **Habilitar** se você quiser interromper temporariamente ou continuar recebendo notificações do alerta.



