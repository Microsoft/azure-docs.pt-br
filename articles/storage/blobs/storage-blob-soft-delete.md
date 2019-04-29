---
title: Exclusão suave para blobs de armazenamento do Azure | Microsoft Docs
description: O Armazenamento do Azure agora oferece a exclusão reversível para objetos de blob para que você possa recuperar os dados mais facilmente quando eles forem modificados ou excluídos erroneamente por outro usuário de conta de armazenamento ou um aplicativo.
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: mihauss
ms.subservice: blobs
ms.openlocfilehash: d9055b0c0decbeca0bb43969af4e854c396c3bb6
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764223"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Exclusão reversível para blobs do Armazenamento do Azure
O Armazenamento do Azure agora oferece a exclusão reversível para objetos de blob para que você possa recuperar os dados mais facilmente quando eles forem modificados ou excluídos erroneamente por outro usuário de conta de armazenamento ou um aplicativo.

## <a name="how-does-it-work"></a>Como ele funciona?
Quando ativado, a exclusão reversível permite que você salve e recupere os dados quando os blobs ou instantâneos de blob são excluídos. Essa proteção se estende para dados de blob que são apagados como resultado de uma substituição.

Quando dados são excluídos, é feita a transição deles para um estado com exclusão reversível em vez de serem apagados permanentemente. Quando a exclusão reversível está ativada e você substitui dados, um instantâneo com exclusão reversível é gerado para salvar o estado dos dados substituídos. Os objetos com exclusão reversível são invisíveis, a menos que explicitamente listados. Você pode configurar o tempo durante o qual os dados com exclusão reversível podem ser recuperados antes de expirar permanentemente.

