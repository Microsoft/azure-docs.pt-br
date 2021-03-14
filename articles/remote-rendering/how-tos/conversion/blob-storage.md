---
title: Usar o armazenamento de Blobs do Azure para conversão de modelo
description: Descreve as etapas comuns para configurar e usar o armazenamento de BLOBs para conversão de modelo.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: d8a6fd458cdcf79cdeb693b25acf72d4ec48def7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102507510"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Usar o armazenamento de Blobs do Azure para conversão de modelo

O serviço de [conversão de modelo](model-conversion.md) requer acesso ao armazenamento de BLOBs do Azure para que ele possa recuperar dados de entrada e armazenar dados de saída. Este artigo descreve como realizar as etapas mais comuns.

## <a name="prepare-azure-storage-accounts"></a>Preparar contas de armazenamento do Azure

- Criar uma conta de armazenamento (StorageV2)
- Crie um contêiner de blob de entrada na conta de armazenamento (por exemplo, chamado "arrinput")
- Crie um contêiner de blob de saída na conta de armazenamento (por exemplo, chamado "arroutput")

> [!TIP]
> Para obter instruções detalhadas sobre como configurar sua conta de armazenamento, consulte o guia [de início rápido: converter um modelo para renderização](../../quickstarts/convert-model.md)

A criação da conta de armazenamento e os contêineres de blob podem ser feitos com uma das seguintes ferramentas:

- [Azure portal](https://portal.azure.com)
- [linha de comando AZ](/cli/azure/install-azure-cli)
- [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)
- SDKs (C#, Python...)

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Verifique se a renderização remota do Azure pode acessar sua conta de armazenamento

O renderização remoto do Azure precisa recuperar dados de modelo de sua conta de armazenamento e gravar dados de volta nele.

Você pode conceder acesso de renderização remota do Azure à sua conta de armazenamento das duas maneiras a seguir:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Conectar sua conta de armazenamento do Azure à sua conta de renderização remota do Azure

Siga as etapas fornecidas na seção [criar uma conta](../create-an-account.md#link-storage-accounts) .

### <a name="retrieve-sas-for-the-storage-containers"></a>Recuperar SAS para os contêineres de armazenamento

As assinaturas de acesso armazenadas (SAS) são usadas para conceder acesso de leitura para entrada e acesso de gravação para saída. É recomendável gerar novos URIs cada vez que um modelo é convertido. Como os URIs expiram depois de algum tempo, mantê-los por uma duração maior pode arriscar a dividir seu aplicativo inesperadamente.

Detalhes sobre SAS podem ser encontrados na [documentação da SAS](../../../storage/common/storage-sas-overview.md).

Um URI SAS pode ser gerado usando um dos:

- módulo AZ PowerShell
  - consulte o [exemplo de scripts do PowerShell](../../samples/powershell-example-scripts.md)
- [linha de comando AZ](/cli/azure/install-azure-cli)
- [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)
  - Clique com o botão direito do mouse no contêiner "obter assinatura de acesso compartilhado" (leitura, acesso à lista para o contêiner de entrada, acesso de gravação para o contêiner de saída)
- SDKs (C#, Python...)

Um exemplo de uso de assinaturas de acesso compartilhado na conversão de ativos é mostrado em Conversion.ps1 dos [scripts de exemplo do PowerShell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="upload-an-input-model"></a>Carregar um modelo de entrada

Para iniciar a conversão de um modelo, você precisa carregá-lo usando uma das seguintes opções:

- [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) -uma interface do usuário conveniente para carregar/baixar/gerenciar arquivos no armazenamento de BLOBs do Azure
- [Linha de comando do Azure](../../../storage/blobs/storage-quickstart-blobs-cli.md)
- [Módulo do Azure PowerShell](/powershell/azure/install-az-ps)
  - consulte o [exemplo de scripts do PowerShell](../../samples/powershell-example-scripts.md)
- [Usando um SDK de armazenamento (Python, C#...)](../../../storage/index.yml)
- [Usando as APIs REST do armazenamento do Azure](/rest/api/storageservices/blob-service-rest-api)

Para obter um exemplo de como carregar dados para conversão, consulte Conversion.ps1 dos [scripts de exemplo do PowerShell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="get-a-sas-uri-for-the-converted-model"></a>Obter um URI de SAS para o modelo convertido

Esta etapa é semelhante à [recuperação de SAS para os contêineres de armazenamento](#retrieve-sas-for-the-storage-containers). No entanto, desta vez você precisa recuperar um URI de SAS para o arquivo de modelo, que foi gravado no contêiner de saída.

Por exemplo, para recuperar um URI de SAS por meio do [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), clique com o botão direito do mouse no arquivo de modelo e selecione "obter assinatura de acesso compartilhado".

Uma assinatura de acesso compartilhado (SAS) para carregar modelos é necessária se você não conectou sua conta de armazenamento à sua conta de renderização remota do Azure. Você pode aprender a conectar sua conta em [criar uma conta](../create-an-account.md#link-storage-accounts).

## <a name="next-steps"></a>Próximas etapas

- [Configurar a conversão de modelo](configure-model-conversion.md)
- [A API REST de conversão de modelo](conversion-rest-api.md)
