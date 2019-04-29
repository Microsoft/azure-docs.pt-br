---
title: Hybrid Runbook Worker do Windows de Automação do Azure
description: Este artigo fornece informações sobre a instalação de um Trabalhador de Runbook Híbrido de Automação do Azure que você pode usar para executar runbooks em computadores baseados no Windows em seu datacenter ou ambiente de nuvem local.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0cc00b4f2075ba77490d310080b9968bedb8dc1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304931"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implantar um Windows híbrido Runbook Worker

É possível usar o recurso Hybrid Runbook Worker da Automação do Azure para executar os runbooks diretamente no computador que hospeda a função e os recursos do ambiente para gerenciar esses recursos locais. Os runbooks são armazenados e gerenciados na Automação do Azure e entregues a um ou mais computadores designados. Este artigo descreve como instalar o Hybrid Runbook Worker em uma máquina Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalação do Hybrid Runbook Worker do Windows

Para instalar e configurar um Windows Hybrid Runbook Worker, você pode usar dois métodos. O método recomendado é usar um runbook de automação para automatizar completamente o processo de configuração de um computador com Windows. O segundo método é seguir um procedimento passo a passo para instalar e configurar a função manualmente.

> [!NOTE]
> Para gerenciar a configuração de seus servidores que suportam a função de executante de caderno híbrido com DSC (Configuração de estado desejado), você precisa adicioná-los como nós DSC.

Os requisitos mínimos para um Windows Hybrid Runbook Worker são:

* Windows Server 2012 ou posterior.
* Windows PowerShell 5.1 ou posterior ([baixe o WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)).
* .NET Framework 4.6.2 ou posterior.
* Dois núcleos.
* 4 GB de RAM.
* Porta 443 (saída).

