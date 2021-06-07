---
title: Visão geral do State Configuration da Automação do Azure
description: Este artigo fornece uma visão geral da State Configuration da Automação do Azure.
keywords: powershell dsc, configuração de estado desejada, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 01/26/2021
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 752d7f86941967c218b3a57fa163698b9f502057
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897013"
---
# <a name="azure-automation-state-configuration-overview"></a>Visão geral do State Configuration da Automação do Azure

A State Configuration da Automação do Azure é um serviço de gerenciamento de configuração do Azure que permite gravar, gerenciar e compilar [configurações](/powershell/scripting/dsc/configurations/configurations) de DSC (Desired State Configuration) do PowerShell para nós em qualquer nuvem ou datacenter local. O serviço também importa [recursos de DSC](/powershell/scripting/dsc/resources/resources) e atribui configurações aos nós de destino, tudo na nuvem. Você pode acessar a State Configuration da Automação do Azure no portal do Azure selecionando **DSC (State Configuration)** em **Gerenciamento de Configuração**.

A State Configuration da Automação do Azure pode ser usada para gerenciar uma variedade de computadores:

- Máquinas Virtuais do Azure
- Máquinas virtuais do Azure (clássico)
- Computadores físicos/máquinas virtuais locais do Windows ou em uma nuvem diferente do Azure (incluindo instâncias do AWS EC2)
- Máquinas físicas/virtuais locais do Linux, no Azure, ou em uma nuvem diferente do Azure

Além disso, se você não estiver pronto para gerenciar a configuração do computador por meio da nuvem, também poderá usar a State Configuration da Automação do Azure como um ponto de extremidade somente relatório. Esse recurso permite definir (enviar por push) configurações por meio de DSC e exibir detalhes de relatório na Automação do Azure.

> [!NOTE]
> O gerenciamento de VMs do Azure com a State Configuration da Automação do Azure será incluído sem custo adicional se a versão da extensão de Desired State Configuration da VM do Azure for maior que 2.70. Para saber mais, confira a [**página de preços da Automação**](https://azure.microsoft.com/pricing/details/automation/).

## <a name="why-use-azure-automation-state-configuration"></a>Por que usar a Configuração de Estado da Automação do Azure?

A State Configuration da Automação do Azure oferece várias vantagens em comparação com o uso do DSC fora do Azure. Esse serviço permite a escalabilidade entre milhares de computadores rápida e facilmente, de um local central e seguro. Você pode facilmente habilitar computadores, atribuir a eles configurações declarativas e exibir relatórios que mostram a conformidade de cada computador com o estado desejado especificado.

O serviço State Configuration da Automação do Azure representa para o DSC o mesmo que os runbooks da Automação do Azure representam para o script do PowerShell. Em outras palavras, da mesma forma que a Automação do Azure ajuda você a gerenciar os scripts do PowerShell, ela também ajuda a gerenciar configurações do DSC.

### <a name="built-in-pull-server"></a>Servidor de pull interno

A State Configuration da Automação do Azure fornece um servidor de pull de DSC semelhante ao [Serviço DSC de Recurso do Windows](/powershell/scripting/dsc/pull-server/pullserver). Os nós de destino podem receber configurações automaticamente, manter a conformidade com o estado desejado e relatar a conformidade deles. O servidor de pull interno na Automação do Azure elimina a necessidade de configurar e manter seu próprio servidor de pull. A Automação do Azure pode destinar computadores Windows ou Linux físicos ou virtuais, na nuvem ou localmente.

### <a name="management-of-all-your-dsc-artifacts"></a>Gerenciamento de todos os seus artefatos de DSC

A State Configuration da Automação do Azure oferece a mesma camada de gerenciamento para a [Desired State Configuration do PowerShell](/powershell/scripting/dsc/overview/overview) que oferece para scripts do PowerShell. No portal do Azure ou do PowerShell, você pode gerenciar todas as suas configurações, recursos e nós de destino da DSC.

![Captura de tela da página de Automação do Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importação de dados de relatório em logs de Azure Monitor

Nós gerenciados com a Configuração do Estado de Automação do Azure enviam dados de status de relatórios detalhados para o servidor de pull interno. É possível configurar a Configuração do Estado de Automação do Azure para enviar esses dados ao espaço de trabalho do Log Analytics. Confira [Encaminhar dados de relatório da State Configuration da Automação do Azure para logs do Azure Monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Pré-requisitos

Considere os requisitos nesta seção ao usar a State Configuration da Automação do Azure.

### <a name="operating-system-requirements"></a>Requisitos do sistema operacional

Para nós que executam o Windows, as seguintes versões são compatíveis:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>O SKU do produto autônomo do [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) não contém uma implementação de DSC. Portanto, ele não pode ser gerenciado pelo DSC do PowerShell nem pela State Configuration da Automação do Azure.

Para nós que executam o Linux, a extensão de DSC do Linux dá suporte a todas as distribuições do Linux listadas na [documentação do DSC do PowerShell](/powershell/scripting/dsc/getting-started/lnxgettingstarted).

### <a name="dsc-requirements"></a>Requisitos de DSC

Para todos os nós do Windows em execução no Azure, o [WMF 5.1](/powershell/scripting/wmf/setup/install-configure) é instalado quando as máquinas virtuais são habilitadas. Para nós que executam o Windows Server 2012 e o Windows 7, o [WinRM](/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) é habilitado.

Para todos os nós do Linux em execução no Azure, o [DSC do PowerShell para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) é instalado quando as máquinas virtuais são habilitadas.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configuração de redes privadas

Verifique a [configuração da rede de automação do Azure](automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration) para obter informações detalhadas sobre as portas, URLs e outros detalhes de rede necessários para os nós em uma rede privada.

#### <a name="proxy-support"></a>Suporte a proxy

O suporte a proxy para o agente DSC está disponível no Windows versão 1809 e posterior. Essa opção é habilitada definindo os valores para as propriedades `ProxyURL` e `ProxyCredential` no [script de metaconfiguração](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) usado para registrar nós.

>[!NOTE]
>A State Configuration da Automação do Azure não é compatível com proxy de DSC para versões anteriores do Windows.

Para os nós do Linux, o agente DSC oferece suporte a proxy e usa a variável `http_proxy` para determinar a URL. Para saber mais sobre o suporte a proxy, confira [Gerar metaconfigurações DSC](automation-dsc-onboarding.md#generate-dsc-metaconfigurations).

#### <a name="dns-records-per-region"></a>Registros DNS por região

É recomendável usar os endereços listados na tabela [registros DNS por região](how-to/automation-region-dns-records.md) ao definir exceções.

## <a name="next-steps"></a>Próximas etapas

- Para começar, veja [Introdução ao serviço State Configuration da Automação do Azure](automation-dsc-getting-started.md).
- Para saber como habilitar os nós, veja [Habilitar o serviço State Configuration da Automação do Azure](automation-dsc-onboarding.md).
- Para saber como compilar configurações DSC para que possam ser atribuídas a nós de destino, confira [Compilar configurações DSC no State Configuration da Automação do Azure](automation-dsc-compile.md).
- Para ver um exemplo de uso do State Configuration da Automação do Azure em um pipeline de implantação contínua, confira [Configurar a implantação contínua com o Chocolatey](automation-dsc-cd-chocolatey.md).
- Para saber mais sobre preços, confira [Preços do State Configuration da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
