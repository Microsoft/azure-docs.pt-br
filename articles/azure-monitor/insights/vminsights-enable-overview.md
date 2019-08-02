---
title: Habilitar o Azure Monitor para visão geral de máquinas virtuais (versão prévia) | Microsoft Docs
description: Saiba como implantar e configurar o Azure Monitor para máquinas virtuais. Descubra os requisitos do sistema.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: magoedte
ms.openlocfilehash: 6fb7e6b9611b28dab856209aaf03aa93c25d3968
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478045"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Habilitar o Azure Monitor para visão geral de máquinas virtuais (versão prévia)

Este artigo fornece uma visão geral das opções disponíveis para configurar o Azure Monitor para máquinas virtuais. Use o Azure Monitor para monitorar a integridade e desempenho para as VMs. Descubra dependências de aplicativos que são executados em máquinas virtuais (VMs) e dimensionamento de máquinas virtuais define, no local VMs ou máquinas virtuais hospedadas em outro ambiente de nuvem.  

Para definir o Monitor do Azure para VMs:

* Habilitar uma única escala de VM do Azure ou máquina virtual configurada selecionando **Insights (visualização)** diretamente do VM ou máquina virtual conjunto de dimensionamento.
* Habilitar dois ou mais VMs do Azure e máquinas virtuais conjuntos de dimensionamento usando a política do Azure. Esse método garante que, em VMs novas e existentes e conjuntos de dimensionamento, as dependências necessárias estão instaladas e configuradas corretamente. VMs incompatíveis e conjuntos de dimensionamento são relatados, para que você possa decidir se deve habilitá-los e corrigi-los.
* Habilite duas ou mais VMs do Azure ou conjuntos de dimensionamento de máquinas virtuais em uma assinatura ou um grupo de recursos especificado usando o PowerShell.
* Habilite o Azure Monitor para máquinas virtuais monitorar máquinas virtuais ou computadores físicos, hospedados em sua rede corporativa ou de outro ambiente de nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você compreende as informações descritas nas seções a seguir.

### <a name="log-analytics"></a>Log Analytics

O Azure Monitor para VMs oferece suporte a um espaço de trabalho do Log Analytics nas seguintes regiões:

- Centro-Oeste dos EUA
- Oeste dos EUA 2<sup>1</sup>
- East US
- Canadá Central
- Sul do Reino Unido
- Europa Ocidental
- Sudeste Asiático

<sup>1</sup> Atualmente, essa região não dá suporte ao recurso Integridade do Azure Monitor para VMs.

>[!NOTE]
>Você pode implantar VMs do Azure de qualquer região. Essas VMs não estão limitadas para as regiões com suporte pelo espaço de trabalho do Log Analytics.
>

