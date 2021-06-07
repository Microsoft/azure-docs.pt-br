---
title: Scripts de exemplo do PowerShell
description: Exemplos que mostram como usar o front-end por meio de scripts do PowerShell
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 53f09dd042543ebc433e9d9cbbd65db332e8fb6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518204"
---
# <a name="example-powershell-scripts"></a>Scripts de exemplo do PowerShell

O Azure Remote Rendering fornece essas duas APIs REST:

- [API REST de conversão](../how-tos/conversion/conversion-rest-api.md)
- [API REST de sessão](../how-tos/session-rest-api.md)

O [repositório de amostras do ARR](https://github.com/Azure/azure-remote-rendering) contém scripts de exemplo na pasta *Scripts* para interagir com as APIs REST do serviço. Este artigo descreve o uso desses scripts.

> [!TIP]
> Também há uma [ferramenta baseada em interface do usuário chamada ARRT](azure-remote-rendering-asset-tool.md) para fazer a interação com o serviço, que é uma alternativa conveniente ao uso de scripts. ![ARRT](./media/azure-remote-rendering-asset-tool.png "Captura de tela do ARRT")

> [!CAUTION]
> Chamar funções da API REST com muita frequência fará com que o servidor seja limitado e retorne uma falha eventualmente. Nesse caso, a ID do código de falha HTTP é 429 ("muitas solicitações"). Como regra geral, deve haver um atraso de **5 a 10 segundos entre as chamadas subsequentes**.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os scripts de exemplo, você precisa de uma configuração funcional do [Azure PowerShell](/powershell/azure/).

1. Instalar o Azure PowerShell:
    1. Abra uma janela do PowerShell com direitos de administrador.
    1. Execute: `Install-Module -Name Az -AllowClobber`

1. Se você obtiver erros sobre a execução de scripts, verifique se a [política de execução](/powershell/module/microsoft.powershell.core/about/about_execution_policies) está definida adequadamente:
    1. Abra uma janela do PowerShell com direitos de administrador.
    1. Execute: `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Preparar uma conta de Armazenamento do Azure](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Faça logon em sua assinatura que contém a conta do Azure Remote Rendering:
    1. Abra uma janela do PowerShell.
    1. Execute: `Connect-AzAccount` e siga as instruções na tela.

    > [!NOTE]
    > Caso sua organização tenha mais de uma assinatura, talvez seja necessário especificar os argumentos SubscriptionId e Tenant. Encontre detalhes na [Documentação sobre Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

1. Baixe a pasta *Scripts* do [repositório do GitHub do Azure Remote Rendering](https://github.com/Azure/azure-remote-rendering).

## <a name="configuration-file"></a>Arquivo de configuração

Ao lado dos arquivos `.ps1` há um `arrconfig.json` que você precisa preencher:

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> Certifique-se de escapar as barras invertidas corretamente no caminho LocalAssetDirectoryPath usando barras invertidas duplas: "\\\\" e usar barras "/" em todos os outros caminhos, como inputFolderPath e inputAssetPath.

> [!CAUTION]
> Os valores opcionais precisam ser preenchidos ou você precisará remover a chave e o valor por completo. Por exemplo, se você não usar o parâmetro `"outputAssetFileName"`, precisará excluir a linha inteira dentro de `arrconfig.json`.

### <a name="accountsettings"></a>accountSettings

Para `arrAccountId` e `arrAccountKey`, confira [Criar uma conta do Azure Remote Rendering](../how-tos/create-an-account.md).
Para `region`, confira a [lista de regiões disponíveis](../reference/regions.md).

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Esta estrutura precisa ser preenchida caso você deseje executar **RenderingSession.ps1**:

- **vmSize:** Seleciona o tamanho da máquina virtual. Selecione [*standard*](../reference/vm-sizes.md) ou [*premium*](../reference/vm-sizes.md). Desligue as sessões de renderização quando você não precisar mais delas.
- **maxLeaseTime:** A duração pela qual você deseja arrendar a VM. Ela será desligada quando a concessão expirar. O tempo de concessão pode ser estendido mais tarde (veja abaixo).

### <a name="assetconversionsettings"></a>assetConversionSettings

Essa estrutura deverá ser preenchida se você quiser executar **Conversion.ps1**.

Para obter detalhes, confira [Preparar uma conta de Armazenamento do Azure](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts).

## <a name="script-renderingsessionps1"></a>Script: RenderingSession.ps1

Esse script é usado para criar, consultar e parar sessões de renderização.

> [!IMPORTANT]
> Confirme se você preencheu as seções *accountSettings* e *renderingSessionSettings* no arrconfig.json.

### <a name="create-a-rendering-session"></a>Criar uma sessão de renderização

Uso normal com um arrconfig.json totalmente preenchido:

```PowerShell
.\RenderingSession.ps1
```

O script chamará a [API REST de gerenciamento de sessão](../how-tos/session-rest-api.md) para criar uma VM de renderização com as configurações especificadas. Em caso de sucesso, ele recuperará a *sessionId*. Em seguida, ele sondará as propriedades da sessão até que a sessão esteja pronta ou um erro tenha ocorrido.

Para usar um arquivo **configuração alternativa**:

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Você pode **substituir configurações individuais** do arquivo de configuração:

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Para apenas **iniciar uma sessão sem sondagem**, você pode usar:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

A *sessionId* que o script recupera deve ser passada para a maioria dos outros comandos da sessão.

### <a name="retrieve-session-properties"></a>Recuperar propriedades da sessão

Para obter as propriedades de uma sessão, execute:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Use `-Poll` para aguardar até que a sessão esteja *pronta* ou até que ocorra um erro.

### <a name="list-active-sessions"></a>Listar sessões ativas

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Parar uma sessão

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Alterar propriedades da sessão

No momento, só é possível a alteração do maxLeaseTime de uma sessão.

> [!NOTE]
> O tempo de concessão sempre é contado tendo por base o momento em que a VM da sessão foi criada inicialmente. Assim, para estender a concessão da sessão por mais uma hora, aumente o *maxLeaseTime* em uma hora.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Script: Conversion.ps1

Esse script é usado para converter modelos de entrada no formato do runtime específico do Azure Remote Rendering.

> [!IMPORTANT]
> Confirme se você preencheu as seções *accountSettings* e *assetConversionSettings* no arrconfig.json.

O script demonstra as duas opções para usar contas de armazenamento com o serviço:

- Conta de armazenamento vinculada à conta do Azure Remote Rendering
- Fornecendo acesso ao armazenamento por meio de SAS (Assinaturas de Acesso Compartilhado)

### <a name="linked-storage-account"></a>Conta de armazenamento vinculada

Depois de preencher completamente o arrconfig.json e vincular uma conta de armazenamento, você pode usar o comando a seguir. A vinculação da sua conta de armazenamento é descrita em [Criar uma conta](../how-tos/create-an-account.md#link-storage-accounts).

O uso de uma conta de armazenamento vinculada é a maneira preferencial para usar o serviço de conversão, visto que não há necessidade de gerar Assinaturas de Acesso Compartilhado.

```PowerShell
.\Conversion.ps1
```

1. Carregar todos os arquivos contidos na `assetConversionSettings.modelLocation` no contêiner de blob de entrada no `inputFolderPath` especificado.
1. Chamar a [API REST de conversão do modelo](../how-tos/conversion/conversion-rest-api.md) para iniciar a [conversão do modelo](../how-tos/conversion/model-conversion.md)
1. Sondar o status da conversão até a conversão ser bem-sucedida ou falhar.
1. Gerar detalhes da localização do arquivo convertido (conta de armazenamento, contêiner de saída e caminho do arquivo no contêiner).

### <a name="access-to-storage-via-shared-access-signatures"></a>Acesso ao armazenamento por meio Assinaturas de Acesso Compartilhado

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Isso vai:

1. Carregar o arquivo local do `assetConversionSettings.localAssetDirectoryPath` no contêiner de blob de entrada.
1. Gerar um URI de SAS para o contêiner de entrada.
1. Gerar um URI de SAS para o contêiner de saída.
1. Chamar a [API REST de conversão do modelo](../how-tos/conversion/conversion-rest-api.md) para iniciar a [conversão do modelo](../how-tos/conversion/model-conversion.md).
1. Sondar o status da conversão até a conversão ser bem-sucedida ou falhar.
1. Gerar detalhes da localização do arquivo convertido (conta de armazenamento, contêiner de saída e caminho do arquivo no contêiner).
1. Gerar um URI de SAS para o modelo convertido no contêiner de blob de saída.

### <a name="additional-command-line-options"></a>Opções adicionais de linha de comando

Para usar um arquivo **configuração alternativa**:

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Para apenas **iniciar conversão de modelo sem sondagem**, você pode usar:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

Você pode **substituir configurações individuais** do arquivo de configuração usando as seguintes opções de linha de comando:

* **Id:** ConversionId usada com GetConversionStatus
* **ArrAccountId:** arrAccountId de accountSettings
* **ArrAccountKey:** substituição para arrAccountKey de accountSettings
* **Region:** substituição da região de accountSettings
* **ResourceGroup:** substituição para resourceGroup de assetConversionSettings
* **StorageAccountName:** substituição para storageAccountName de assetConversionSettings
* **BlobInputContainerName:** substituição para blobInputContainer de assetConversionSettings
* **LocalAssetDirectoryPath:** substituição para localAssetDirectoryPath de assetConversionSettings
* **InputAssetPath:** substituição para inputAssetPath de assetConversionSettings
* **BlobOutputContainerName:** substituição para blobOutputContainerName de assetConversionSettings
* **OutputFolderPath:** substituição para outputFolderPath de assetConversionSettings
* **OutputAssetFileName:** substituição para outputAssetFileName de assetConversionSettings

Por exemplo, você pode combinar um número de opções fornecidas como esta:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>Executar os estágios de conversão individuais

Se você quiser executar etapas individuais do processo, poderá usar:

Somente carregar dados do LocalAssetDirectoryPath especificado.

```PowerShell
.\Conversion.ps1 -Upload
```

Somente iniciar o processo de conversão de um modelo já carregado no Armazenamento de Blobs (não executar Carregar, não sondar o status da conversão). O script retornará uma *conversionId*.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

E você pode recuperar o status de conversão dessa conversão usando:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Use `-Poll` para aguardar até que a conversão seja concluída ou um erro tenha ocorrido.

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Renderizar um modelo com o Unity](../quickstarts/render-model.md)
- [Início Rápido: Converter um modelo para renderização](../quickstarts/convert-model.md)
- [Conversão de modelo](../how-tos/conversion/model-conversion.md)