A exclusão reversível é compatível com versões anteriores. Você não precisa alterar nada nos aplicativos para aproveitar as proteções que esse recurso oferece. No entanto, a [recuperação de dados](#recovery) apresenta uma nova API **Restaurar Blob**.

### <a name="configuration-settings"></a>Definições de configuração
Quando você cria uma nova conta, a exclusão reversível fica desativada por padrão. A exclusão reversível também está desativada por padrão para as contas de armazenamento existentes. Você pode ativar ou desativar o recurso a qualquer momento durante a existência de uma conta de armazenamento.

Você ainda poderá acessar e recuperar dados com exclusão reversível quando o recurso estiver desativado, supondo que os dados com exclusão reversível foram salvos quando o recurso foi ativado anteriormente. Quando ativar a exclusão reversível, você precisará também configurar o período de retenção.

O período de retenção indica o tempo de armazenamento e disponibilidade dos dados com exclusão reversível para recuperação. Para blobs e instantâneos de blob que são excluídos explicitamente, a contagem do período de retenção se inicia quando os dados são excluídos. Para instantâneos com exclusão reversível gerados pelo recurso de exclusão reversível quando os dados são substituídos, o tempo começa a ser contado quando o instantâneo é gerado. No momento, você pode manter os dados com exclusão reversível entre 1 e 365 dias.

Você pode alterar o período de retenção de exclusão reversível a qualquer momento. Um período de retenção atualizado será aplicado apenas aos dados recentemente excluídos. Os dados excluídos anteriormente expirarão com base no período de retenção que foi configurado quando esses dados foram excluídos. A tentativa de excluir um objeto que sofreu exclusão reversível não afetará seu tempo de expiração.

### <a name="saving-deleted-data"></a>Salvando dados excluídos
A exclusão reversível preserva os dados em muitos casos em que blobs ou instantâneos de blob são excluídos ou substituídos.

Quando um blob é substituído usando **Colocar Blob**, **Colocar Bloco**, **Colocar Lista de Blocos** ou **Copiar Blob**, um instantâneo do estado do blob anterior à operação de gravação é gerado automaticamente. Esse instantâneo é um instantâneo com exclusão reversível; é invisível, a menos que os objetos com exclusão reversível estejam listados explicitamente. Consulte a seção [Recuperação](#recovery) para saber como listar os objetos com exclusão reversível.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Os dados com exclusão reversível são cinza, enquanto os dados ativos, azuis. Os dados gravados mais recentemente aparecem abaixo dos mais antigos. Quando B0 é substituído por B1, é gerado um instantâneo com exclusão reversível de B0 é gerado. Quando B1 é substituído por B2, é gerado um instantâneo com exclusão reversível de B1 é gerado.*

> [!NOTE]  
> A exclusão reversível só permite substituir a proteção para operações de cópia quando ela está habilitada para a conta do blob de destino.

> [!NOTE]  
> A exclusão reversível não permite substituir a proteção para blobs na camada de arquivos. Se um blob no arquivo for substituído por um novo blob em qualquer nível, o blob substituído ficará permanentemente expirado.

Quando **Excluir Blob** é chamado em um instantâneo, esse instantâneo é marcado como com exclusão reversível. Um novo instantâneo não é gerado.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Os dados com exclusão reversível são cinza, enquanto os dados ativos, azuis. Os dados gravados mais recentemente aparecem abaixo dos mais antigos. Quando **Blob de Instantâneo** é chamado, B0 torna-se um instantâneo, e B1 é o estado ativo do blob. Quando o instantâneo B0 é excluído, ele é marcado como com exclusão reversível.*

Quando **Excluir Blob** é chamado em um blob de base (qualquer blob que não seja ele próprio um instantâneo), esse blob é marcado como com exclusão reversível. Conforme o comportamento anterior, chamar **Excluir Blob** em um blob que tenha instantâneos ativos retornará um erro. Chamar **Excluir Blob** em um blob com instantâneos com exclusão reversível não retornará um erro. Você ainda poderá excluir um blob e todos os seus instantâneos em única operação quando a exclusão reversível estiver ativada. Isso marca o blob de base e os instantâneos como com exclusão reversível.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Os dados com exclusão reversível são cinza, enquanto os dados ativos, azuis. Os dados gravados mais recentemente aparecem abaixo dos mais antigos. Aqui, uma chamada **Excluir Blob** é feita para excluir B2 e todos os instantâneos associados. O blob ativo, B2 e todos os instantâneos associados são marcados como com exclusão reversível.*

> [!NOTE]  
> Quando um blob com exclusão reversível é substituído, um instantâneo com exclusão reversível do estado do blob antes da operação de gravação é gerado automaticamente. O novo blob herda a camada do blob substituído.

A exclusão reversível não salve seus dados nos casos de exclusões de contêiner ou conta, nem quando os metadados de blob e as propriedades de blob são substituídos. Para proteger uma conta de armazenamento contra exclusão incorreta, você pode configurar um bloqueio usando o Azure Resource Manager. Consulte o artigo do Azure Resource Manager [Bloquear recursos para evitar alterações inesperadas](../../azure-resource-manager/resource-group-lock-resources.md) para saber mais.

A tabela a seguir detalha o comportamento esperado quando a exclusão reversível é ativada:

| Operação de API REST | Tipo de recurso | DESCRIÇÃO | Alteração no comportamento |
|--------------------|---------------|-------------|--------------------|
| [Excluir](/rest/api/storagerp/StorageAccounts/Delete) | Conta | Exclui a conta de armazenamento, incluindo todos os contêineres e blobs que ela contém.                           | Sem alteração. Contêineres e blobs na conta excluída não são recuperáveis. |
| [Excluir Contêiner](/rest/api/storageservices/delete-container) | Contêiner | Exclui o contêiner, incluindo todos os blobs que ele contém. | Sem alteração. Os blobs no contêiner excluído não são recuperáveis. |
| [Colocar Blob](/rest/api/storageservices/put-blob) | Blobs de bloco, acréscimo e página | Cria um novo blob ou substitui um blob existente dentro de um contêiner | Se usado para substituir um blob existente, um instantâneo do estado do blob anterior à chamada é gerado automaticamente. Isso também se aplica a um blob com exclusão reversível anterior se e somente se ele for substituído por um blob do mesmo tipo (bloco, acréscimo ou página). Se ele for substituído por um blob de um tipo diferente, todos os dados com exclusão reversível existentes expirarão permanentemente. |
| [Excluir Blob](/rest/api/storageservices/delete-blob) | Blobs de bloco, acréscimo e página | Marca um blob ou instantâneo de blob para exclusão. O blob ou o instantâneo é excluído depois durante a coleta de lixo | Se usado para excluir que um instantâneo de blob, esse instantâneo será marcado como com exclusão reversível. Se usado para excluir um blob, esse blob será marcado como com exclusão reversível. |
| [Copiar Blob](/rest/api/storageservices/copy-blob) | Blobs de bloco, acréscimo e página | Copia um blob de origem para um blob de destino na mesma conta de armazenamento ou em outra conta de armazenamento. | Se usado para substituir um blob existente, um instantâneo do estado do blob anterior à chamada é gerado automaticamente. Isso também se aplica a um blob com exclusão reversível anterior se e somente se ele for substituído por um blob do mesmo tipo (bloco, acréscimo ou página). Se ele for substituído por um blob de um tipo diferente, todos os dados com exclusão reversível existentes expirarão permanentemente. |
| [Colocar Bloco](/rest/api/storageservices/put-block) | Blobs de blocos | Cria um novo bloco a ser confirmado como parte de um blob de bloco. | Se usado para confirmar um bloco para um blob que está ativo, não haverá nenhuma alteração. Se usado para confirmar um bloco para um blob com exclusão reversível, um novo blob será criado, e um instantâneo, gerado automaticamente para capturar o estado do blob com exclusão reversível. |
| [Colocar lista de blocos](/rest/api/storageservices/put-block-list) | Blobs de blocos | Confirma um blob especificando o conjunto de IDs de bloco que compõem o blob de bloco. | Se usado para substituir um blob existente, um instantâneo do estado do blob anterior à chamada é gerado automaticamente. Isso também se aplicará a um blob com exclusão reversível anterior se e somente se ele for um blob de blocos. Se ele for substituído por um blob de um tipo diferente, todos os dados com exclusão reversível existentes expirarão permanentemente. |
| [Colocar Página](/rest/api/storageservices/put-page) | Blobs de Páginas | Grava um intervalo de páginas em um blob de páginas. | Sem alteração. Os dados de blob de páginas substituídos ou limpos com o uso dessa operação não são salvos nem recuperáveis. |
| [Acrescentar Bloco](/rest/api/storageservices/append-block) | Blob de acréscimo | Grava um bloco de dados no final de um blob de acréscimo | Sem alteração. |
| [Definir propriedades de Blob](/rest/api/storageservices/set-blob-properties) | Blobs de bloco, acréscimo e página | Define valores para propriedades do sistema definidas para um blob. | Sem alteração. As propriedades do blob substituído não são recuperáveis. |
| [Definir Metadados de Blob](/rest/api/storageservices/set-blob-metadata) | Blobs de bloco, acréscimo e página | Define metadados definidos pelo usuário para o blob especificado como um ou mais pares de nome-valor. | Sem alteração. Os metadados de blob substituídos não são recuperáveis. |

É importante observar que chamar "Colocar Página" para substituir ou limpar intervalos de um Blob de Página não gera instantâneos automaticamente. Os discos de máquina virtual têm o suporte de blobs de páginas e usam **Colocar Página** para gravar dados.

### <a name="recovery"></a>Recuperação
Para facilitar a recuperação de dados excluídos, apresentamos uma nova API, a "Restaurar Blob". Chamar a API Restaurar em um blob de base com exclusão reversível restaurará o blob e todos os instantâneos com exclusão reversível associados como ativos. Chamar a API Restaurar em um blob de base ativo restaurará todos os instantâneos com exclusão reversível associados como ativos. Quando instantâneos são restaurados como ativos, eles se assemelham a instantâneos gerados pelo usuário; eles não podem substituir o blob de base.

Para restaurar um blob para um instantâneo com exclusão reversível flexível, você pode chamar **Restaurar Blob** no blob de base. Em seguida, você pode copiar o instantâneo sobre o blob ativo agora. Você também pode copiar o instantâneo para um novo blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Os dados com exclusão reversível são cinza, enquanto os dados ativos, azuis. Os dados gravados mais recentemente aparecem abaixo dos mais antigos. Aqui, **Restaurar Blob** é chamado no blob B, restaurando assim o blob de base, B1 e todos os instantâneos associados, aqui apenas B0, como ativos. Na segunda etapa, B0 é copiado sobre o blob de base. Essa operação de cópia gera um instantâneo com exclusão reversível de B1.*

Para exibir blobs com exclusão reversível e instantâneos de blob, você pode optar por incluir dados excluídos em **Listar Blobs**. Você pode optar por exibir somente blobs de base com exclusão reversível ou incluir também instantâneos de blob com exclusão reversível. Para todos os dados com exclusão reversível, você pode exibir a hora quando os dados foram excluídos, bem como o número de dias antes da expiração permanente dos dados.

### <a name="example"></a>Exemplo
Esta é a saída do console de um script do .NET que carrega, substitui, cria instantâneos, exclui e restaura um blob denominado "HelloWorld" quando a exclusão reversível está habilitada:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Consulte a seção [Próximas etapas](#next-steps) seção para obter um ponteiro para o aplicativo que gerou essa saída.

## <a name="pricing-and-billing"></a>Preços e cobrança
Todos os dados com exclusão reversível são cobrados com a mesma taxa dos dados ativos. Você não será cobrado pelos dados que são excluídos permanentemente após o período de retenção configurado. Para se aprofundar nos instantâneos e como eles acumulam cobranças, consulte [Noções básicas sobre como os instantâneos acumulam cobranças](storage-blob-snapshots.md).

Você não será cobrado pelas transações relacionadas à geração automática de instantâneos. Você será cobrado pelas transações de **Restaurar Blob** na taxa de "Operações de Gravação".

Para obter mais detalhes sobre preços para Armazenamento de Blob do Azure em geral, confira [Página de Preços do Armazenamento de Blob do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Quando você inicialmente ativa a exclusão reversível, é recomendável usar um período de retenção pequeno para entender melhor como o recurso afetará sua fatura.

## <a name="quickstart"></a>Início rápido
### <a name="azure-portal"></a>Portal do Azure
Para habilitar a exclusão reversível, navegue até a opção **Exclusão reversível** em **Serviço Blob**. Em seguida, clique em **Habilitado** e insira o número de dias que você deseja manter os dados com exclusão reversível.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Para exibir blobs com exclusão reversível, marque a caixa de seleção **Mostrar blobs excluídos**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Para exibir instantâneos com exclusão reversível para um determinado blob, selecione o blob e clique em **Exibir instantâneos**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Verifique se a caixa de seleção **Mostrar instantâneos excluídos** está selecionada.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Quando você clica em um blob com exclusão reversível ou um instantâneo, observe as novas propriedades do blob. Elas indicam quando o objeto foi excluído e o número de dias até que o blob ou o instantâneo de blob expire permanentemente. Se o objeto com exclusão reversível não for um instantâneo, você também terá a opção de restaurá-lo.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Lembre-se de que restaurar um blob também restaurará todos os instantâneos associados. Para restaurar instantâneos com exclusão reversível para um blob ativo, clique no blob e selecione **Restaurar todos os instantâneos**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Depois que restaurar os instantâneos de um blob, você poderá clicar em **Promover** para copiar um instantâneo sobre o blob raiz, restaurando assim o blob para o instantâneo.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para habilitar a exclusão reversível, atualize as propriedades do serviço do cliente de um blob. O exemplo a seguir habilita a exclusão reversível para um subconjunto de contas em uma assinatura:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
É possível verificar se a exclusão reversível foi ativada usando o comando a seguir:

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

Para recuperar os blobs que foram excluídos acidentalmente, chame Restaurar nesses blobs. Lembre-se de que chamar **Restaurar Blob** em blobs ativos e com exclusão reversível restaurará todos os instantâneos com exclusão reversível associados como ativos. O exemplo a seguir chama Restaurar em todos os blobs com exclusão reversível e ativos em um contêiner:
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Para localizar a política de retenção de exclusão reversível atual, use o comando a seguir:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

### <a name="azure-cli"></a>CLI do Azure 
Para habilitar a exclusão reversível, atualize as propriedades do serviço do cliente de um blob:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Para verificar se a exclusão reversível está ativada, use o seguinte comando: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

### <a name="python-client-library"></a>Biblioteca de clientes Python
Para habilitar a exclusão reversível, atualize as propriedades do serviço do cliente de um blob:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(delete_retention_policy = DeleteRetentionPolicy(enabled = True, days = 7))
```

### <a name="net-client-library"></a>Biblioteca de clientes do .NET
Para habilitar a exclusão reversível, atualize as propriedades do serviço do cliente de um blob:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Para recuperar os blobs que foram excluídos acidentalmente, chame Restaurar nesses blobs. Lembre-se de que chamar **Restaurar Blob** em blobs ativos e com exclusão reversível restaurará todos os instantâneos com exclusão reversível associados como ativos. O exemplo a seguir chama Restaurar em todos os blobs com exclusão reversível e ativos em um contêiner:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Para recuperar para uma versão específica do blob, primeiro chame Restaurar em um blob e depois copie o instantâneo desejado sobre o blob. O exemplo a seguir recupera um blob de blocos para o seu instantâneo gerado mais recentemente:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

## <a name="should-i-use-soft-delete"></a>Devo usar a exclusão reversível?
Se houver uma possibilidade de que seus dados sejam acidentalmente modificados ou excluídos por outro usuário de conta de armazenamento ou um aplicativo, é recomendável ativar a exclusão reversível. A exclusão reversível é uma parte de uma estratégia de proteção de dados e pode ajudar a evitar a perda acidental de dados.

## <a name="faq"></a>Perguntas frequentes
**Existem considerações especiais para usar a exclusão reversível?**  
Habilitando exclusão reversível para dados substituídos com frequência pode resultar em encargos de capacidade de armazenamento maior e aumento da latência ao listar blobs. Você pode mitigar isso ao armazenar dados substituídos com frequência em uma conta de armazenamento separada com exclusão reversível desabilitado. 

**Para quais tipos de armazenamento posso utilizar a exclusão reversível?**  
Atualmente, a exclusão reversível só está disponível para armazenamento de blob (objeto).

**A exclusão reversível está disponível para todos os tipos de conta de armazenamento?**  
Sim, a exclusão reversível está disponível para contas de armazenamento de blobs e também para blobs nas contas de armazenamento de finalidade geral (GPv1 e GPv2). Isso se aplica às contas standard e premium. A exclusão reversível não está disponível para discos gerenciados.

**A exclusão reversível está disponível para todas as camadas de armazenamento?**  
Sim, a exclusão reversível está disponível para todas as camadas de armazenamento, incluindo frequente, esporádico e arquivos. Contudo, a exclusão reversível não permite substituir a proteção para blobs na camada de arquivos.

**Posso usar a API de definição de camada do blob para criar camadas de blobs com instantâneos que foram excluídos com exclusão reversível?**  
Sim. Os instantâneos com exclusão reversível permanecerão na camada original, mas o blob de base será movido para a nova camada. 

**As contas de armazenamento Premium têm um limite de 100 instantâneos por blob. A contagem de instantâneos com exclusão reversível vai além desse limite?**  
Não, a contagem de instantâneos com exclusão reversível não vai além desse limite.

**Posso ativar a exclusão reversível para as contas de armazenamento existentes?**  
Sim, a exclusão reversível é configurável para contas de armazenamento existentes e novas.

**Se eu excluir uma conta ou um contêiner inteiro com a exclusão reversível ativada, todos os blobs associados serão salvos?**  
Não, se você excluir uma conta ou um contêiner inteiro, todos os blobs associados serão excluídos permanentemente. Para saber como proteger uma conta de armazenamento contra exclusões acidentais, consulte o artigo do Azure Resource Manager [Bloquear recursos para evitar alterações inesperadas](../../azure-resource-manager/resource-group-lock-resources.md).

**Posso exibir as métricas de capacidade para os dados excluídos?**  
Os dados com exclusão reversível serão incluídos como parte de sua capacidade de conta de armazenamento total. Para obter mais informações sobre como controlar e monitorar a capacidade de armazenamento, consulte o artigo [Análise de Armazenamento](../common/storage-analytics.md).

**Se eu desativar a exclusão reversível, ainda conseguirei acessar os dados com exclusão reversível?**  
Sim, você ainda conseguirá acessar e recuperar dados com exclusão reversível não expirados quando a exclusão reversível for desativada.

**Consigo ler e copiar instantâneos com exclusão reversível de meu blob?**  
Sim, mas você deve primeiro chamar Restaurar no blob.

**A exclusão reversível está disponível para todos os tipos de blob?**  
Sim, a exclusão reversível está disponível para blobs de bloco, blobs de acréscimo e blobs de página.

**A exclusão reversível está disponível para discos de máquina virtual?**  
A exclusão reversível está disponível para discos não gerenciados premium e standard. A exclusão reversível apenas o ajudará a recuperar os dados excluídos por **Excluir Blob**, **Colocar Blob**, **Colocar Lista de Blocos**, **Colocar Bloco** e **Copiar Blob**. Os dados substituídos por uma chamada para **Colocar Página** não são recuperáveis.

**Preciso alterar meus aplicativos existentes para usar a exclusão reversível?**  
É possível aproveitar a exclusão reversível, independentemente da versão de API que você está usando. No entanto, para listar e recuperar blobs com exclusão reversível e instantâneos de blob, você precisará usar a versão de 29-07-2017 da [API REST dos Serviços de Armazenamento](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) ou superior. Em geral, é sempre recomendável usar a versão mais recente, independentemente de você estar usando ou não esse recurso.

## <a name="next-steps"></a>Próximas etapas
* [Código de exemplo do .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [API REST do serviço Blob](/rest/api/storageservices/blob-service-rest-api)
* [Replicação de armazenamento do Azure](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Projetando aplicativos altamente disponíveis usando RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Recuperação de desastre e failover de conta de armazenamento (versão prévia) no Armazenamento do Azure](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
