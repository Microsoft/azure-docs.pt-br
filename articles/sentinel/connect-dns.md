---
title: Conectar dados DNS no Azure Sentinel | Microsoft Docs
description: Saiba como conectar qualquer DNS (servidor de nomes de domínio) em execução no Windows para o Azure Sentinel instalando um agente no computador DNS.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: abecddb6f5469cb4ef463e65d6c74149bf34dca9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590232"
---
# <a name="connect-your-domain-name-server"></a>Conectar seu servidor de nome de domínio

> [!IMPORTANT]
> O conector de dados DNS no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode conectar qualquer DNS (servidor de nomes de domínio) em execução no Windows para o Azure Sentinel. Isso é feito por meio da instalação de um agente no computador DNS. Usando logs DNS, você pode obter informações relacionadas à segurança, ao desempenho e às operações na infraestrutura de DNS da sua organização, coletando, analisando e correlacionando logs analíticos e de auditoria e outros dados relacionados dos servidores DNS.

Ao habilitar a conexão de log DNS, você pode:
- Identificar clientes que tentam resolver nomes de domínio mal-intencionados
- Identificar registros de recursos obsoletos
- Identificar os nomes de domínio consultados com frequência e clientes DNS comunicativos
- Exibir carga de solicitação em servidores DNS
- Exibir falhas de registro de DNS dinâmico

## <a name="connected-sources"></a>Fontes conectadas

A seguinte tabela descreve as fontes conectadas que têm suporte nessa solução:

| **Fonte conectada** | **Suporte** | **Descrição** |
| --- | --- | --- |
| [Agentes do Windows](../azure-monitor/agents/agent-windows.md) | Sim | A solução coleta informações de DNS dos agentes do Windows. |
| [Agentes do Linux](../azure-monitor/vm/quick-collect-linux-computer.md) | Não | A solução não coleta informações de DNS dos agentes diretos do Linux. |
| [Grupo de gerenciamento do System Center Operations Manager](../azure-monitor/agents/om-agents.md) | Sim | A solução coleta informações de DNS dos agentes em um grupo de gerenciamento conectado do Operations Manager. Não é necessária uma conexão direta entre o agente do Operations Manager e o Azure Monitor. Os dados são encaminhados do grupo de gerenciamento para o espaço de trabalho do Log Analytics. |
| [Conta de Armazenamento do Azure](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) | Não | O armazenamento do Azure não é usado pela solução. |

### <a name="data-collection-details"></a>Detalhes da coleta de dados

A solução coleta dados relacionados a eventos e ao inventário DNS dos servidores DNS, onde um agente do Log Analytics está instalado. Os dados relacionados ao inventário, como número de servidores DNS, zonas e registros de recursos, são coletados pela execução de cmdlets do PowerShell do DNS. Os dados são atualizados uma vez a cada dois dias. Os dados relacionados a eventos são coletados quase em tempo real dos [logs de análise e auditoria](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) fornecidos pelo log e diagnóstico avançado de DNS no Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Conectar seu dispositivo DNS

1. No portal do Azure Sentinel, selecione **conectores de dados** e escolha o bloco **DNS (visualização)** .
1. Se os computadores DNS estiverem no Azure:
    1. Clique em **instalar agente na máquina virtual do Windows do Azure**.
    1. Na lista **máquinas virtuais** , selecione o computador DNS que você deseja transmitir para o Azure Sentinel. Verifique se esta é uma VM do Windows.
    1. Na janela que é aberta para essa VM, clique em **conectar**.  
    1. Clique em **habilitar** na janela do **conector DNS** . 

2. Se o computador DNS não for uma VM do Azure:
    1. Clique em **instalar agente em computadores não Azure**.
    1. Na janela **agente direto** , selecione baixar o **agente do Windows (64 bits)** ou **baixar o agente do Windows (32 bits)**.
    1. Instale o agente em seu computador DNS. Copie a **ID do espaço de trabalho**, a **chave primária** e a **chave secundária** e use-as quando solicitado durante a instalação.

3. Para usar o esquema relevante no Log Analytics para os logs DNS, procure **DnsEvents**.

## <a name="validate"></a>Validar 

Em Log Analytics, procure o esquema **DnsEvents** e verifique se há eventos.

## <a name="troubleshooting"></a>Solução de problemas

Se as consultas de pesquisa não aparecerem no Azure Sentinel, siga estas etapas para que as consultas sejam exibidas corretamente:
1. Ative os [logs de análise de DNS em seus servidores](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Certifique-se de que DNSEvents apareça na lista de coleções de Log Analytics.
3. Ativar [análise de DNS do Azure](../azure-monitor/insights/dns-analytics.md).
4. No Azure Análise de DNS, em **configuração**, altere qualquer uma das configurações, salve-a e, em seguida, altere-a novamente, se necessário, e salve-a novamente.
5. Verifique a análise de DNS do Azure para certificar-se de que as consultas estão sendo exibidas agora.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar os dispositivos locais do DNS ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).