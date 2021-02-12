---
title: Conectar os dados do Secrmm Technologies Squadra ao Azure sentinela | Microsoft Docs
description: Saiba como conectar os dados do Secrmm Technologies Squadra ao Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: a00b4b1e81c0d644cf1475aa46dda3848fda1365
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632894"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Conecte seus dados do Secrmm Technologies Squadra ao Azure Sentinel 

O conector do Secrmm Technologies Squadra permite que você conecte facilmente os logs de solução de segurança do Secrmm Technologies Squadra com o Azure Sentinel. Ele permite exibir painéis, criar alertas personalizados e melhorar a investigação. Esse conector fornece informações sobre eventos de armazenamento removível USB. A integração entre o Secrmm Technologies Squadra e o Azure Sentinel usa a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Configurar e conectar o Secrmm Technologies Squadra 

O secrmm Technologies Squadra pode integrar e exportar logs diretamente para o Azure Sentinel.
1. No portal do Azure Sentinel, clique em conectores de dados e selecione Secrmm Technologies Squadra e abra a página conector.

2. Siga as etapas descritas no guia de [integração do Secrmm Technologies para o Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) para obter dados de secrmm Squadra no Azure sentinela.   

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção **CustomLogs** , na `secRMM_CL` tabela.

Para consultar os logs do Secrmm Technologies Squadra, insira o nome da tabela na parte superior da janela de consulta.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Secrmm Technologies Squadra ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
