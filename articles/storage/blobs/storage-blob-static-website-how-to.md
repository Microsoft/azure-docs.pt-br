---
title: Hospedar site estático no Armazenamento do Microsoft Azure
description: Saiba como fornecer conteúdo estático (HTML, CSS, JavaScript e arquivos de imagem) diretamente de um contêiner em uma conta GPv2 do Armazenamento do Microsoft Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: a90251097f2b4cced5b30f5d6eea03b6ca0b1c61
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664742"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hospedar site estático no Armazenamento do Microsoft Azure

Você pode fornecer conteúdo estático (HTML, CSS, JavaScript e arquivos de imagem) diretamente de um contêiner em uma conta GPv2 do Armazenamento do Microsoft Azure. Para saber mais, confira [Hospedagem de site estático no Armazenamento do Microsoft Azure](storage-blob-static-website.md).

Este artigo mostra como habilitar a hospedagem estática de sites usando o portal do Azure, a CLI do Azure ou o PowerShell.

> [!NOTE]
> Certifique-se de criar uma conta de armazenamento padrão de uso geral v2. Os sites estáticos não estão disponíveis em nenhum outro tipo de conta de armazenamento.

## <a name="enable-static-website-hosting"></a>Habilitar hospedagem de sites estáticos

A hospedagem de site estática é um recurso que você precisa habilitar na conta de armazenamento.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Entre no [portal do Azure](https://portal.azure.com/) para começar.

2. Localize sua conta de armazenamento e exiba a visão geral dela.

3. Selecione **Site estático** para exibir a página de configuração de sites estáticos.

4. Selecione **Ativado** para habilitar a hospedagem de site estático para a conta de armazenamento.

5. No campo **Nome do documento de índice**, especifique uma página de índice padrão (por exemplo: *index.html*). 

   A página de índice padrão é exibida quando um usuário navega para a raiz do site estático.  

6. No campo **Caminho do documento de erro**, especifique uma página de erro padrão (por exemplo: *404.html*). 

   A página de erro padrão é exibida quando o usuário tenta navegar até uma página que não existe no site estático.

7. Clique em **Save** (Salvar). Agora, o portal do Azure exibe seu ponto de extremidade do site estático. 

    ![Habilitar a hospedagem de site estático para uma conta de armazenamento](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

<a id="cli"></a>

Você pode habilitar a hospedagem de site estático usando a [CLI (interface de linha de comando) do Azure](/cli/azure/).

1. Primeiro, abra o [Azure Cloud Shell](../../cloud-shell/overview.md)ou, se você [instalou](/cli/azure/install-azure-cli) a CLI do Azure localmente, abra um aplicativo de console de comando, como o Windows PowerShell.

2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento que hospedará seu site estático.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Substitua o valor de espaço reservado `<subscription-id>` pela ID da sua assinatura.

3. Habilitar hospedagem de sites estáticos.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

   * Substitua o espaço reservado `<error-document-name>` pelo nome do documento de erro que será exibido aos usuários quando um navegador solicitar uma página no site que não existe.

   * Substitua o espaço reservado `<index-document-name>` pelo nome do documento de indexação. Este documento é normalmente "index. html".

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

Você pode habilitar a hospedagem de site estático usando o módulo do Azure PowerShell.

1. Abra uma janela de comando do Windows PowerShell.

2. Estes exemplos exigem a versão Az 0.7 ou posterior do módulo do Azure PowerShell.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

3. Entre na sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

   ```powershell
   Connect-AzAccount
   ```

4. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento que hospedará seu site estático.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua o valor de espaço reservado `<subscription-id>` pela ID da sua assinatura.

5. Obtenha o contexto da conta de armazenamento que define a conta de armazenamento que você deseja usar.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Substitua o valor do espaço reservado `<resource-group-name>` pelo nome do grupo de recursos.

   * Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

6. Habilitar hospedagem de sites estáticos.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Substitua o espaço reservado `<error-document-name>` pelo nome do documento de erro que será exibido aos usuários quando um navegador solicitar uma página no site que não existe.

   * Substitua o espaço reservado `<index-document-name>` pelo nome do documento de indexação. Este documento é normalmente "index. html".

---

## <a name="upload-files"></a>Carregar arquivos 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Estas instruções mostram como carregar arquivos usando a versão do Gerenciador de Armazenamento que aparece na portal do Azure. No entanto, você também pode usar a versão do [Gerenciador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/) executada fora do portal do Azure. Você pode usar [AzCopy](../common/storage-use-azcopy-v10.md), PowerShell, CLI ou qualquer aplicativo personalizado que possa carregar arquivos para o contêiner **$web** de sua conta. Para obter um tutorial passo a passo que carrega arquivos usando o Visual Studio Code, consulte [Tutorial: Hospedar um site estático no Armazenamento de Blobs](./storage-blob-static-website-host.md).

1. Selecione **Gerenciador de Armazenamento do Microsoft Azure (versão prévia)** .

2. Expanda o nó **BLOB CONTAINERS** e selecione o contêiner **$web**.

3. Selecione o botão **Carregar** para carregar os arquivos.

   ![Carregar arquivos](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Se você pretende que o navegador exiba o conteúdo do arquivo, certifique-se de que o tipo de conteúdo desse arquivo esteja definido como `text/html`. 

   ![Verifique os tipos de conteúdo](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > O Gerenciador de Armazenamento define automaticamente essa propriedade como `text/html` para extensões comumente reconhecidas, como `.html`. No entanto, em alguns casos, você terá que definir isso por conta própria. Se você não definir essa propriedade como `text/html`, o navegador solicitará que os usuários baixem o arquivo em vez de renderizar o conteúdo. Para definir essa propriedade, clique com botão direito do mouse no arquivo e, em seguida, clique em **Propriedades**.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Carregar objetos para o contêiner *$web* de um diretório de origem.

Este exemplo pressupõe que você esteja executando comandos da sessão do Azure Cloud Shell.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d '$web' --account-name <storage-account-name>
```

> [!NOTE] 
> Se o navegar solicitar que os usuários baixem o arquivo em vez de renderizar o conteúdo, você pode anexar `--content-type 'text/html; charset=utf-8'` ao comando. 

* Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

* Substitua o espaço reservado `<source-path>` por um caminho para o local dos arquivos que você deseja carregar.

> [!NOTE]
> Se você estiver usando uma instalação de local da CLI do Azure, poderá usar o caminho para qualquer local no computador local (por exemplo: `C:\myFolder`.
>
> Se estiver usando Azure Cloud Shell, você precisará fazer referência a um compartilhamento de arquivos que é visível para o Cloud Shell. Esse local pode ser o compartilhamento de arquivos do compartilhamento de nuvem em si ou um compartilhamento de arquivos existente que você monta na Cloud Shell. Para obter mais informações, consulte [Persistir arquivos no Azure Cloud Shell](../../cloud-shell/persisting-shell-storage.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Carregar objetos para o contêiner *$web* de um diretório de origem.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE] 
> Se o navegar solicitar que os usuários baixem o arquivo em vez de renderizar o conteúdo, você pode anexar `-Properties @{ ContentType = "text/html; charset=utf-8";}` ao comando.

* Substitua o valor do espaço reservado `<path-to-file>` pelo caminho totalmente qualificado para o arquivo que você deseja carregar (por exemplo: `C:\temp\index.html`).

* Substitua o valor do espaço reservado `<blob-name>` pelo nome que você deseja dar ao blob resultante (por exemplo: `index.html`).

---

<a id="portal-find-url"></a>

## <a name="find-the-website-url"></a>Localizar a URL do site

Você pode exibir as páginas do site de um navegador usando a URL pública do site.

### <a name="portal"></a>[Portal](#tab/azure-portal)

No painel que aparece ao lado da página de visão geral da conta da sua conta de armazenamento, selecione **site estático**. A URL do seu site aparece no campo **Ponto de extremidade primário**.

![Métrica de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Localize a URL pública do seu site estático usando o seguinte comando:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

* Substitua o valor do espaço reservado `<resource-group-name>` pelo nome do grupo de recursos.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Localize a URL pública do seu site estático usando o seguinte comando:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Substitua o valor do espaço reservado `<resource-group-name>` pelo nome do grupo de recursos.

* Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

---

<a id="metrics"></a>

## <a name="enable-metrics-on-static-website-pages"></a>Você pode habilitar métricas em páginas de sites estáticos

Depois que você tiver habilitado as métricas, as estatísticas de tráfego nos arquivos no contêiner **$web** serão relatadas no painel de métricas.

1. Clique em **Métricas** na seção **Monitor** do menu da conta de armazenamento.

   > [!div class="mx-imgBorder"]
   > ![Link de métricas](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Dados de métricas são gerados pendurando em diferentes APIs de métrica. O portal exibe apenas membros da API usados em um determinado período de tempo para se concentrar apenas em membros que retornam dados. Para garantir que você possa selecionar o membro de API necessário, a primeira etapa é expandir o intervalo de tempo.

2. Clique no botão de período, selecione o período e clique em **Aplicar**.

   ![Intervalo de tempo de métricas de sites estático do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Selecione o **Blob** da lista suspensa *Namespace*.

   ![Namespace de métricas de sites estático do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Em seguida, selecione a métrica **Egresso**.

   ![Captura de tela que mostra a métrica de saída dos sites estáticos do armazenamento do Azure.](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Selecione **Somar** do seletor *Agregação*.

   ![Agregação de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Clique no botão **Adicionar filtro** e escolha **Nome da API** do seletor *Propriedade*.

   ![Nome da API de métricas de sies estáticos do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Marque a caixa de seleção ao lado de **GetWebContent** no seletor *Valores* para preencher o relatório de métricas.

   ![O GetWebContent de métricas de websites estáticos do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > A caixa de seleção **GetWebContent** aparece somente se o membro da API foi usado em um período específico. O portal exibe apenas membros da API usados em um determinado período de tempo para se concentrar apenas em membros que retornam dados. Se você não encontrar um membro de API específico nessa lista, expanda o intervalo de tempo.

## <a name="next-steps"></a>Próximas etapas

* Aprenda a configurar um domínio personalizado com seu site estático. Consulte [Mapear um domínio personalizado para um ponto de extremidade do Armazenamento de Blobs do Azure](storage-custom-domain-name.md).