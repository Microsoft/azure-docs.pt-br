---
title: Gerencie expiração de armazenamento Blob do Azure na Rede de Distribuição de Conteúdo do Microsoft Azure | Microsoft Docs
description: Aprenda sobre as opções para controlar a vida útil de blobs no cache do Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: edeb2a798031e34a8ee3f93fd104ebb221ce9c61
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593774"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Gerenciar a expiração do armazenamento de Blobs do Azure na CDN do Azure
> [!div class="op_single_selector"]
> * [Conteúdo da Web do Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento de Blobs do Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

O [Serviço de armazenamento de blobs](../storage/common/storage-introduction.md#blob-storage) no Armazenamento do Azure é uma das várias origens baseadas no Azure integradas à CDN (Rede de Distribuição de Conteúdo) do Azure. Qualquer conteúdo de blob publicamente acessível pode ser armazenado em cache no Azure CDN até que o tempo de vida (TTL) seja decorrido. A vida útil é determinada pelo cabeçalho `Cache-Control` na resposta HTTP do servidor de origem. Este artigo descreve várias maneiras que você pode definir o cabeçalho `Cache-Control` em um blob no Armazenamento do Azure.

Também é possível controlar as configurações de cache do portal do Azure definindo as regras de cache da CDN. Se você criar uma regra de cache e definir o comportamento de cache para **Substituição** ou **Ignorar cache**, as configurações de cache fornecidas pela origem discutidas neste artigo serão ignoradas. Para obter informações sobre conceitos gerais de cache, consulte [Como funciona o cache](cdn-how-caching-works.md).

> [!TIP]
> Você pode optar por não definir nenhuma vida útil em um blob. Nesse caso, a CDN do Azure aplica automaticamente um TTL padrão de sete dias, exceto se você tiver configurado as regras de cache no portal do Azure. Esse padrão TTL aplica-se somente para otimizações de entrega da web gerais. Para otimizações de arquivo grande, o TTL padrão é de um dia e para otimizações de streaming de mídia, o TTL padrão é um ano.
> 
> Para obter mais informações sobre como a CDN do Azure trabalha para acelerar o acesso a blob e outros arquivos, consulte [Visão geral da Rede de Distribuição de Conteúdo do Azure](cdn-overview.md).
> 
> Para mais informações sobre o Armazenamento de Blobs do Azure, confira [Introdução ao armazenamento de Blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Configurar cabeçalhos de Controle de Cache usando regras de cache da CDN
O método preferido para configuração do cabeçalho `Cache-Control` do blob é usar regras de cache no Portal do Azure. Para obter mais informações sobre as regras de cache da CDN, consulte [Controlar o comportamento de cache da CDN do Azure com regras de cache](cdn-caching-rules.md).

> [!NOTE] 
> As regras de cache estão disponíveis apenas para perfis de **CDN Standard do Azure do Verizon** e **CDN Standard do Azure da Akamai**. Para perfis de **CDN Premium do Azure do Verizon**, é necessário usar o [mecanismo de regras da CDN do Azure](cdn-rules-engine.md) no portal **Gerenciar** para uma funcionalidade semelhante.

**Para navegar até a página de regras de cache da CDN**:

1. No Portal do Azure, selecione um perfil CDN e selecione o ponto de extremidade para o blob.

2. No painel esquerdo em Configurações, selecione **Regras de cache**.

   ![Botão de Regras de cache da CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   A página **Regras de cache** é exibida.

   ![Página de Cache da CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Para configurar os cabeçalho de Controle de Cache do serviço de armazenamento de Blobs usando regras de cache globais:**

1. Em **Regras de cache globais**, defina **Comportamento de cache da cadeia de caracteres de consulta** para **Ignorar cadeias de consulta** e defina **Comportamento do cache** para **Substituição**.
      
2. Para **Duração da expiração do cache**, insira 3600 na caixa **Segundos**ou 1 na caixa **Horas**. 

   ![Exemplo de regras de cache globais da CDN](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Essa regra de cache global define uma duração de cache de uma hora e afeta todas as solicitações para o ponto de extremidade. Ela substitui todos os cabeçalhos HTTP `Cache-Control` ou `Expires` que são enviados pelo servidor de origem especificado pelo ponto de extremidade.   

3. Clique em **Salvar**.
 
**Para definir os cabeçalhos de Controle de Cache do arquivo de blob usando regras de cache personalizadas:**

1. Em **Personalizar regras de cache**, crie duas condições de combinação:

     a. Para a primeira condição de correspondência, ajuste **Condição de correspondência** para **Path** e insira `/blobcontainer1/*` para o **Valor de correspondência**. Defina o **Comportamento de cache**  para **Substituição** e insira 4 na caixa **Horas**.

    B. Para a segunda condição de correspondência, ajuste **Condição de correspondência** para **Path** e insira `/blobcontainer1/blob1.txt` para o **Valor de correspondência**. Defina o **Comportamento de cache**  para **Substituição** e insira 2 na caixa **Horas**.

    ![Exemplo de regras de cache personalizadas da CDN](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    A primeira regra de cache personalizada estabelece uma duração de cache de quatro horas para qualquer arquivo de blob na pasta `/blobcontainer1` no servidor de origem especificado pelo ponto de extremidade. A segunda regra substitui a primeira regra somente para o arquivo de blob `blob1.txt` e define uma duração de cache de duas horas para isso.

2. Clique em **Salvar**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Configurando cabeçalhos de Cache-Control usando Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/overview) é uma das maneiras mais rápidas e eficientes de administrar os serviços do Azure. Use o cmdlet `Get-AzStorageBlob` para obter uma referência para o blob, em seguida, defina a propriedade `.ICloudBlob.Properties.CacheControl`. 

Por exemplo:

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Você também pode usar o PowerShell para [gerenciar os perfis e os pontos de extremidade da CDN](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Configurando cabeçalhos de Cache-Control usando .NET
Para especificar o cabeçalho `Cache-Control` de um blob usando o código .NET, use a [Biblioteca de Cliente de Armazenamento do Microsoft Azure para .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) a fim de definir a propriedade [CloudBlob.Properties.CacheControl](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol).

Por exemplo:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Existem mais exemplos de código .NET disponíveis nos [Exemplos de Armazenamento de Blobs do Azure para .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Configurando cabeçalhos de Cache-Control usando outros métodos

### <a name="azure-storage-explorer"></a>Gerenciador de Armazenamento do Azure
Com [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), você pode exibir e editar seus recursos de armazenamento de blob, incluindo propriedades, como a propriedade *CacheControl*. 

Para atualizar a propriedade *CacheControl* de um blob com o Gerenciador de Armazenamento do Microsoft Azure:
   1. Selecione um blob e, em seguida, **Propriedades** no menu de contexto. 
   2. Role para baixo até a propriedade *CacheControl*.
   3. Insira um valor e, em seguida, selecione **Salvar**.


![Propriedades do Gerenciador do Armazenamento do Microsoft Azure](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Interface de linha de comando do Azure
Com a [Interface de linha de comando (CLI) do Azure](https://docs.microsoft.com/cli/azure), você pode gerenciar recursos de blob do Azure a partir da linha de comando. Para definir um cabeçalho de cache-control ao carregar um blob com a CLI do Azure, defina a propriedade *cacheControl* usando a opção `-p`. O exemplo a seguir mostra como definir o TTL para uma hora (3600 segundos):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>API REST de serviços de armazenamento do Azure
Você pode usar o [API REST de serviços de armazenamento do Azure](/rest/api/storageservices/) para definir explicitamente a propriedade *x-ms-blob-cache-control* usando as seguintes operações em uma solicitação:
  
   - [Colocar Blob](/rest/api/storageservices/Put-Blob)
   - [Colocar lista de blocos](/rest/api/storageservices/Put-Block-List)
   - [Definir propriedades de Blob](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>Testando o cabeçalho de Controle de Cache
Você pode facilmente verificar as configurações TTL dos seus blobs. Com as [ferramentas para desenvolvedores](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) do seu navegador, teste se o blob inclui `Cache-Control` no cabeçalho de resposta. Você também pode usar uma ferramenta como [Wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/) ou [Fiddler](https://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Próximas etapas
* [Saiba como gerenciar a expiração do conteúdo do Serviço de Nuvem na CDN do Azure](cdn-manage-expiration-of-cloud-service-content.md)
* [Saiba mais sobre conceitos de cache](cdn-how-caching-works.md)

