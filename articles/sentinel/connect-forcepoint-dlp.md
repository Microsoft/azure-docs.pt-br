---
title: Conectar o Forcepoint DLP ao Azure sentinela | Microsoft Docs
description: Saiba como conectar o Forcepoint DLP ao Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 62ed3915dcaf596d144a2f59817626cdf8ec47e5
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100092767"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Conectar seu Forcepoint DLP ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados de DLP (prevenção de perda de dados) do Forcepoint no Azure Sentinel está atualmente em visualização pública. Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



O conector do Forcepoint DLP permite que você exporte automaticamente os dados de incidente de DLP para o Azure Sentinel. Você pode usá-lo para obter visibilidade de atividades do usuário e incidentes de perda de dados. Ele também permite correlações com dados de cargas de trabalho do Azure e outros feeds, além de aprimorar a capacidade de monitoramento com as planilhas dentro do Azure Sentinel.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-forcepoint-dlp"></a>Configurar e conectar o Forcepoint DLP

Configure o Forcepoint DLP para encaminhar dados de incidentes no formato JSON para seu espaço de trabalho do Azure por meio da API REST, conforme explicado no [Guia de integração do FORCEPOINT DLP](https://frcpnt.com/dlp-sentinel).


## <a name="find-your-data"></a>Encontre seus dados

Depois que o conector do Forcepoint DLP for configurado, os dados aparecerão em Log Analytics em CustomLogs **ForcepointDLPEvents_CL**.


Para usar o esquema relevante no Log Analytics para o Forcepoint DLP, procure **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Forcepoint DLP ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.