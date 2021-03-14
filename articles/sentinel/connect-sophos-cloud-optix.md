---
title: Conectar dados do Sophos Cloud Optix ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector do Sophos Cloud Optix para <PRODUCT NAME> efetuar pull de logs no Azure Sentinel. Exiba <PRODUCT NAME> dados em pastas de trabalho, crie alertas e melhore a investigação.
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
ms.date: 01/26/2021
ms.author: yelevin
ms.openlocfilehash: c66205ffd9bd5a742d645cbf2f9251a44329a16e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700780"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>Conectar seu Sophos Cloud Optix ao Azure Sentinel

> [!IMPORTANT]
> O conector do Sophos Cloud Optix está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

O Sophos Cloud Optix Connector permite que você conecte facilmente todos os seus logs de solução de segurança do Sophos Cloud Optix com sua Sentinela do Azure, para exibir painéis, criar alertas personalizados e melhorar a investigação.  Esse recurso oferece mais informações sobre a postura de conformidade e segurança na nuvem de sua organização e melhora os recursos de operação de segurança da nuvem. A integração entre o Sophos Cloud Optix e o Azure Sentinel usa a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-sophos-cloud-optix"></a>Configurar e conectar o Sophos Cloud Optix

O Sophos Cloud Optix pode integrar e exportar logs diretamente para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione **Sophos Cloud Optix (versão prévia)**.

1. Selecione a **página abrir conector**.

1. Copie e salve a **ID do espaço de trabalho** e a **chave primária** na página do conector.

1. Siga as instruções do Sophos para [integrar com o Microsoft Azure Sentinel](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html) (a partir da terceira etapa).

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs** na seção **logs personalizados** , na tabela *SophosCloudOptix_CL* .

Para consultar os dados do Sophos Cloud Optix, insira `SophosCloudOptix_CL` na janela de consulta. Consulte a guia **próximas etapas** na página do conector e a [documentação do Sophos](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html)para obter alguns exemplos de consulta úteis.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Sophos Cloud Optix ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
