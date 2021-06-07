---
title: Atualizar destinos de armazenamento do cache HPC do Azure
description: Como editar destinos de armazenamento do cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/10/2021
ms.author: v-erkel
ms.openlocfilehash: 0c505937d4adbe2596e91ed7269676e60ada8253
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772562"
---
# <a name="edit-storage-targets"></a>Editar destinos de armazenamento

Você pode remover ou modificar destinos de armazenamento com o portal do Azure ou usando o CLI do Azure.

Dependendo do tipo de armazenamento, você pode modificar esses valores de destino de armazenamento:

* Para destinos de armazenamento de BLOBs, você pode alterar o caminho do namespace e a política de acesso.

* Para destinos de armazenamento NFS, você pode alterar esses valores:

  * Caminhos de namespace
  * Política de acesso
  * O subdiretório de exportação de armazenamento ou exportação associado a um caminho de namespace
  * Modelo de uso

* Para destinos de armazenamento ADLS-NFS, você pode alterar o caminho do namespace, a política de acesso e o modelo de uso.

Você não pode editar o nome, tipo ou sistema de armazenamento de back-end de um destino de armazenamento (contêiner de BLOB ou endereço IP/nome de host do NFS). Se você precisar alterar essas propriedades, exclua o destino de armazenamento e crie uma substituição com o novo valor.

> [!TIP]
> O [vídeo Gerenciando o cache HPC do Azure](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) mostra como editar um destino de armazenamento no portal do Azure.

## <a name="remove-a-storage-target"></a>Remover um destino de armazenamento

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para remover um destino de armazenamento, abra a página **destinos de armazenamento** . Selecione o destino de armazenamento na lista e clique no botão **excluir** .

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar CLI do Azure para o cache do HPC do Azure](./az-cli-prerequisites.md).

Use [AZ HPC-cache Storage-Target remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) para excluir um destino de armazenamento do cache.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

A exclusão de um destino de armazenamento remove a associação do sistema de armazenamento com esse sistema de cache HPC do Azure, mas não altera o sistema de armazenamento de back-end. Por exemplo, se você usou um contêiner de armazenamento de BLOBs do Azure, o contêiner e seu conteúdo ainda existem depois de você excluí-lo do cache. Você pode adicionar o contêiner a um cache do HPC do Azure diferente, adicioná-lo novamente a esse cache ou excluí-lo com o portal do Azure.

Todas as alterações de arquivo armazenadas no cache são gravadas no sistema de armazenamento de back-end antes de o destino de armazenamento ser removido. Esse processo pode levar uma hora ou mais se muitos dados alterados estiverem no cache.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Alterar o caminho do namespace de um destino de armazenamento de BLOBs

Os caminhos de namespace são os caminhos que os clientes usam para montar esse destino de armazenamento. (Para saber mais, leia [planejar o namespace agregado](hpc-cache-namespace.md) e [Configurar o namespace agregado](add-namespace-paths.md)).

O caminho do namespace é a única atualização que você pode fazer em um destino de armazenamento de BLOBs do Azure. Use o portal do Azure ou o CLI do Azure para alterá-lo.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Use a página de **namespace** para o cache do Azure HPC. A página namespace é descrita mais detalhadamente no artigo [Configurar o namespace agregado](add-namespace-paths.md).

Clique no nome do caminho que você deseja alterar e crie o novo caminho na janela de edição que aparece.

![Captura de tela da página de namespace depois de clicar em um caminho de namespace de blob-os campos de edição aparecem em um painel à direita](media/update-namespace-blob.png)

Depois de fazer alterações, clique em **OK** para atualizar o destino de armazenamento ou clique em **Cancelar** para descartar as alterações.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar CLI do Azure para o cache do HPC do Azure](./az-cli-prerequisites.md).

Para alterar o namespace de um destino de armazenamento de BLOBs com o CLI do Azure, use o comando [AZ HPC-cache BLOB-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update). Somente o `--virtual-namespace-path` valor pode ser alterado.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Atualizar um destino de armazenamento NFS

Para destinos de armazenamento NFS, você pode alterar ou adicionar caminhos de namespace virtual, alterar os valores de exportação ou subdiretório NFS aos quais um caminho de namespace aponta e alterar o modelo de uso.

Os destinos de armazenamento em caches com alguns tipos de configurações personalizadas de DNS também têm um controle para atualizar seus endereços IP. (Esse tipo de configuração é raro.)

Os detalhes estão abaixo:

