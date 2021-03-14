---
title: Implantar um Hybrid Runbook Worker do Linux na Automação do Azure
description: Este artigo informa como instalar um Hybrid Runbook Worker de automação do Azure para executar runbooks em computadores baseados em Linux em seu datacenter local ou ambiente de nuvem.
services: automation
ms.subservice: process-automation
ms.date: 02/26/2021
ms.topic: conceptual
ms.openlocfilehash: d4d9bcd16e36e76808f19f7fbd43dd0d3e7550c3
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182325"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Implantar o Hybrid Runbook Worker do Linux

Você pode usar o recurso Hybrid Runbook Worker do usuário da automação do Azure para executar runbooks diretamente no computador do Azure ou não Azure, incluindo servidores registrados com [servidores habilitados para Arc do Azure](../azure-arc/servers/overview.md). No computador ou servidor que está hospedando a função, você pode executar runbooks diretamente e em recursos no ambiente para gerenciar esses recursos locais.

O Linux Hybrid Runbook Worker executa runbooks como um usuário especial que pode ser elevado para executar comandos que precisam de elevação. A automação do Azure armazena e gerencia runbooks e, em seguida, os entrega a um ou mais computadores designados. Este artigo descreve como instalar o Hybrid Runbook Worker em um computador Linux, como remover o trabalho e como remover um grupo do Hybrid Runbook Worker.

