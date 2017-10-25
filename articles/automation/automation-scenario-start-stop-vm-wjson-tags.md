---
title: Usar marcas formatada em JSON para agendar o estado da VM do Azure | Microsoft Docs
description: "Este artigo demonstra como usar cadeias de caracteres JSON em marcações para automatizar o agendamento do desligamento e inicialização de VM."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 6afed5d2-e939-4749-8b2c-9312b4c16fb2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: magoedte;paulomarquesc
ms.openlocfilehash: cae4020741003be54b133efa121b3c09b859a176
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario-using-json-formatted-tags-to-create-a-schedule-for-azure-vm-startup-and-shutdown"></a>Cenário de Automação do Azure: usando marcações formatadas JSON para criar um agendamento para inicialização e desligamento de VM do Azure
Muitas vezes, os clientes desejam agendar a inicialização e desligamento de máquinas virtuais para ajudar a reduzir os custos de assinatura ou dar suporte a requisitos técnicos e de negócios.

O cenário a seguir permite a você configurar a inicialização e o desligamento automatizados de suas VMs usando uma marcação chamada de Agendamento no nível do grupo de recursos ou da máquina virtual no Azure. Esse agendamento pode ser configurado de domingo a sábado com hora de inicialização e de desligamento.

Temos algumas opções imediatas. Estão incluídos:

* [Conjuntos de escala de máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) com configurações de dimensionamento automático que permitem a você escalar ou reduzir horizontalmente.
* [DevTest Labs](../devtest-lab/devtest-lab-overview.md) , que tem o recurso interno de agendamento de operações de inicialização e desligamento.

No entanto, essas opções dão suporte somente a cenários específicos e não podem ser aplicadas às VMs de IaaS (infraestrutura como serviço).

Quando a marcação Agendamento for aplicada a um grupo de recursos, ela também será aplicada a todas as máquinas virtuais dentro daquele grupo de recursos. Se um agendamento também for aplicado diretamente a uma VM, o último agendamento terá precedência na seguinte ordem:

1. Agendamento aplicado a um grupo de recursos
2. Agendamento aplicado a um grupo de recursos e à máquina virtual no grupo de recursos
3. Agendamento aplicado a uma máquina virtual

Essencialmente, esse cenário usa uma cadeia de caracteres JSON com um formato especificado e a adiciona como um valor de uma marcação chamada Agendamento. Em seguida, um runbook lista todos os grupos de recursos e máquinas virtuais e identifica as agendas para cada VM com base nos cenários listados anteriormente. Em seguida, ele percorre as VMs que têm agendas anexadas e avalia a ação que deve ser executada. Por exemplo, ele determina quais máquinas virtuais precisam ser interrompidas, desligadas ou ignoradas.

Esses runbooks são autenticados usando a [conta Run As do Azure](automation-sec-configure-azure-runas-account.md).

## <a name="download-the-runbooks-for-the-scenario"></a>Baixar os runbooks para o cenário
Este cenário consiste em quatro runbooks de Fluxo de Trabalho do PowerShell que podem ser baixados da [Galeria do TechNet](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7) ou do repositório do [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) para esse projeto.

| Runbook | Descrição |
| --- | --- |
| Test-ResourceSchedule |Verifica cada agenda de máquina virtual e executa o desligamento ou inicialização, dependendo do agendamento. |
| Add-ResourceSchedule |Adiciona a marcação Agendamento a uma VM ou grupo de recursos. |
| Update-ResourceSchedule |Modifica a marcação Agendamento existente substituindo-a por uma nova. |
| Remove-ResourceSchedule |Remove a marcação Agendamento de uma VM ou grupo de recursos. |

## <a name="install-and-configure-this-scenario"></a>Instalar e configurar esse cenário
### <a name="install-and-publish-the-runbooks"></a>Instalar e publicar os runbooks
Depois de baixar os runbooks, você poderá importá-los usando o procedimento em [Criando ou importando um runbook na Automação do Azure](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation).  Publica cada runbook depois que ele é importado com êxito para sua conta de Automação.

### <a name="add-a-schedule-to-the-test-resourceschedule-runbook"></a>Adicionar uma agenda ao runbook Test-ResourceSchedule
Siga estas etapas para habilitar o agendamento para o runbook Test-ResourceSchedule. Esse é o runbook que verifica quais máquinas virtuais devem ser iniciadas, desligadas ou deixadas como estão.

