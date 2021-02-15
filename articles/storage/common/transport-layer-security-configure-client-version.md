---
title: Configurar o protocolo TLS para um aplicativo cliente
titleSuffix: Azure Storage
description: Configure um aplicativo cliente para se comunicar com o armazenamento do Azure usando uma versão mínima do protocolo TLS.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 37b8c79df5b208feea185292fa09c323b64fa27d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89001800"
---
# <a name="configure-transport-layer-security-tls-for-a-client-application"></a>Configurar o protocolo TLS para um aplicativo cliente

Para fins de segurança, uma conta de armazenamento do Azure pode exigir que os clientes usem uma versão mínima do protocolo TLS para enviar solicitações. As chamadas para o armazenamento do Azure falharão se o cliente estiver usando uma versão do TLS inferior à versão mínima necessária. Por exemplo, se uma conta de armazenamento exigir TLS 1,2, uma solicitação enviada por um cliente que está usando o TLS 1,1 falhará.

Este artigo descreve como configurar um aplicativo cliente para usar uma versão específica do TLS. Para obter informações sobre como configurar uma versão mínima necessária do TLS para uma conta de armazenamento do Azure, consulte [Configurar a versão mínima necessária do protocolo TLS para uma conta de armazenamento](transport-layer-security-configure-minimum-version.md).

## <a name="configure-the-client-tls-version"></a>Configurar a versão do TLS do cliente

Para que um cliente envie uma solicitação com uma versão específica do TLS, o sistema operacional deve dar suporte a essa versão.

Os exemplos a seguir mostram como definir a versão do TLS do cliente como 1,2 do PowerShell ou .NET. O .NET Framework usado pelo cliente deve dar suporte a TLS 1,2. Para obter mais informações, consulte [suporte para TLS 1,2](/dotnet/framework/network-programming/tls#support-for-tls-12).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir mostra como habilitar o TLS 1,2 em um cliente do PowerShell:

```powershell
# Set the TLS version used by the PowerShell client to TLS 1.2.
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

# Create a new container.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context
New-AzStorageContainer -Name "sample-container" -Context $ctx
```

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

O exemplo a seguir mostra como habilitar o TLS 1,2 em um cliente .NET usando a versão 12 da biblioteca de cliente do armazenamento do Azure:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Networking.cs" id="Snippet_ConfigureTls12":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

O exemplo a seguir mostra como habilitar o TLS 1,2 em um cliente .NET usando a versão 11 da biblioteca de cliente do armazenamento do Azure:

```csharp
static void EnableTls12()
{
    // Enable TLS 1.2 before connecting to Azure Storage
    System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

    // Add your connection string here.
    string connectionString = "";

    // Connect to Azure Storage and create a new container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
}
```

---

## <a name="verify-the-tls-version-used-by-a-client"></a>Verificar a versão do TLS usada por um cliente

Para verificar se a versão especificada do TLS foi usada pelo cliente para enviar uma solicitação, você pode usar o [Fiddler](https://www.telerik.com/fiddler) ou uma ferramenta semelhante. Abra o Fiddler para iniciar a captura do tráfego de rede do cliente e execute um dos exemplos na seção anterior. Examine o rastreamento Fiddler para confirmar se a versão correta do TLS foi usada para enviar a solicitação, conforme mostrado na imagem a seguir.

:::image type="content" source="media/transport-layer-security-configure-client-version/fiddler-trace-tls-version.png" alt-text="Captura de tela mostrando o rastreamento Fiddler que indica a versão do TLS usada na solicitação":::

## <a name="next-steps"></a>Próximas etapas

- [Configurar a versão mínima necessária da TLS (segurança da camada de transporte) para uma conta de armazenamento](transport-layer-security-configure-minimum-version.md)
- [Recomendações de segurança para o armazenamento de blobs](../blobs/security-recommendations.md)
