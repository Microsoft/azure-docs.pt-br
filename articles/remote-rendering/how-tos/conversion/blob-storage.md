---
title: Usar o armazenamento de Blobs do Azure para conversão de modelo
description: Descreve as etapas comuns para configurar e usar o Armazenamento de Blobs para conversão de modelo.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: d8a6fd458cdcf79cdeb693b25acf72d4ec48def7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507510"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Usar o armazenamento de Blobs do Azure para conversão de modelo

O serviço de [conversão de modelo](model-conversion.md) requer acesso ao Armazenamento de Blobs do Azure para recuperar os dados de entrada e armazenar os dados de saída. Este artigo descreve como realizar as etapas mais comuns.

## <a name="prepare-azure-storage-accounts"></a>Preparar as contas do Armazenamento do Azure

- Crie uma conta de armazenamento (StorageV2)
- Crie um contêiner de blob de entrada na conta de armazenamento (por exemplo, chamado "arrinput")
- Crie um contêiner de blob de saída na conta de armazenamento (por exemplo, chamado "arroutput")

> [!TIP]
> Veja instruções passo a passo para a configuração de contas de armazenamento em [Guia de início rápido: converter um modelo para renderização](../../quickstarts/convert-model.md)

É possível criar a conta de armazenamento e os contêineres de blob usando uma destas ferramentas:

- [Portal do Azure](https://portal.azure.com)
- [Linha de comando az](/cli/azure/install-azure-cli)
- [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)
- SDKs (C#, Python...)

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Verifique se o Azure Remote Rendering pode acessar sua conta de armazenamento

O Azure Remote Rendering precisa recuperar os dados de modelo da conta de armazenamento e gravar dados nela.

Você pode conceder acesso ao Azure Remote Rendering à sua conta de armazenamento das duas seguintes maneiras:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Conectar sua conta do Armazenamento do Azure à sua conta do Azure Remote Rendering

Siga as etapas na seção [Criar uma conta](../create-an-account.md#link-storage-accounts).

### <a name="retrieve-sas-for-the-storage-containers"></a>Recuperar SAS dos contêineres de armazenamento

As SAS (Assinaturas de Acesso Compartilhado) são usadas para conceder acesso de leitura para entrada e acesso de gravação para saída. É recomendável gerar novos URIs cada vez que um modelo é convertido. Como os URIs expiram depois de algum tempo, mantê-los por uma duração maior pode quebrar o aplicativo inesperadamente.

Veja mais detalhes sobre SAS na [documentação do SAS](../../../storage/common/storage-sas-overview.md).

Um URI de SAS pode ser gerado usando um destes métodos:

- Módulo do PowerShell Az
  - confira os [exemplos de scripts do PowerShell](../../samples/powershell-example-scripts.md)
- [Linha de comando az](/cli/azure/install-azure-cli)
- [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)
  - Clique com o botão direito do mouse no contêiner "Obter Assinatura de Acesso Compartilhado" (leitura, acesso à lista para o contêiner de entrada, acesso de gravação para o contêiner de saída)
- SDKs (C#, Python...)

Um exemplo de uso de assinaturas de acesso compartilhado na conversão de ativos é mostrado em Conversion.ps1 dos [exemplos de scripts do PowerShell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="upload-an-input-model"></a>Carregar um modelo de entrada

Para iniciar a conversão de um modelo, você precisa carregá-lo usando uma das seguintes opções:

- [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) – uma interface do usuário conveniente para carregar/baixar/gerenciar arquivos no Armazenamento de Blobs do Azure
- [Linha de comando do Azure](../../../storage/blobs/storage-quickstart-blobs-cli.md)
- [Módulo do Azure PowerShell](/powershell/azure/install-az-ps)
  - confira os [exemplos de scripts do PowerShell](../../samples/powershell-example-scripts.md)
- [Usando um SDK de armazenamento (Python, C#...)](../../../storage/index.yml)
- [Usando as APIs REST do Armazenamento do Azure](/rest/api/storageservices/blob-service-rest-api)

Para ver um exemplo de como carregar dados para conversão, confira o Conversion.ps1 dos [exemplos de script do PowerShell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="get-a-sas-uri-for-the-converted-model"></a>Obter um URI de SAS para o modelo convertido

Esta etapa é semelhante à [recuperação de SAS para os contêineres de armazenamento](#retrieve-sas-for-the-storage-containers). No entanto, desta vez você precisa recuperar o URI de SAS do arquivo de modelo, que foi gravado no contêiner de saída.

Por exemplo, para recuperar um URI de SAS por meio do [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), clique com o botão direito do mouse no arquivo de modelo e selecione "Obtenha Assinatura de Acesso Compartilhado".

Uma SAS (Assinatura de Acesso Compartilhado) para carregar modelos é necessária se você não conectou sua conta de armazenamento à sua conta do Azure Remote Rendering. Você pode aprender a conectar sua conta em [criar uma conta](../create-an-account.md#link-storage-accounts).

## <a name="next-steps"></a>Próximas etapas

- [Configurar a conversão de modelo](configure-model-conversion.md)
- [A API REST de conversão de modelo](conversion-rest-api.md)