Depois de implantar com êxito um trabalhador de runbook, revise [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem o seguinte.

### <a name="a-log-analytics-workspace"></a>Um workspace do Log Analytics

A função Hybrid Runbook Worker depende de um espaço de trabalho de Log Analytics de Azure Monitor para instalar e configurar a função. Você pode criá-lo por meio de [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), por meio do [PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json), ou no [portal do Azure](../azure-monitor/logs/quick-create-workspace.md).

Se você não tiver um Azure Monitor Log Analytics espaço de trabalho, examine as [diretrizes de design do Azure monitor log](../azure-monitor/logs/design-logs-deployment.md) antes de criar o espaço de trabalho.

### <a name="log-analytics-agent"></a>Agente do Log Analytics

A função Hybrid Runbook Worker requer o [agente de log Analytics](../azure-monitor/agents/log-analytics-agent.md) para o sistema operacional Linux com suporte. Para servidores ou computadores hospedados fora do Azure, você pode instalar o agente de Log Analytics usando os [servidores habilitados para Arc do Azure](../azure-arc/servers/overview.md).

>[!NOTE]
>Depois de instalar o agente de Log Analytics para Linux, você não deve alterar as permissões da `sudoers.d` pasta ou sua propriedade. A permissão sudo é necessária para a conta **nxautomation** , que é o contexto do usuário no qual a Hybrid runbook Worker é executada. As permissões não devem ser removidas. Restringir isso a determinadas pastas ou comandos pode resultar em uma alteração significativa.
>

### <a name="supported-linux-operating-systems"></a>Sistemas operacionais Linux com suporte

O recurso Hybrid Runbook Worker dá suporte às seguintes distribuições. Todos os sistemas operacionais são considerados x64. Não há suporte para x86 em nenhum sistema operacional.

* Amazon Linux 2012, 9 a 2015, 9
* CentOS Linux 5, 6, 7 e 8
* Oracle Linux 5, 6 e 7
* Red Hat Enterprise Linux Server 5, 6, 7 e 8
* Debian GNU/Linux 6, 7 e 8
* Ubuntu 12, 4 LTS, 14, 4 LTS, 16, 4 LTS e 18, 4 LTS
* SUSE Linux Enterprise Server 12 e 15 (o SUSE não liberou as versões 13 ou 14)

> [!IMPORTANT]
> Antes de habilitar o recurso Gerenciamento de Atualizações, que depende da função de Hybrid Runbook Worker do sistema, confirme as distribuições às quais ele dá suporte [aqui](update-management/overview.md#supported-operating-systems).

### <a name="minimum-requirements"></a>Requisitos mínimos

Os requisitos mínimos para um sistema Linux e Hybrid Runbook Worker de usuário são:

* Dois núcleos
* 4 GB de RAM
* Porta 443 (saída)

| **Pacote necessário** | **Descrição** | **Versão mínima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteca GNU C| 2.5-12 |
|Openssl| Bibliotecas OpenSSL | 1.0 (há suporte para TLS 1.1 e TLS 1.2)|
|Curl | cliente Web cURL | 7.15.5|
|Python-ctypes | Python 2. x ou Python 3. x são necessários |
|PAM | Módulos de autenticação conectáveis|
| **Pacotes opcionais** | **Descrição** | **Versão mínima**|
| PowerShell Core | Para executar runbooks do PowerShell, o PowerShell Core precisa ser instalado. Consulte [Instalar PowerShell Core no Linux](/powershell/scripting/install/installing-powershell-core-on-linux) para saber como instalá-lo. | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Adicionando um computador a um grupo de Hybrid Runbook Worker

Você pode adicionar o computador de trabalho a um grupo de Hybrid Runbook Worker em uma de suas contas de automação. Para computadores que hospedam o Hybrid runbook Worker do sistema gerenciado pelo Gerenciamento de Atualizações, eles podem ser adicionados a um grupo de Hybrid Runbook Worker. Mas você deve usar a mesma conta de automação para Gerenciamento de Atualizações e a associação de grupo de Hybrid Runbook Worker.

>[!NOTE]
>A automação do Azure [Gerenciamento de atualizações](./update-management/overview.md) instala automaticamente o Hybrid runbook Worker do sistema em um computador Azure ou não Azure habilitado para gerenciamento de atualizações. No entanto, esse trabalhador não está registrado com nenhum grupo de Hybrid Runbook Worker em sua conta de automação. Para executar seus runbooks nesses computadores, você precisa adicioná-los a um grupo de Hybrid Runbook Worker. Siga a etapa 4 na seção [instalar um Hybrid runbook Worker Linux](#install-a-linux-hybrid-runbook-worker) para adicioná-lo a um grupo.

## <a name="supported-linux-hardening"></a>Proteção do Linux com suporte

Os itens a seguir ainda não têm suporte:

* ICS

## <a name="supported-runbook-types"></a>Tipos de runbook com suporte

Os Hybrid runbook Workers do Linux dão suporte a um conjunto limitado de tipos de runbook na automação do Azure, e eles são descritos na tabela a seguir.

|Tipo de runbook | Com suporte |
|-------------|-----------|
|Python 3 (visualização)|Sim, necessário somente para esses distribuições: SUSE LES 15, RHEL 8 e CentOS 8|
|Python 2 |Sim, para qualquer distribuição que não exija Python 3<sup>1</sup> |
|PowerShell |Sim<sup>2</sup> |
|Fluxo de trabalho do PowerShell |Não |
|Gráfico |Não |
|Fluxo de Trabalho Gráfico do PowerShell |Não |

<sup>1</sup> Consulte [sistemas operacionais Linux com suporte](#supported-linux-operating-systems).

<sup>2</sup> Os runbooks do PowerShell exigem que o PowerShell Core seja instalado no computador Linux. Consulte [Instalar PowerShell Core no Linux](/powershell/scripting/install/installing-powershell-core-on-linux) para saber como instalá-lo.

### <a name="network-configuration"></a>Configuração de rede

Para os requisitos de rede para o Hybrid Runbook Worker, consulte [Configurando sua rede](automation-hybrid-runbook-worker.md#network-planning).

## <a name="install-a-linux-hybrid-runbook-worker"></a>Instalar um Hybrid Runbook Worker do Linux

Para instalar e configurar um Hybrid Runbook Worker do Linux, execute as etapas a seguir.

1. Habilite a solução de automação do Azure em seu espaço de trabalho Log Analytics executando o seguinte comando em um prompt de comando do PowerShell com privilégios elevados ou em Cloud Shell no [portal do Azure](https://portal.azure.com):

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Implante o agente de Log Analytics no computador de destino.

    * Para VMs do Azure, instale o agente de Log Analytics para Linux usando a [extensão de máquina virtual para Linux](../virtual-machines/extensions/oms-linux.md). A extensão instala o agente do Log Analytics em máquinas virtuais do Azure e registra máquinas virtuais em um espaço de trabalho do Log Analytics existente. Você pode usar um modelo de Azure Resource Manager, o CLI do Azure ou Azure Policy para atribuir a política de [implantação do agente de log Analytics para *Linux* ou VMS do *Windows*](../governance/policy/samples/built-in-policies.md#monitoring) integrada. Depois que o agente é instalado, o computador pode ser adicionado a um grupo de Hybrid Runbook Worker em sua conta de automação.

    * Para computadores não Azure, você pode instalar o agente de Log Analytics usando os [servidores habilitados para Arc do Azure](../azure-arc/servers/overview.md). Os servidores habilitados para Arc dão suporte à implantação do agente de Log Analytics usando os seguintes métodos:

        - Usando a estrutura de extensões de VM.

            Esse recurso nos servidores habilitados para Arc do Azure permite que você implante a extensão de VM do agente de Log Analytics em um servidor Windows e/ou Linux que não seja do Azure. As extensões de VM podem ser gerenciadas usando os seguintes métodos em seus computadores híbridos ou servidores gerenciados por servidores habilitados para Arc:

            - O [Portal do Azure](../azure-arc/servers/manage-vm-extensions-portal.md)
            - A [CLI do Azure](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [PowerShell do Azure](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Modelos do Azure [Resource Manager](../azure-arc/servers/manage-vm-extensions-template.md)

        - Usando Azure Policy.

            Usando essa abordagem, você usa o Azure Policy [implantar o agente log Analytics em](../governance/policy/samples/built-in-policies.md#monitoring) uma política interna do Windows Azure ARC para máquinas de arco para a auditoria se o servidor habilitado para Arc tiver o agente de log Analytics instalado. Se o agente não estiver instalado, ele o implantará automaticamente usando uma tarefa de correção. Como alternativa, se você planeja monitorar as máquinas com Azure Monitor para VMs, use a iniciativa [habilitar Azure monitor para VMs](../governance/policy/samples/built-in-initiatives.md#monitoring) para instalar e configurar o agente de log Analytics.

        É recomendável instalar o agente de Log Analytics para Windows ou Linux usando Azure Policy.

    > [!NOTE]
    > Para gerenciar a configuração de computadores que dão suporte à função de Hybrid Runbook Worker com a DSC (configuração de estado desejado), você deve adicionar os computadores como nós DSC.

    > [!NOTE]
    > A conta de [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) com as permissões sudo correspondentes deve estar presente durante a instalação do Hybrid Worker do Linux. Se você tentar instalar o trabalho e a conta não estiver presente ou não tiver as permissões apropriadas, a instalação falhará.

3. Verifique se o agente está relatando para o espaço de trabalho.

    O agente de Log Analytics para Linux conecta computadores a um espaço de trabalho Azure Monitor Log Analytics. Quando você instala o agente em seu computador e o conecta ao seu espaço de trabalho, ele baixa automaticamente os componentes necessários para o Hybrid Runbook Worker.

    Quando o agente tiver se conectado ao seu workspace do Log Analytics após alguns minutos, você poderá executar a consulta a seguir para verificar se ele está enviando dados de pulsação para o workspace.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Nos resultados da pesquisa, você deve ver os registros de pulsação do computador, indicando que ele está conectado e relatando para o serviço. Por padrão, cada agente encaminha um registro de pulsação para seu workspace atribuído.

4. Execute o comando a seguir para adicionar o computador a um grupo de Hybrid runbook Worker, especificando os valores para os parâmetros `-w` , `-k` , `-g` e `-e` .

    Você pode obter as informações necessárias para parâmetros `-k` e `-e` na página **chaves** em sua conta de automação. Selecione **chaves** na seção **configurações de conta** do lado esquerdo da página.

    ![Página Gerenciar Chaves](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Para o `-e` parâmetro, copie o valor para **URL**.

    * Para o `-k` parâmetro, copie o valor da **chave de acesso primária**.

    * Para o `-g` parâmetro, especifique o nome do grupo de Hybrid runbook Worker ao qual o novo Hybrid runbook Worker do Linux deve ingressar. Se esse grupo já existir na conta de automação, o computador atual será adicionado a ele. Se esse grupo não existir, ele será criado com esse nome.

    * Para o `-w` parâmetro, especifique sua ID de espaço de trabalho log Analytics.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Verifique a implantação após a conclusão do script. Na página **grupos de Hybrid runbook Worker** em sua conta de automação, na guia **grupo Hybrid runbook Workers do usuário** , ele mostra o grupo novo ou existente e o número de membros. Se for um grupo existente, o número de membros é incrementado. Você pode selecionar o grupo na lista na página, no menu à esquerda, escolher **Hybrid Workers**. Na página **Hybrid Workers** , você pode ver cada membro do grupo listado.

    > [!NOTE]
    > Se você estiver usando a extensão de máquina virtual Log Analytics para Linux para uma VM do Azure, é recomendável definir `autoUpgradeMinorVersion` `false` como as versões de atualização automática podem causar problemas com o Hybrid runbook Worker. Para saber como atualizar a extensão manualmente, confira [Implantação da CLI do Azure](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Desativar a validação de assinatura

Por padrão, o Linux Hybrid Runbook Workers exige validação de assinatura. Se você executar um runbook não assinado em um trabalho, verá um erro `Signature validation failed`. Para desativar a validação de assinatura, execute o comando a seguir. Substitua o segundo parâmetro pela ID do workspace do Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>Remover o Hybrid Runbook Worker

Você pode usar o comando `ls /var/opt/microsoft/omsagent` no Hybrid Runbook Worker para obter a ID do workspace. É criada uma pasta nomeada com a ID do workspace.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Esse script não remove o agente de Log Analytics para Linux do computador. Ele remove apenas a funcionalidade e a configuração da função Hybrid Runbook Worker.

## <a name="remove-a-hybrid-worker-group"></a>Remover um grupo de Hybrid Worker

Para remover um grupo de Hybrid Runbook Worker de computadores Linux, use as mesmas etapas de um grupo do Hybrid Worker do Windows. Confira [Remover um grupo do Hybrid Worker](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Para saber como solucionar problemas de seus Hybrid Runbook Workers, confira [Solucionar problemas do Hybrid Runbook Worker – Linux](troubleshoot/hybrid-runbook-worker.md#linux).