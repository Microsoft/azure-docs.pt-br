---
title: Adicionar runbooks de automação do Azure a Site Recovery planos de recuperação
description: Saiba como estender planos de recuperação com a automação do Azure para recuperação de desastre usando Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: ramamill
ms.openlocfilehash: a141280338632fdad7053cbbe76c8bdf2797443d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89424864"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Adicionar runbooks de Automação do Azure aos planos de recuperação

Este artigo descreve como integrar runbooks de automação do Azure para estender [Azure site Recovery](site-recovery-overview.md) planos de recuperação. Mostraremos como automatizar tarefas básicas que, de outra forma, precisariam de intervenção manual e como converter uma recuperação de várias etapas em uma ação de clique único.

## <a name="recovery-plans"></a>Planos de recuperação 

Você pode usar planos de recuperação ao fazer failover de computadores locais ou VMs do Azure. Os planos de recuperação ajudam você a definir um processo de recuperação sistemático que define como os computadores fazem failover e como eles são iniciados e recuperados após o failover. 

Aplicativos grandes de recuperação podem ser complexos. Os planos de recuperação ajudam a impor a ordem para que a recuperação seja consistente, Reproduzível e automática. Você pode automatizar tarefas em um plano de recuperação usando scripts, bem como runbooks de automação do Azure. Exemplos típicos podem estar definindo configurações em uma VM do Azure após o failover ou reconfigurando um aplicativo em execução na VM.

- [Saiba mais](recovery-plan-overview.md) sobre planos de recuperação.
- [Saiba mais](../automation/automation-runbook-types.md) sobre Runbooks de automação do Azure.



## <a name="runbooks-in-recovery-plans"></a>Runbooks em planos de recuperação

Você adiciona uma conta de automação do Azure e runbooks a um plano de recuperação. O runbook é invocado quando o plano de recuperação é executado.

- A conta de automação pode estar em qualquer região do Azure e deve estar na mesma assinatura que o cofre de Site Recovery. 
- Um runbook pode ser executado em um plano de recuperação durante o failover de um local primário para secundário ou durante o failback do local secundário para o primário.
- Os Runbooks em um plano de recuperação são executados em série, um após o outro, na ordem definida.
- Se os runbooks em um plano de recuperação configurarem VMs para iniciar em grupos diferentes, o plano de recuperação continuará somente quando o Azure relatar todas as VMs como em execução.
- Os planos de recuperação continuam a ser executados, mesmo se um script falhar.

### <a name="recovery-plan-context"></a>Contexto do plano de recuperação

Quando um script é executado, ele injeta um contexto de plano de recuperação para o runbook. O contexto contém as variáveis resumidas na tabela.

| **Nome da variável** | **Descrição** |
| --- | --- |
| RecoveryPlanName |Nome do plano de recuperação. Usado em ações com base no nome. |
| FailoverType |Especifica se é um failover de teste ou de produção. 
| FailoverDirection | Especifica se a recuperação é para um local primário ou secundário. |
| GroupID |Identifica o número de grupo no plano de recuperação quando o plano está em execução. |
| VmMap |Uma matriz de todas as VMs do grupo. |
| Chave VMMap |Uma chave exclusiva (GUID) para cada VM. |
| SubscriptionId |A ID da assinatura do Azure em que a VM foi criada. |
| ResourceGroupName | Nome do grupo de recursos no qual a VM está localizada.
| CloudServiceName |O nome de serviço de nuvem do Azure no qual a VM foi criada. |
| RoleName |O nome da VM do Azure. |
| RecoveryPointId|O carimbo de data/hora para a recuperação da VM. |

>[!Note]
>O valor da variável ' A failoverdirection ' será ' PrimaryToSecondary ' no caso de failover e ' SecondaryToPrimary ' em caso de failback.

O seguinte exemplo mostra uma variável de contexto:

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

Se você quiser acessar todas as VMs no VMMap em um loop, poderá usar o seguinte código:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


