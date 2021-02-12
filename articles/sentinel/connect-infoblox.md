---
title: Conectar dados de NIOS (Infoblox Network Identity Operating System) ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados de NIOS (Infoblox Network Identity Operating System) ao Azure Sentinel.
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
ms.openlocfilehash: 28abb9f09e3bca2522b959c6a9b890de5320b17a
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567448"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Conectar seu Infoblox NIOS ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados Infoblox NIOS no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como conectar seu [dispositivo nios (sistema operacional de identidade de rede) Infoblox](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) ao Azure Sentinel. O conector de dados Infoblox NIOS permite que você conecte facilmente seus logs do Infoblox com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. A integração entre o Infoblox NIOS e o Azure Sentinel usa o syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Encaminhar logs do Infoblox para o agente de syslog  

Configure o Infoblox para encaminhar mensagens de syslog para seu espaço de trabalho do Azure Sentinel por meio do agente de syslog.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione conector do **Infoblox NIOS** .

1. Selecione a **página abrir conector**.

1. Siga as instruções na página **INFOBLOX nios** .

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem em Log Analytics em syslog.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Infoblox NIOS ao Azure sentinela. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.