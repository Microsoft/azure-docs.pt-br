---
title: Conectar dados do Cisco Meraki ao Azure sentinela | Microsoft Docs
description: Saiba como usar o conector Cisco Meraki para efetuar pull dos logs do Cisco Meraki para o Azure Sentinel. Exiba os dados do Cisco Meraki em pastas de trabalho, crie alertas e aprimore a investigação.
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
ms.openlocfilehash: dc2d2a0724f18a02a5b667eb1004963a326ec360
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744290"
---
# <a name="connect-your-cisco-meraki-to-azure-sentinel"></a>Conectar seu Cisco Meraki ao Azure Sentinel

> [!IMPORTANT]
> O conector do Cisco Meraki está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Este artigo explica como conectar seus dispositivos Cisco Meraki (MX/MS/MR) ao Azure Sentinel. O Cisco Meraki data Connector permite que você conecte facilmente seus logs do Cisco Meraki com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. A integração entre o Cisco Meraki e o Azure Sentinel usa um servidor syslog com o agente de Log Analytics instalado. Ele também usa um Log Parser personalizado baseado em uma função Kusto.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissão de leitura e gravação no espaço de trabalho do Azure Sentinel.

- Sua solução Cisco Meraki deve ser configurada para exportar logs via syslog.

## <a name="send-cisco-meraki-logs-to-azure-sentinel-via-the-syslog-agent"></a>Enviar logs do Cisco Meraki para o Azure Sentinel por meio do agente de syslog  

Configure o Cisco Meraki para encaminhar mensagens de syslog para seu espaço de trabalho do Azure Sentinel por meio do agente de syslog.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione o conector **Cisco Meraki (versão prévia)** e, em seguida, **abra a página conector**.

1. Siga as instruções na página do conector do **Cisco Meraki** :

    1. Instalar e carregar o agente para Linux

        - Escolha uma VM Linux do Azure ou um computador Linux não Azure (físico ou virtual).

    1. Configurar os logs a serem coletados

        - Selecione as instalações e severidades na **configuração de agentes de espaço de trabalho**.

    1. Configurar e conectar os dispositivos do Cisco Meraki

        - Siga [estas instruções](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) para configurar os dispositivos Cisco Meraki para encaminhar o syslog. Para o servidor remoto, use o endereço IP do computador Linux no qual você instalou o agente Linux.

## <a name="validate-connectivity-and-find-your-data"></a>Validar a conectividade e localizar seus dados

Pode levar até 20 minutos até que os logs comecem a aparecer no Azure Sentinel. 

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção *Gerenciamento de log* , na tabela *syslog* .

Esse conector de dados depende de um analisador baseado em uma função Kusto para funcionar conforme o esperado. [Siga estas etapas](https://aka.ms/sentinel-ciscomeraki-parser) para criar o alias da função **CiscoMeraki** Kusto. Em seguida, você pode digitar `CiscoMeraki` na janela de consulta para consultar os dados.

Consulte a guia **próximas etapas** na página conector para obter alguns exemplos de consulta úteis.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Cisco Meraki ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
