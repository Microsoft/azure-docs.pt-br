---
title: Conectar dados de VMware ESXi ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados VMware ESXi para efetuar pull de logs de ESXi no Azure Sentinel. Exiba dados do ESXi em pastas de trabalho, crie alertas e melhore a investigação.
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
ms.date: 03/01/2021
ms.author: yelevin
ms.openlocfilehash: 3d478a9ac3cf91f3f6815859b8534efff88f07b1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700779"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>Conectar seu VMware ESXi ao Azure Sentinel

> [!IMPORTANT]
> O conector de VMware ESXi está atualmente em **Visualização**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Este artigo explica como conectar seu dispositivo de VMware ESXi ao Azure Sentinel. O conector de dados VMware ESXi permite que você ingerir facilmente seus logs de VMware ESXi no Azure Sentinel, fornecendo mais informações sobre as atividades ESXi da sua organização e ajudando a melhorar seus recursos de operações de segurança. A integração entre VMware ESXi e o Azure Sentinel usa um servidor syslog com o agente Log Analytics instalado. Ele também usa um Log Parser personalizado baseado em uma função Kusto.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissão de leitura e gravação no espaço de trabalho do Azure Sentinel.

- Sua solução de VMware ESXi deve ser configurada para exportar logs via syslog.

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>Enviar logs de VMware ESXi para o agente de syslog  

Configure VMware ESXi para encaminhar mensagens de syslog para seu espaço de trabalho do Azure Sentinel por meio do agente de syslog.
3. Siga as instruções na página **VMware ESXi** .


1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione o conector **VMware ESXi (versão prévia)** e, em seguida, **abra a página conector**.

1. Siga as instruções na página conector de **VMware ESXi** :

    1. Instalar e carregar o agente para Linux

        - Escolha uma VM Linux do Azure ou um computador Linux não Azure (físico ou virtual).

    1. Configurar os logs a serem coletados

        - Selecione as instalações e severidades na **configuração de agentes de espaço de trabalho**.

    1. Configurar e conectar o VMware ESXi

        - Siga estas instruções para configurar o VMware ESXi para encaminhar o syslog:
            - [VMware ESXi 3,5 e 4. x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5,0 e superior](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            Para o servidor remoto, use o endereço IP do computador Linux no qual você instalou o agente Linux.

## <a name="validate-connectivity-and-find-your-data"></a>Validar a conectividade e localizar seus dados

Pode levar até 20 minutos até que os logs comecem a aparecer no Azure Sentinel. 

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção *Gerenciamento de log* , na tabela *syslog* .

Esse conector de dados depende de um analisador baseado em uma função Kusto para funcionar conforme o esperado. [Siga estas etapas](https://aka.ms/sentinel-vmwareesxi-parser) para criar o alias da função **VMwareESXi** Kusto. Em seguida, você pode digitar `VMwareESXi` qualquer janela de consulta no Azure Sentinel para consultar os dados.

Consulte a guia **próximas etapas** na página conector para obter alguns exemplos de consulta úteis.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar VMware ESXi ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
