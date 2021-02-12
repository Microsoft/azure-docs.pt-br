---
title: Criar um compartilhamento de arquivo do Azure
titleSuffix: Azure Files
description: Como criar um compartilhamento de arquivos do Azure usando o portal do Azure, o PowerShell ou o CLI do Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 3ff7b3cd29740461a4f94f3c1d433086db119a09
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673799"
---
# <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivo do Azure
Para criar um compartilhamento de arquivos do Azure, você precisa responder a três perguntas sobre como você irá usá-lo:

- **Quais são os requisitos de desempenho para o compartilhamento de arquivos do Azure?**  
    Os arquivos do Azure oferecem compartilhamentos de arquivos padrão (incluindo compartilhamentos de arquivos de transações otimizados, quentes e interessantes), que são hospedados em hardware baseado em disco rígido (baseado em HDD) e compartilhamentos de arquivos premium, que são hospedados em hardware baseado em disco (SSD) de estado sólido.

- **De que tamanho o compartilhamento de arquivos você precisa?**  
    Os compartilhamentos de arquivos padrão podem abranger até 100 TiB, no entanto, esse recurso não está habilitado por padrão; Se você precisar de um compartilhamento de arquivos com mais de 5 TiB, será necessário habilitar o recurso de compartilhamento de arquivos grandes para sua conta de armazenamento. Os compartilhamentos de arquivos Premium podem abranger até 100 TiB sem nenhuma configuração especial, no entanto, os compartilhamentos de arquivos Premium são provisionados, em vez de pagar conforme o uso de compartilhamentos de arquivos padrão. Isso significa que o provisionamento de um compartilhamento de arquivos muito maior do que o necessário aumentará o custo total de armazenamento.

