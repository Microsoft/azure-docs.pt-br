---
title: Conectar dados de BIG-IP do F5 ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados de BIG-IP F5 ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e895640daeb3fbbba2f2708969ee4cf856bf1033
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621174"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Conectar seu dispositivo de BIG-IP F5 

O conector de BIG-IP F5 permite que você conecte facilmente todos os seus logs de BIG-IP do F5 com o Azure Sentinel, para exibir pastas de trabalho, criar alertas personalizados e melhorar a investigação. Isso lhe dá mais informações sobre a rede da sua organização e aprimora seus recursos de operação de segurança. A integração entre F5 BIG-IP e o Azure Sentinel usa a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-f5-big-ip"></a>Configurar e conectar F5 BIG-IP 

F5 BIG-IP pode integrar e exportar logs diretamente para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione **F5 Big-IP** e **abra a página conector**. 
1. Para conectar seu F5 BIG-IP, você precisa postar uma declaração JSON no ponto de extremidade de API do sistema. Para obter instruções sobre como fazer isso, consulte [integrando o Big-IP do F5 com o Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. Na página do conector F5 BIG-IP, copie a ID do espaço de trabalho e a chave primária e cole-as conforme instruído em [dados de streaming para o Azure log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. Depois de concluir as instruções F5 BIG-IP, na página conector do Azure Sentinel, você verá os tipos de dados conectados.
1. Para usar o esquema relevante em Log Analytics para os eventos BIG-IP F5, pesquise por **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL** e **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar F5 BIG-IP ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.


