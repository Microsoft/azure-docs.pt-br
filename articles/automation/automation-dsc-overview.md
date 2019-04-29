---
title: Visão geral da Configuração de Estado da Automação do Azure
description: Uma visão geral da Configuração do Estado de Automação do Azure (DSC), seus termos e problemas conhecidos
keywords: powershell dsc, configuração de estado desejada, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: da746d80e3ae1fa5cc02683a8bb0ff0402722b8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61071442"
---
# <a name="azure-automation-state-configuration-overview"></a>Visão geral da Configuração de Estado da Automação do Azure

A Configuração do Estado de Automação do Azure é um serviço do Azure que permite gravar, gerenciar e compilar [configurações](/powershell/dsc/configurations) de DSC (Desired State Configuration) do PowerShell, importar [recursos de DSC](/powershell/dsc/resources) e atribuir configurações a nós de destino, tudo na nuvem.

## <a name="why-use-azure-automation-state-configuration"></a>Por que usar a Configuração de Estado da Automação do Azure?

A Configuração do Estado de Automação do Azure oferece várias vantagens a usar o DSC fora do Azure.

### <a name="built-in-pull-server"></a>Servidor de pull interno

A Configuração do Estado Automação do Azure fornece um servidor de recepção DSC semelhante ao [Serviço de DSC do Recurso do Windows](/powershell/dsc/pullserver) para que os nós de destino recebam automaticamente configurações, estejam em conformidade com o estado desejado e relatem sua conformidade. O servidor de pull interno na Automação do Azure elimina a necessidade de configurar e manter seu próprio servidor de pull. A Automação do Azure pode destinar computadores Windows ou Linux físicos ou virtuais, na nuvem ou localmente.

### <a name="management-of-all-your-dsc-artifacts"></a>Gerenciamento de todos os seus artefatos de DSC

A Configuração do Estado de Automação do Azure oferece a mesma camada de gerenciamento para a [Desired State Configuration do PowerShell](/powershell/dsc/overview) que a Automação do Azure oferece para scripts do PowerShell.

No portal do Azure ou do PowerShell, você pode gerenciar todas as suas configurações, recursos e nós de destino da DSC.

![Captura de tela da página de Automação do Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importar dados de relatórios para logs do Azure Monitor

Nós gerenciados com a Configuração do Estado de Automação do Azure enviam dados de status de relatórios detalhados para o servidor de pull interno. É possível configurar a Configuração do Estado de Automação do Azure para enviar esses dados ao espaço de trabalho do Log Analytics. Para saber como enviar dados de status de configuração de estado para seu espaço de trabalho do Log Analytics, consulte [encaminhar configuração automação do Azure estado dados de relatórios para logs do Azure Monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes requisitos ao usar a configuração de estado na automação do Azure (DSC).

### <a name="operating-system-requirements"></a>Requisitos do sistema operacional

Para nós executando o Windows, há suporte para as seguintes versões:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

Para nós que executam o Linux, há suporte para as seguintes distribuições/versões:

A extensão de DSC Linux dá suporte a todas as distribuições do Linux [endossada no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , exceto:

Distribuição | Versão
-|-
Debian  | todas as versões
Ubuntu  | 18.04

### <a name="dsc-requirements"></a>Requisitos de DSC

Para todos os nós do Windows em execução no Azure, [WMF 5.1](https://docs.microsoft.com/powershell/wmf/5.1/install-configure) será instalado durante a integração.  Para nós que executam o Windows Server 2012 e Windows 7, [WinRM será habilitado](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency).

Para todos os nós do Linux em execução no Azure, [PowerShell DSC para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) será instalado durante a integração.

### <a name="network-planning"></a>Configurar redes privadas

Se os nós estão localizados em uma rede privada, a porta e as URLs a seguir são necessárias para o estado de configuração (DSC) para se comunicar com a automação:

* Porta: Somente a TCP 443 é necessária para acesso à Internet de saída.
* URL global: *.azure-automation.net
* URL global do EUA Gov Virgínia: *.azure automation.us
* Serviço de agente: https://\<workspaceId\>.agentsvc.azure-automation.net

É recomendável usar os endereços listados ao definir exceções. Para endereços IP, baixe os [Intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Esse arquivo é atualizado semanalmente e tem os intervalos atualmente implantados e as alterações futuras nos intervalos de IP.

Se você tiver uma conta de Automação do Azure definida para uma região específica, você pode restringir a comunicação para esse centro de dados regional. A tabela a seguir fornece o registro DNS para cada região:

| **Região** | **Registro DNS** |
| --- | --- |
| Centro-Oeste dos EUA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Centro-Sul dos Estados Unidos |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Leste dos EUA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Norte da Europa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sudeste da Ásia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gov. dos EUA – Virgínia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Para obter uma lista de endereços IP da região em vez de nomes da região, faça o download do arquivo XML do [Endereço IP do Centro de Dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653) do Centro de Download da Microsoft.

> [!NOTE]
> O arquivo XML do endereço IP do centro de dados do Azure lista os intervalos de endereços IP que são usados nos centros de dados do Microsoft Azure. O arquivo inclui intervalos de computação, SQL e armazenamento.
>
>Um arquivo atualizado é postado semanalmente. O arquivo reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos centros de dados por pelo menos uma semana.
>
> É uma boa ideia fazer o download do novo arquivo XML toda semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Azure ExpressRoute devem observar que esse arquivo é usado para atualizar o anúncio BGP (Border Gateway Protocol) do espaço do Azure na primeira semana de cada mês.

## <a name="introduction-video"></a>Vídeo de introdução

Prefere assistir do que ler? Assista ao vídeo abaixo, de maio de 2015, quando a Configuração do Estado de Automação do Azure foi anunciada pela primeira vez.

> [!NOTE]
> Embora os conceitos e o ciclo de vida abordados neste vídeo estejam corretos, a Configuração do Estado de Automação do Azure avançou muito desde que este vídeo foi gravado. Agora ele está disponível totalmente, tem uma interface do usuário mais ampla no Portal do Azure e dá suporte a vários recursos adicionais.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [Introdução à Configuração de Estado da Automação do Azure](automation-dsc-getting-started.md)
- Para saber mai sobre nós de integração, veja [Máquinas de integração para o gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md)
- Para saber como compilar configurações de DSC para que possam ser atribuídas a nós de destino, consulte [Compilar configurações na Configuração de Estado da Automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [Cmdlets da Configuração de Estado da Automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [Preço da Configuração de Estado da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de uso da Configuração de Estado da Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua usando Configuração de Estado da Automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
