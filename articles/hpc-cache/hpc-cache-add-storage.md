---
title: Adicionar armazenamento a um cache HPC do Azure
description: Como definir destinos de armazenamento para que o cache HPC do Azure possa usar seu sistema NFS local ou contêineres de blob do Azure para o armazenamento de arquivos de longo prazo
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 01/28/2021
ms.author: v-erkel
ms.openlocfilehash: b4df5863cc746490f13685a8d412232217af3bc8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054358"
---
# <a name="add-storage-targets"></a>Adicionar destinos de armazenamento

Os *destinos de armazenamento* são armazenamento de back-end para arquivos que são acessados por meio de um cache do HPC do Azure. Você pode adicionar armazenamento NFS (como um sistema de hardware local) ou armazenar dados no blob do Azure.

Você pode definir até dez destinos de armazenamento diferentes para um cache. O cache apresenta todos os destinos de armazenamento em um namespace agregado.

Os caminhos de namespace são configurados separadamente depois que você adiciona os destinos de armazenamento. Em geral, um destino de armazenamento NFS pode ter até dez caminhos de namespace ou mais para algumas configurações grandes. Leia [caminhos de namespace NFS](add-namespace-paths.md#nfs-namespace-paths) para obter detalhes.

Lembre-se de que as exportações de armazenamento devem ser acessíveis da rede virtual do seu cache. Para o armazenamento de hardware local, talvez seja necessário configurar um servidor DNS que possa resolver nomes de host para acesso de armazenamento NFS. Leia mais em [acesso DNS](hpc-cache-prerequisites.md#dns-access).

Adicione destinos de armazenamento depois de criar o cache. Siga este processo:

1. [Criar o cache](hpc-cache-create.md)
1. Definir um destino de armazenamento (informações neste artigo)
1. [Criar os caminhos voltados](add-namespace-paths.md) para o cliente (para o [namespace agregado](hpc-cache-namespace.md))

O procedimento para adicionar um destino de armazenamento é ligeiramente diferente, dependendo se você está adicionando o armazenamento de BLOBs do Azure ou uma exportação de NFS. Os detalhes de cada um estão abaixo.

Clique na imagem abaixo para assistir a uma [demonstração em vídeo](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) da criação de um cache e da adição de um destino de armazenamento do portal do Azure.

[![miniatura de vídeo: cache do HPC do Azure: instalação (clique para visitar a página de vídeo)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Adicionar um novo destino de armazenamento de BLOBs do Azure

Um novo destino de armazenamento de BLOBs precisa de um contêiner de blob vazio ou um contêiner que é preenchido com dados no formato do sistema de arquivos da nuvem de cache do HPC do Azure. Leia mais sobre pré-carregando um contêiner de BLOBs em [mover dados para o armazenamento de BLOBs do Azure](hpc-cache-ingest.md).

A página portal do Azure **Adicionar destino de armazenamento** inclui a opção de criar um novo contêiner de BLOBs antes de adicioná-lo.

### <a name="portal"></a>[Portal](#tab/azure-portal)

No portal do Azure, abra sua instância de cache e clique em **destinos de armazenamento** na barra lateral esquerda.

![captura de tela das configurações > página de destino de armazenamento, com dois destinos de armazenamento existentes em uma tabela e um realce ao contrário do botão + Adicionar destino de armazenamento acima da tabela](media/add-storage-target-button.png)

A página **destinos de armazenamento** lista todos os destinos existentes e fornece um link para adicionar um novo.

Clique no botão **Adicionar destino de armazenamento** .

![captura de tela da página Adicionar destino de armazenamento, preenchida com informações para um novo destino de armazenamento de BLOBs do Azure](media/hpc-cache-add-blob.png)

Para definir um contêiner de blob do Azure, insira essas informações.

* **Nome do destino de armazenamento** -defina um nome que identifique esse destino de armazenamento no cache do HPC do Azure.
* **Tipo de destino** -escolha **blob**.
* **Conta de armazenamento** -selecione a conta que você deseja usar.

  Você precisará autorizar a instância de cache a acessar a conta de armazenamento, conforme descrito em [Adicionar as funções de acesso](#add-the-access-control-roles-to-your-account).

  Para obter informações sobre o tipo de conta de armazenamento que você pode usar, leia [requisitos de armazenamento de BLOBs](hpc-cache-prerequisites.md#blob-storage-requirements).

* **Contêiner de armazenamento** -selecione o contêiner de BLOB para este destino ou clique em **criar novo**.

  ![captura de tela da caixa de diálogo para especificar o nome e o nível de acesso (privado) para o novo contêiner](media/add-blob-new-container.png)

Quando terminar, clique em **OK** para adicionar o destino de armazenamento.

> [!NOTE]
> Se o firewall da sua conta de armazenamento estiver definido para restringir o acesso somente a "redes selecionadas", use a solução alternativa temporária documentada em contornar [as configurações de firewall da conta de armazenamento de BLOBs](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Adicionar as funções de controle de acesso à sua conta

O cache HPC do Azure usa o [Azure RBAC (controle de acesso baseado em função)](../role-based-access-control/index.yml) para autorizar o serviço de cache a acessar sua conta de armazenamento para destinos do armazenamento de BLOBs do Azure.

O proprietário da conta de armazenamento deve adicionar explicitamente o colaborador da [conta de armazenamento](../role-based-access-control/built-in-roles.md#storage-account-contributor) de funções e o colaborador de dados do [blob de armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) para o usuário "provedor de recursos de cache do HPC".

Você pode fazer isso antecipadamente ou clicando em um link na página em que você adiciona um destino de armazenamento de BLOBs. Tenha em mente que pode levar até cinco minutos para que as configurações de função se propaguem por meio do ambiente do Azure, portanto, você deve aguardar alguns minutos depois de adicionar as funções antes de criar um destino de armazenamento.

Etapas para adicionar as funções do Azure:

1. Abra a página **controle de acesso (iam)** para a conta de armazenamento. (O link na página **Adicionar destino de armazenamento** abre automaticamente esta página para a conta selecionada.)

1. Clique no **+** na parte superior da página e escolha **Adicionar uma atribuição de função**.

1. Selecione a função "colaborador da conta de armazenamento" na lista.

1. No campo **atribuir acesso a** , deixe o valor padrão selecionado ("usuário do Azure AD, grupo ou entidade de serviço").  

1. No campo **selecionar** , procure "HPC".  Essa cadeia de caracteres deve corresponder a uma entidade de serviço, denominada "provedor de recursos de cache do HPC". Clique nessa entidade para selecioná-la.

   > [!NOTE]
   > Se uma pesquisa por "HPC" não funcionar, tente usar a cadeia de caracteres "storagecache" em vez disso. Os usuários que participaram de visualizações (antes do GA) talvez precisem usar o nome mais antigo para a entidade de serviço.

1. Clique no botão **salvar** na parte inferior.

1. Repita esse processo para atribuir a função "colaborador de dados de blob de armazenamento".  

![captura de tela de adicionar GUI de atribuição de função](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Pré-requisito: acesso à conta de armazenamento

[Configurar CLI do Azure para o cache do HPC do Azure](./az-cli-prerequisites.md).

Antes de adicionar um destino de armazenamento de BLOBs, verifique se o cache tem as funções corretas para acessar a conta de armazenamento e se as configurações de firewall permitirão a criação do destino de armazenamento.

O cache HPC do Azure usa o [Azure RBAC (controle de acesso baseado em função)](../role-based-access-control/index.yml) para autorizar o serviço de cache a acessar sua conta de armazenamento para destinos do armazenamento de BLOBs do Azure.

O proprietário da conta de armazenamento deve adicionar explicitamente o colaborador da [conta de armazenamento](../role-based-access-control/built-in-roles.md#storage-account-contributor) de funções e o colaborador de dados do [blob de armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) para o usuário "provedor de recursos de cache do HPC".

A criação do destino de armazenamento falhará se o cache não tiver essas funções.

Pode levar até cinco minutos para que as configurações de função se propaguem por meio do ambiente do Azure, portanto, você deve aguardar alguns minutos depois de adicionar as funções antes de criar um destino de armazenamento.

Leia [Adicionar ou remover atribuições de função do Azure usando CLI do Azure](../role-based-access-control/role-assignments-cli.md) para obter instruções detalhadas.

Verifique também as configurações de firewall da sua conta de armazenamento. Se o firewall estiver definido para restringir o acesso apenas a "redes selecionadas", a criação do destino de armazenamento poderá falhar. Use a solução alternativa documentada em contornar [as configurações de firewall da conta de armazenamento de BLOBs](hpc-cache-blob-firewall-fix.md).

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Adicionar um destino de armazenamento de BLOBs com CLI do Azure

Use o [AZ HPC-cache BLOB-Storage-Target adicionar](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add) interface para definir um destino de armazenamento de BLOBs do Azure.

> [!NOTE]
> Os comandos CLI do Azure atualmente exigem que você crie um caminho de namespace ao adicionar um destino de armazenamento. Isso é diferente do processo usado com a interface portal do Azure.

Além dos parâmetros padrão do grupo de recursos e do nome do cache, você deve fornecer essas opções para o destino de armazenamento:

* ``--name`` -Defina um nome que identifica esse destino de armazenamento no cache do HPC do Azure.

* ``--storage-account`` -O identificador de conta, neste formato:/subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/Providers/Microsoft.Storage/storageAccounts/*<account_name>*

  Para obter informações sobre o tipo de conta de armazenamento que você pode usar, leia [requisitos de armazenamento de BLOBs](hpc-cache-prerequisites.md#blob-storage-requirements).

* ``--container-name`` -Especifique o nome do contêiner a ser usado para este destino de armazenamento.

* ``--virtual-namespace-path`` -Defina o caminho de arquivo voltado para o cliente para este destino de armazenamento. Coloque os caminhos entre aspas. Leia [planejar o namespace agregado](hpc-cache-namespace.md) para saber mais sobre o recurso de namespace virtual.

Exemplo de comando:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Adicionar um novo destino de armazenamento NFS

Um destino de armazenamento NFS tem configurações diferentes de um destino de armazenamento de BLOBs. A configuração do modelo de uso ajuda o cache a armazenar em cache com eficiência os dados desse sistema de armazenamento.

![Captura de tela da página Adicionar destino de armazenamento com o destino NFS definido](media/add-nfs-target.png)

> [!NOTE]
> Antes de criar um destino de armazenamento NFS, verifique se o sistema de armazenamento está acessível no cache do HPC do Azure e atende aos requisitos de permissão. A criação do destino de armazenamento falhará se o cache não puder acessar o sistema de armazenamento. Leia [requisitos de armazenamento NFS](hpc-cache-prerequisites.md#nfs-storage-requirements) e solucione problemas de [configuração do nas e destino de armazenamento NFS](troubleshoot-nas.md) para obter detalhes.

### <a name="choose-a-usage-model"></a>Escolher um modelo de uso
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Ao criar um destino de armazenamento que aponta para um sistema de armazenamento NFS, você precisa escolher o modelo de uso para esse destino. Esse modelo determina como os dados são armazenados em cache.

Os modelos de uso internos permitem que você escolha como balancear a resposta rápida com o risco de obter dados obsoletos. Se você quiser otimizar a velocidade de leitura do arquivo, talvez não se importa se os arquivos no cache são verificados em relação aos arquivos de back-end. Por outro lado, se você quiser ter certeza de que os arquivos estão sempre atualizados com o armazenamento remoto, escolha um modelo que verifica com frequência.

Há três opções:

* **Ler gravações pesadas e frequentes** – Use essa opção se você quiser acelerar o acesso de leitura para arquivos que são estáticos ou raramente alterados.

  Essa opção armazena em cache os arquivos que os clientes lêem, mas passa gravações para o armazenamento de back-end imediatamente. Os arquivos armazenados no cache não são automaticamente comparados aos arquivos no volume de armazenamento NFS. (Leia a observação abaixo sobre a verificação de back-end para saber mais.)

  Não use essa opção se houver um risco de que um arquivo possa ser modificado diretamente no sistema de armazenamento sem primeiro gravá-lo no cache. Se isso acontecer, a versão em cache do arquivo estará fora de sincronia com o arquivo de back-end.

* **Mais de 15% de gravações** – essa opção acelera o desempenho de leitura e gravação. Ao usar essa opção, todos os clientes devem acessar arquivos por meio do cache HPC do Azure em vez de montar o armazenamento de back-end diretamente. Os arquivos armazenados em cache terão alterações recentes que não são armazenadas no back-end.

  Nesse modelo de uso, os arquivos no cache só são verificados em relação aos arquivos no armazenamento de back-end a cada oito horas. Pressupõe-se que a versão em cache do arquivo seja mais atual. Um arquivo modificado no cache é gravado no sistema de armazenamento de back-end depois que ele está no cache por uma hora sem nenhuma alteração adicional.

* **Os clientes gravam no destino NFS, ignorando o cache** -escolha esta opção se algum cliente em seu fluxo de trabalho gravar dados diretamente no sistema de armazenamento sem primeiro gravar no cache ou se você quiser otimizar a consistência dos dados. Os arquivos que os clientes solicitam são armazenados em cache, mas quaisquer alterações nesses arquivos do cliente são passadas de volta para o sistema de armazenamento de back-end imediatamente.

  Com esse modelo de uso, os arquivos no cache são verificados frequentemente em relação às versões de back-end para atualizações. Essa verificação permite que os arquivos sejam alterados fora do cache enquanto mantêm a consistência dos dados.

Esta tabela resume as diferenças do modelo de uso:

| Modelo de uso                   | Modo de cache | Verificação de back-end | Atraso máximo de write-back |
|-------------------------------|--------------|-----------------------|--------------------------|
| Leia gravações pesadas e frequentes | Ler         | Nunca                 | Nenhum                     |
| Mais de 15% de gravações       | Leitura/gravação   | 8 horas               | 1 hora                   |
| Clientes ignoram o cache      | Ler         | 30 segundos            | Nenhum                     |

> [!NOTE]
> O valor de **verificação de back-end** mostra quando o cache compara automaticamente seus arquivos com os arquivos de origem no armazenamento remoto. No entanto, você pode forçar o cache do Azure HPC a comparar arquivos executando uma operação de diretório que inclui uma solicitação readdirplus. Readdirplus é uma API padrão do NFS (também chamada de leitura estendida) que retorna os metadados do diretório, o que faz com que o cache compare e atualize arquivos.

### <a name="create-an-nfs-storage-target"></a>Criar um destino de armazenamento NFS

### <a name="portal"></a>[Portal](#tab/azure-portal)

No portal do Azure, abra sua instância de cache e clique em **destinos de armazenamento** na barra lateral esquerda.

![captura de tela das configurações > página de destino de armazenamento, com dois destinos de armazenamento existentes em uma tabela e um realce ao contrário do botão + Adicionar destino de armazenamento acima da tabela](media/add-storage-target-button.png)

A página **destinos de armazenamento** lista todos os destinos existentes e fornece um link para adicionar um novo.

Clique no botão **Adicionar destino de armazenamento** .

![Captura de tela da página Adicionar destino de armazenamento com o destino NFS definido](media/add-nfs-target.png)

Forneça essas informações para um destino de armazenamento com backup em NFS:

* **Nome do destino de armazenamento** -defina um nome que identifique esse destino de armazenamento no cache do HPC do Azure.

* **Tipo de destino** -escolha **NFS**.

* **Hostname** – Insira o endereço IP ou o nome de domínio totalmente qualificado para o sistema de armazenamento NFS. (Use um nome de domínio somente se o cache tiver acesso a um servidor DNS que possa resolver o nome.)

* **Modelo de uso** – escolha um dos perfis de cache de dados com base em seu fluxo de trabalho, descrito em [escolher um modelo de uso](#choose-a-usage-model) acima.

Quando terminar, clique em **OK** para adicionar o destino de armazenamento.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar CLI do Azure para o cache do HPC do Azure](./az-cli-prerequisites.md).

Use o comando CLI do Azure [AZ HPC-cache NFS-Storage-Target Add](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add) para criar o destino de armazenamento.

> [!NOTE]
> Os comandos CLI do Azure atualmente exigem que você crie um caminho de namespace ao adicionar um destino de armazenamento. Isso é diferente do processo usado com a interface portal do Azure.

Forneça esses valores além do nome do cache e do grupo de recursos de cache:

* ``--name`` -Defina um nome que identifica esse destino de armazenamento no cache do HPC do Azure.
* ``--nfs3-target`` -O endereço IP do seu sistema de armazenamento NFS. (Você pode usar um nome de domínio totalmente qualificado aqui se o cache tiver acesso a um servidor DNS que possa resolver o nome.)
* ``--nfs3-usage-model`` -Um dos perfis de cache de dados, descrito em [escolher um modelo de uso](#choose-a-usage-model), acima.

  Verifique os nomes dos modelos de uso com o comando [AZ HPC-cache Usage-Model List](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

* ``--junction`` -O parâmetro de junção vincula o caminho de arquivo virtual voltado ao cliente com um caminho de exportação no sistema de armazenamento.

  Um destino de armazenamento NFS pode ter vários caminhos virtuais, desde que cada caminho represente uma exportação ou subdiretório diferente no mesmo sistema de armazenamento. Crie todos os caminhos para um sistema de armazenamento em um destino de armazenamento.

  Você pode [Adicionar e editar caminhos de namespace](add-namespace-paths.md) em um destino de armazenamento a qualquer momento.

  O ``--junction`` parâmetro usa estes valores:

  * ``namespace-path`` -O caminho do arquivo virtual voltado para o cliente
  * ``nfs-export`` -A exportação do sistema de armazenamento para associar ao caminho voltado para o cliente
  * ``target-path`` (opcional)-um subdiretório da exportação, se necessário

  Exemplo: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Leia [Configurar namespace agregado](hpc-cache-namespace.md) para saber mais sobre o recurso de namespace virtual.

Exemplo de comando:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Saída:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="view-storage-targets"></a>Exibir destinos de armazenamento

Você pode usar o portal do Azure ou o CLI do Azure para mostrar os destinos de armazenamento já definidos para o cache.

### <a name="portal"></a>[Portal](#tab/azure-portal)

No portal do Azure, abra sua instância de cache e clique em **destinos de armazenamento**, que está sob o título configurações na barra lateral esquerda. A página destinos de armazenamento lista todos os destinos e controles existentes para adicioná-los ou excluí-los.

Clique no nome de um destino de armazenamento para abrir sua página de detalhes.

Leia [Editar destinos de armazenamento](hpc-cache-edit-storage.md) para saber mais.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar CLI do Azure para o cache do HPC do Azure](./az-cli-prerequisites.md).

Use a opção [AZ HPC-cache Storage-Target List](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) para mostrar os destinos de armazenamento existentes para um cache. Forneça o nome do cache e o grupo de recursos (a menos que você o tenha definido globalmente).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Use [AZ HPC-cache Storage-Target show](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) para ver detalhes sobre um determinado destino de armazenamento. (Especifique o destino de armazenamento por nome.)

Exemplo:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>Próximas etapas

Depois de criar destinos de armazenamento, continue com essas tarefas para que seu cache fique pronto para uso:

* [Configurar o namespace agregado](add-namespace-paths.md)
* [Montar o Azure HPC Cache](hpc-cache-mount.md)
* [Mover dados para o armazenamento de BLOBs do Azure](hpc-cache-ingest.md)

Se precisar atualizar as configurações, você poderá [Editar um destino de armazenamento](hpc-cache-edit-storage.md).