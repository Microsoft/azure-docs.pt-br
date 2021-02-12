---
title: Visão geral de blobs de páginas do Azure | Microsoft Docs
description: Uma visão geral dos blobs de página do Azure e suas vantagens, incluindo casos de uso com scripts de exemplo.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/15/2020
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: aada418b4f74c38a2a35c793deb85b94b703fb89
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629350"
---
# <a name="overview-of-azure-page-blobs"></a>Visão geral de blobs de páginas do Azure

O Armazenamento do Azure oferece três tipos de armazenamento de blobs: Blobs de Blocos, Blobs de Acréscimo e blobs de páginas. Blobs de blocos são compostos por blocos e são ideais para armazenar arquivos de texto ou binários e carregar arquivos grandes com eficiência. Blobs de acréscimo também são compostos por blocos, mas são otimizados para operações de acréscimo, tornando-os ideais para cenários de log. Blobs de páginas são compostos por páginas de 512 bytes até um tamanho total de 8 TB e são projetados para operações de leitura/gravação aleatórias frequentes. Os blobs de páginas são a base dos Discos IaaS do Azure. Este artigo ressalta a explicação dos recursos e benefícios de blobs de páginas.

Blobs de páginas são uma coleção de páginas de 512 bytes, que fornecem a capacidade de ler/gravar intervalos arbitrários de bytes. Portanto, os blobs de páginas são ideais para armazenar estruturas de dados esparsos e baseados em índice como discos do sistema operacional e de dados para Máquinas Virtuais e Bancos de Dados. Por exemplo, o BD SQL do Azure usa blobs de páginas como o armazenamento persistente subjacente para seus bancos de dados. Além disso, os blobs de páginas também costumam ser usados para arquivos com atualizações Baseadas em Intervalo.  

Os principais recursos dos blobs de páginas do Azure são sua interface REST, a durabilidade do armazenamento subjacente e as funcionalidades de migração direta para o Azure. Esses recursos são discutidos mais detalhadamente na próxima seção. Além disso, no momento, os blobs de páginas do Azure são compatíveis com dois tipos de armazenamento: Armazenamento Premium e Armazenamento Standard. O armazenamento Premium é projetado especificamente para cargas de trabalho que exigem alto desempenho e baixa latência consistentes, tornando os blobs de páginas Premium ideais para cenários de armazenamento de alto desempenho. As contas de armazenamento standard são mais econômicas para executar cargas de trabalho que não fazem distinção de latência.

## <a name="restrictions"></a>Restrições

Os blobs de páginas só podem usar a camada de acesso **quente** , eles não podem usar as camadas **frias** ou de **arquivo morto** . Para obter mais informações sobre camadas de acesso, consulte [camadas de acesso para armazenamento de BLOBs do Azure – frequente, fria e arquivo morto](storage-blob-storage-tiers.md).

## <a name="sample-use-cases"></a>Casos de uso de exemplo

Vamos discutir alguns casos de uso para blobs de página, começando com os discos de IaaS do Azure. Os blobs de páginas do azure são a base da plataforma de discos virtuais do IaaS do Azure. Os discos do sistema operacional e de discos do Azure são implementados como discos virtuais em que os dados são persistidos de forma duradoura na plataforma de Armazenamento do Azure e, em seguida, entregues para as máquinas virtuais para desempenho máximo. Os Discos do Azure são persistidos no [formato VHD](/previous-versions/windows/it-pro/windows-7/dd979539(v=ws.10)) do Hyper-V e armazenados como um [blob de páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) no Armazenamento do Azure. Além de usar discos virtuais para VMs IaaS do Azure, os blobs de páginas também possibilitam cenários de PaaS e DBaaS como o serviço BD SQL do Azure, que atualmente usa blobs de páginas para armazenar dados do SQL, permitindo operações de leitura/gravação aleatórias rápidas para o banco de dados. Outro exemplo é se você tem um serviço de PaaS para o acesso de mídia compartilhada em aplicativos de edição colaborativa de vídeos: os blobs de páginas permitem um acesso rápido aos locais aleatórios na mídia. Também permite edição e mesclagem rápidas e eficientes da mesma mídia por vários usuários. 

Os serviços da Microsoft de primeira ordem, como o Azure Site Recovery, o Azure Backup, bem como muitos desenvolvedores de terceiros, implementaram inovações líderes do setor usando a interface REST do blob da página. Estes são alguns dos cenários exclusivos implementados no Azure: 

* Gerenciamento de instantâneos incrementais direcionados ao aplicativo: os aplicativos podem aproveitar os instantâneos e as APIs REST do blob de páginas para salvar os pontos de verificação do aplicativo sem incorrer em custos elevados de duplicação de dados. O Armazenamento do Azure oferece suporte a instantâneos locais para blobs de páginas, que não exigem a cópia do blob inteiro. Essas APIs públicas de instantâneos também permitem o acesso e a cópia de deltas entre instantâneos.
* Migração dinâmica de aplicativos e dados do local para a nuvem: Copie os dados locais e use as APIs REST para gravar diretamente em um blob de páginas do Azure enquanto a VM local continua a ser executada. Depois que o destino for atualizado, faça failover rapidamente para a VM do Azure usando esses dados. Dessa forma, você pode migrar suas VMs e discos virtuais do local para a nuvem com tempo de inatividade mínimo, já que a migração de dados ocorre em segundo plano enquanto você continua a usar a VM e o tempo de inatividade necessário para o failover será curto (em minutos).
* Acesso compartilhado [baseado em SAS](../common/storage-sas-overview.md), que permite cenários como vários leitores e um único gravador com suporte para o controle de simultaneidade.

