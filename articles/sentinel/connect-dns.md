---
title: Conectar-se os dados DNS na visualização do Azure Sentinel | Microsoft Docs
description: Saiba como se conectar a dados DNS no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 1c79aad557efb85a8797584c33c74983ef645d07
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611306"
---
# <a name="connect-your-domain-name-server"></a>Connect your domain name server

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode se conectar a qualquer servidor DNS (Domain Name) em execução no Windows para o Azure Sentinel. Isso é feito ao instalar um agente no computador do DNS. Registra em log usando o DNS, você pode obter insights de operações relacionadas à infra-estrutura do DNS da sua organização, desempenho e segurança ao coletar, analisar, e correlacionar analítica e logs de auditoria e outros dados relacionados dos servidores DNS.

Quando você habilita a conexão do log DNS, você pode:
- Identificar clientes que tentam resolver nomes de domínio mal-intencionados
- Identificar registros de recursos obsoletos
- Identificar os nomes de domínio consultados com frequência e clientes DNS comunicativos
- Exibir carga de solicitação em servidores DNS
- Exibir falhas de registro de DNS dinâmico

## <a name="connected-sources"></a>Fontes conectadas

A seguinte tabela descreve as fontes conectadas que têm suporte nessa solução:

| **Fonte conectada** | **Suporte** | **Descrição** |
| --- | --- | --- |
| [Agentes do Windows](../azure-monitor/platform/agent-windows.md) | Sim | A solução coleta informações de DNS dos agentes do Windows. |
| [Agentes do Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | Não | A solução não coleta informações de DNS dos agentes diretos do Linux. |
| [Grupo de gerenciamento do System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Sim | A solução coleta informações de DNS dos agentes em um grupo de gerenciamento conectado do Operations Manager. Não é necessária uma conexão direta entre o agente do Operations Manager e o Azure Monitor. Os dados são encaminhados do grupo de gerenciamento para o espaço de trabalho do Log Analytics. |
| [Conta de armazenamento do Azure](../azure-monitor/platform/collect-azure-metrics-logs.md) | Não | O armazenamento do Azure não é usado pela solução. |

### <a name="data-collection-details"></a>Detalhes da coleta de dados

A solução coleta dados relacionados a eventos e ao inventário DNS dos servidores DNS, onde um agente do Log Analytics está instalado. Os dados relacionados ao inventário, como número de servidores DNS, zonas e registros de recursos, são coletados pela execução de cmdlets do PowerShell do DNS. Os dados são atualizados uma vez a cada dois dias. Os dados relacionados a eventos são coletados quase em tempo real dos [logs de análise e auditoria](https://technet.microsoft.com/library/dn800669.aspx#enhanc) fornecidos pelo log e diagnóstico avançado de DNS no Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Conectar seu dispositivo de DNS

1. No portal do Azure Sentinel, selecione **conectores de dados** e escolha o **DNS** lado a lado.
1. Se suas máquinas DNS estão no Azure:
    1. Clique em **instalar o agente na máquina de virtual do Windows Azure**.
    1. No **máquinas virtuais** , selecione a máquina DNS que você deseja transmitir para o Azure Sentinel. Verifique se que esta é uma VM do Windows.
    1. Na janela que é aberta para a VM, clique em **Connect**.  
    1. Clique em **habilitar** na **conector DNS** janela. 

2. Se sua máquina DNS não for uma VM do Azure:
    1. Clique em **instalar o agente em computadores não Azure**.
    1. No **agente direto** janela, selecione **agente baixar o Windows (64 bits)** ou **agente baixar o Windows (32 bits)** .
    1. Instale o agente em seu computador DNS. Cópia de **ID do espaço de trabalho**, **chave primária**, e **chave secundária** e usá-los quando solicitado durante a instalação.

3. Para usar o esquema relevante no Log Analytics para os logs DNS, pesquise **DnsEvents**.

## <a name="validate"></a>Validar 

No Log Analytics, procure o esquema **DnsEvents** e verifique se há eventos.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar dispositivos de locais DNS ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).
