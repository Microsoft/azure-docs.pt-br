---
title: Conectar os dados do SRX Networks Juniper ao Azure sentinela | Microsoft Docs
description: Saiba como usar o conector de dados Juniper SRX para efetuar pull de logs do Juniper SRX no Azure Sentinel. Exiba dados do Juniper SRX em pastas de trabalho, crie alertas e melhore a investigação.
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
ms.openlocfilehash: 0583846fcd847e92a6f001dae828d31d9d11cb00
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567856"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>Conectar seu firewall Juniper SRX ao Azure Sentinel

> [!IMPORTANT]
> O conector Juniper SRX está atualmente em **Visualização**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Este artigo explica como conectar seu dispositivo Juniper SRX firewall ao Azure Sentinel. O conector de dados Juniper SRX permite que você conecte facilmente seus logs do SRX com o Azure Sentinel, para que você possa exibir os dados em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. A integração entre o Juniper SRX e o Azure Sentinel usa o syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissão de leitura e gravação no espaço de trabalho do Azure Sentinel.

- Sua solução Juniper SRX deve ser configurada para exportar logs por meio do syslog.

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>Encaminhe os logs do Juniper SRX para o agente de syslog  

Configure o Juniper SRX para encaminhar mensagens de syslog para seu espaço de trabalho do Azure Sentinel por meio do agente de syslog.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione o conector **Juniper SRX (versão prévia)** e, em seguida, **abra a página conector**.

1. Siga as instruções na página do conector **Juniper SRX** :

    1. Instalar e carregar o agente para Linux

        - Escolha uma VM Linux do Azure ou um computador Linux não Azure (físico ou virtual).

    1. Configurar os logs a serem coletados

        - Selecione as instalações e severidades na configuração configurações avançadas do espaço de trabalho

    1. Configurar e conectar o Juniper SRX

        - Siga estas instruções para configurar o Juniper SRX para encaminhar o syslog.
            - [Logs de tráfego (logs de política de segurança)](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [Logs do sistema](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - Para o servidor remoto, use o endereço IP do computador Linux no qual você instalou o agente Linux.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem em Log Analytics em syslog.

Consulte a guia **próximas etapas** na página conector para ver algumas consultas de exemplo úteis.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Juniper SRX ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
