---
title: Conectar o Azure defender para IoT ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar o Azure defender (anteriormente a central de segurança do Azure) para dados de IoT para o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 67bc104434dc0db30f5973bec0979afb7480fe4c
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621372"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Conectar seus dados do Azure defender (anteriormente a central de segurança do Azure) para IoT ao Azure Sentinel 

Use o conector do defender for IoT para transmitir todos os seus eventos do defender for IoT para o Azure Sentinel. 

Essa integração permite que as organizações detectem rapidamente ataques de várias etapas que geralmente cruzam e ficam limites. Além disso, a integração do defender para IoT com os recursos de orquestração de segurança, automação e resposta (disparar) do Azure Sentinel permite a resposta e a prevenção automáticas usando guias estratégicos internos de OT. 
## <a name="prerequisites"></a>Pré-requisitos

- Permissões de **leitura** e **gravação** no espaço de trabalho no qual o Azure Sentinel é implantado
- O **defender para IOT** deve ser **habilitado** em seus hubs (s) IOT relevantes
- Você deve ter permissões de **colaborador** na **assinatura** que deseja conectar

## <a name="connect-to-defender-for-iot"></a>Conectar-se ao defender para IoT

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, selecione **defender para IOT** (ainda pode ser chamado de central de segurança do Azure para IOT) na galeria.

1. Na parte inferior do painel direito, clique em **abrir página do conector**. 

1. Clique em **conectar**, ao lado de cada assinatura do Hub IOT cujos alertas e alertas de dispositivo você deseja transmitir para o Azure Sentinel. 
    - Você receberá uma mensagem de erro se o defender para IoT não estiver habilitado em pelo menos um hub IoT dentro de uma assinatura. Habilite o defender para IoT no Hub IoT para remover o erro.

1. Você pode decidir se deseja que os alertas do defender para IoT gerem incidentes automaticamente no Azure Sentinel. Em **criar incidentes**, selecione **habilitar** para habilitar a regra de análise padrão para criar automaticamente incidentes a partir dos alertas gerados. Essa regra pode ser alterada ou editada em  >  **regras ativas** de análise.

> [!NOTE]
> Pode levar 10 segundos ou mais para que a lista de **assinaturas** seja atualizada depois de fazer alterações na conexão. 

## <a name="log-analytics-alert-view"></a>Log Analytics exibição de alerta

Para usar o esquema relevante no Log Analytics para exibir os alertas do defender para IoT:

1. Abra **logs**  >  **SecurityInsights**  >  **SecurityAlert** ou pesquise por **SecurityAlert**. 

2. Filtre para ver somente os alertas gerados pelo defender for IoT usando o seguinte filtro kql:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Notas de serviço

Depois de conectar uma **assinatura**, os dados do Hub estarão disponíveis no Azure Sentinel aproximadamente 15 minutos mais tarde.


## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o defender para IoT ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
