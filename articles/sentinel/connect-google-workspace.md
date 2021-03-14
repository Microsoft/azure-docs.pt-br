---
title: Conectar dados do Google Workspace (G Suite) ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados do Google Workspace (G Suite) para ingerir eventos de atividade do Google Workspace no Azure Sentinel. Exibir dados do Google Workspace em pastas de trabalho, criar alertas e melhorar a investigação.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 4ada570502d913283ba9ee4cc4c65b7bdd853935
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744110"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>Conecte seu Google Workspace ao Azure Sentinel

> [!IMPORTANT]
> O conector do Google Workspace está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

O conector de dados do [Google Workspace (anteriormente chamado G Suite)](https://workspace.google.com/) fornece a capacidade de ingerir eventos de atividade do Google Workspace no Azure Sentinel por meio da API REST. O conector fornece a visibilidade desses [eventos](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities) no SOC, o que ajuda a examinar possíveis riscos de segurança, a analisar o uso de colaboração da sua equipe, diagnosticar problemas de configuração, controlar quem entra e quando, analisar a atividade do administrador, entender como os usuários criam e compartilham conteúdo e examinam mais eventos em sua organização.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

Para coletar dados do Google Workspace, você deve ter as seguintes permissões e credenciais:

- Permissões de leitura e gravação no seu espaço de trabalho do Azure Sentinel.

- Permissões de leitura para chaves compartilhadas para o espaço de trabalho. [Saiba mais sobre as chaves do espaço de trabalho](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Permissões de leitura e gravação para Azure Functions para criar um Aplicativo de funções. [Saiba mais sobre Azure Functions](../azure-functions/index.yml).

- A credencial **GooglePickleString** é necessária para a API REST. [Saiba mais sobre a API REST do Google](https://developers.google.com/admin-sdk/reports/v1/reference/activities). [Saiba como obter credenciais](https://developers.google.com/admin-sdk/reports/v1/quickstart/python).

## <a name="configure-and-connect-google-workspace"></a>Configurar e conectar o Google Workspace

O Google Workspace pode integrar e exportar logs diretamente para o Azure Sentinel usando uma Aplicativo de funções do Azure.

> [!NOTE]
> Esse conector usa Azure Functions para se conectar à API do Google Reports para efetuar pull de eventos de atividade para o Azure Sentinel. Isso pode resultar em custos adicionais de ingestão de dados. Confira a página de [preços Azure Functions](https://azure.microsoft.com/pricing/details/functions/) para obter detalhes.

1. No portal do Azure Sentinel, clique em **conectores de dados**. 

1. Selecione **Google Workspace (G Suite) (visualização)** na Galeria de conectores e selecione a **página abrir conector**.

1. Siga as etapas descritas na seção **configuração** da página conector.

## <a name="validate-connectivity-and-find-your-data"></a>Validar a conectividade e localizar seus dados

Pode levar até 20 minutos até que você possa ver dados ingeridos no Azure Sentinel.

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção **logs personalizados** , nas seguintes tabelas:
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

Esse conector de dados depende de um analisador baseado em uma função Kusto para funcionar conforme o esperado. [Siga estas etapas](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser) para criar o alias de funções do **GWorkspaceActivityReports** Kusto.

Consulte a guia **próximas etapas** na página conector para ver algumas consultas de exemplo úteis.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Google Workspace ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
