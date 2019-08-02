---
title: Hospedar um site estático no armazenamento do Azure
description: Saiba como servir conteúdo estático (HTML, CSS, JavaScript e arquivos de imagem) diretamente de um contêiner em uma conta do Azure Storage GPv2.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: e53f8dd832b7f19692b9095e913330848e5b0330
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385769"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hospedar um site estático no armazenamento do Azure

Você pode fornecer conteúdo estático (HTML, CSS, JavaScript e arquivos de imagem) diretamente de um contêiner em uma conta do Azure Storage GPv2. Para saber mais, confira [Hospedagem de site estático no armazenamento do Azure](storage-blob-static-website.md).

Este artigo mostra como habilitar a hospedagem estática de sites usando o portal do Azure, o CLI do Azure ou o PowerShell.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Para obter um tutorial passo a passo, consulte [o tutorial: Hospedar um site estático no Armazenamento de Blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

Depois de habilitar a hospedagem de site estático, você pode exibir as páginas do seu site em um navegador usando a URL pública do site.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Localize a URL do site usando o portal do Azure

No painel que aparece ao lado da página Visão geral da conta da sua conta de armazenamento, selecione **site estático**. A URL do seu site aparece no campo **ponto de extremidade primário** .

![Métrica de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

Você pode habilitar a hospedagem de site estático usando a [CLI (interface de linha de comando) do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Primeiro, abra o [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)ou, se você tiver [instalado](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o CLI do Azure localmente, abra um aplicativo de console de comando, como o Windows PowerShell.

2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento que hospedará seu site estático.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pela ID da sua assinatura.

3. Habilitar Hospedagem de site estático.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

   * Substitua o `<error-document-name>` espaço reservado pelo nome do documento de erro que será exibido aos usuários quando um navegador solicitar uma página no site que não existe.

   * Substitua o `<index-document-name>` espaço reservado pelo nome do documento de índice. Este documento é normalmente "index. html".

4. Carregar objetos para o contêiner *$web* de um diretório de origem.

   > [!NOTE]
   > Se você estiver usando Azure cloud Shell, certifique-se de adicionar `\` um caractere de escape ao fazer referência `$web` ao contêiner (por exemplo `\$web`:). Se você estiver usando uma instalação local do CLI do Azure, não precisará usar o caractere de escape.

   Este exemplo pressupõe que você está executando comandos de Azure Cloud Shell sessão.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

   * Substitua o `<source-path>` espaço reservado por um caminho para o local dos arquivos que você deseja carregar.

   > [!NOTE]
   > Se você estiver usando uma instalação de local do CLI do Azure, poderá usar o caminho para qualquer local no computador local (por exemplo: `C:\myFolder`.
   >
   > Se estiver usando Azure Cloud Shell, você precisará fazer referência a um compartilhamento de arquivos que é visível para o Cloud Shell. Esse local pode ser o compartilhamento de arquivos do compartilhamento de nuvem em si ou um compartilhamento de arquivos existente que você monta da Cloud Shell. Para saber como fazer isso, consulte [arquivos de persistência em Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Localize a URL do site usando o CLI do Azure

Você pode exibir o conteúdo de um navegador usando a URL pública do site.

Localize a URL usando o seguinte comando:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

* Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do seu grupo de recursos.

<a id="powershell" />

## <a name="use-powershell"></a>Usar o PowerShell

Você pode habilitar a hospedagem de site estático usando o módulo Azure PowerShell.

1. Abra uma janela de comando do Windows PowerShell.

2. Verifique se você tem Azure PowerShell módulo AZ versão 0,7 ou posterior.

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

   Substitua o `<subscription-id>` valor do espaço reservado pela ID da sua assinatura.

5. Obtenha o contexto da conta de armazenamento que define a conta de armazenamento que você deseja usar.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do seu grupo de recursos.

   * Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

6. Habilitar Hospedagem de site estático.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Substitua o `<error-document-name>` espaço reservado pelo nome do documento de erro que será exibido aos usuários quando um navegador solicitar uma página no site que não existe.

   * Substitua o `<index-document-name>` espaço reservado pelo nome do documento de índice. Este documento é normalmente "index. html".

7. Carregar objetos para o contêiner *$web* de um diretório de origem.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Substitua o `<path-to-file>` valor do espaço reservado pelo caminho totalmente qualificado para o arquivo que você deseja carregar (por exemplo: `C:\temp\index.html`).

   * Substitua o `<blob-name>` valor do espaço reservado pelo nome que você deseja dar ao blob resultante (por exemplo: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Localizar a URL do site usando o PowerShell

Você pode exibir o conteúdo de um navegador usando a URL pública do site.

Localize a URL usando o seguinte comando:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do seu grupo de recursos.

* Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Habilitar métricas em páginas de site estáticos

Depois de habilitar as métricas, as estatísticas de tráfego nos arquivos no contêiner de **$Web** são relatadas no painel de métricas.

1. Clique em **configurações** > **monitoramento** > de**métricas**.

   Dados de métricas são gerados pendurando em diferentes APIs de métrica. O portal exibe apenas membros da API usados em um determinado período de tempo para se concentrar apenas em membros que retornam dados. Para garantir que você seja capaz de selecionar o membro de API necessário, a primeira etapa é expandir o intervalo de tempo.

2. Clique no botão intervalo de tempo e selecione **últimas 24 horas** e, em seguida, clique em **aplicar**.

   ![Intervalo de tempo de métricas de sites estático do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Selecione **blob** na lista suspensa *namespace* .

   ![Namespace de métricas de sites estático do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Em seguida, selecione a métrica **Egresso**.

   ![Métrica de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Selecione **Somar** do seletor *Agregação*.

   ![Agregação de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Clique no botão **Adicionar filtro** e escolha **nome da API** no seletor de *Propriedade* .

   ![Nome da API de métricas de sies estáticos do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Marque a caixa ao lado de **GetWebContent** no seletor de *valores* para preencher o relatório de métricas.

   ![O GetWebContent de métricas de websites estáticos do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Próximas etapas

* [Hospedagem de site estático no Armazenamento do Azure](storage-blob-static-website.md)
* [Usar a CDN do Azure para acessar BLOBs com domínios personalizados por HTTPS](storage-https-custom-domain-cdn.md)
* [Configurar um nome de domínio personalizado para seu ponto de extremidade de Blobs ou web](storage-custom-domain-name.md)
* [Funções do Azure](/azure/azure-functions/functions-overview)
* [Serviço de Aplicativo do Azure](/azure/app-service/overview)
* [Compile seu primeiro aplicativo web sem servidor](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutorial: Hospede seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md)
