---
title: Introdução ao PowerShell – Lote do Azure | Microsoft Docs
description: Uma rápida introdução aos cmdlets do Azure PowerShell que podem ser usados para gerenciar os recursos do Lote.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 01/15/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 21930d5240225540159fa425d9d9fa518a1b19d5
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323072"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Gerenciar recursos do Lote com cmdlets do PowerShell

Com os cmdlets do PowerShell do Lote do Azure, você pode executar e criar scripts para muitas das tarefas que você executa com as APIs do Lote, o portal do Azure e a Interface de Linha de Comando (CLI) do Azure. Essa é uma breve introdução aos cmdlets que você pode usar para gerenciar suas contas do Lote e trabalhar com seus recursos do Lote, como pools, trabalhos e tarefas.

Para obter uma lista completa de cmdlets do Lote e a sintaxe detalhada do cmdlet, consulte a [Referência de cmdlet do Lote do Azure](/powershell/module/az.batch).

Este artigo baseia-se nos cmdlets do módulo do Lote do Az 1.0.0. É recomendável que você atualize os módulos do Azure PowerShell com frequência para tirar proveito de atualizações e aprimoramentos do serviço.

## <a name="prerequisites"></a>Pré-requisitos

* [Instalar e configurar o módulo do Azure PowerShell](/powershell/azure/overview). Para instalar um módulo específico do Lote do Azure, como um módulo de pré-lançamento, consulte a [Galeria do PowerShell](https://www.powershellgallery.com/packages/Az.Batch/1.0.0).

* Execute o cmdlet **Connect-AzAccount** para se conectar à sua assinatura (os cmdlets do Lote do Azure são fornecidos no módulo Azure Resource Manager):

  ```powershell
  Connect-AzAccount
  ```

* **Registrar com o namespace do provedor de Lote**. Você só precisa executar essa operação **uma vez por assinatura**.
  
  ```powershell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>Gerenciar contas e chaves do Batch

### <a name="create-a-batch-account"></a>Criar uma conta do Batch

**New-AzBatchAccount** cria uma conta do Lote em um grupo de recursos especificado. Se você ainda não tiver um grupo de recursos, crie um executando o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Especifique uma das regiões do Azure no parâmetro**Location**, como "EUA Central”. Por exemplo:

```powershell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

Em seguida, crie uma conta do Lote no grupo de recursos. Especifique um nome para a conta em <*account_name*> e a localização e o nome do seu grupo de recursos. A criação da conta de lote pode levar algum tempo para ser concluída. Por exemplo:

```powershell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> O nome da conta do Lote deve ser exclusivo na região do Azure para o grupo de recursos, conter entre três e 24 caracteres e usar somente números e letras minúsculas.

### <a name="get-account-access-keys"></a>Obter chaves de acesso da conta

**Get-AzBatchAccountKeys** mostra as chaves de acesso associadas a uma conta do Lote do Azure. Por exemplo, execute o seguinte para obter as chaves primária e secundária da conta criada por você.

 ```powershell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>Gerar uma nova chave de acesso

**New-AzBatchAccountKey** gera uma nova chave de conta primária ou secundária para uma conta do Lote do Azure. Por exemplo, para gerar uma nova chave primária para a sua conta do Batch, digite:

```powershell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> Para gerar uma nova chave secundária, especifique "Secondary" para o parâmetro **KeyType** . É necessário gerar novamente as chaves primária e secundária separadamente.

### <a name="delete-a-batch-account"></a>Excluir uma conta do Batch

**Remove-AzBatchAccount** exclui uma conta do Lote. Por exemplo:

```powershell
Remove-AzBatchAccount -AccountName <account_name>
```

Quando solicitado, confirme que você deseja remover a conta. A remoção de conta pode levar algum tempo para ser concluída.

## <a name="create-a-batchaccountcontext-object"></a>Criar um objeto BatchAccountContext

Você pode autenticar para gerenciar os recursos do Lote usando a autenticação de chave compartilhada ou a autenticação do Azure Active Directory. Para autenticar usando os cmdlets do PowerShell do Lote, primeiro crie um objeto BatchAccountContext para armazenar suas credenciais de conta ou a identidade. Você passa o objeto BatchAccountContext para os cmdlets que usam o parâmetro **BatchContext** .

### <a name="shared-key-authentication"></a>Autenticação de chave compartilhada

```powershell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> Por padrão, a chave primária da conta é usada para autenticação, mas você pode selecionar a chave a ser usada alterando a propriedade **KeyInUse** do seu objeto de BatchAccountContext: `$context.KeyInUse = "Secondary"`.

### <a name="azure-active-directory-authentication"></a>Autenticação do Azure Active Directory

```powershell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Criar e modificar recursos do Lote

Use cmdlets como **New-AzBatchPool**, **New-AzBatchJob** e **New-AzBatchTask** para criar recursos em uma conta do Lote. Há cmdlets correspondentes **Get-** e **Set-** para atualizar as propriedades de recursos existentes e cmdlets **Remove-** para remover recursos em uma conta do Lote.

Ao usar muitos desses cmdlets, além de passar um objeto BatchContext, você precisa criar ou passar objetos que contêm as configurações detalhadas de recursos, conforme mostrado no exemplo a seguir. Confira a ajuda detalhada de cada cmdlet para obter exemplos adicionais.

### <a name="create-a-batch-pool"></a>Criar um pool do Lote

Ao criar ou atualizar um pool de Lote, selecione a configuração de serviços de nuvem ou a configuração de máquina virtual para o sistema operacional em nós de computação (confira [Visão geral do recurso de Lote](batch-api-basics.md#pool)). Se você especificar a configuração dos serviços de nuvem, os nós de computação serão representados com uma das [versões do sistema operacional convidado do Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases). Se você especificar a configuração de máquina virtual, poderá especificar uma das imagens de VM Linux ou Windows com suporte listadas no [Marketplace de máquinas virtuais do Azure][vm_marketplace]ou fornecer uma imagem personalizada que você preparou.

Ao executar **New-AzBatchPool**, passe as configurações do sistema operacional em um objeto PSCloudServiceConfiguration ou PSVirtualMachineConfiguration. Por exemplo, o snippet a seguir cria um pool do Lote na com nós de computação do tamanho Standard_A1 na configuração da máquina virtual, com imagem gerada com o servidor do Ubuntu 18.04-LTS. Aqui, o parâmetro **VirtualMachineConfiguration** especifica a variável *$configuration* do objeto PSVirtualMachineConfiguration. O parâmetro **BatchContext** especifica uma variável definida anteriormente *$context* como o objeto BatchAccountContext.

```powershell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

O número de nós de computação no novo pool de destino é determinado por uma fórmula de dimensionamento automático. Nesse caso, a fórmula é simplesmente **$TargetDedicated=4**, indicando que o número de nós de computação no pool é 4, no máximo.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Consultar pools, trabalhos, tarefas e outros detalhes

Use cmdlets como **Get-AzBatchPool**, **Get-AzBatchJob** e **Get-AzBatchTask** para consultar entidades criadas em uma conta do Lote.

### <a name="query-for-data"></a>Consultar dados

Por exemplo, use **Get-AzBatchPools** para encontrar os pools. Por padrão, isso consulta todos os pools em sua conta, supondo que você já tenha armazenado o objeto BatchAccountContext em *$context*:

```powershell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>Usar um filtro OData

Você pode fornecer um filtro OData usando o parâmetro **Filter** para localizar apenas os objetos de seu interesse. Por exemplo, você pode localizar todos os pools com IDs que começam com "myPool":

```powershell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

Esse método não é tão flexível quanto usar "Where-Object" em um pipeline local. No entanto, a consulta é enviada para o serviço Batch diretamente para que toda a filtragem ocorra no servidor, poupando largura de banda de Internet.

### <a name="use-the-id-parameter"></a>Use o parâmetro de Id

Uma alternativa a um filtro OData é usar o parâmetro **Id** . Para consultar um pool específico com id "myPool":

```powershell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

O parâmetro **Id** só dá suporte à pesquisa de ID completa, não a curingas nem filtros no estilo OData.

### <a name="use-the-maxcount-parameter"></a>Usar o parâmetro MaxCount

Por padrão, cada cmdlet retorna no máximo 1.000 objetos. Se você atingir esse limite, refine seu filtro para retornar menos objetos ou defina explicitamente um máximo usando o parâmetro **MaxCount** . Por exemplo:

```powershell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

Para remover o limite superior, defina **MaxCount** como 0 ou menos.

### <a name="use-the-powershell-pipeline"></a>Usar o pipeline do PowerShell

Os cmdlets do Batch usam o pipeline do PowerShell para enviar dados entre cmdlets. Isso tem o mesmo efeito que especificar um parâmetro, mas facilita o trabalho com várias entidades.

Por exemplo, localize e exiba todas as tarefas em sua conta:

```powershell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

Reinicializar todos os nós de computação em um pool:

```powershell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>Gerenciamento de pacote de aplicativos

Os pacotes de aplicativos fornecem uma maneira simplificada de implantar aplicativos para nós de computação em seus pools. Com os cmdlets do PowerShell do Lote, carregue e gerencie pacotes de aplicativos em sua conta do Lote e implante versões do pacote para nós de computação.

**Criar** um aplicativo:

```powershell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

**Adicionar** um pacote de aplicativos:

```powershell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

Defina a **versão padrão** para o aplicativo:

```powershell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

**Listar** pacotes de um aplicativo

```powershell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

**Excluir** um pacote de aplicativo

```powershell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

**Excluir** um aplicativo

```powershell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> Você deve excluir todas as versões do pacote do aplicativo antes de excluir o aplicativo. Se você tentar excluir um aplicativo que atualmente tem pacotes de aplicativos, receberá um erro de 'Conflito'.

### <a name="deploy-an-application-package"></a>Implantar um pacote de aplicativos

Você pode especificar um ou mais pacotes de aplicativos para implantação durante a criação de um pool. Quando você especifica um pacote no momento da criação do pool, ele é implantado em cada nó como o pool de junções de nó. Pacotes também são implantados quando um nó é reinicializado ou quando sua imagem é refeita.

Especifique a opção `-ApplicationPackageReference` durante a criação de um pool para implantar um pacote de aplicativos nos nós do pool à medida que eles ingressarem no pool. Primeiro, crie um objeto **PSApplicationPackageReference** e configure-o com a versão do pacote e a ID de aplicativo que deseja implantar em nós de computação do pool:

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

Agora, crie o pool e especifique o objeto de referência do pacote como o argumento para a opção `ApplicationPackageReferences`:

```powershell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

Veja mais informações sobre pacotes de aplicativos em [Implantar aplicativos em nós de computação com pacotes de aplicativos do Lote](batch-application-packages.md).

> [!IMPORTANT]
> Você precisará vincular uma conta do Armazenamento do Azure à sua conta do Lote para usar os pacotes de aplicativos.

### <a name="update-a-pools-application-packages"></a>Atualizar pacotes de aplicativos de um pool

Para atualizar os aplicativos atribuídos a um pool existente, primeiro crie um objeto PSApplicationPackageReference com as propriedades desejadas (versão do pacote e ID de aplicativo):

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

Em seguida, obtenha o pool do Lote, apague todos os pacotes existentes, adicione nossa nova referência de pacote e atualize o serviço do Lote com as novas configurações do pool:

```powershell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

Agora você atualizou as propriedades do pool no serviço de Lote. Para realmente implantar o novo pacote de aplicativo para nós de computação no pool, entretanto, reinicie ou recrie esses nós. Você pode reiniciar todos os nós em um pool com este comando:

```powershell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> Você pode implantar vários pacotes de aplicativos nos nós de computação em um pool. Se você quiser *adicionar* um pacote de aplicativo em vez de substituir os pacotes implantados atualmente, omita a linha `$pool.ApplicationPackageReferences.Clear()` acima.

## <a name="next-steps"></a>Próximas etapas

* Para obter a sintaxe detalhada do cmdlet, veja [Referência de cmdlet do Lote do Azure](/powershell/module/az.batch).
* Para obter mais informações sobre aplicativos e pacotes de aplicativos no Lote, confira [Implantação de aplicativos com pacotes de aplicativos do Lote](batch-application-packages.md).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