* [Alterar valores de namespace agregados](#change-aggregated-namespace-values) (caminho de namespace virtual, política de acesso, exportar e subdiretório de exportação)
* [Alterar o modelo de uso](#change-the-usage-model)
* [Atualizar DNS](#update-ip-address-custom-dns-configurations-only)

### <a name="change-aggregated-namespace-values"></a>Alterar valores de namespace agregados

Você pode usar a portal do Azure ou a CLI do Azure para alterar o caminho do namespace voltado para o cliente, a exportação de armazenamento e o subdiretório de exportação (se usado).

Leia as diretrizes em [adicionar caminhos de namespace NFS](add-namespace-paths.md#nfs-namespace-paths) se você precisar de um lembrete sobre como criar vários caminhos válidos em um destino de armazenamento.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Use a página de **namespace** para o cache do Azure HPC para atualizar valores de namespace. Esta página é descrita mais detalhadamente no artigo [Configurar o namespace agregado](add-namespace-paths.md).

![captura de tela da página namespace do portal com a página de atualização do NFS aberta à direita](media/update-namespace-nfs.png)

1. Clique no nome do caminho que você deseja alterar.
1. Use a janela Editar para digitar novos valores de caminho virtual, exportação ou subdiretório, ou para selecionar uma política de acesso diferente.
1. Depois de fazer alterações, clique em **OK** para atualizar o destino de armazenamento ou em **Cancelar** para descartar as alterações.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar CLI do Azure para o cache do HPC do Azure](./az-cli-prerequisites.md).

Use a ``--junction`` opção no comando [AZ HPC-cache NFS-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) para alterar o caminho do namespace, a exportação de NFS ou o subdiretório de exportação.

O ``--junction`` parâmetro usa estes valores:

* ``namespace-path`` -O caminho do arquivo virtual voltado para o cliente
* ``nfs-export`` -A exportação do sistema de armazenamento para associar ao caminho voltado para o cliente
* ``target-path`` (opcional)-um subdiretório da exportação, se necessário

Exemplo: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Você deve fornecer todos os três valores para cada caminho na ``--junction`` instrução. Use os valores existentes para quaisquer valores que você não deseja alterar.

O nome do cache, o nome do destino de armazenamento e o grupo de recursos também são necessários em todos os comandos de atualização.

Exemplo de comando:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Alterar o modelo de uso

O modelo de uso influencia como o cache retém os dados. Leia [escolher um modelo de uso](hpc-cache-add-storage.md#choose-a-usage-model) para saber mais.

Para alterar o modelo de uso de um destino de armazenamento NFS, use um desses métodos.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Altere o modelo de uso da página **destinos de armazenamento** no portal do Azure. Clique no nome do destino de armazenamento a ser alterado.

![captura de tela da página de edição de um destino de armazenamento NFS](media/edit-storage-nfs.png)

Use o seletor suspenso para escolher um novo modelo de uso. Clique em **OK** para atualizar o destino de armazenamento ou clique em **Cancelar** para descartar as alterações.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar CLI do Azure para o cache do HPC do Azure](./az-cli-prerequisites.md).

Use o comando [AZ HPC-cache NFS-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) .

O comando Update é quase idêntico ao comando que você usa para adicionar um destino de armazenamento NFS. Consulte [criar um destino de armazenamento NFS](hpc-cache-add-storage.md#create-an-nfs-storage-target) para obter detalhes e exemplos.

Para alterar o modelo de uso, atualize a ``--nfs3-usage-model`` opção. Exemplo: ``--nfs3-usage-model WRITE_WORKLOAD_15``

O nome do cache, o nome do destino de armazenamento e os valores do grupo de recursos também são necessários.

Se você quiser verificar os nomes dos modelos de uso, use o comando [AZ HPC-cache Usage-Model List](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

Se o cache for interrompido ou não estiver em um estado íntegro, a atualização será aplicada depois que o cache estiver íntegro.

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>Atualizar endereço IP (somente configurações de DNS personalizadas)

Se o seu cache usa uma configuração de DNS não padrão, é possível que o endereço IP do destino de armazenamento NFS seja alterado devido a alterações de DNS de back-end. Se o servidor DNS alterar o endereço IP do sistema de armazenamento de back-end, o cache HPC do Azure poderá perder o acesso ao sistema de armazenamento.

O ideal é que você trabalhe com o gerente do sistema DNS personalizado do seu cache para planejar as atualizações, pois essas alterações tornam o armazenamento indisponível.

Se você precisar atualizar o endereço IP fornecido pelo DNS de um destino de armazenamento, há um botão na lista de destinos de armazenamento. Clique em **Atualizar DNS** para consultar o servidor DNS personalizado em busca de um novo endereço IP.

![Captura de tela da lista de destino de armazenamento. Para um destino de armazenamento, o "..." na coluna da extrema direita está aberta e duas opções são exibidas: excluir e atualizar o DNS.](media/refresh-dns.png)

Se for bem-sucedido, a atualização deverá levar menos de dois minutos. Você só pode atualizar um destino de armazenamento de cada vez; Aguarde a conclusão da operação anterior antes de tentar outra.

## <a name="update-an-adls-nfs-storage-target-preview"></a>Atualizar um destino de armazenamento ADLS-NFS (visualização)

Assim como os destinos NFS, você pode alterar o caminho do namespace e o modelo de uso para destinos de armazenamento ADLS-NFS.

### <a name="change-an-adls-nfs-namespace-path"></a>Alterar um caminho de namespace ADLS-NFS

Use a página de **namespace** para o cache do Azure HPC para atualizar valores de namespace. Esta página é descrita mais detalhadamente no artigo [Configurar o namespace agregado](add-namespace-paths.md).

![captura de tela da página de namespace do portal com uma página de atualização do ADS-NFS aberta à direita](media/update-namespace-adls.png)

1. Clique no nome do caminho que você deseja alterar.
1. Use a janela Editar para digitar o novo caminho virtual ou atualize a política de acesso.
1. Depois de fazer alterações, clique em **OK** para atualizar o destino de armazenamento ou em **Cancelar** para descartar as alterações.

### <a name="change-adls-nfs-usage-models"></a>Alterar modelos de uso ADLS-NFS

A configuração para modelos de uso ADLS-NFS é idêntica à seleção de modelo de uso do NFS. Leia as instruções do portal em [alterar o modelo de uso](#change-the-usage-model) na seção de NFS acima. Ferramentas adicionais para atualizar os destinos de armazenamento ADLS-NFS estão em desenvolvimento.


## <a name="next-steps"></a>Próximas etapas

* Leia [Adicionar destinos de armazenamento](hpc-cache-add-storage.md) para saber mais sobre essas opções.
* Leia [planejar o namespace agregado](hpc-cache-namespace.md) para obter mais dicas sobre como usar caminhos virtuais.