Para obter mais requisitos de rede para o Hybrid Runbook Worker, consulte [ Configurando sua rede ](automation-hybrid-runbook-worker.md#network-planning).

Para obter mais informações sobre servidores de integração para gerenciamento com o DSC, consulte [Máquinas de integração para gerenciamento pelo DSC de automação do Azure](automation-dsc-onboarding.md).
Se você ativar a [ solução de Gerenciamento de Atualizações ](../operations-management-suite/oms-solution-update-management.md), qualquer computador Windows conectado ao seu espaço de trabalho do Log Analytics do Azure será automaticamente configurado como um Trabalhador de Runbook Híbrido para oferecer suporte a runbooks incluídos nessa solução. No entanto, ele não está registrado em nenhum grupo de Hybrid Worker já definido em sua conta de automação. 

O computador pode ser adicionado a um grupo de executável de manual híbrido em sua conta de automação para oferecer suporte a registros de execução de automação, desde que você esteja usando a mesma conta para a solução e a associação de grupo de trabalhador de executável híbrido. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

Depois de implantar com êxito um trabalhador de runbook, revise [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

### <a name="automated-deployment"></a>Implantação automatizada

Execute as etapas a seguir para automatizar a instalação e configuração da função do Windows Hybrid Worker:

1. Faça o download do script New-OnPremiseHybridWorker.ps1 da [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) diretamente do computador que executa a função de executor de manual híbrido ou de outro computador em seu ambiente. Copie o script para o trabalhador.

   O script New-OnPremiseHybridWorker.ps1 requer os seguintes parâmetros durante a execução:

   * *AutomationAccountName* (obrigatório): O nome da sua conta de automação.
   * *AAResourceGroupName* (obrigatório): O nome do grupo de recursos associado à sua conta de automação.
   * *OMSResourceGroupName* (opcional): O nome do grupo de recursos do espaço de trabalho do Log Analytics. Se este grupo de recursos não for especificado, *AAResourceGroupName* será usado.
   * *HybridGroupName* (obrigatório): O nome de um grupo do Hybrid Runbook Worker que você especifica como um destino para os runbooks que dão suporte a esse cenário.
   * *SubscriptionID* (obrigatório): A ID de assinatura do Azure em que sua conta de automação está.
   * *WorkspaceName* (opcional): O nome do espaço de trabalho do Log Analytics. Se você não tiver um espaço de trabalho do Log Analytics, o script cria e configura um.

     > [!NOTE]
     > Atualmente, são as únicas regiões de automação com suporte para integração com os logs do Azure Monitor **Sudeste da Austrália**, **Leste dos EUA 2**, **Sudeste Asiático**e **Europa Ocidental**. Se a sua conta de automação não estiver em uma dessas regiões, o script criará um espaço de trabalho do Log Analytics, mas avisará que não é possível vinculá-los.

2. No computador, abra o **Windows PowerShell** na tela **Iniciar** no modo Administrador.
3. No shell de linha de comando do PowerShell, navegue até a pasta que contém o script que você baixou. Altere os valores para os parâmetros *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-ScriptioncriptionId* e *-WorkspaceName*. Em seguida, execute o script.

     > [!NOTE]
     > Você é solicitado a autenticar com o Azure depois de executar o script. Você *deve* fazer login com uma conta que seja membro da função Administradores da inscrição e co-administrador da assinatura.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Você será solicitado a concordar em instalar o NuGet e será solicitado a autenticar com suas credenciais do Azure.

5. Depois que o script for concluído, a página **Grupos de Trabalhadores Hybrid** mostrará o novo grupo e o número de membros. Se for um grupo existente, o número de membros é incrementado. Você pode selecionar o grupo da lista na página **Grupos do Hybrid Worker** e no bloco **Hybrid Workers**. Na página **Hybrid Workers**, você verá cada membro do grupo listado.

### <a name="manual-deployment"></a>Implantação manual

Execute as duas primeiras etapas uma vez para o seu ambiente de automação e repita as etapas restantes para cada computador de trabalho.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. Criar um espaço de trabalho do Log Analytics

Se você ainda não possui um espaço de trabalho do Log Analytics, crie uma usando as instruções em [Gerenciar seu espaço de trabalho](../azure-monitor/platform/manage-access.md). Você pode usar um workspace existente se já tiver um.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Adicione a solução de automação ao espaço de trabalho do Log Analytics

A solução de logs de automação do Azure Monitor adiciona a funcionalidade de automação do Azure, incluindo suporte para o Hybrid Runbook Worker. Quando você adiciona a solução ao seu workspace, ele envia automaticamente os componentes do trabalhador para o computador do agente que você instalará na próxima etapa.

Para adicionar o **automação** Azure Monitor registra a solução ao espaço de trabalho, execute o seguinte PowerShell.

```powershell-interactive
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Instalar o Microsoft Monitoring Agent

O Microsoft Monitoring Agent conecta computadores para os logs do Azure Monitor. Quando você instala o agente em seu computador local e o conecta ao seu workspace, ele faz o download automaticamente dos componentes necessários para o Hybrid Runbook Worker.

Para instalar o agente no computador local, siga as instruções em [computadores Windows conectar-se aos logs do Azure Monitor](../log-analytics/log-analytics-windows-agent.md). Você poderá repetir esse processo em vários computadores para adicionar vários trabalhos ao ambiente.

Quando o agente se conectou com êxito para os logs do Azure Monitor, ele será listado na **fontes conectadas** guia do log analytics **configurações** página. Você pode verificar se o agente baixou corretamente a solução de Automação quando ele tiver uma pasta chamada **AzureAutomationFiles** em C:\Arquivos de Programas\Microsoft Monitoring Agent\Agent. Para confirmar a versão do Hybrid Runbook Worker, você pode procurar C: \ Arquivos de Programas \ Microsoft Monitoring Agent \ Agent \ AzureAutomation \ e observar a subpasta \\*versão*.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Instalar o ambiente de runbook e conectá-lo à Automação do Azure

Quando você adiciona um agente para logs do Azure Monitor, a solução de automação envia por push o **HybridRegistration** módulo do PowerShell, que contém o **Add-HybridRunbookWorker** cmdlet. Você pode usar esse cmdlet para instalar o ambiente de runbook no computador e registrá-lo na Automação do Azure.

Abra uma sessão do PowerShell no modo Administrador e execute os seguintes comandos para importar o módulo:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Em seguida, execute o **cmdlet Add-HybridRunbookWorker** usando a seguinte sintaxe:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Você pode obter as informações necessárias para esse cmdlet na página **Gerenciar Chaves** no portal do Azure. Abra esta página selecionando a opção **Chaves** na página **Configurações** na sua conta de automação.

![Página "Gerenciar Chaves"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** é o nome do grupo Hybrid Runbook Worker. Se esse grupo já existir na conta de automação, o computador atual será adicionado a ele. Se esse grupo não existir, será adicionado.
* **EndPoint** é a entrada do **URL** na página **Gerenciar Chaves**.
* **O token** é a entrada **da CHAVE PRIMÁRIA DE ACESSO** na página **Gerenciar Chaves**.

Para receber informações detalhadas sobre a instalação, use a opção **-Verbose** com **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Instalar módulos do PowerShell

Os runbooks podem usar qualquer uma das atividades e cmdlets definidos nos módulos instalados no ambiente de Automação do Azure. Esses módulos não são implantados automaticamente em computadores locais, portanto, você deve instalá-los manualmente. A exceção é o módulo do Azure, que é instalado por padrão e fornece acesso a cmdlets para todos os serviços e atividades do Azure para automação do Azure.

Como o objetivo principal do recurso Hybrid Runbook Worker é gerenciar recursos locais, você provavelmente precisará instalar os módulos que suportam esses recursos. Para obter informações sobre como instalar módulos do Windows PowerShell, consulte [Instalando módulos](/powershell/developer/windows-powershell). 

Os módulos instalados devem estar em um local referenciado pela variável de ambiente **PSModulePath** para que o trabalhador híbrido possa importá-los automaticamente. Para obter mais informações, consulte [Modificando o caminho de instalação do PSModulePath](/powershell/developer/windows-powershell).

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Para obter instruções sobre como remover os Hybrid Runbook Workers, consulte [Remover Hybrid Runbook Workers da Automação do Azure](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Para saber como solucionar problemas de seus Trabalhadores de Runbook Híbridos, consulte [Solucionando problemas de trabalhadores de runbook híbrido do Windows](troubleshoot/hybrid-runbook-worker.md#windows)
* Para obter etapas adicionais sobre como solucionar problemas com o Gerenciamento de Atualizações, consulte [Gerenciamento de Atualizações: solução de problemas](troubleshoot/update-management.md).
