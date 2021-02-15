---
title: Mapear um domínio personalizado para um ponto de extremidade do armazenamento de BLOBs do Azure
titleSuffix: Azure Storage
description: Mapeie um domínio personalizado para um armazenamento de BLOBs ou ponto de extremidade da Web em uma conta de armazenamento do Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2021
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 52fc7b9c1229421fd46b8110857a0a7a8a4f916a
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520418"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Mapear um domínio personalizado para um ponto de extremidade do armazenamento de BLOBs do Azure

Você pode mapear um domínio personalizado para um ponto de extremidade de serviço BLOB ou um ponto de extremidade de [site estático](storage-blob-static-website.md) . 

> [!NOTE] 
> Esse mapeamento funciona apenas para subdomínios (por exemplo: `www.contoso.com` ). Se desejar que o ponto de extremidade da Web esteja disponível no domínio raiz (por exemplo: `contoso.com` ), você precisará usar a CDN do Azure. Para obter diretrizes, consulte a seção [mapear um domínio personalizado com https habilitado](#enable-https) deste artigo. Como você vai para essa seção deste artigo para habilitar o domínio raiz do seu domínio personalizado, a etapa dentro dessa seção para habilitar HTTPS é opcional. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Mapear um domínio personalizado somente com HTTP habilitado

Essa abordagem é mais fácil, mas só permite o acesso HTTP. Se a conta de armazenamento estiver configurada para [exigir transferência segura](../common/storage-require-secure-transfer.md) via HTTPS, você deverá habilitar o acesso HTTPS para seu domínio personalizado. 

Para habilitar o acesso HTTPS, consulte a seção [mapear um domínio personalizado com https habilitado](#enable-https) deste artigo. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Mapear um domínio personalizado

> [!IMPORTANT]
> Seu domínio personalizado estará indisponível brevemente para os usuários enquanto você conclui a configuração. Se o seu domínio atualmente dá suporte a um aplicativo com um SLA (contrato de nível de serviço) que requer zero tempo de inatividade, siga as etapas na seção [mapear um domínio personalizado com tempo de inatividade zero](#zero-down-time) deste artigo para garantir que os usuários possam acessar seu domínio enquanto o mapeamento de DNS ocorrer.

Se você não tiver dúvidas de que o domínio está indisponível brevemente para os usuários, siga estas etapas.

: heavy_check_mark: etapa 1: obter o nome do host do seu ponto de extremidade de armazenamento.

: heavy_check_mark: etapa 2: criar um registro de nome canônico (CNAME) com seu provedor de domínio.

: heavy_check_mark: etapa 3: registrar o domínio personalizado com o Azure. 

: heavy_check_mark: etapa 4: testar seu domínio personalizado.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Etapa 1: obter o nome do host do seu ponto de extremidade de armazenamento 

O nome do host é a URL do ponto de extremidade de armazenamento sem o identificador de protocolo e a barra à direita. 

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.

2. No painel de menus, em **configurações**, selecione **Propriedades**.  

3. Copie o valor do **ponto de extremidade do serviço blob primário** ou o **ponto de extremidade do site estático primário** para um arquivo de texto. 
  
   > [!NOTE]
   > Não há suporte para o ponto de extremidade de armazenamento Data Lake (por exemplo: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Remova o identificador de protocolo (por exemplo: `HTTPS` ) e a barra à direita dessa cadeia de caracteres. A tabela a seguir contém exemplos.

   | Tipo de ponto de extremidade |  endpoint | nome de host |
   |------------|-----------------|-------------------|
   |serviço blob  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |site estático  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Defina esse valor para mais tarde.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Etapa 2: criar um registro de nome canônico (CNAME) com seu provedor de domínio

Crie um registro CNAME para apontar para o nome do host. Um registro CNAME é um tipo de registro DNS (sistema de nomes de domínio) que mapeia um nome de domínio de origem para um nome de domínio de destino.

1. Entre no site do registrador de domínio e vá para a página para gerenciar a configuração de DNS.

   Você pode encontrar a página em uma seção denominada **Domain Name**, **DNS** ou **Name Server Management**.

2. Localize a seção para gerenciar registros CNAME. 

   Talvez seja necessário acessar uma página de configurações avançadas e procurar **CNAME**, **Alias** ou **Subdomínios**.

3. Crie um registro CNAME. Como parte desse registro, forneça os seguintes itens: 

   - O alias de subdomínio, como `www` ou `photos` . O subdomínio é necessário, não há suporte para domínios raiz. 
      
   - O nome do host que você obteve na seção [obter o nome de host do ponto de extremidade de armazenamento](#endpoint) anteriormente neste artigo. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Etapa 3: registrar seu domínio personalizado com o Azure

##### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.

2. No painel de menu, em **Serviço de Blob**, selecione **Domínio personalizado**.

   > [!NOTE]
   > Essa opção não aparece em contas que têm o recurso de namespace hierárquico habilitado. Para essas contas, use o PowerShell ou o CLI do Azure para concluir esta etapa.

   ![opção de domínio personalizado](./media/storage-custom-domain-name/custom-domain-button.png "domínio personalizado")

   O painel **Domínio personalizado** é aberto.

3. Na caixa de texto **nome de domínio** , digite o nome do seu domínio personalizado, incluindo o subdomínio  
   
   Por exemplo, se seu domínio for *contoso.com* e o alias de subdomínio for *www*, digite `www.contoso.com` . Se o subdomínio for *photos*, digite `photos.contoso.com` .

4. Para registrar o domínio personalizado, escolha o botão **salvar** .

   Depois que o registro CNAME for propagado por meio dos servidores de nome de domínio (DNS) e se os usuários tiverem as permissões apropriadas, eles poderão exibir dados de BLOB usando o domínio personalizado.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Execute o seguinte comando do PowerShell

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $false
```

- Substitua o `<resource-group-name>` espaço reservado pelo nome do grupo de recursos.

- Substitua o `<storage-account-name>` espaço reservado pelo nome da conta de armazenamento.

- Substitua o `<custom-domain-name>` espaço reservado pelo nome do seu domínio personalizado, incluindo o subdomínio.

  Por exemplo, se seu domínio for *contoso.com* e o alias de subdomínio for *www*, digite `www.contoso.com` . Se o subdomínio for *photos*, digite `photos.contoso.com` .

Depois que o registro CNAME for propagado por meio dos servidores de nome de domínio (DNS) e se os usuários tiverem as permissões apropriadas, eles poderão exibir dados de BLOB usando o domínio personalizado.

##### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Execute o seguinte comando do PowerShell

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain false
  ```

- Substitua o `<resource-group-name>` espaço reservado pelo nome do grupo de recursos.

- Substitua o `<storage-account-name>` espaço reservado pelo nome da conta de armazenamento.

- Substitua o `<custom-domain-name>` espaço reservado pelo nome do seu domínio personalizado, incluindo o subdomínio.

  Por exemplo, se seu domínio for *contoso.com* e o alias de subdomínio for *www*, digite `www.contoso.com` . Se o subdomínio for *photos*, digite `photos.contoso.com` .

Depois que o registro CNAME for propagado por meio dos servidores de nome de domínio (DNS) e se os usuários tiverem as permissões apropriadas, eles poderão exibir dados de BLOB usando o domínio personalizado.

---

#### <a name="step-4-test-your-custom-domain"></a>Etapa 4: testar seu domínio personalizado

Para confirmar que seu domínio personalizado está mapeado para o ponto de extremidade do serviço de blob, crie um blob em um contêiner público em sua conta de armazenamento. Em seguida, em um navegador da Web, acesse o blob usando um URI no seguinte formato: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Por exemplo, para acessar um formulário da Web no `myforms` contêiner no subdomínio personalizado do *photos.contoso.com* , você pode usar o seguinte URI: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Mapear um domínio personalizado com tempo de inatividade zero

> [!NOTE]
> Se você não tiver dúvidas de que o domínio está indisponível brevemente para seus usuários, considere o uso das etapas na seção [mapear um domínio personalizado](#map-a-domain) deste artigo. Trata-se de uma abordagem mais simples com menos etapas.  

Se o seu domínio atualmente dá suporte a um aplicativo com um SLA (contrato de nível de serviço) que requer zero tempo de inatividade, siga estas etapas para garantir que os usuários possam acessar seu domínio enquanto o mapeamento DNS ocorre. 

: heavy_check_mark: etapa 1: obter o nome do host do seu ponto de extremidade de armazenamento.

: heavy_check_mark: etapa 2: criar um registro CNAME (nome canônico) intermediário com seu provedor de domínio.

: heavy_check_mark: etapa 3: pré-registrar o domínio personalizado com o Azure.

: heavy_check_mark: etapa 4: criar um registro CNAME com seu provedor de domínio.

: heavy_check_mark: etapa 5: testar seu domínio personalizado.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Etapa 1: obter o nome do host do seu ponto de extremidade de armazenamento 

O nome do host é a URL do ponto de extremidade de armazenamento sem o identificador de protocolo e a barra à direita. 

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.

2. No painel de menus, em **configurações**, selecione **Propriedades**.  

3. Copie o valor do **ponto de extremidade do serviço blob primário** ou o **ponto de extremidade do site estático primário** para um arquivo de texto. 

   > [!NOTE]
   > Não há suporte para o ponto de extremidade de armazenamento Data Lake (por exemplo: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Remova o identificador de protocolo (por exemplo: `HTTPS` ) e a barra à direita dessa cadeia de caracteres. A tabela a seguir contém exemplos.

   | Tipo de ponto de extremidade |  endpoint | nome de host |
   |------------|-----------------|-------------------|
   |serviço blob  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |site estático  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Defina esse valor para mais tarde.

#### <a name="step-2-create-an-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Etapa 2: criar um registro CNAME (nome canônico) intermediário com seu provedor de domínio

Crie um registro CNAME temporário para apontar para o nome do host. Um registro CNAME é um tipo de registro DNS que mapeia um nome de domínio de origem para um nome de domínio de destino.

1. Entre no site do registrador de domínio e vá para a página para gerenciar a configuração de DNS.

   Você pode encontrar a página em uma seção denominada **Domain Name**, **DNS** ou **Name Server Management**.

2. Localize a seção para gerenciar registros CNAME. 

   Talvez seja necessário acessar uma página de configurações avançadas e procurar **CNAME**, **Alias** ou **Subdomínios**.

3. Crie um registro CNAME. Como parte desse registro, forneça os seguintes itens: 

   - O alias de subdomínio, como `www` ou `photos` . O subdomínio é necessário, não há suporte para domínios raiz.

     Adicione o `asverify` subdomínio ao alias. Por exemplo: `asverify.www` ou `asverify.photos`.
       
   - O nome do host que você obteve na seção [obter o nome de host do ponto de extremidade de armazenamento](#endpoint) anteriormente neste artigo. 

     Adicione o subdomínio `asverify` ao nome do host. Por exemplo: `asverify.mystorageaccount.blob.core.windows.net`.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Etapa 3: registrar previamente seu domínio personalizado com o Azure

Ao registrar previamente seu domínio personalizado com o Azure, você permite que o Azure reconheça seu domínio personalizado sem precisar modificar o registro DNS para o domínio. Dessa forma, quando você modificar o registro DNS para o domínio, ele será mapeado para o ponto de extremidade do blob sem tempo de inatividade.

##### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.

2. No painel de menu, em **Serviço de Blob**, selecione **Domínio personalizado**.

   > [!NOTE]
   > Essa opção não aparece em contas que têm o recurso de namespace hierárquico habilitado. Para essas contas, use o PowerShell ou o CLI do Azure para concluir esta etapa.

   ![opção de domínio personalizado](./media/storage-custom-domain-name/custom-domain-button.png "domínio personalizado")

   O painel **Domínio personalizado** é aberto.

3. Na caixa de texto **nome de domínio** , digite o nome do seu domínio personalizado, incluindo o subdomínio  
   
   Por exemplo, se seu domínio for *contoso.com* e o alias de subdomínio for *www*, digite `www.contoso.com` . Se o subdomínio for *photos*, digite `photos.contoso.com` .

4. Selecione a caixa de seleção **Usar validação de CNAME indireta**.

5. Para registrar o domínio personalizado, escolha o botão **salvar** .
  
   Se o registro for bem-sucedido, o portal notificará que sua conta de armazenamento foi atualizada com sucesso. Seu domínio personalizado foi verificado pelo Azure, mas o tráfego para seu domínio ainda não está sendo roteado para sua conta de armazenamento até que você crie um registro CNAME com seu provedor de domínio. Você fará isso na próxima seção.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Execute o seguinte comando do PowerShell

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $true
```

- Substitua o `<resource-group-name>` espaço reservado pelo nome do grupo de recursos.

- Substitua o `<storage-account-name>` espaço reservado pelo nome da conta de armazenamento.

- Substitua o `<custom-domain-name>` espaço reservado pelo nome do seu domínio personalizado, incluindo o subdomínio.

  Por exemplo, se seu domínio for *contoso.com* e o alias de subdomínio for *www*, digite `www.contoso.com` . Se o subdomínio for *photos*, digite `photos.contoso.com` .

O tráfego para seu domínio ainda não está sendo roteado para sua conta de armazenamento até que você crie um registro CNAME com seu provedor de domínio. Você fará isso na próxima seção.

##### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Execute o seguinte comando do PowerShell

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain true
  ```

- Substitua o `<resource-group-name>` espaço reservado pelo nome do grupo de recursos.

- Substitua o `<storage-account-name>` espaço reservado pelo nome da conta de armazenamento.

- Substitua o `<custom-domain-name>` espaço reservado pelo nome do seu domínio personalizado, incluindo o subdomínio.

  Por exemplo, se seu domínio for *contoso.com* e o alias de subdomínio for *www*, digite `www.contoso.com` . Se o subdomínio for *photos*, digite `photos.contoso.com` .

O tráfego para seu domínio ainda não está sendo roteado para sua conta de armazenamento até que você crie um registro CNAME com seu provedor de domínio. Você fará isso na próxima seção.

---

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Etapa 4: criar um registro CNAME com seu provedor de domínio

Crie um registro CNAME temporário para apontar para o nome do host.

1. Entre no site do registrador de domínio e vá para a página para gerenciar a configuração de DNS.

   Você pode encontrar a página em uma seção denominada **Domain Name**, **DNS** ou **Name Server Management**.

2. Localize a seção para gerenciar registros CNAME. 

   Talvez seja necessário acessar uma página de configurações avançadas e procurar **CNAME**, **Alias** ou **Subdomínios**.

3. Crie um registro CNAME. Como parte desse registro, forneça os seguintes itens: 

   - O alias de subdomínio, como `www` ou `photos` . O subdomínio é necessário, não há suporte para domínios raiz.
      
   - O nome do host que você obteve na seção [obter o nome de host do ponto de extremidade de armazenamento](#endpoint-2) anteriormente neste artigo. 

#### <a name="step-5-test-your-custom-domain"></a>Etapa 5: testar seu domínio personalizado

Para confirmar que seu domínio personalizado está mapeado para o ponto de extremidade do serviço de blob, crie um blob em um contêiner público em sua conta de armazenamento. Em seguida, em um navegador da Web, acesse o blob usando um URI no seguinte formato: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Por exemplo, para acessar um formulário da Web no `myforms` contêiner no subdomínio personalizado do *photos.contoso.com* , você pode usar o seguinte URI: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Remover um mapeamento de domínio personalizado

Para remover um mapeamento de domínio personalizado, cancele o registro do domínio personalizado. Use um dos procedimentos a seguir.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.

2. No painel de menu, em **Serviço de Blob**, selecione **Domínio personalizado**.  
   O painel **Domínio personalizado** é aberto.

3. Limpe o conteúdo da caixa de texto que contém seu nome de domínio personalizado.

4. Selecione o botão **Salvar**.

Depois que o domínio personalizado for removido com sucesso, você verá uma notificação do portal informando que sua conta de armazenamento foi atualizada com sucesso.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para remover um registro de domínio personalizado, use o cmdlet [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) do PowerShell e especifique uma sequência vazia (`""`) para o valor do argumento `-CustomDomainName`.

* Formato do comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Exemplo de comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para remover um registro de domínio personalizado, use o comando da CLI [az storage account](/cli/azure/storage/account) e, em seguida, especifique uma cadeia vazia (`""`) para o valor do argumento `--custom-domain`.

* Formato do comando:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Exemplo de comando:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>Mapear um domínio personalizado com HTTPS habilitado

Essa abordagem envolve mais etapas, mas habilita o acesso HTTPS. 

Se você não precisar que os usuários acessem seu conteúdo de BLOB ou da Web usando HTTPS, consulte a seção [mapear um domínio personalizado com apenas o http habilitado](#enable-http) neste artigo. 

1. Habilite a [CDN do Azure](../../cdn/cdn-overview.md) em seu BLOB ou ponto de extremidade da Web. 

   Para um ponto de extremidade de armazenamento de BLOB, consulte [integrar uma conta de armazenamento do Azure com a CDN do Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   Para um ponto de extremidade de site estático, consulte [integrar um site estático com a CDN do Azure](static-website-content-delivery-network.md).

2. [Mapeie conteúdo da CDN do Azure para um domínio personalizado](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Habilite o HTTPS em um domínio personalizado da CDN do Azure](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > Ao atualizar seu site estático, certifique-se de limpar o conteúdo armazenado em cache nos servidores de borda da CDN limpando o ponto de extremidade da CDN. Para obter mais informações, confira [Como limpar um ponto de extremidade do CDN do Azure](../../cdn/cdn-purge-endpoint.md).

4. Adicional Examine as seguintes diretrizes:

   * [Tokens de SAS (assinatura de acesso compartilhado) com a CDN do Azure](../../cdn/cdn-storage-custom-domain-https.md#shared-access-signatures).

   * [Redirecionamento de http para HTTPS com a CDN do Azure](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection).

   * [Preços e cobrança ao usar o armazenamento de BLOBs com a CDN do Azure](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection).

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre hospedagem de site estático no armazenamento de BLOBs do Azure](storage-blob-static-website.md)