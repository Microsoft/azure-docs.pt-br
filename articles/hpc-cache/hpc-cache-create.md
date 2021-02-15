---
title: Criar um Azure HPC Cache
description: Como criar uma instância do Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 77bd5e3b7a258ef83e5de4ec645ea70578fb9dfb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657084"
---
# <a name="create-an-azure-hpc-cache"></a>Criar um Azure HPC Cache

Use o portal do Azure ou o CLI do Azure para criar o cache.

![captura de tela de uma visão geral do cache no portal do Azure, com botão de criação na parte inferior](media/hpc-cache-home-page.png)

Clique na imagem abaixo para assistir a uma [demonstração em vídeo](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) da criação de um cache e da adição de um destino de armazenamento.

[![miniatura de vídeo: cache do HPC do Azure: instalação (clique para visitar a página de vídeo)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="define-basic-details"></a>Definir os detalhes básicos

![captura de tela da página de detalhes do projeto no portal do Azure](media/hpc-cache-create-basics.png)

Em **Detalhes do Projeto**, selecione a assinatura e o grupo de recursos que hospedarão o cache.

Em **Detalhes do Serviço**, defina o nome do cache e estes outros atributos:

* Localização – selecione uma das [regiões com suporte](hpc-cache-overview.md#region-availability).
* Rede virtual – você pode selecionar uma existente ou criar uma nova rede virtual.
* Sub-rede – escolha ou crie uma sub-rede com pelo menos 64 endereços IP (/24). Essa sub-rede deve ser usada somente para esta instância de cache do Azure HPC.

## <a name="set-cache-capacity"></a>Definir a capacidade de cache
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na página **Cache**, você precisa definir a capacidade de seu cache. Os valores definidos aqui determinam a quantidade de dados que o cache pode armazenar e a rapidez com que ele pode atender às solicitações do cliente.

A capacidade também afeta o custo do cache.

Escolha a capacidade definindo estes dois valores:

* A taxa máxima de transferência de dados para o cache (taxa de transferência), em GB/segundo
* A quantidade de armazenamento alocada para dados armazenados em cache, em TB

Escolha um dos valores de taxa de transferência disponíveis e tamanhos de armazenamento em cache.

Tenha em mente que a taxa de transferência de dados real depende da carga de trabalho, das velocidades de rede e do tipo dos destinos de armazenamento. Os valores escolhidos definem a taxa de transferência máxima para todo o sistema de cache, mas uma parte dela é usada para tarefas de sobrecarga. Por exemplo, se um cliente solicitar um arquivo que ainda não esteja armazenado no cache, ou se o arquivo estiver marcado como obsoleto, seu cache usará parte de sua taxa de transferência para obtê-lo do armazenamento de back-end.

O Azure HPC Cache gerencia quais arquivos são armazenados em cache e pré-carregados para maximizar as tarifas de acesso ao cache. O conteúdo do cache é avaliado continuamente e os arquivos são movidos para o armazenamento de longo prazo quando são acessados com menos frequência. Escolha um tamanho de armazenamento de cache que possa manter confortavelmente o conjunto ativo de arquivos de trabalho, além de espaço adicional para metadados e outras sobrecargas.

![captura de tela da página de dimensionamento do cache](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Habilitar criptografia de Azure Key Vault (opcional)

Se o cache estiver em uma região que dá suporte a chaves de criptografia gerenciadas pelo cliente, a página **chaves de criptografia de disco** aparecerá entre as guias **cache** e **marcas** . Leia [disponibilidade regional](hpc-cache-overview.md#region-availability) para saber mais sobre o suporte de região.

Se você quiser gerenciar as chaves de criptografia usadas para o armazenamento de cache, forneça as informações de Azure Key Vault na página **chaves de criptografia de disco** . O cofre de chaves deve estar na mesma região e na mesma assinatura que o cache.

Você poderá ignorar esta seção se não precisar de chaves gerenciadas pelo cliente. Por padrão, o Azure criptografa dados com chaves gerenciadas pela Microsoft. Leia [criptografia de armazenamento do Azure](../storage/common/storage-service-encryption.md) para saber mais.

> [!NOTE]
>
> * Você não pode alterar entre chaves gerenciadas pela Microsoft e chaves gerenciadas pelo cliente depois de criar o cache.
> * Depois que o cache é criado, você deve autorizá-lo a acessar o cofre de chaves. Clique no botão **habilitar criptografia** na página **visão geral** do cache para ativar a criptografia. Siga esta etapa dentro de 90 minutos da criação do cache.
> * Os discos de cache são criados após essa autorização. Isso significa que o tempo de criação do cache inicial é curto, mas o cache não estará pronto para ser usado por dez minutos ou mais depois que você autorizar o acesso.

Para obter uma explicação completa do processo de criptografia de chave gerenciada pelo cliente, leia [usar chaves de criptografia gerenciadas pelo cliente para o cache do Azure HPC](customer-keys.md).

![captura de tela da página chaves de criptografia com os campos "gerenciado pelo cliente" selecionados e cofre de chaves mostrando](media/create-encryption.png)

Selecione **cliente gerenciado** para escolher criptografia de chave gerenciada pelo cliente. Os campos de especificação do cofre de chaves são exibidos. Selecione a Azure Key Vault a ser usada e, em seguida, selecione a chave e a versão a serem usadas para esse cache. A chave deve ser uma chave RSA de 2048 bits. Você pode criar um novo cofre de chaves, chave ou versão de chave nesta página.

Depois de criar o cache, você deve autorizá-lo a usar o serviço de cofre de chaves. Leia [autorizar Azure Key Vault criptografia do cache](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) para obter detalhes.

## <a name="add-resource-tags-optional"></a>Adicionar marcas de recurso (opcional)

A página **Marcas** permite que você adicione [tags de recurso](../azure-resource-manager/management/tag-resources.md) à sua instância do Azure HPC Cache.

## <a name="finish-creating-the-cache"></a>Concluir a criação do cache

Depois de configurar o novo cache, clique na guia **revisar + criar** . O portal valida suas seleções e permite que você examine suas escolhas. Se tudo estiver correto, clique em **Criar**.

A criação do cache leva cerca de 10 minutos. Você pode acompanhar o progresso no painel de notificações do portal do Azure.

![captura de tela das páginas "implantação em andamento" e "notificações" da criação de cache no portal](media/hpc-cache-deploy-status.png)

Quando a criação for concluída, uma notificação será exibida com um link para a nova instância do Azure HPC Cache e o cache será exibido na lista **Recursos** de sua assinatura.

![captura de tela da instância do Azure HPC Cache no portal do Azure](media/hpc-cache-new-overview.png)

> [!NOTE]
> Se o cache usar chaves de criptografia gerenciadas pelo cliente, o cache poderá aparecer na lista de recursos antes que o status da implantação seja alterado para concluído. Assim que o status do cache estiver **aguardando a chave** , você poderá [autorizá-lo](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) a usar o cofre de chaves.

## <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Criar o cache com CLI do Azure

[Configurar CLI do Azure para o cache do HPC do Azure](./az-cli-prerequisites.md).

> [!NOTE]
> Atualmente, o CLI do Azure não dá suporte à criação de um cache com chaves de criptografia gerenciadas pelo cliente. Use o portal do Azure.

Use o comando [AZ HPC-cache Create](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-create) para criar um novo cache do Azure HPC.

Forneça estes valores:

* Nome do grupo de recursos de cache
* Nome do cache
* Região do Azure
* Sub-rede de cache, neste formato:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  A sub-rede de cache precisa de pelo menos 64 endereços IP (/24) e não pode alojar outros recursos.

* Capacidade de cache. Dois valores definem a taxa de transferência máxima do cache do Azure HPC:

  * O tamanho do cache (em GB)
  * A SKU das máquinas virtuais usadas na infraestrutura de cache

  [AZ HPC-cache SKUs List](/cli/azure/ext/hpc-cache/hpc-cache/skus) mostra as SKUs disponíveis e as opções de tamanho de cache válidas para cada uma. As opções de tamanho de cache variam de 3 TB a 48 TB, mas há suporte apenas para alguns valores.

  Este gráfico mostra quais combinações de tamanho de cache e SKU são válidas no momento em que este documento está sendo preparado (julho de 2020).

  | Tamanho do cache | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | sim         | no          | no          |
  | 6144 GB    | sim         | sim         | no          |
  | 12288 GB   | sim         | sim         | sim         |
  | 24576 GB   | no          | sim         | sim         |
  | 49152 GB   | no          | no          | sim         |

  Leia a seção **definir capacidade de cache** na guia instruções do portal para obter informações importantes sobre preços, taxa de transferência e como dimensionar o cache adequadamente para seu fluxo de trabalho.

Exemplo de criação de cache:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

A criação do cache leva vários minutos. Em caso de sucesso, o comando Create retorna uma saída como esta:

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

A mensagem inclui algumas informações úteis, incluindo estes itens:

* Endereços de montagem do cliente-use esses endereços IP quando estiver pronto para conectar clientes ao cache. Leia [montar o cache HPC do Azure](hpc-cache-mount.md) para saber mais.
* Status da atualização-quando uma atualização de software for liberada, essa mensagem será alterada. Você pode [atualizar o software de cache](hpc-cache-manage.md#upgrade-cache-software) manualmente em um momento conveniente ou ele será aplicado automaticamente após vários dias.

## <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

> [!CAUTION]
> O módulo AZ. HPCCache do PowerShell está atualmente em visualização pública. A versão prévia é fornecida sem um contrato de nível de serviço. Ela não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter recursos restritos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Requisitos

Se você optar por usar o PowerShell localmente, este artigo exigirá que você instale o módulo Az PowerShell e conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Para obter mais informações sobre como instalar o módulo Az PowerShell, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Se você optar por usar o Cloud Shell, confira [Visão geral do Azure Cloud Shell](../cloud-shell/overview.md) para obter mais informações.

> [!IMPORTANT]
> Enquanto o módulo **AZ. HPCCache** do PowerShell está em versão prévia, você deve instalá-lo separadamente usando o `Install-Module` cmdlet. Depois que esse módulo do PowerShell estiver disponível para o público geral, ele fará parte das versões futuras do módulo do PowerShell AZ e estará disponível nativamente em Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Criar o cache com Azure PowerShell

> [!NOTE]
> Atualmente, Azure PowerShell não dá suporte à criação de um cache com chaves de criptografia gerenciadas pelo cliente. Use o portal do Azure.

Use o cmdlet [New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) para criar um novo cache do Azure HPC.

Forneça esses valores:

* Nome do grupo de recursos de cache
* Nome do cache
* Região do Azure
* Sub-rede de cache, neste formato:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  A sub-rede de cache precisa de pelo menos 64 endereços IP (/24) e não pode alojar outros recursos.

* Capacidade de cache. Dois valores definem a taxa de transferência máxima do cache do Azure HPC:

  * O tamanho do cache (em GB)
  * A SKU das máquinas virtuais usadas na infraestrutura de cache

  [Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) mostra as SKUs disponíveis e as opções de tamanho de cache válidas para cada uma. As opções de tamanho de cache variam de 3 TB a 48 TB, mas há suporte apenas para alguns valores.

  Este gráfico mostra quais combinações de tamanho de cache e SKU são válidas no momento em que este documento está sendo preparado (julho de 2020).

  | Tamanho do cache | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | sim         | no          | no          |
  | 6144 GB    | sim         | sim         | no          |
  | 12.288 GB   | sim         | sim         | sim         |
  | 24.576 GB   | no          | sim         | sim         |
  | 49.152 GB   | no          | no          | sim         |

  Leia a seção **definir capacidade de cache** na guia instruções do portal para obter informações importantes sobre preços, taxa de transferência e como dimensionar o cache adequadamente para seu fluxo de trabalho.

Exemplo de criação de cache:

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

A criação do cache leva vários minutos. Em caso de sucesso, o comando Create retorna a seguinte saída:

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

A mensagem inclui algumas informações úteis, incluindo estes itens:

* Endereços de montagem do cliente-use esses endereços IP quando estiver pronto para conectar clientes ao cache. Leia [montar o cache HPC do Azure](hpc-cache-mount.md) para saber mais.
* Status da atualização-quando uma atualização de software é liberada, essa mensagem é alterada. Você pode [atualizar o software de cache](hpc-cache-manage.md#upgrade-cache-software) manualmente em um momento conveniente ou ele é aplicado automaticamente após vários dias.

---

## <a name="next-steps"></a>Próximas etapas

Depois que o cache aparecer na lista de **recursos** , você poderá passar para a próxima etapa.

* [Defina os destinos de armazenamento](hpc-cache-add-storage.md) para dar acesso ao cache às suas fontes de dados.
* Se você usar chaves de criptografia gerenciadas pelo cliente, será necessário [autorizar Azure Key Vault criptografia](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) na página Visão geral do cache para concluir a configuração do cache. Você deve fazer essa etapa antes de poder adicionar armazenamento. Leia [usar chaves de criptografia gerenciadas pelo cliente](customer-keys.md) para obter detalhes.