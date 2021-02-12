---
title: Conectar dados de UCS (sistema de computação unificada) da Cisco ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados do Cisco UCS para efetuar pull de logs do Cisco UCS para o Azure Sentinel. Exiba dados do UCS Cisco em pastas de trabalho, crie alertas e melhore a investigação.
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
ms.openlocfilehash: caa83b9149f39f69d0cbf44a2d6cb01fdaf29721
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567855"
---
# <a name="connect-your-cisco-unified-computing-system-ucs-to-azure-sentinel"></a>Conectar seu UCS (sistema de computação unificada) ao Azure Sentinel

> [!IMPORTANT]
> O conector do Cisco UCS está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Este artigo explica como conectar seu dispositivo de UCS (sistema de computação unificada) da Cisco para o Azure Sentinel. O Cisco UCS data Connector permite que você conecte facilmente seus logs do UCS com o Azure Sentinel, para que você possa exibir os dados em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. A integração entre o UCS Cisco e o Azure Sentinel usa o syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissão de leitura e gravação no espaço de trabalho do Azure Sentinel.

- Sua solução Cisco UCS deve ser configurada para exportar logs via syslog.

## <a name="forward-cisco-ucs-logs-to-the-syslog-agent"></a>Encaminhe os logs do UCS Cisco para o agente de syslog  

Configure o Cisco UCS para encaminhar mensagens de syslog para seu espaço de trabalho do Azure Sentinel por meio do agente de syslog.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione o conector **Cisco UCS (versão prévia)** e, em seguida, **abra a página conector**.

1. Siga as instruções na página do conector do **Cisco UCS** :

    1. Instalar e carregar o agente para Linux

        - Escolha uma VM Linux do Azure ou um computador Linux não Azure (físico ou virtual).

    1. Configurar os logs a serem coletados

        - Selecione as instalações e severidades na configuração configurações avançadas do espaço de trabalho

    1. Configurar e conectar o UCS Cisco

        - Siga [estas instruções](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) para configurar o UCS Cisco para encaminhar o syslog. Para o servidor remoto, use o endereço IP do computador Linux no qual você instalou o agente Linux.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem em Log Analytics em syslog.

Consulte a guia **próximas etapas** na página conector para ver algumas consultas de exemplo úteis.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Cisco UCS ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
