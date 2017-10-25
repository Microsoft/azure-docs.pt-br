---
title: "Dimensionar automaticamente nós de cluster do HPC Pack | Microsoft Docs"
description: "Aumentar e reduzir automaticamente o número de nós de computação de cluster do HPC Pack no Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: 
editor: tysonn
ms.assetid: 38762cd1-f917-464c-ae5d-b02b1eb21e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/08/2016
ms.author: danlep
ms.openlocfilehash: 0dc0d15c64d8951c3c457df73588c37418a3c8a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Aumentar e reduzir automaticamente os recursos de cluster do HPC Pack no Azure conforme a carga de trabalho de cluster
Se você implantar nós de "intermitência" no seu cluster do HPC Pack ou criar um cluster HPC Pack em VMs do Azure, poderá ser conveniente encontrar uma maneira de aumentar ou reduzir automaticamente os recursos do cluster, como nós ou núcleos, de acordo com a carga de trabalho no cluster. Dimensionar os recursos de cluster dessa maneira permite que você use os recursos do Azure com mais eficiência e controle seus custos.

Este artigo mostra duas maneiras que o HPC Pack fornece para dimensionamento automático de recursos de computação:

* A propriedade **AutoGrowShrink** de cluster do HPC Pack

* O script PowerShell do HPC **AzureAutoGrowShrink.ps1**

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

No momento somente é possível aumentar ou reduzir automaticamente nós de computação HPC Pack que estão em execução em um sistema operacional Windows Server.


## <a name="set-the-autogrowshrink-cluster-property"></a>Definir a propriedade de cluster AutoGrowShrink
### <a name="prerequisites"></a>Pré-requisitos

* **Cluster do HPC Pack 2012 R2 Atualização 2 ou posterior** : o nó de cabeçalho do cluster pode ser implantado localmente ou em uma VM do Azure. Veja [Configurar um cluster híbrido com o HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) para começar com um nó de cabeçalho local e os nós de “disparo contínuo” do Azure. Consulte o [script de implantação de IaaS do HPC Pack](hpcpack-cluster-powershell-script.md) para implantar rapidamente um cluster HPC Pack em VMs do Azure.

* **Para um cluster com um nó de cabeçalho no Azure (modelo de implantação do Resource Manager)** – No HPC Pack 2016, a autenticação de certificado em um aplicativo do Azure Active Directory é usada para expandir e reduzir automaticamente as VMs de cluster implantado usando o Azure Resource Manager. Configure um certificado da seguinte maneira:

  1. Após a implantação do cluster, conecte-se a um nó de cabeçalho pela Área de Trabalho Remota.

  2. Faça upload do certificado (formato PFX com chave privada) em cada nó de cabeçalho e instale em Cert:\LocalMachine\My e Cert:\LocalMachine\Root.

  3. Inicie o Azure PowerShell como administrador e execute os seguintes comandos em um nó de cabeçalho:

    ```powershell
        cd $env:CCP_HOME\bin

        Login-AzureRmAccount
    ```
        
    Se sua conta estiver em mais de um locatário do Azure Active Directory ou em mais de uma assinatura do Azure, execute o seguinte comando para selecionar o locatário e a assinatura corretos:
  
    ```powershell
        Login-AzureRMAccount -TenantId <TenantId> -SubscriptionId <subscriptionId>
    ```     
       
    Execute o seguinte comando para exibir o locatário e a assinatura atualmente selecionados:
    
    ```powershell
        Get-AzureRMContext
    ```

  4. Execute o seguinte script

    ```powershell
        .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -CertificateThumbprint "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" -TenantId xxxxxxxx-xxxxx-xxxxx-xxxxx-xxxxxxxxxxxx
    ```

    onde

    **DisplayName** – nome de exibição do aplicativo ativo do Azure. Se o aplicativo não existir, ele será criado no Azure Active Directory.

    **Home page** – a home page do aplicativo. Você pode configurar uma URL fictícia, como no exemplo anterior.

    **IdentifierUri** – identificador do aplicativo. Você pode configurar uma URL fictícia, como no exemplo anterior.

    **CertificateThumbprint** – impressão digital do certificado instalado no nó de cabeçalho na etapa 1.

    **TenantId** – ID de locatário do Azure Active Directory. Obtenha a ID de Locatário na página **Propriedades** do portal do Azure Active Directory.

    Para obter mais detalhes sobre **ConfigARMAutoGrowShrinkCert.ps1**, execute `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`.


