---
title: Conectar melhor defesa contra ameaças móveis (MTD) ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o melhor conector de dados de defesa contra ameaças móveis (MTD) para efetuar pull de MTD logs no Azure Sentinel. Exiba dados do MTD em pastas de trabalho, crie alertas e melhore a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 4828e31b9d15f101740c158ee62c90c95673c9a7
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541529"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>Conecte sua melhor defesa contra ameaças móveis (MTD) ao Azure Sentinel

> [!IMPORTANT]
> O conector de defesa contra ameaças móveis (MTD) melhor está em **Visualização** no momento. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

O melhor conector de defesa contra ameaças móveis (MTD) permite que você conecte facilmente todos os seus logs de solução de segurança MTD melhores à sentinela do Azure, para exibir painéis, criar alertas personalizados e melhorar a investigação. A integração entre a melhor defesa contra ameaças móveis e o Azure Sentinel usa a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-better-mobile-threat-defense"></a>Configurar e conectar melhor defesa contra ameaças móveis

UM MTD melhor pode integrar e exportar logs diretamente para o Azure Sentinel.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione **melhor defesa contra ameaças móveis (MTD) (visualização)** e, em seguida, **abra a página do conector**.

1. Siga as etapas na página do conector e nesta [página da melhor documentação do MTD](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration) para finalizar a integração no melhor console do MTD.

    Quando solicitado a inserir a **ID do espaço de trabalho** e os valores de **chave primária** , copie-os da página do conector do Azure Sentinel e cole-os na melhor configuração do MTD.

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{ID do espaço de trabalho e chave primária}":::

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção **CustomLogs** , em uma ou mais das seguintes tabelas:
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

Para consultar os logs de MTD melhores em regras de análise, procurando consultas ou em qualquer outro lugar no Azure Sentinel, insira um dos nomes de tabela acima na parte superior da janela de consulta.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a se conectar a uma melhor defesa contra ameaças móveis (MTD) ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
