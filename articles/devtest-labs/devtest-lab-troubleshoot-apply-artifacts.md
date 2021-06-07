---
title: Solucionar problemas com artefatos no Azure DevTest Labs | Microsoft Docs
description: Saiba como solucionar problemas que ocorrem ao aplicar artefatos em uma máquina virtual Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a89b675a1b3bf134b98e09c7278f0eccb594c325
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483186"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Solucionar problemas ao aplicar artefatos em uma máquina virtual Azure DevTest Labs
A aplicação de artefatos em uma máquina virtual pode falhar por vários motivos. Este artigo orienta você por alguns dos métodos para ajudar a identificar possíveis causas.

Se precisar de mais ajuda a qualquer momento neste artigo, você pode entrar em contato com os especialistas do Azure DevTest Labs (DTL) nos [fóruns do Azure e Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione obter suporte.   

> [!NOTE]
> Este artigo se aplica a máquinas virtuais Windows e não Windows. Embora haja algumas diferenças, elas serão chamadas explicitamente neste artigo.

## <a name="quick-troubleshooting-steps"></a>Etapas rápidas para solucionar problemas
Verifique se a VM está em execução. O DevTest Labs requer que a VM esteja em execução e que o Microsoft Azure agente de [máquina virtual (agente de VM)](../virtual-machines/extensions/agent-windows.md) esteja instalado e pronto.

> [!TIP]
> Na **portal do Azure**, navegue até a página **gerenciar artefatos** da VM para ver se a VM está pronta para aplicar artefatos. Você verá uma mensagem na parte superior da página. 
> 
> Usando **Azure PowerShell**, inspecione o sinalizador **canApplyArtifacts**, que é retornado somente quando você expande em uma operação get. Consulte o seguinte comando de exemplo:

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>Maneiras de solucionar problemas 
Você pode solucionar problemas de VMs criadas usando o DevTest Labs e o modelo de implantação do Gerenciador de recursos usando um dos seguintes métodos:

- **Portal do Azure** -ótimo se você precisar obter rapidamente uma dica visual do que pode estar causando o problema.
- **Azure PowerShell** -se você estiver familiarizado com um prompt do PowerShell, consulte rapidamente os recursos do DevTest Labs usando os cmdlets Azure PowerShell.

> [!TIP]
> Para obter mais informações sobre como examinar a execução de artefato em uma VM, consulte [diagnosticar falhas de artefato no laboratório](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="symptoms-causes-and-potential-resolutions"></a>Sintomas, causas e possíveis resoluções 

### <a name="artifact-appears-to-stop-responding"></a>O artefato parece parar de responder

Um artefato parece parar de responder até que um período de tempo limite predefinido expire e o artefato seja marcado como **com falha**.

Quando um artefato parece parar, primeiro determine onde ele está preso. Um artefato pode ser bloqueado em qualquer uma das seguintes etapas durante a execução:

- **Durante a solicitação inicial**. O DevTest Labs cria um modelo de Azure Resource Manager para solicitar o uso da extensão de script personalizado (CSE). Portanto, em segundo plano, uma implantação de grupo de recursos é disparada. Quando ocorre um erro nesse nível, você obtém detalhes nos logs de **atividade** do grupo de recursos da VM em questão.  
    - Você pode acessar o log de atividades na barra de navegação da página VM do laboratório. Ao selecioná-lo, você verá uma entrada para **aplicar artefatos à máquina virtual** (se a operação aplicar artefatos tiver sido disparada diretamente) ou **Adicionar ou modificar máquinas virtuais** (se a operação de aplicação de artefatos tiver sido parte do processo de criação de VM).
    - Procure erros nessas entradas. Às vezes, o erro não será marcado de forma adequada e você terá que investigar cada entrada.
    - Ao investigar os detalhes de cada entrada, certifique-se de examinar o conteúdo da carga JSON. Você pode ver um erro na parte inferior desse documento.
- **Ao tentar executar o artefato**. Pode ser devido a problemas de rede ou de armazenamento. Consulte a respectiva seção mais adiante neste artigo para obter detalhes. Isso também pode ocorrer devido à maneira como o script é criado. Por exemplo:
    - Um script do PowerShell tem **parâmetros obrigatórios**, mas um falha ao passar um valor para ele, seja porque você permite que o usuário o deixe em branco ou porque você não tem um valor padrão para a propriedade na artifactfile.jsno arquivo de definição. O script irá parar de responder porque está aguardando a entrada do usuário.
    - Um script do PowerShell **requer entrada do usuário** como parte da execução. Os scripts devem ser escritos para funcionar silenciosamente sem a necessidade de qualquer intervenção do usuário.
- O **agente de VM demora muito para estar pronto**. Quando a VM é iniciada pela primeira vez ou quando a extensão de script personalizado é instalada pela primeira vez para atender à solicitação de aplicação de artefatos, a VM pode exigir a atualização do agente de VM ou aguardar a inicialização do agente de VM. Pode haver serviços nos quais o agente de VM depende que esteja demorando muito tempo para inicializar. Nesses casos, consulte [visão geral do agente de máquina virtual do Azure](../virtual-machines/extensions/agent-windows.md) para obter mais soluções de problemas.

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-script"></a>Para verificar se o artefato parece parar de responder devido ao script

1. Faça logon na máquina virtual em questão.
2. Copie o script localmente na máquina virtual ou localize-o na máquina virtual em `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>` . É o local onde os scripts de artefatos são baixados.
3. Usando um prompt de comandos com privilégios elevados, execute o script localmente, fornecendo os mesmos valores de parâmetro usados para causar o problema.
4. Determine se o script sofre de qualquer comportamento indesejado. Nesse caso, solicite uma atualização para o artefato (se for do repositório público); ou então, faça as correções por conta própria (se for do seu repositório particular).

> [!TIP]
> Você pode corrigir problemas com artefatos hospedados em nosso [repositório público](https://github.com/Azure/azure-devtestlab) e enviar as alterações para nossa revisão e aprovação. Consulte a seção de **contribuições** no documento [README.MD](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md) .
> 
> Para obter informações sobre como escrever seus próprios artefatos, consulte [AUTHORING.MD](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) Document.

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-vm-agent"></a>Para verificar se o artefato parece parar de responder devido ao agente de VM:
1. Faça logon na máquina virtual em questão.
2. Usando o explorador de arquivos, navegue até **C:\WindowsAzure\logs**.
3. Localize e abra o arquivo **WaAppAgent. log**.
4. Procure entradas que mostram quando o agente de VM é iniciado e quando ele está concluindo a inicialização (ou seja, a primeira pulsação é enviada). Favorecer entradas mais recentes ou, especificamente, aquelas em vez do período de tempo durante o qual você enfrenta o problema.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    Neste exemplo, você pode ver que a hora de início do agente de VM demorou 10 minutos e 20 segundos porque uma pulsação foi enviada. A causa, nesse caso, foi o serviço OOBE demorando muito para começar.

> [!TIP]
> Para obter informações gerais sobre extensões do Azure, consulte [recursos e extensões de máquina virtual do Azure](../virtual-machines/extensions/overview.md).

## <a name="storage-errors"></a>Erros de armazenamento
O DevTest Labs requer acesso à conta de armazenamento do laboratório que é criada para artefatos de cache. Quando o DevTest Labs aplica um artefato, ele lê a configuração do artefato e seus arquivos a partir dos repositórios configurados. Por padrão, o DevTest Labs configura o acesso ao **repositório de artefatos público**.

Dependendo de como uma VM é configurada, ela pode não ter acesso direto a esse repositório. Portanto, por design, o DevTest Labs armazena em cache os artefatos em uma conta de armazenamento que é criada quando o laboratório é inicializado pela primeira vez.

Se o acesso a essa conta de armazenamento for bloqueado de alguma forma, como quando o tráfego for bloqueado da VM para o serviço de armazenamento do Azure, você poderá ver um erro semelhante ao seguinte:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

O erro acima apareceria na seção **mensagem de implantação** na página **resultados do artefato** em **gerenciar artefatos**. Ele também aparecerá nos **logs de atividade** no grupo de recursos da máquina virtual em questão.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Para garantir que a comunicação com o serviço de armazenamento do Azure não esteja sendo bloqueada:

- **Verifique se há NSG (grupos de segurança de rede) adicionados**. Pode ser que uma política de assinatura tenha sido adicionada onde NSGs são automaticamente configuradas em todas as redes virtuais. Ele também afetaria a rede virtual padrão do laboratório, se usado, ou outra rede virtual configurada em seu laboratório, usada para a criação de VMs.
- **Verifique a conta de armazenamento do laboratório padrão** (ou seja, a primeira conta de armazenamento criada quando o laboratório foi criado, cujo nome normalmente começa com a letra "a" e termina com um número de vários dígitos que é, um \<labname\> #).
    1. Navegue até o grupo de recursos do laboratório.
    2. Localize o recurso do tipo **conta de armazenamento**, cujo nome corresponde à Convenção.
    3. Navegue até a página da conta de armazenamento chamada **firewalls e redes virtuais**.
    4. Verifique se ele está definido como **todas as redes**. Se a opção **redes selecionadas** estiver selecionada, verifique se as redes virtuais do laboratório usadas para criar VMs são adicionadas à lista.

Para solucionar problemas mais detalhados, consulte [Configurar firewalls de armazenamento do Azure e redes virtuais](../storage/common/storage-network-security.md).

> [!TIP]
> **Verifique as regras do grupo de segurança de rede**. Use a [verificação de fluxo de IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) para confirmar se uma regra em um grupo de segurança de rede está bloqueando o tráfego para ou de uma máquina virtual. Você também pode examinar as regras de grupo de segurança efetivas para garantir que a regra de entrada **permitir** NSG exista. Para obter mais informações, consulte [usando regras de segurança efetivas para solucionar problemas de fluxo de tráfego de VM](../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="other-sources-of-error"></a>Outras fontes de erro
Há outras fontes de erro menos frequentes possíveis. Certifique-se de avaliar cada um para ver se ele se aplica ao seu caso. Aqui está um deles: 

- **Token de acesso pessoal expirado para o repositório particular**. Quando expirado, o artefato não será listado e todos os scripts que se referem a artefatos de um repositório com um token de acesso privado expirado falharão adequadamente.

## <a name="next-steps"></a>Próximas etapas
Se nenhum desses erros ocorreu e você ainda não puder aplicar artefatos, poderá arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