* **Para um cluster com um nó de cabeçalho no Azure (modelo de implantação clássico)** – Se você usar o script de implantação do IaaS HPC Pack para criar o cluster no modelo de implantação clássico, habilite a propriedade de cluster **AutoGrowShrink** definindo a opção AutoGrowShrink no arquivo de configuração do cluster. Para obter detalhes, consulte a documentação que acompanha o [download do script](https://www.microsoft.com/download/details.aspx?id=44949).

    Como alternativa, habilite a propriedade de cluster **AutoGrowShrink** depois de implantá-lo usando os comandos do PowerShell HPC descritos na seção a seguir. Para se preparar para isso, primeiro conclua as seguintes etapas:

  1. Configure o certificado de gerenciamento do Azure no nó de cabeçalho na assinatura do Azure. Para uma implantação de teste, você pode usar o certificado autoassinado padrão do Microsoft HPC Azure que instala o HPC Pack no nó de cabeçalho e então carregar esse certificado para a sua assinatura do Azure. Para ver as opções e as etapas, consulte a [diretriz da Biblioteca do TechNet](https://technet.microsoft.com/library/gg481759.aspx).

  2. Execute **regedit** no nó de cabeçalho, vá para HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo e adicione um valor de cadeia de caracteres. Defina o nome do valor como "Impressão digital" e dados de Valor para a impressão digital do certificado na Etapa 1.

### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>Comandos do PowerShell HPC para definir a propriedade AutoGrowShrink
A seguir estão exemplos de comandos do PowerShell HPC para definir **AutoGrowShrink** e ajustar seu comportamento com parâmetros adicionais. Consulte [parâmetros de AutoGrowShrink](#AutoGrowShrink-parameters) posteriormente neste artigo para obter uma lista de configurações.

Para executar esses comandos, inicie o PowerShell HPC no nó principal do cluster como um administrador.

**Para habilitar a propriedade AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 1
```

**Para desabilitar a propriedade AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 0
```

**Para alterar o intervalo de crescimento em minutos**

```powershell
Set-HpcClusterProperty –GrowInterval <interval>
```

**Para alterar o intervalo de diminuição em minutos**

```powershell
Set-HpcClusterProperty –ShrinkInterval <interval>
```

**Para exibir a configuração atual de AutoGrowShrink**

```powershell
Get-HpcClusterProperty –AutoGrowShrink
```

**Para excluir grupos de nó de AutoGrowShrink**

```powershell
Set-HpcClusterProperty –ExcludeNodeGroups <group1,group2,group3>
```

>[!NOTE] 
> Esse parâmetro tem suporte do HPC Pack 2016
>

### <a name="autogrowshrink-parameters"></a>parâmetros de AutoGrowShrink
A seguir estão os parâmetros de AutoGrowShrink que podem ser modificados usando o comando **Set-HpcClusterProperty** .

* **EnableGrowShrink** – Mude para habilitar ou desabilitar a propriedade **AutoGrowShrink**.
* **ParamSweepTasksPerCore** : número de tarefas de limpeza paramétrica para aumentar um núcleo. O padrão é a aumentar um núcleo por tarefa.

  > [!NOTE]
  > O QFE KB3134307 do HPC Pack altera o **ParamSweepTasksPerCore** para **TasksPerResourceUnit**. Ele se baseia no tipo de recurso de trabalho e pode ser nó, soquete ou núcleo.
  >
  >
* **GrowThreshold** : limite de tarefas em fila para disparar o crescimento automático. O padrão é 1, o que significa que, se houver 1 ou mais tarefas no estado em fila, os nós aumentarão automaticamente.
* **GrowInterval** : intervalo em minutos para disparar o crescimento automático. O intervalo padrão é 5 minutos.
* **ShrinkInterval** : intervalo em minutos para disparar a redução automática. O intervalo padrão é 5 minutos.
* **ShrinkIdleTimes** : o número de verificações contínuas para reduzir para indicar que os nós estão ociosos. O padrão é 3. Por exemplo, se o **ShrinkInterval** for de 5 minutos, o HPC Pack verifica se o nó está ocioso a cada 5 minutos. Se os nós estiverem no estado ocioso após 3 verificações contínuas (15 minutos), o HPC Pack reduzirá esse nó.
* **ExtraNodesGrowRatio** : percentual adicional de nós para crescimento de trabalhos de MPI (Interface Transferência de Mensagens). O valor padrão é 1, o que significa que o HPC Pack aumenta 1% dos nós para trabalhos MPI.
* **GrowByMin** : alterne para indicar se a política de crescimento automático se baseia nos recursos mínimos necessários para o trabalho. O padrão é false, o que significa que o HPC Pack aumenta os nós para trabalhos com base nos recursos máximo necessários para os trabalhos.
* **SoaJobGrowThreshold** : limite de solicitações SOA de entrada para disparar o processo de crescimento automático. O valor padrão é 50000.

  > [!NOTE]
  > Esse parâmetro tem suporte a partir do HPC Pack 2012 R2 Atualização 3.
  >
  >
* **SoaRequestsPerCore** : o número de solicitações SOA de entrada para aumentar um núcleo. O valor padrão é 20000.

  > [!NOTE]
  > Esse parâmetro tem suporte a partir do HPC Pack 2012 R2 Atualização 3.
  >
* **ExcludeNodeGroups** – Nós nos grupos de nó especificados não aumentam ou reduzem automaticamente.
  
  > [!NOTE]
  > Esse parâmetro tem suporte do HPC Pack 2016.
  >

### <a name="mpi-example"></a>Exemplo de MPI
Por padrão, o HPC Pack aumenta 1% de nós extras para trabalhos MPI (**ExtraNodesGrowRatio** está definido como 1). O motivo é que MPI pode exigir vários nós e o trabalho pode ser executado somente quando todos os nós estiverem prontos. Quando o Azure inicia os nós, ocasionalmente, um nó pode precisar de mais tempo para iniciar que os outros, fazendo com que outros nós fiquem ociosos enquanto aguardam esse nó ficar pronto. Ao aumentar nós extras, o HPC Pack reduz o tempo de espera desse recurso e potencialmente reduz os custos. Para aumentar o percentual de nós extras para trabalhos de MPI (por exemplo, para 10%), execute um comando semelhante a

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>Exemplo de SOA
Por padrão, **SoaJobGrowThreshold** é definido como 50000 e **SoaRequestsPerCore** é definido como 200000. Se você enviar um trabalho SOA com 70000 solicitações, há uma tarefa na fila e as solicitações de entrada serão 70000. Nesse caso, o HPC Pack aumenta 1 núcleo para a tarefa em fila e para solicitações de entrada aumenta (70000 - 50000)/20000 = 1 núcleo, portanto o total aumenta em 2 núcleos para este trabalho SOA.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Executar o script AzureAutoGrowShrink.ps1
### <a name="prerequisites"></a>Pré-requisitos

* **Cluster HPC Pack 2012 R2 Atualização 1 ou cluster posterior** – O script **AzureAutoGrowShrink.ps1** está instalado na pasta %CCP_HOME%bin. O nó de cabeçalho do cluster pode ser implantado localmente ou em uma VM do Azure. Veja [Configurar um cluster híbrido com o HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) para começar com um nó de cabeçalho local e os nós de “disparo contínuo” do Azure. Consulte o [script de implantação IaaS do HPC Pack](hpcpack-cluster-powershell-script.md) para implantar rapidamente um cluster HPC Pack em VMs do Azure ou use um [modelo de início rápido do Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).
* **Azure PowerShell 1.4.0** – O script depende atualmente desta versão específica do Azure PowerShell.
* **Para um cluster com nós de disparo contínuo do Azure** - Execute o script em um computador cliente em que o HPC Pack está instalado ou no nó de cabeçalho. Se executado em um computador cliente, certifique-se de que você defina a variável $env:CCP_SCHEDULER para apontar para o nó de cabeçalho. Os nós de "intermitência" do Azure devem ter sido adicionados ao cluster, mas podem estar no estado Não Implantado.
* **Para um cluster implantado em VMs do Azure (modelo de implantação do Resource Manager)** – Para um cluster de VMs do Azure implantado no modelo de implantação do Resource Manager, o script oferece suporte a dois métodos de autenticação do Azure: entrar em sua conta do Azure para executar o script todas as vezes (executando `Login-AzureRmAccount`, ou configurar uma entidade de serviço para autenticar com um certificado. O HPC Pack fornece o script **ConfigARMAutoGrowShrinkCert.ps** para criar uma entidade de serviço com certificado. O script cria um aplicativo do Azure Active Directory (Azure AD) e uma entidade de serviço e atribui a função de Colaborador para a entidade de serviço. Para executar o script, inicie o Azure PowerShell como administrador e execute os seguintes comandos:

    ```powershell
    cd $env:CCP_HOME\bin

    Login-AzureRmAccount

    .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -PfxFile "d:\yourcertificate.pfx"
    ```

    Para obter mais detalhes sobre **ConfigARMAutoGrowShrinkCert.ps1**, execute `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`,

* **Para um cluster implantado em VMs do Azure (modelo de implantação clássico)** – Execute o script no nó de cabeçalho da VM, pois ele depende dos scripts **Start-HpcIaaSNode.ps1** e **Stop-HpcIaaSNode.ps1** que estão instalados ali. Além disso, esses scripts exigem um certificado de gerenciamento ou um arquivo de configurações de publicação do Azure (veja [Gerenciar nós de computação em um cluster HPC Pack no Azure](hpcpack-cluster-node-manage.md)). Verifique se todas as VMs de nó de computação necessárias já foram adicionadas ao cluster. Eles podem estar no estado Parado.



### <a name="syntax"></a>Sintaxe
```powershell
AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfActiveQueuedTasksPerNodeToGrow <Single> [-NumOfActiveQueuedTasksToGrowThreshold <Int32>]
    [-NumOfInitialNodesToGrow <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>]
    [-ShrinkCheckIdleTimes <Int32>] [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>]
    [<CommonParameters>]

AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfQueuedJobsPerNodeToGrow <Single> [-NumOfQueuedJobsToGrowThreshold <Int32>] [-NumOfInitialNodesToGrow
    <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>] [-ShrinkCheckIdleTimes <Int32>]
    [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]

AzureAutoGrowShrink.ps1 -UseLastConfigurations [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]
```
### <a name="parameters"></a>Parâmetros
* **NodeTemplates** - Nomes dos modelos de nó para definir o escopo para que os nós sejam aumentados ou reduzidos. Se não for especificado (o valor padrão é @()), todos os nós no grupo de nós **AzureNodes** estarão no escopo quando **NodeType** tiver um valor de AzureNodes e todos os nós do grupo de nós **ComputeNodes** estarão no escopo quando **NodeType** tiver um valor de ComputeNodes.
* **JobTemplates** - Nomes dos modelos de trabalho para definir o escopo para que os nós sejam aumentados.
* **NodeType** - O tipo de nó para ser aumentado ou reduzido. Os valores para os quais há suporte são:

  * **AzureNodes** – para os nós (de disparo contínuo) de PaaS do Azure em cluster local ou de IaaS do Azure.
  * **ComputeNodes** - para VMs de nó de computação apenas em um cluster de IaaS do Azure.

* **NumOfQueuedJobsPerNodeToGrow** - Número de trabalhos em fila necessários para aumentar um nó.
* **NumOfQueuedJobsToGrowThreshold** - O número de trabalhos em fila para iniciar o processo de aumento.
* **NumOfActiveQueuedTasksPerNodeToGrow** - O número de tarefas em fila ativas necessárias para aumentar um nó. Se **NumOfQueuedJobsPerNodeToGrow** for especificado com um valor maior que 0, esse parâmetro será ignorado.
* **NumOfActiveQueuedTasksToGrowThreshold** - O número de tarefas em fila ativas para iniciar o processo de aumento.
* **NumOfInitialNodesToGrow** – O número mínimo inicial de nós a ser aumentado se todos os nós no escopo forem **Não Implantados** ou **Interrompidos (Desalocados)**.
* **GrowCheckIntervalMins** - O intervalo em minutos entre as verificações a ser aumentado.
* **ShrinkCheckIntervalMins** - O intervalo em minutos entre as verificações a ser reduzido.
* **ShrinkCheckIdleTimes** – O número de verificações de redução contínuas (separado por **ShrinkCheckIntervalMins**) para indicar que os nós estão ociosos.
* **UseLastConfigurations** : as configurações anteriores salvas no arquivo de argumento.
* **ArgFile**- O nome do arquivo argumento usado para salvar e atualizar as configurações para executar o script.
* **LogFilePrefix** - O nome do prefixo do arquivo de log. Você pode especificar um caminho. Por padrão, o log é gravado no atual diretório de trabalho.

### <a name="example-1"></a>Exemplo 1
O exemplo a seguir configura os nós de disparo contínuo do Azure implantados com o Modelo Padrão AzureNode para aumentar ou reduzir automaticamente. Se todos os nós estiverem inicialmente no estado **Não Implantado** , pelo menos três nós serão iniciados. Se o número de trabalhos em fila exceder oito, o script iniciará os nós até seu número exceder a taxa de trabalhos em fila para **NumOfQueuedJobsPerNodeToGrow**. Se for descoberto que um nó está ocioso em 3 tempos ociosos consecutivos, ele será interrompido.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Exemplo 2
O exemplo a seguir configura as VMs de nó de computação do Azure implantadas com o Modelo Padrão ComputeNode para serem aumentadas ou reduzidas automaticamente.
Os trabalhos configurados pelo modelo de trabalho Padrão definem o escopo da carga de trabalho no cluster. Se todos os nós forem interrompidos inicialmente, pelo menos cinco nós serão iniciados. Se o número de tarefas em fila ativas exceder quinze, o script iniciará os nós até seu número exceder a taxa de tarefas em fila ativas para **NumOfActiveQueuedTasksPerNodeToGrow**. Se for descoberto que um nó está ocioso em 10 tempos ociosos consecutivos, ele será interrompido.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
