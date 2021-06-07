---
title: Conectar dados do VMware Carbon Black Cloud Endpoint Standard ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do VMware Carbon Black Cloud Endpoint Standard ao Azure Sentinel.
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
ms.openlocfilehash: 2c26931db617109c36c19b73bd3ec9263f4b40cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096439"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>Conectar o VMware Carbon Black Cloud Endpoint Standard ao Azure Sentinel com o Azure Functions

> [!IMPORTANT]
> Atualmente, o conector de dados do VMware Carbon Black Cloud Endpoint Standard no Azure Sentinel está em versão prévia pública.
> Esse recurso é fornecido sem um Contrato de Nível de Serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O conector do VMware Carbon Black Cloud Endpoint Standard permite que você conecte com facilidade todos os seus logs da solução de segurança [VMware Carbon Black Endpoint Standard](https://www.carbonblack.com/products/endpoint-standard/) ao Azure Sentinel, para ver painéis, criar alertas personalizados e aprimorar a investigação. A integração entre o VMware Carbon Black e o Azure Sentinel usa o Azure Functions para efetuar pull de dados de log por meio da API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do workspace no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>Configurar e conectar o VMware Carbon Black Cloud Endpoint Standard

O Azure Functions pode integrar eventos e logs e efetuar pull deles diretamente no VMware Carbon Black Cloud Endpoint Standard e encaminhá-los para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **Conectores de dados** e selecione p conector **VMware Carbon Black**.
2. Clique em **Abrir página do conector**.
3. Siga as instruções da página do **VMware Carbon Black**.


## <a name="find-your-data"></a>Encontrar seus dados

Depois que uma conexão bem-sucedida for estabelecida, os dados serão exibidos no Log Analytics nas tabelas **CarbonBlackAuditLogs_CL**, **CarbonBlackNotifications_CL** e ****CarbonBlackEvents_CL****.

## <a name="validate-connectivity"></a>Validar a conectividade
Pode levar até 20 minutos até que os logs comecem a ser exibidos no Log Analytics. 


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o VMware Carbon Black Cloud Endpoint Standard ao Azure Sentinel usando os aplicativos de funções do Azure. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.