O blog de Aman Sharma na [coleta de nuvens](http://harvestingclouds.com) tem um exemplo útil de um [script de contexto de plano de recuperação](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/).



## <a name="before-you-start"></a>Antes de começar

- Se você for novo na automação do Azure, poderá [se inscrever](https://azure.microsoft.com/services/automation/) e [baixar scripts de exemplo](https://azure.microsoft.com/documentation/scripts/).
- Garanta que a conta de Automação tem os seguintes módulos:
    - AzureRM.profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Todos os módulos devem ser de versões compatíveis. A maneira mais simples é sempre usar as versões mais recentes de todos os módulos.



## <a name="customize-the-recovery-plan"></a>Personalizar o plano de recuperação

1. No cofre, selecione **planos de recuperação (site Recovery)**
2. Para criar um plano de recuperação, clique em **+ plano de recuperação**. [Saiba mais](site-recovery-create-recovery-plans.md). Se você já tiver um plano de recuperação, selecione para abri-lo.
3. Na página plano de recuperação, clique em **Personalizar**.

    ![Clicar no botão Personalizar](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Clique nas reticências (...) ao lado de **grupo 1: iniciar**  >  **Adicionar ação de postagem**.
3. Em **Inserir ação**, verifique se o **script** está selecionado e especifique um nome para o script (**Olá, mundo**).
4. Especifique uma conta de automação e selecione um runbook. Para salvar o script, clique em **OK**. O script é adicionado ao **Grupo 1: Pós-etapas**.


## <a name="reuse-a-runbook-script"></a>Reutilizar um script de runbook

Você pode usar um único script de runbook em vários planos de recuperação usando variáveis externas. 

- Você usa [variáveis de automação do Azure](../automation/shared-resources/variables.md) para armazenar parâmetros para executar um plano de recuperação.
- Adicionando o nome do plano de recuperação como um prefixo da variável, você pode criar variáveis individuais para cada plano de recuperação. Em seguida, use as variáveis como parâmetros.
- Altere um parâmetro sem alterar o script, mas ainda altere a maneira como o script funciona.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Usar uma variável de cadeia de caracteres simples em um script de runbook

Neste exemplo, um script usa a entrada de um NSG (grupo de segurança de rede) e o aplica às VMs em um plano de recuperação. 

1. Para que o script possa detectar qual plano de recuperação está em execução, use este contexto do plano de recuperação:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Observe o nome do NSG e o grupo de recursos. Você usa essas variáveis como entradas para scripts do plano de recuperação. 
1. Nos ativos da conta de automação. Crie uma variável para armazenar o nome do NSG. Adicione um prefixo ao nome da variável com o nome do plano de recuperação.

    ![Criar uma variável do nome do NSG](media/site-recovery-runbook-automation-new/var1.png)

2. Crie uma variável para armazenar o nome do grupo de recursos para o recurso NSG. Adicione um prefixo ao nome da variável com o nome do plano de recuperação.

    ![Criar um nome do grupo de recursos do NSG](media/site-recovery-runbook-automation-new/var2.png)


3.  No script, use este código de referência para obter os valores de variáveis:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Use as variáveis no runbook para aplicar o NSG ao adaptador de rede da VM com failover:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


Para cada plano de recuperação, crie variáveis independentes, de modo que você possa reutilizar o script. Adicione um prefixo usando o nome do plano de recuperação. 

Para obter um script completo de ponta a ponta para esse cenário, examine [este script](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Usar uma variável complexa para armazenar mais informações

Em alguns cenários, talvez você não possa criar variáveis separadas para cada plano de recuperação. Considere um cenário no qual você deseja que um único script atribua um endereço IP público em VMs específicas. Em outro cenário, talvez você deseje aplicar NSGs diferentes a VMs diferentes (não em todas as VMs). Observe que:

- Você pode criar um script que é reutilizável para qualquer plano de recuperação.
- Cada plano de recuperação pode ter um número variável de VMs.
- Por exemplo, uma recuperação do SharePoint tem dois front-ends. Um aplicativo LOB (linha de negócios) básico tem apenas um front-end.
- Nesse cenário, você não pode criar variáveis separadas para cada plano de recuperação.

No exemplo a seguir, criamos uma [variável complexa](/powershell/module/servicemanagement/azure.service/set-azureautomationvariable) na conta de automação do Azure.

Fazemos isso especificando vários valores, usando Azure PowerShell.

1. No PowerShell, entre em sua assinatura do Azure:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Para armazenar os parâmetros, crie a variável complexa usando o nome do plano de recuperação:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. Nessa variável complexa, **VMDetails** é a ID de VM da VM protegida. Para obter a ID de VM, no portal do Azure, exiba as propriedades da VM. A seguinte captura de tela mostra uma variável que armazena os detalhes de duas VMs:

    ![Usar a ID de VM como o GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Use essa variável no runbook. Se o GUID de VM indicado for encontrado no contexto do plano de recuperação, aplique o NSG à VM:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. No runbook, percorra as VMs do contexto do plano de recuperação. Verifique se a VM existe em **$VMDetailsObj**. Se ela existir, acesse as propriedades da variável para aplicar o NSG:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Use o mesmo script para planos de recuperação diferentes. Insira parâmetros diferentes armazenando o valor que corresponde a um plano de recuperação em variáveis diferentes.

## <a name="sample-scripts"></a>Scripts de exemplo

Para implantar scripts de exemplo em sua conta de Automação, clique no botão **Implantar no Azure**.

[![Implantar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Este vídeo fornece outro exemplo. Ele demonstra como recuperar um aplicativo do WordPress de duas camadas no Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre uma [conta Executar como da automação do Azure](../automation/manage-runas-account.md)
- Examine os [scripts de exemplo da automação do Azure](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team).
- [Saiba mais](site-recovery-failover.md) sobre a execução de failovers.
