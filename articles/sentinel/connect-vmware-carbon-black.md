---
title: Conectar dados padrão de ponto de extremidade de nuvem do VMware carbono preto ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados padrão de ponto de extremidade de nuvem do VMware carbono preto ao Azure Sentinel.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: e13b251c9bd95a5b52d63d8ea1bbf265c9c46fd8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023867"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>Conectar seu ponto de extremidade de nuvem do VMware carbono preto padrão ao Azure Sentinel com o Azure function

> [!IMPORTANT]
> O conector de dados standard do ponto de extremidade de nuvem do VMware carbono preto no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O conector padrão de ponto de extremidade de nuvem do VMware carbono preto permite que você conecte facilmente todos os seus logs de solução de segurança de [ponto de extremidade do VMware carbono preto](https://www.carbonblack.com/products/endpoint-standard/) com o Azure Sentinel, para exibir painéis, criar alertas personalizados e melhorar a investigação. A integração entre o VMware carbono preto e o Azure Sentinel usa Azure Functions para efetuar pull de dados de log usando a API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>Configurar e conectar o ponto de extremidade de nuvem do VMware carbono preto padrão

Azure Functions pode integrar e efetuar pull de eventos e logs diretamente do padrão de ponto de extremidade de nuvem do VMware carbono preto e encaminhá-los para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione conector do **VMware carbono preto** .
2. Selecione a **página abrir conector**.
3. Siga as instruções na página **preta do VMware carbono** .


## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem em Log Analytics nas tabelas **CarbonBlackAuditLogs_CL**, **CarbonBlackNotifications_CL** e * * * CarbonBlackEvents_CL * * * *.

## <a name="validate-connectivity"></a>Validar a conectividade
Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o VMware carbono preto Cloud Endpoint ponto de extremidade ao Azure Sentinel usando os aplicativos de funções do Azure. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.

