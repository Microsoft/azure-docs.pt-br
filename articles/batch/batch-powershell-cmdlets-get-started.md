---
title: "Introdução ao PowerShell do Lote do Azure | Microsoft Docs"
description: "Uma rápida introdução aos cmdlets do Azure PowerShell que podem ser usados para gerenciar os recursos do Lote."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: f9ad62c5-27bf-4e6b-a5bf-c5f5914e6199
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e33be6ed658e00250ea1e80cd7da4d348fb18296
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Gerenciar recursos do Lote com cmdlets do PowerShell

Com os cmdlets do PowerShell do Lote do Azure, você pode executar e criar scripts para muitas das mesmas tarefas que executa com as APIs do Lote, o portal do Azure e a CLI (Interface de Linha de Comando) do Azure. Essa é uma breve introdução aos cmdlets que você pode usar para gerenciar suas contas do Lote e trabalhar com seus recursos do Lote, como pools, trabalhos e tarefas.

Para obter uma lista completa de cmdlets do Lote e a sintaxe detalhada do cmdlet, consulte a [Referência de cmdlet do Lote do Azure](/powershell/module/azurerm.batch/#batch).

Este artigo baseia-se nos cmdlets do Azure PowerShell versão 3.0.0. É recomendável que você atualize o Azure PowerShell com frequência para tirar proveito de atualizações e aprimoramentos do serviço.

## <a name="prerequisites"></a>Pré-requisitos
Execute as seguintes operações para usar o Azure PowerShell para gerenciar os recursos de Lote.

* [Instalar e configurar o PowerShell do Azure](/powershell/azure/overview)
* Execute o cmdlet **Login-AzureRmAccount** para se conectar à sua assinatura (os cmdlets do Lote do Azure são fornecidos no módulo Azure Resource Manager):
  
    `Login-AzureRmAccount`
* **Registrar com o namespace do provedor de Lote**. Essa operação só precisa ser executada **uma vez por assinatura**.
  
    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Gerenciar contas e chaves do Batch
### <a name="create-a-batch-account"></a>Criar uma conta do Batch
**New-AzureRmBatchAccount** cria uma conta do Lote em um grupo de recursos especificado. Se você ainda não tiver um grupo de recursos, crie um executando o cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Especifique uma das regiões do Azure no parâmetro**Location**, como "EUA Central”. Por exemplo:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Em seguida, crie uma conta do Lote no grupo de recursos, especificando um nome para a conta em <*nome_da_conta*> e o local e o nome de seu grupo de recursos. A criação da conta de lote pode levar algum tempo para ser concluída. Por exemplo:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [!NOTE]
> O nome da conta do Lote deve ser exclusivo na região do Azure para o grupo de recursos, conter entre três e 24 caracteres e usar somente números e letras minúsculas.
> 
> 

### <a name="get-account-access-keys"></a>Obter chaves de acesso da conta
**Get-AzureRmBatchAccountKeys** mostra as chaves de acesso associadas a uma conta do Lote do Azure. Por exemplo, execute o seguinte para obter as chaves primária e secundária da conta criada por você.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Gerar uma nova chave de acesso
**New-AzureRmBatchAccountKey** gera uma nova chave de conta primária ou secundária para uma conta do Lote do Azure. Por exemplo, para gerar uma nova chave primária para a sua conta do Batch, digite:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [!NOTE]
> Para gerar uma nova chave secundária, especifique "Secondary" para o parâmetro **KeyType** . É necessário gerar novamente as chaves primária e secundária separadamente.
> 
> 

### <a name="delete-a-batch-account"></a>Excluir uma conta do Batch
**Remove-AzureRmBatchAccount** exclui uma conta do Lote. Por exemplo:

    Remove-AzureRmBatchAccount -AccountName <account_name>

Quando solicitado, confirme que você deseja remover a conta. A remoção de conta pode levar algum tempo para ser concluída.

## <a name="create-a-batchaccountcontext-object"></a>Criar um objeto BatchAccountContext
Para se autenticar usando os cmdlets do PowerShell do Lote ao criar e gerenciar pools, trabalhos, tarefas e outros recursos do Lote, primeiro crie um objeto BatchAccountContext para armazenar o nome e as chaves da conta:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Você passa o objeto BatchAccountContext para os cmdlets que usam o parâmetro **BatchContext** .

> [!NOTE]
> Por padrão, a chave primária da conta é usada para autenticação, mas você pode selecionar a chave a ser usada alterando a propriedade **KeyInUse** do seu objeto de BatchAccountContext: `$context.KeyInUse = "Secondary"`.
> 
> 

## <a name="create-and-modify-batch-resources"></a>Criar e modificar recursos do Lote
Use cmdlets como **New-AzureBatchPool**, **New-AzureBatchJob** e **New-AzureBatchTask** para criar recursos em uma conta do Lote. Há cmdlets correspondentes **Get-** e **Set-** para atualizar as propriedades de recursos existentes e cmdlets **Remove-** para remover recursos em uma conta do Lote.

Ao usar muitos desses cmdlets, além de passar um objeto BatchContext, você precisa criar ou passar objetos que contêm as configurações detalhadas de recursos, conforme mostrado no exemplo a seguir. Confira a ajuda detalhada de cada cmdlet para obter exemplos adicionais.

### <a name="create-a-batch-pool"></a>Criar um pool do Lote
Ao criar ou atualizar um pool de lote, selecione a configuração de serviço de nuvem ou a configuração de máquina virtual para o sistema operacional em nós de computação (confira [Visão geral do recurso de Lote](batch-api-basics.md#pool)). Se você especificar a configuração do serviço de nuvem, os nós de computação serão representados com uma das [versões do sistema operacional convidado do Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases). Se você especificar a configuração da máquina virtual, poderá especificar uma das imagens de VM Linux ou Windows com suporte listadas no [Marketplace de Máquinas Virtuais do Azure][vm_marketplace] ou fornecer uma imagem personalizada preparada.

Ao executar **New-AzureBatchPool**, passe as configurações do sistema operacional em um objeto PSCloudServiceConfiguration ou PSVirtualMachineConfiguration. Por exemplo, o cmdlet a seguir cria um novo pool do Lote com nós de computação de tamanho pequeno na configuração de serviço de nuvem, com a imagem criada com a última versão do sistema operacional da família 3 (Windows Server 2012). Aqui, o parâmetro **CloudServiceConfiguration** especifica a variável *$configuration* do objeto PSCloudServiceConfiguration. O parâmetro **BatchContext** especifica uma variável definida anteriormente *$context* como o objeto BatchAccountContext.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

O número de nós de computação no novo pool de destino é determinado por uma fórmula de dimensionamento automático. Nesse caso, a fórmula é simplesmente **$TargetDedicated=4**, indicando que o número de nós de computação no pool é 4, no máximo.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Consultar pools, trabalhos, tarefas e outros detalhes
Use cmdlets como**Get-AzureBatchPool**, **Get-AzureBatchJob** e **Get-AzureBatchTask** para consultar entidades criadas em uma conta do Lote.

### <a name="query-for-data"></a>Consultar dados
Por exemplo, use **Get-AzureBatchPools** para encontrar os pools. Por padrão, isso consulta todos os pools em sua conta, supondo que você já tenha armazenado o objeto BatchAccountContext em *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Usar um filtro OData
Você pode fornecer um filtro OData usando o parâmetro **Filter** para localizar apenas os objetos de seu interesse. Por exemplo, você pode localizar todos os pools com ids que começam com "myPool":

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Esse método não é tão flexível quanto usar "Where-Object" em um pipeline local. No entanto, a consulta é enviada para o serviço Batch diretamente para que toda a filtragem ocorra no servidor, poupando largura de banda de Internet.

### <a name="use-the-id-parameter"></a>Use o parâmetro de Id
Uma alternativa a um filtro OData é usar o parâmetro **Id** . Para consultar um pool específico com id "myPool":

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

O parâmetro **ID** só dá suporte à pesquisa de ID completo, não a curingas ou a filtros de estilo OData.

### <a name="use-the-maxcount-parameter"></a>Usar o parâmetro MaxCount
Por padrão, cada cmdlet retorna no máximo 1.000 objetos. Se você atingir esse limite, refine seu filtro para retornar menos objetos ou defina explicitamente um máximo usando o parâmetro **MaxCount** . Por exemplo:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Para remover o limite superior, defina **MaxCount** como 0 ou menos.

### <a name="use-the-powershell-pipeline"></a>Usar o pipeline do PowerShell
Os cmdlets do Batch podem aproveitar o pipeline do PowerShell para enviar dados entre cmdlets. Isso tem o mesmo efeito que especificar um parâmetro, mas facilita o trabalho com várias entidades.

Por exemplo, localize e exiba todas as tarefas em sua conta:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Reinicializar todos os nós de computação em um pool:

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Gerenciamento de pacote de aplicativos
Os pacotes de aplicativos fornecem uma maneira simplificada de implantar aplicativos para nós de computação em seus pools. Com os cmdlets do PowerShell do Lote, carregue e gerencie pacotes de aplicativos em sua conta do Lote e implante versões do pacote para nós de computação.

**Criar** um aplicativo:

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Adicionar** um pacote de aplicativos:

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Defina a **versão padrão** para o aplicativo:

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Listar** pacotes de um aplicativo

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Excluir** um pacote de aplicativo

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Excluir** um aplicativo

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

> [!NOTE]
> Você deve excluir todas as versões do pacote do aplicativo antes de excluir o aplicativo. Se você tentar excluir um aplicativo que atualmente tem pacotes de aplicativos, receberá um erro de 'Conflito'.
> 
> 

### <a name="deploy-an-application-package"></a>Implantar um pacote de aplicativos
Você pode especificar um ou mais pacotes de aplicativos para implantação durante a criação de um pool. Quando você especifica um pacote no momento da criação do pool, ele é implantado em cada nó como o pool de junções de nó. Pacotes também são implantados quando um nó é reinicializado ou quando sua imagem é refeita.

Especifique a opção `-ApplicationPackageReference` durante a criação de um pool para implantar um pacote de aplicativos nos nós do pool à medida que eles ingressarem no pool. Primeiro, crie um objeto **PSApplicationPackageReference** e configure-o com a versão do pacote e a Id de aplicativo que deseja implantar em nós de computação do pool:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Agora, crie o pool e especifique o objeto de referência do pacote como o argumento para a opção `ApplicationPackageReferences`:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Veja mais informações sobre pacotes de aplicativos em [Implantar aplicativos em nós de computação com pacotes de aplicativos do Lote](batch-application-packages.md).

> [!IMPORTANT]
> Você deve [vincular uma conta de Armazenamento do Azure](#linked-storage-account-autostorage) à sua conta do Lote para usar os pacotes de aplicativos.
> 
> 

### <a name="update-a-pools-application-packages"></a>Atualizar pacotes de aplicativos de um pool
Para atualizar os aplicativos atribuídos a um pool existente, primeiro crie um objeto PSApplicationPackageReference com as propriedades desejadas (versão do pacote e Id de aplicativo):

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Em seguida, obtenha o pool do Lote, apague todos os pacotes existentes, adicione nossa nova referência de pacote e atualize o serviço do Lote com as novas configurações do pool:

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Agora você atualizou as propriedades do pool no serviço de Lote. Para realmente implantar o novo pacote de aplicativo para nós de computação no pool, entretanto, reinicie ou recrie esses nós. Você pode reiniciar todos os nós em um pool com este comando:

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

> [!TIP]
> Você pode implantar vários pacotes de aplicativos nos nós de computação em um pool. Se você quiser *adicionar* um pacote de aplicativo em vez de substituir os pacotes implantados atualmente, omita a linha `$pool.ApplicationPackageReferences.Clear()` acima.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Para obter a sintaxe detalhada do cmdlet, veja [Referência de cmdlet do Lote do Azure](/powershell/module/azurerm.batch/#batch).
* Para obter mais informações sobre aplicativos e pacotes de aplicativos no Lote, confira [Implantação de aplicativos com pacotes de aplicativos do Lote](batch-application-packages.md).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/