- **Quais são seus requisitos de redundância para o compartilhamento de arquivos do Azure?**  
    Compartilhamentos de arquivos padrão oferecem armazenamento com redundância local (LRS), com redundância de zona (ZRS), com redundância geográfica (GRS) ou com redundância de zona geográfica (GZRS), no entanto, o recurso de compartilhamento de arquivo grande tem suporte apenas em compartilhamentos de arquivos com redundância local e com redundância de zona. Os compartilhamentos de arquivos Premium não dão suporte a qualquer forma de redundância geográfica.

    Os compartilhamentos de arquivos premium estão disponíveis com redundância local na maioria das regiões que oferecem contas de armazenamento e com redundância de zona em um subconjunto menor de regiões. Para descobrir se os compartilhamentos de arquivos premium estão disponíveis atualmente em sua região, confira a página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=storage) para o Azure. Para obter informações sobre regiões que dão suporte a ZRS, consulte [redundância de armazenamento do Azure](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Para obter mais informações sobre essas três opções, consulte [planejando uma implantação de arquivos do Azure](storage-files-planning.md).

## <a name="prerequisites"></a>Pré-requisitos
- Este artigo pressupõe que você já tenha criado uma assinatura do Azure. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Se pretende usar o Azure PowerShell, [instale a versão mais recente](/powershell/azure/install-az-ps).
- Se pretende usar a CLI do Azure, [instale a versão mais recente](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Os compartilhamentos de arquivo do Azure são implantados em *contas de armazenamento*, que são objetos de nível superior que representam um pool de armazenamento compartilhado. Esse pool de armazenamento pode ser usado para implantar vários compartilhamentos de arquivos. 

O Azure dá suporte a vários tipos de contas de armazenamento para diferentes cenários de armazenamento que os clientes podem ter, mas há dois tipos principais de contas de armazenamento para arquivos do Azure. O tipo de conta de armazenamento que você precisa criar depende se você deseja criar um compartilhamento de arquivos padrão ou um compartilhamento de arquivos Premium: 

- **Contas de armazenamento GPv2 (uso geral versão 2)** : as contas de armazenamento GPv2 permitem que você implante compartilhamentos de arquivo do Azure em hardware padrão/baseado em HD (disco rígido). Além de armazenar compartilhamentos de arquivo do Azure, as contas de armazenamento GPv2 podem armazenar outros recursos de armazenamento, como contêineres de blob, filas ou tabelas. Os compartilhamentos de arquivos podem ser implantados nas camadas otimizadas para transação (padrão), quente ou fria.

- **Contas de armazenamento FileStorage**: as contas de armazenamento FileStorage permitem que você implante compartilhamentos de arquivo do Azure em hardware premium/baseado em SSD (disco de estado sólido). As contas FileStorage só podem ser usadas para armazenar compartilhamentos de arquivo do Azure; nenhum outro recurso de armazenamento (contêineres de blob, filas, tabelas etc.) pode ser implantado em uma conta FileStorage.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar uma conta de armazenamento por meio do portal do Azure, selecione **+ criar um recurso** no painel. Na janela de pesquisa resultante do Azure Marketplace, procure **conta de armazenamento** e selecione o resultado da pesquisa resultante. Isso levará a uma página de visão geral para contas de armazenamento; Selecione **criar** para continuar com o assistente de criação de conta de armazenamento.

![Uma captura de tela da opção criação rápida da conta de armazenamento em um navegador](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>A seção noções básicas
A primeira seção a ser concluída para criar uma conta de armazenamento é rotulada **noções básicas**. Isso contém todos os campos obrigatórios para criar uma conta de armazenamento. Para criar uma conta de armazenamento GPv2, verifique se o botão de opção **desempenho** está definido como *padrão* e se a lista suspensa **tipo de conta** está selecionada para *StorageV2 (uso geral v2)*.

![Uma captura de tela do botão de opção de desempenho com padrão selecionado e tipo de conta com StorageV2 selecionado](media/storage-how-to-create-file-share/create-storage-account-1.png)

Para criar uma conta de armazenamento do FileStorage, verifique se o botão de opção **desempenho** está definido como *Premium* e se a lista suspensa **tipo de conta** está selecionada para *armazenamento* em File.

![Uma captura de tela do botão de opção de desempenho com Premium selecionado e tipo de conta com FileStorage selecionado](media/storage-how-to-create-file-share/create-storage-account-2.png)

Os outros campos básicos são independentes da escolha da conta de armazenamento:
- **Assinatura**: a assinatura da conta de armazenamento a ser implantada. 
- **Grupo de recursos**: o grupo de recursos para a conta de armazenamento a ser implantada. Você pode criar um novo grupo de recursos ou usar um grupo de recursos existente. Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente.
- **Nome da conta de armazenamento**: o nome do recurso da conta de armazenamento a ser criado. Esse nome deve ser globalmente exclusivo, mas, caso contrário, pode ser qualquer nome desejado. O nome da conta de armazenamento será usado como o nome do servidor quando você montar um compartilhamento de arquivos do Azure via SMB.
- **Local**: a região da conta de armazenamento a ser implantada. Isso pode ser a região associada ao grupo de recursos ou qualquer outra região disponível.
- **Replicação**: embora isso seja rotulado como replicação, esse campo significa, na verdade, **redundância**; Este é o nível de redundância desejado: redundância local (LRS), redundância de zona (ZRS), redundância geográfica (GRS) e redundância de zona geográfica. Essa lista suspensa também contém a redundância geográfica com acesso de leitura (RA-GRS) e a redundância de zona geográfica com acesso de leitura (RA-GZRS), que não se aplica aos compartilhamentos de arquivos do Azure; qualquer compartilhamento de arquivos criado em uma conta de armazenamento com esses selecionados será, na verdade, com redundância geográfica ou com redundância de zona geográfica, respectivamente. Dependendo de sua região ou tipo de conta de armazenamento selecionado, algumas opções de redundância podem não ser permitidas.
- **Camada de acesso ao blob**: esse campo não se aplica aos arquivos do Azure, portanto, você pode escolher um dos botões de opção. 

> [!Important]  
> A seleção da camada de acesso ao BLOB não afeta a camada do compartilhamento de arquivos.

#### <a name="the-networking-blade"></a>A folha rede
A seção rede permite que você configure as opções de rede. Essas configurações são opcionais para a criação da conta de armazenamento e podem ser configuradas posteriormente, se desejado. Para obter mais informações sobre essas opções, consulte [considerações de rede de arquivos do Azure](storage-files-networking-overview.md).

#### <a name="the-advanced-blade"></a>A folha avançado
A seção avançado contém várias configurações importantes para compartilhamentos de arquivos do Azure:

- **Transferência segura necessária**: Este campo indica se a conta de armazenamento requer criptografia em trânsito para comunicação com a conta de armazenamento. É recomendável que isso seja deixado habilitado. no entanto, se você precisar de suporte a SMB 2,1, você deve desabilitar isso. Recomendamos se você desabilitar a criptografia para restringir o acesso de sua conta de armazenamento a uma rede virtual com pontos de extremidade de serviço e/ou pontos de extremidade privados.
- **Compartilhamentos de arquivos grandes**: esse campo habilita a conta de armazenamento para compartilhamentos de arquivos que abrangem até 100 Tib. Habilitar esse recurso limitará sua conta de armazenamento somente a opções de armazenamento com redundância local e com redundância de zona. Depois que uma conta de armazenamento GPv2 tiver sido habilitada para grandes compartilhamentos de arquivos, você não poderá desabilitar o recurso de compartilhamento de arquivo grande. Contas de armazenamento de armazenamento de arquivo (contas de armazenamento para compartilhamentos de arquivos Premium) não têm essa opção, pois todos os compartilhamentos de arquivos Premium podem ser escalados verticalmente para 100 TiB. 

![Uma captura de tela das configurações avançadas importantes que se aplicam aos arquivos do Azure](media/storage-how-to-create-file-share/create-storage-account-3.png)

As outras configurações disponíveis na guia Avançado (exclusão reversível de BLOB, namespace hierárquico para Azure Data Lake armazenamento Gen 2 e NFSv3 para armazenamento de BLOB) não se aplicam aos arquivos do Azure.

#### <a name="tags"></a>Marcas
Marcas são pares nome/valor que permitem categorizar recursos e exibir a cobrança consolidada por meio da aplicação da mesma marca a vários recursos e grupos de recursos. Eles são opcionais e podem ser aplicados após a criação da conta de armazenamento.

#### <a name="review--create"></a>Examinar + criar
A etapa final para criar a conta de armazenamento é selecionar o botão **criar** na guia **revisar + criar** . Esse botão não estará disponível se todos os campos obrigatórios para uma conta de armazenamento não estiverem preenchidos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para criar uma conta de armazenamento usando o PowerShell, usaremos o `New-AzStorageAccount` cmdlet. Este cmdlet tem muitas opções; somente as opções necessárias são mostradas. Para saber mais sobre as opções avançadas, consulte a [ `New-AzStorageAccount` documentação do cmdlet](/powershell/module/az.storage/new-azstorageaccount).

Para simplificar a criação da conta de armazenamento e o compartilhamento de arquivos subsequente, armazenaremos vários parâmetros em variáveis. Você pode substituir o conteúdo da variável por quaisquer valores que desejar. no entanto, observe que o nome da conta de armazenamento deve ser globalmente exclusivo.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Para criar uma conta de armazenamento capaz de armazenar compartilhamentos de arquivos padrão do Azure, usaremos o comando a seguir. O `-SkuName` parâmetro está relacionado ao tipo de redundância desejado; se desejar uma conta de armazenamento com redundância geográfica ou com redundância de zona geográfica, você também deverá remover o `-EnableLargeFileShare` parâmetro.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Para criar uma conta de armazenamento capaz de armazenar compartilhamentos de arquivos premium do Azure, usaremos o comando a seguir. Observe que o `-SkuName` parâmetro foi alterado para incluir `Premium` e o nível de redundância desejado de localmente redundante ( `LRS` ). O `-Kind` parâmetro é `FileStorage` , em vez de, `StorageV2` porque os compartilhamentos de arquivos Premium devem ser criados em uma conta de armazenamento de armazenamento em vez de uma conta de armazenamento GPv2.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para criar uma conta de armazenamento usando o CLI do Azure, usaremos o comando AZ Storage Account Create. Este comando tem várias opções; somente as opções necessárias são mostradas. Para saber mais sobre as opções avançadas, consulte a [ `az storage account create` documentação do comando](/cli/azure/storage/account).

Para simplificar a criação da conta de armazenamento e o compartilhamento de arquivos subsequente, armazenaremos vários parâmetros em variáveis. Você pode substituir o conteúdo da variável por quaisquer valores que desejar. no entanto, observe que o nome da conta de armazenamento deve ser globalmente exclusivo.

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Para criar uma conta de armazenamento capaz de armazenar compartilhamentos de arquivos padrão do Azure, usaremos o comando a seguir. O `--sku` parâmetro está relacionado ao tipo de redundância desejado; se desejar uma conta de armazenamento com redundância geográfica ou com redundância de zona geográfica, você também deverá remover o `--enable-large-file-share` parâmetro.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Para criar uma conta de armazenamento capaz de armazenar compartilhamentos de arquivos premium do Azure, usaremos o comando a seguir. Observe que o `--sku` parâmetro foi alterado para incluir `Premium` e o nível de redundância desejado de localmente redundante ( `LRS` ). O `--kind` parâmetro é `FileStorage` , em vez de, `StorageV2` porque os compartilhamentos de arquivos Premium devem ser criados em uma conta de armazenamento de armazenamento em vez de uma conta de armazenamento GPv2.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Criar o compartilhamento de arquivos
Depois de criar sua conta de armazenamento, tudo o que resta é criar o compartilhamento de arquivos. Esse processo é praticamente o mesmo, independentemente de você estar usando um compartilhamento de arquivos Premium ou um compartilhamento de arquivos padrão. Você deve considerar as diferenças a seguir.

Os compartilhamentos de arquivos padrão podem ser implantados em uma das camadas padrão: transações otimizadas (padrão), quente ou fria. Essa é uma camada de compartilhamento por arquivo que não é afetada pela **camada de acesso de blob** da conta de armazenamento (essa propriedade está relacionada apenas ao armazenamento de BLOBs do Azure-ela não está relacionada aos arquivos do Azure). Você pode alterar a camada do compartilhamento a qualquer momento após sua implantação. Os compartilhamentos de arquivos Premium não podem ser convertidos diretamente em compartilhamentos de arquivos padrão em qualquer camada padrão.

> [!Important]  
> Você pode mover compartilhamentos de arquivo entre camadas dentro de tipos de conta de armazenamento GPv2 (transação otimizada, quente e fria). Movimentações de compartilhamento entre camadas incorrem transações: mover de uma camada mais quente para uma camada mais fria incorrerá na cobrança da transação de gravação da camada mais fria para cada arquivo no compartilhamento, enquanto uma movimentação de uma camada mais fria para uma camada mais quente incorrerá na cobrança da transação de leitura da camada fria para cada arquivo no compartilhamento.

A propriedade **quota** significa algo ligeiramente diferente entre compartilhamentos de arquivos Premium e Standard:

- Para compartilhamentos de arquivos padrão, ele é um limite superior do compartilhamento de arquivos do Azure, além do qual os usuários finais não podem ir. O objetivo principal da cota de um compartilhamento de arquivos padrão é o orçamentário: "não quero que esse compartilhamento de arquivos cresça além desse ponto". Se uma cota não for especificada, o compartilhamento de arquivos padrão poderá abranger até 100 TiB (ou 5 TiB se a propriedade compartilhamentos de arquivos grandes não estiver definida para uma conta de armazenamento).

- Para compartilhamentos de arquivos premium, a cota é sobrecarregada para significar o **tamanho provisionado**. O tamanho provisionado é o valor para o qual você será cobrado, independentemente do uso real. Ao provisionar um compartilhamento de arquivos premium, você deve considerar dois fatores: 1) o crescimento futuro do compartilhamento de uma perspectiva de utilização de espaço e 2) o IOPS necessário para sua carga de trabalho. Cada GiB provisionada lhe dá direito a IOPS reservada e de intermitência adicional. Para obter mais informações sobre como planejar um compartilhamento de arquivos premium, consulte [Provisionando compartilhamentos de arquivos Premium](understanding-billing.md#provisioned-model).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Se você acabou de criar sua conta de armazenamento, poderá navegar até ela na tela de implantação selecionando **ir para o recurso**. Se você já tiver criado a conta de armazenamento, poderá navegar para ela por meio do grupo de recursos que a contém. Uma vez na conta de armazenamento, selecione o bloco rotulado **compartilhamentos de arquivos** (você também pode navegar até **compartilhamentos de arquivos** por meio do Sumário da conta de armazenamento).

![Uma captura de tela do bloco de compartilhamentos de arquivos](media/storage-how-to-create-file-share/create-file-share-1.png)

Na listagem compartilhamento de arquivos, você deve ver os compartilhamentos de arquivos criados anteriormente nesta conta de armazenamento; uma tabela vazia se nenhum compartilhamento de arquivos tiver sido criado ainda. Selecione **+ compartilhamento de arquivos** para criar um novo compartilhamento de arquivos.

A folha novo compartilhamento de arquivos deve aparecer na tela. Preencha os campos na folha novo compartilhamento de arquivos para criar um compartilhamento de arquivos:

- **Nome**: o nome do compartilhamento de arquivos a ser criado.
- **Cota**: a cota do compartilhamento de arquivos para compartilhamentos de arquivos padrão; o tamanho provisionado do compartilhamento de arquivos para compartilhamentos de arquivos premium.
- **Camadas**: a camada selecionada para um compartilhamento de arquivos. Esse campo só está disponível em uma **conta de armazenamento de uso geral (GPv2)**. Você pode escolher transação otimizada, quente ou fria. A camada do compartilhamento pode ser alterada a qualquer momento. É recomendável escolher a camada mais alta possível durante uma migração, para minimizar as despesas de transação e, em seguida, alternar para uma camada inferior, se desejado, depois que a migração for concluída.

Selecione **criar** para concluir a criação do novo compartilhamento. Observe que se sua conta de armazenamento estiver em uma rede virtual, você não poderá criar com êxito um compartilhamento de arquivos do Azure, a menos que o cliente também esteja na rede virtual. Você também pode contornar essa limitação pontual usando o `New-AzRmStorageShare` cmdlet Azure PowerShell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Você pode criar um compartilhamento de arquivos do Azure com o [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet. Os comandos do PowerShell a seguir pressupõem que você definiu as variáveis `$resourceGroupName` e `$storageAccountName` conforme definido acima na seção criando uma conta de armazenamento com Azure PowerShell. 

O exemplo a seguir mostra a criação de um compartilhamento de arquivos com uma camada explícita usando o `-AccessTier` parâmetro. Isso requer o uso do módulo de visualização AZ. Storage conforme indicado no exemplo. Se uma camada não for especificada, seja porque você está usando o módulo AZ. Storage GA ou porque você não incluiu esse comando, a camada padrão para compartilhamentos de arquivos padrão é a transação otimizada.

> [!Important]  
> Para compartilhamentos de arquivos premium, o `-QuotaGiB` parâmetro refere-se ao tamanho provisionado do compartilhamento de arquivos. O tamanho provisionado do compartilhamento de arquivos é o valor que será cobrado, independentemente do uso. Os compartilhamentos de arquivos padrão são cobrados com base no uso em vez do tamanho provisionado.

```powershell
# Update the Azure storage module to use the preview version. You may need to close and 
# reopen PowerShell before running this command. If you are running PowerShell 5.1, ensure 
# the following:
# - Run the below cmdlets as an administrator.
# - Have PowerShellGet 2.2.3 or later. Uncomment the following line to check.
# Get-Module -ListAvailable -Name PowerShellGet
Remove-Module -Name Az.Storage -ErrorAction SilentlyContinue
Uninstall-Module -Name Az.Storage
Install-Module -Name Az.Storage -RequiredVersion "2.1.1-preview" -AllowClobber -AllowPrerelease 

# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

> [!Note]  
> A capacidade de definir e alterar as camadas por meio do PowerShell é fornecida no módulo de visualização AZ. Storage PowerShell. Esses cmdlets ou sua saída podem ser alterados antes de serem liberados no módulo AZ. Storage PowerShell disponível para o público geral. portanto, crie scripts com isso em mente.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Você pode criar um compartilhamento de arquivos do Azure com o [`az storage share-rm create`](/cli/azure/storage/share-rm?preserve-view=true&view=azure-cli-latest#az_storage_share_rm_create) comando. Os comandos CLI do Azure a seguir pressupõem que você definiu as variáveis `$resourceGroupName` e `$storageAccountName` conforme definido acima na seção criando uma conta de armazenamento com CLI do Azure.

A funcionalidade para criar ou mover um compartilhamento de arquivos para uma camada específica está disponível na atualização mais recente do CLI do Azure. A atualização CLI do Azure é específica para a distribuição do sistema operacional/Linux que você está usando. Para obter instruções sobre como atualizar CLI do Azure em seu sistema, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

> [!Important]  
> Para compartilhamentos de arquivos premium, o `--quota` parâmetro refere-se ao tamanho provisionado do compartilhamento de arquivos. O tamanho provisionado do compartilhamento de arquivos é o valor que será cobrado, independentemente do uso. Os compartilhamentos de arquivos padrão são cobrados com base no uso em vez do tamanho provisionado.

```azurecli
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

> [!Note]  
> A capacidade de definir uma camada com o `--access-tier` parâmetro é fornecida uma visualização no pacote de CLI do Azure mais recente. Este comando ou sua saída pode mudar antes de ser marcado como disponível para o público geral, portanto, crie scripts com isso em mente.

---

> [!Note]  
> O nome do seu compartilhamento de arquivo deve estar em minúsculas. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

### <a name="changing-the-tier-of-an-azure-file-share"></a>Alterando a camada de um compartilhamento de arquivos do Azure
Os compartilhamentos de arquivos implantados na **conta de armazenamento de uso geral v2 (GPv2)** podem estar nas camadas de transação otimizada, quente ou fria. Você pode alterar a camada do compartilhamento de arquivos do Azure a qualquer momento, sujeito a custos de transações, conforme descrito acima.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Na página conta de armazenamento principal, selecione **compartilhamentos de arquivos**  selecione o bloco rotulado **compartilhamentos de arquivos** (você também pode navegar para **compartilhamentos de arquivos** por meio do Sumário da conta de armazenamento).

![Uma captura de tela do bloco de compartilhamentos de arquivos](media/storage-how-to-create-file-share/create-file-share-1.png)

Na lista tabela de compartilhamentos de arquivos, selecione o compartilhamento de arquivos para o qual você gostaria de alterar a camada. Na página Visão geral do compartilhamento de arquivos, selecione **alterar camada** no menu.

![Uma captura de tela da página Visão geral do compartilhamento de arquivos com o botão Alterar camada realçado](media/storage-how-to-create-file-share/change-tier-0.png)

Na caixa de diálogo resultante, selecione a camada desejada: transação otimizada, quente ou fria.

![Uma captura de tela da caixa de diálogo Alterar camada](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
O cmdlet do PowerShell a seguir pressupõe que você definiu as `$resourceGroupName` `$storageAccountName` variáveis,, `$shareName` conforme descrito nas seções anteriores deste documento.

```PowerShell
# This cmdlet requires Az.Storage version 2.1.1-preview, which is installed
# in the earlier example.
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
O comando a seguir CLI do Azure pressupõe que você definiu `$resourceGroupName` as `$storageAccountName` variáveis, e `$shareName` conforme descrito nas seções anteriores deste documento.

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Próximas etapas
- [Planeje uma implantação de arquivos do Azure](storage-files-planning.md) ou [planeje uma implantação de sincronização de arquivos do Azure](storage-sync-files-planning.md). 
- [Visão geral da rede](storage-files-networking-overview.md).
- Conecte e monte um compartilhamento de arquivos no [Windows](storage-how-to-use-files-windows.md), no [MacOS](storage-how-to-use-files-mac.md)e no [Linux](storage-how-to-use-files-linux.md).