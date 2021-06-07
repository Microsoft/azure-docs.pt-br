---
title: Conectar dados de segurança de email do proofpoint on Demand ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados de segurança de email do proofpoint on Demand para receber logs de segurança de email de POD no Azure Sentinel. Exiba dados de segurança de email de POD em pastas de trabalho, crie alertas e melhore a investigação.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 73a9d9c7ab321aebd615922e5d4395c0318e809c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580441"
---
# <a name="connect-your-proofpoint-on-demand-email-security-pod-solution-to-azure-sentinel"></a>Conectar sua solução POD (segurança de email do proofpoint on Demand) ao Azure Sentinel

> [!IMPORTANT]
> O conector de segurança de email do proofpoint on Demand está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Este artigo explica como conectar seu dispositivo de segurança de email do proofpoint on Demand ao Azure Sentinel. O conector de dados POD permite que você conecte facilmente os logs de POD com o Azure Sentinel, para que você possa exibir os dados em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação.  A integração entre a segurança de email do proofpoint on demand e o Azure Sentinel usa a API WebSocket.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissão de leitura e gravação no espaço de trabalho do Azure Sentinel.

- Você deve ter permissões de leitura para chaves compartilhadas para o espaço de trabalho. [Saiba mais sobre as chaves do espaço de trabalho](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Você deve ter permissões de leitura e gravação para Azure Functions a fim de criar uma Aplicativo de funções. [Saiba mais sobre Azure Functions](../azure-functions/index.yml).

- Você deve ter as seguintes credenciais de API do WebSocket: ProofpointClusterID, ProofpointToken. [Saiba mais sobre a API WebSocket](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API).

## <a name="configure-and-connect-proofpoint-on-demand-email-security"></a>Configurar e conectar a segurança de email do proofpoint on Demand

A segurança de email do proofpoint on Demand pode integrar e exportar logs diretamente para o Azure Sentinel.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione **proofpoint segurança de email sob demanda (versão prévia)** e **abra a página conector**.

1. Siga as etapas descritas na seção **configuração** da página conector.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, em *logs personalizados*, nas seguintes tabelas:
- `ProofpointPOD_message_CL`
- `ProofpointPOD_maillog_CL`

Consulte a guia **próximas etapas** na página conector para ver algumas consultas de exemplo úteis.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 60 minutos até que os logs comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar a segurança de email do proofpoint on Demand ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.