Se você não tiver um espaço de trabalho, você pode criar um usando um destes recursos:
* [A CLI do Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [O portal do Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Você também pode criar um espaço de trabalho enquanto você estiver habilitando o monitoramento para uma única escala de VM do Azure ou máquina virtual definida no portal do Azure.

Para configurar um cenário em escala que usa modelos de política do Azure, Azure PowerShell ou do Azure Resource Manager, no espaço de trabalho do Log Analytics:

* Instale as soluções ServiceMap e InfrastructureInsights. Você pode concluir a instalação usando um modelo do Azure Resource Manager fornecido. Ou o **começar** guia, selecione **configurar o espaço de trabalho**.
* Configure o espaço de trabalho do Log Analytics para coletar contadores de desempenho.

Para configurar seu espaço de trabalho para o cenário em escala, use um dos seguintes métodos:

* Use [o Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* No Monitor do Azure para VMs [ **cobertura de política** ](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) página, selecione **configurar espaço de trabalho**. 

### <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

A tabela a seguir lista os sistemas operacionais Windows e Linux que dá suporte ao Azure Monitor para VMs. Mais adiante nesta seção, você encontrará uma lista completa que detalha o sistema operacional Linux principais e secundárias de versão e versões de kernel com suporte.

|Versão do SO |Desempenho |Mapas |Integridade |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14.04, 16.04, 18.04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SLES (SUSE Linux Enterprise Server) 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> O recurso Desempenho do Azure Monitor para VMs está disponível somente no Azure Monitor. Ele não está disponível diretamente no painel esquerdo da VM do Azure.

>[!NOTE]
>O recurso de integridade do Monitor do Azure para VMs não suporta [virtualização aninhada](../../virtual-machines/windows/nested-virtualization.md) em uma VM do Azure.
>

>[!NOTE]
>No sistema operacional Linux:
> - Somente as versões de kernel padrão e Linux SMP têm suporte.
> - Não há suporte para nenhuma distribuição do Linux em versões de kernel não padrão, como PAE (Extensão do Endereço Físico) e Xen. Por exemplo, não há suporte para um sistema com a cadeia de caracteres de versão *2.6.16.21-0.8-xen*.
> - Não há suporte para kernels personalizados, incluindo recompilações de kernels padrão.
> - Há suporte para o kernel CentOSPlus.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versão do SO | Versão do kernel |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versão do SO | Versão do kernel |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

#### <a name="centosplus"></a>CentOSPlus
| Versão do SO | Versão do kernel |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versão do SO | Versão do kernel |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versão do SO | Versão do kernel
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |
|12 SP4 | 4.4.* |
|12 SP4 | Kernel ajustado pelo Azure |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

O recurso de mapa no Azure Monitor para VMs obtém seus dados do Microsoft Dependency agent. O Dependency Agent depende do agente do Log Analytics para suas conexões com o Log Analytics. Portanto, o sistema deve ter o agente do Log Analytics, instalado e configurado com o agente de dependência.

Se você habilitar o Azure Monitor para as VMs para uma única VM do Azure ou use o método de implantação em escala, use a extensão do agente de dependência de VM do Azure para instalar o agente como parte da experiência.

Em um ambiente híbrido, você pode baixar e instalar manualmente o agente de dependência. Se suas VMs são hospedadas fora do Azure, use um método de implantação automatizada.

A tabela a seguir descreve as fontes conectadas para as quais o recurso Mapa dá suporte em um ambiente híbrido.

| Fonte conectada | Suportado | Descrição |
|:--|:--|:--|
| Agentes do Windows | Sim | Juntamente com o [agente do Log Analytics para Windows](../../azure-monitor/platform/log-analytics-agent.md), agentes do Windows precisam o agente de dependência. Para obter mais informações, consulte [sistemas operacionais com suporte](#supported-operating-systems). |
| Agentes do Linux | Sim | Juntamente com o [agente do Log Analytics para Linux](../../azure-monitor/platform/log-analytics-agent.md), agentes do Linux precisam o agente de dependência. Para obter mais informações, consulte [sistemas operacionais com suporte](#supported-operating-systems). |
| Grupo de gerenciamento do System Center Operations Manager | Não | |

Você pode baixar o agente de dependência nesses locais:

| Arquivo | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

Para habilitar e acessar os recursos do Azure Monitor para VMs, você deve ter o *Colaborador do Log Analytics* função. Para exibir o desempenho, integridade e dados de mapa, você deve ter o *leitor de monitoramento* função para a VM do Azure. O espaço de trabalho do Log Analytics precisa ser configurado para o Azure Monitor para VMs.

Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, veja [Gerenciar espaços de trabalho](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Como habilitar o Azure Monitor para máquinas virtuais (versão prévia)

Habilite o Azure Monitor para VMs usando um dos métodos descritos nesta tabela:

| Estado de implantação | Método | DESCRIÇÃO |
|------------------|--------|-------------|
| Conjunto de dimensionamento de VM do Azure ou máquina virtual único | [Habilitar da VM](vminsights-enable-single-vm.md) | Você pode habilitar uma única VM do Azure, selecionando **Insights (visualização)** diretamente do VM ou máquina virtual conjunto de dimensionamento. |
| Várias VMs do Azure ou conjuntos de dimensionamento de máquina virtual | [Habilitar por meio da política do Azure](vminsights-enable-at-scale-policy.md) | Você pode habilitar várias VMs do Azure usando a política do Azure e definições de política disponíveis. |
| Várias VMs do Azure ou conjuntos de dimensionamento de máquina virtual | [Habilitar por meio de modelos do Azure PowerShell ou do Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Você pode habilitar vários conjuntos de dimensionamento de VMs do Azure ou máquina virtual em uma assinatura especificada ou o grupo de recursos usando modelos do Azure PowerShell ou do Azure Resource Manager. |
| Nuvem híbrida | [Habilitar para o ambiente híbrido](vminsights-enable-hybrid-cloud.md) | Você pode implantar máquinas virtuais ou computadores físicos que são hospedados em seu datacenter ou em outros ambientes de nuvem. |

## <a name="performance-counters-enabled"></a>Contadores de desempenho habilitados 

O Azure Monitor para VMs configura um espaço de trabalho do Log Analytics para coletar os contadores de desempenho que ele usa. As tabelas seguintes listam os objetos e contadores que são coletados a cada 60 segundos.

### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows

|Nome do objeto |Nome do contador |
|------------|-------------|
|LogicalDisk |% de Espaço Livre |
|LogicalDisk |Média de segundos/Leitura do Disco |
|LogicalDisk |Média de segundos/Transferência do Disco |
|LogicalDisk |Média de segundos/Gravação do Disco |
|LogicalDisk |Bytes de Disco/s |
|LogicalDisk |Bytes Lidos no Disco/s |
|LogicalDisk |Leituras de Disco/s |
|LogicalDisk |Transferências de Disco/s |
|LogicalDisk |Bytes Gravados no Disco/s |
|LogicalDisk |Gravações de Disco/s |
|LogicalDisk |Megabytes Livres |
|Memória |MBytes Disponíveis |
|Adaptador de rede |Bytes Recebidos/s |
|Adaptador de rede |Bytes Enviados/s |
|Processador |% Tempo do Processador |

### <a name="linux-performance-counters"></a>Contadores de desempenho do Linux

|Nome do objeto |Nome do contador |
|------------|-------------|
|Disco Lógico |% de Espaço Usado |
|Disco Lógico |Bytes Lidos no Disco/s |
|Disco Lógico |Leituras de Disco/s |
|Disco Lógico |Transferências de Disco/s |
|Disco Lógico |Bytes Gravados no Disco/s |
|Disco Lógico |Gravações de Disco/s |
|Disco Lógico |Megabytes Livres |
|Disco Lógico |Bytes de Disco Lógico/s |
|Memória |MBytes de Memória Disponíveis |
|Rede |Total de Bytes Recebidos |
|Rede |Total de Bytes Transmitidos |
|Processador |% Tempo do Processador |

## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico

A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço Azure Monitor. A Microsoft usa esses dados para melhorar a qualidade, segurança e integridade do serviço. 

Para fornecer recursos de solução de problemas precisos e eficientes, o recurso mapa inclui dados sobre a configuração do software. Os dados fornecem informações como o sistema operacional e versão, endereço IP, nome DNS e o nome de estação de trabalho. A Microsoft não coleta nomes, endereços nem outras informações de contato.

Para obter mais informações sobre a coleta e uso de dados, consulte a [Política de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Agora que você habilitou o monitoramento para sua VM, informações de monitoramento está disponível para análise no Azure Monitor para máquinas virtuais.

## <a name="next-steps"></a>Próximas etapas

Para saber como usar o recurso Integridade, confira [Exibir a Integridade do Azure Monitor para VMs](vminsights-health.md). Para exibir as dependências de aplicativos descobertas, confira [Exibir o Mapa do Azure Monitor para VMs](vminsights-maps.md).