## <a name="pricing"></a>Preços

Os dois tipos de armazenamento oferecidos com blobs de páginas têm seu próprio modelo de preços. Os blobs de páginas Premium seguem o modelo de preços dos Managed disks, enquanto os blobs de página padrão são cobrados no tamanho usado e em cada transação. Para obter mais informações, consulte a [página de preços de blobs de páginas do Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

## <a name="page-blob-features"></a>Recursos do blob de páginas

### <a name="rest-api"></a>API REST

Veja o documento a seguir para começar a [desenvolver o uso de blobs de páginas](./storage-quickstart-blobs-dotnet.md). Por exemplo, observe como acessar os blobs de páginas usando a Biblioteca de Clientes de Armazenamento para .NET. 

O diagrama a seguir descreve as relações gerais entre a conta, os contêineres e os blobs de páginas.

![Captura de tela mostrando relações entre a conta, os contêineres e os blobs de páginas](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Criando um blob de páginas vazio de um tamanho específico

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Primeiro, obtenha uma referência a um contêiner. Para criar um blob de páginas, chame o método GetPageBlobClient e, em seguida, chame o método [PageBlobClient. Create](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.create) . Passe o tamanho máximo para o blob a ser criado. Esse tamanho deve ser um múltiplo de 512 bytes.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_CreatePageBlob":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Para criar um blob de páginas, primeiro criamos um objeto **CloudBlobClient** , com o URI base para acessar o armazenamento de BLOBs da sua conta de armazenamento (*pbaccount* na Figura 1) junto com o objeto **StorageCredentialsAccountAndKey** , conforme mostrado no exemplo a seguir. Em seguida, o exemplo mostra a criação de uma referência a um objeto **CloudBlobContainer** e, em seguida, a criação do contêiner (*testvhds*), caso ele ainda não exista. Em seguida, usando o objeto **CloudBlobContainer**, crie uma referência para um objeto **CloudPageBlob** especificando o nome do blob de páginas (os4.vhd) para acessar. Para criar o blob de páginas, chame [CloudPageBlob. Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create), passando o tamanho máximo do blob a ser criado. O *blobSize* deve ser um múltiplo de 512 bytes.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

---

#### <a name="resizing-a-page-blob"></a>Redimensionando um blob de páginas

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Para redimensionar um blob de páginas após a criação, use o método [redimensionar](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.resize) . O tamanho solicitado deve ser um múltiplo de 512 bytes.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ResizePageBlob":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Para redimensionar um blob de páginas após a criação, use o método [redimensionar](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) . O tamanho solicitado deve ser um múltiplo de 512 bytes.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

---

#### <a name="writing-pages-to-a-page-blob"></a>Gravando páginas em um blob de páginas

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Para gravar páginas, use o método [PageBlobClient. UploadPages](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.uploadpages) .  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_WriteToPageBlob":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Para gravar páginas, use o método [CloudPageBlob.WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages).  

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

---

Isso permite que você grave um conjunto sequencial de páginas de até 4 MB. O deslocamento que está sendo gravado deve começar em um limite de 512 bytes (startingOffset % 512 == 0) e terminar em um limite de 512 -1. 

Assim que uma solicitação de gravação em um conjunto sequencial de páginas tiver êxito no serviço blob e for replicada para durabilidade e resiliência, a gravação será confirmada e o êxito será retornado para o cliente.  

O diagrama abaixo mostra duas operações de gravação separadas:

![Um diagrama que mostra as duas opções de gravação separadas.](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Uma operação de Gravação começando no deslocamento 0 com um tamanho de 1.024 bytes 
2.  Uma operação de Gravação começando no deslocamento 4.096 com um tamanho de 1.024 

#### <a name="reading-pages-from-a-page-blob"></a>Lendo páginas de um blob de páginas

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Para ler páginas, use o método [PageBlobClient. download](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.download) para ler um intervalo de bytes do blob de páginas. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadFromPageBlob":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Para ler páginas, use o método [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) para ler um intervalo de bytes do blob de páginas. 

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

---

Isso permite que você baixe o blob ou o intervalo de bytes completo, começando em qualquer deslocamento no blob. Durante a leitura, o deslocamento não precisa começar em um múltiplo de 512. Ao ler bytes de uma página NULL, o serviço retorna zero bytes.

A figura a seguir mostra uma operação de leitura com um deslocamento de 256 e um tamanho de intervalo de 4352. Os dados retornados são realçados em laranja. Zeros são retornados para páginas NUL.

![Um diagrama que mostra uma operação de leitura com um deslocamento de 256 e um tamanho de intervalo de 4352](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Se você tiver um blob populado de modo disperso, baixe apenas as regiões de página válidas para evitar pagar pela saída de zero bytes e reduzir a latência de download.  

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Para determinar quais páginas são apoiadas pelos dados, use [PageBlobClient. GetPageRanges](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.getpageranges). Em seguida, você pode enumerar os intervalos retornados e baixar os dados em cada intervalo. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadValidPageRegionsFromPageBlob":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Para determinar quais páginas têm suporte de dados, use [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges). Em seguida, você pode enumerar os intervalos retornados e baixar os dados em cada intervalo. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

---

#### <a name="leasing-a-page-blob"></a>Arrendando um blob de páginas

A operação Lease Blob estabelece e gerencia um bloqueio em um blob para operações de gravação e exclusão. Essa operação é útil em cenários em que um blob de páginas está sendo acessado em vários clientes, para garantir que apenas um cliente possa gravar no blob por vez. Os Discos do Azure, por exemplo, aproveitam este mecanismo de concessão para garantir que o disco seja gerenciado apenas por uma única VM. A duração do bloqueio pode ser de 15 a 60 segundos, ou pode ser infinita. Consulte a documentação [aqui](/rest/api/storageservices/lease-blob) para obter mais detalhes.

Além das APIs REST avançadas, os blobs de páginas também fornecem acesso compartilhado, durabilidade e segurança aprimorada. Abordaremos esses benefícios mais detalhadamente nos próximos parágrafos. 

### <a name="concurrent-access"></a>Acesso simultâneo

A API REST dos blobs de páginas e seu mecanismo de concessão permitem que os aplicativos acessem o blob de páginas em vários clientes. Por exemplo, digamos que você precise criar um serviço de nuvem distribuída que compartilhe objetos de armazenamento com vários usuários. Ele pode ser um aplicativo Web que disponibiliza uma coleção grande de imagens para vários usuários. Uma opção para implementar isso é usar uma VM com discos anexados. As desvantagens dessa abordagem incluem: (i) a restrição de que um disco pode ser anexado apenas a uma única VM, limitando a escalabilidade e a flexibilidade, e aumentando os riscos. Se houver um problema com a VM ou o serviço em execução na VM, devido à concessão, a imagem ficará inacessível até que a concessão expire ou seja interrompida; e (ii) o custo adicional de ter uma VM da IaaS. 

Uma opção alternativa é usar os blobs de páginas diretamente por meio das APIs REST do Armazenamento do Azure. Esta opção elimina a necessidade de VMs da IaaS de alto custo, oferece flexibilidade total de acesso direto de vários clientes, simplifica o modelo de implantação clássico, eliminando a necessidade de anexar/desanexar discos e elimina o risco de problemas na VM. Além disso, ele fornece o mesmo nível de desempenho para operações de leitura/gravação aleatórias como um disco

### <a name="durability-and-high-availability"></a>Durabilidade e alta disponibilidade

O armazenamento Standard e Premium são um armazenamento durável, no qual os dados do blob de páginas são sempre replicados para garantir durabilidade e alta disponibilidade. Para obter mais informações sobre a Redundância do Armazenamento do Azure, consulte esta [documentação](../common/storage-redundancy.md). O Azure fornece consistentemente a durabilidade de nível empresarial para discos de IaaS e blobs de páginas, com uma [taxa de falha anual](https://en.wikipedia.org/wiki/Annualized_failure_rate)de porcentagem de zero por cento no setor.

### <a name="seamless-migration-to-azure"></a>Migração direta para o Azure

Para os clientes e desenvolvedores interessados em implementar sua própria solução personalizada de backup, o Azure também oferece instantâneos incrementais que contêm apenas os deltas. Esse recurso evita o custo da cópia completa inicial, o que reduz consideravelmente o custo de backup. Juntamente com a capacidade de ler e copiar dados diferenciais com eficiência, essa é outra funcionalidade avançada que possibilita ainda mais inovações dos desenvolvedores, levando a uma melhor experiência de backup e DR (recuperação de desastre) no Azure. Configure sua própria solução de backup ou DR para suas VMs no Azure usando o [Instantâneo de Blobs](/rest/api/storageservices/snapshot-blob), juntamente com a API [Obter Intervalos de Página](/rest/api/storageservices/get-page-ranges) e a API [Cópia Incremental de Blob](/rest/api/storageservices/incremental-copy-blob), que você pode usar para copiar dados incrementais com facilidade para DR. 

Além disso, muitas empresas têm cargas de trabalho críticas já em execução em datacenters locais. Para migrar a carga de trabalho para a nuvem, uma das principais preocupações é o tempo de inatividade necessário para copiar os dados e o risco de problemas imprevistos após a mudança. Em muitos casos, o tempo de inatividade pode ser um impedimento para a migração para a nuvem. Usando a API REST dos blobs de páginas, o Azure resolve esse problema permitindo a migração na nuvem com o mínimo de interrupção para cargas de trabalho críticas. 

Para obter exemplos de como criar um instantâneo e de como restaurar um blob de páginas com base em um instantâneo, veja o artigo [Configurar um processo de backup usando instantâneos incrementais](../../virtual-machines/windows/incremental-snapshots.md).