1. No portal do Azure, abra sua conta da Automação e clique no bloco **Runbooks** .
2. Na folha **Test-ResourceSchedule**, clique no bloco **Agendamentos**.
3. Na folha **Agendamentos**, clique em **Adicionar um agendamento**.
4. Na folha **Agendamentos**, selecione **Associar um agendamento ao runbook**. Em seguida, selecione **Criar um nova agendamento**.
5. Na folha **Nova agenda** , digite o nome dela, por exemplo: *HourlyExecution*.
6. Para o **Início**do agendamento, defina a hora de início como um incremento de hora.
7. Selecione **Recorrência** e em **Repetir a cada intervalo**, selecione **1 hora**.
8. Verifique se **Definir expiração** está definido como **Não** e clique em **Criar** para salvar seu novo agendamento.
9. Na folha de opções **Agendar Runbook**, selecione **Definições de parâmetros e de execução**. Na folha Test-ResourceSchedule **Parâmetros**, insira o nome da sua assinatura no campo **SubscriptionName**.  Esse é o único parâmetro necessário para o runbook.  Quando tiver terminado, clique em **OK**.

O agendamento de runbook deverá parecer com o seguinte quando concluído:

![Runbook de Test-ResourceSchedule configurado](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## <a name="format-the-json-string"></a>Formatar cadeia de caracteres JSON
Essa solução basicamente usa uma cadeia de caracteres JSON com um formato especificado e adiciona-a como um valor de uma marcação chamada Agendamento. Em seguida, um runbook lista todos os grupos de recursos e máquinas virtuais e identifica os agendamentos para cada máquina virtual.

O runbook faz um loop sobre as máquinas virtuais que possuem agendas anexadas e verifica quais ações devem ser executadas. Veja um exemplo abaixo de como as soluções devem ser formatadas:

```json
{
    "TzId": "Eastern Standard Time",
    "0": {
        "S": "11",
        "E": "17"
    },
    "1": {
        "S": "9",
        "E": "19"
    },
    "2": {
        "S": "9",
        "E": "19"
    },
}
```

Veja algumas informações detalhadas sobre esta estrutura:

1. O formato dessa estrutura JSON é otimizado para contornar a limitação de 256 caracteres de um valor de marcação única no Azure.
2. *TzId* representa o fuso horário da máquina virtual. Essa ID pode ser obtida usando a classe .NET TimeZoneInfo em uma sessão do PowerShell -**[System.TimeZoneInfo]:: GetSystemTimeZones()**.

   ![GetSystemTimeZones no PowerShell](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

   * Dias da semana são representados com um valor numérico de zero a seis. O valor zero é igual a domingo.
   * A hora de início é representada pelo atributo **S** e seu valor está no formato de 24 horas.
   * A hora de término é representada pelo atributo **E** e seu valor está no formato de 24 horas.

     Se os atributos **S** e **E** tiverem o valor zero (0), a máquina virtual será deixada em seu estado atual no momento da avaliação.
3. Se você quiser ignorar a avaliação de um dia específico da semana, não adicione uma seção para esse dia da semana. No exemplo abaixo, apenas a segunda-feira é avaliada; os outros dias da semana são ignorados:

    ```json
    {
        "TzId": "Eastern Standard Time",
        "1": {
            "S": "11",
            "E": "17"
        }
    }
    ```

## <a name="tag-resource-groups-or-vms"></a>Marcar grupos de recursos ou VMs
Para desligar as VMs, você precisa marcar as VMs ou os grupos de recursos nos quais elas estão localizadas. Máquinas virtuais que não têm uma marcação de Agenda não são avaliadas. Portanto, elas não iniciadas ou desligadas.

Há duas maneiras de marcar grupos de recursos ou VMs com essa solução. Você pode fazer isso diretamente do portal. Ou pode usar os runbooks Add-ResourceSchedule, Update-ResourceSchedule de e Remove-ResourceSchedule.

### <a name="tag-through-the-portal"></a>Marcar pelo portal
Siga estas etapas para marcar uma máquina virtual ou um grupo de recursos no portal:

1. Nivele a cadeia de caracteres JSON e verifique se não há espaços.  A cadeia de caracteres JSON deve ter esta aparência:

    ```json
    {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
    ```

2. Selecione o ícone **Marcação** de uma VM ou um grupo de recursos para aplicar este agendamento.

   ![Opção de marcação de VM](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)

3. As marcas são definidas seguindo um par chave/valor. Digite **Agendamento** no campo **Chave** e cole a cadeia de caracteres JSON no campo **Valor**. Clique em **Salvar**. Agora, a nova marca deve aparecer na lista de marcas do seu recurso.

   ![Marcação de agendamento de VM](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)

### <a name="tag-from-powershell"></a>Marcar do PowerShell
Todos os runbooks importados contém informações de Ajuda no início do script para descrever como executar runbooks diretamente do PowerShell. Você pode chamar os runbooks Add-ScheduleResource e Update-ScheduleResource do PowerShell. Você pode fazer isso transmitindo parâmetros necessários que permitem a você criar ou atualizar a marcação de Agendamento em uma VM ou em um grupo de recursos fora do portal.

Para criar, adicionar e excluir marcações pelo PowerShell, primeiramente você precisa [configurar seu ambiente do PowerShell para o Azure](/powershell/azure/overview). Depois de concluir a instalação, você poderá continuar com as etapas a seguir.

### <a name="create-a-schedule-tag-with-powershell"></a>Criar uma marcação de agendamento com o PowerShell
1. Abra uma sessão do PowerShell. Em seguida, use o exemplo abaixo para autenticar com sua conta Executar como e para especificar uma assinatura:

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. Defina uma tabela de hash de agendamento. Aqui está um exemplo de como deve ser criado:

    ```powershell
    $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}
    ```

3. Defina os parâmetros exigidos para o runbook. O exemplo a seguir destina-se a uma VM:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "VmName"="VM01";"Schedule"=$schedule}
    ```

    Se você estiver marcando um grupo de recursos, remova o parâmetro *VMName* da tabela de hash $params da seguinte maneira:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "Schedule"=$schedule}
    ```

4. Execute o runbook Add-ResourceSchedule com os seguintes parâmetros para criar a marcação Agendamento:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

5. Para atualizar um grupo de recursos ou marcação de máquina virtual, execute o runbook **Update-ResourceSchedule** com os seguintes parâmetros:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

### <a name="remove-a-schedule-tag-with-powershell"></a>Remover uma marcação de agendamento com o PowerShell
1. Abra uma sessão do PowerShell e execute o seguinte para autenticar com sua conta Executar como, selecionar e especificar uma assinatura:

    ```powershell
    Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. Defina os parâmetros exigidos para o runbook. O exemplo a seguir destina-se a uma VM:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01";"VmName"="VM01"}
    ```

    Se você estiver removendo uma marcação de um grupo de recursos, remova o parâmetro *VMName* da tabela de hash $params da seguinte maneira:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}
    ```

3. Execute o runbook Remove-ResourceSchedule para remover a marcação Agendamento:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

4. Para atualizar um grupo de recursos ou marcação de máquina virtual, execute o runbook Remove-ResourceSchedule com os seguintes parâmetros:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

> [!NOTE]
> Recomendamos monitorar proativamente esses runbooks (e o estado da máquina virtual) para verificar se as máquinas virtuais estão sendo devidamente encerradas e iniciadas.
>

Para exibir os detalhes do trabalho do runbook Test-ResourceSchedule no Portal do Azure, selecione o bloco **Trabalhos** do runbook. O resumo do trabalho exibe os parâmetros de entrada e o fluxo de saída, além de informações gerais sobre o trabalho e todas as exceções, caso tenham ocorrido.

O **Resumo do Trabalho** inclui mensagens de fluxos de saída, de aviso e de erro. Selecione o bloco de **Saída** para exibir os resultados detalhados da execução do runbook.

![Saída do Test-ResourceSchedule](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## <a name="next-steps"></a>Próximas etapas
* Para começar a usar os runbooks do fluxo de trabalho do PowerShell, veja [Meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).
* Para aprender mais sobre os tipos de runbook, suas vantagens e limitações, veja [Tipos de runbook da Automação do Azure](automation-runbook-types.md).
* Para saber mais sobre o recurso de suporte a scripts do PowerShell, veja [Suporte a scripts nativos do PowerShell na Automação do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
* Para saber mais sobre o registro em log de runbook e de saída, consulte [Saída e mensagens de runbook na Automação do Azure](automation-runbook-output-and-messages.md).
* Para saber mais sobre uma conta Run As do Azure e como autenticar seus runbooks usando essa conta, consulte [Autenticar runbooks com a conta Run As do Azure](automation-sec-configure-azure-runas-account.md).
