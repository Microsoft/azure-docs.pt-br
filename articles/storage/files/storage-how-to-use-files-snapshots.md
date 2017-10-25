---
title: "Como usar o instantâneo de compartilhamento de Arquivo do Azure (versão prévia) | Microsoft Docs"
description: "Use o instantâneo de compartilhamento de Arquivo do Azure. Instantâneos de compartilhamento de Arquivo do Azure são uma versão somente leitura de um compartilhamento de Arquivo do Azure feita em um ponto no tempo. Quando um instantâneo de compartilhamento tiver sido criado, ele poderá ser lido, copiado ou excluído, mas não modificado. Os instantâneos de compartilhamento fornecem uma maneira de fazer backup do compartilhamento da maneira como ele aparece em um momento específico."
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 6643dad5ea3ba703e26f5708cdd2e925f702847f
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="work-with-azure-file-share-snapshots-preview"></a>Trabalhar com instantâneos de compartilhamento de Arquivo do Azure (versão prévia)
Instantâneos de compartilhamento de Arquivo do Azure (versão prévia) são uma versão somente leitura de um compartilhamento de Arquivo do Azure feita em um ponto no tempo. Quando um instantâneo de compartilhamento tiver sido criado, ele poderá ser lido, copiado ou excluído, mas não modificado. Os instantâneos de compartilhamento fornecem uma maneira de fazer backup do compartilhamento da maneira como ele aparece em um momento específico. Neste artigo, aprenderá sobre como criar, gerenciar e excluir instantâneos de compartilhamento de Arquivos do Azure. Para saber mais sobre o instantâneo de compartilhamento, consulte [visão geral de instantâneo de compartilhamento](storage-snapshots-files.md) ou [perguntas frequentes sobre instantâneos](storage-files-faq.md)

## <a name="create-azure-files-share-snapshots"></a>Criar instantâneos de compartilhamento de Arquivos do Azure

Você pode criar um instantâneo de compartilhamento usando o Portal, o Powershell, CLI, REST ou qualquer SDK de Armazenamento. As seguintes seções informarão como criar um instantâneo de compartilhamento usando o Portal, a CLI e o PowerShell. 

Você pode fazer um instantâneo de compartilhamento de um compartilhamento de arquivos enquanto ele está em uso. No entanto, instantâneos de compartilhamento compartilham somente os dados de captura já gravados no compartilhamento de Arquivos do Azure no momento em que o comando de instantâneo de compartilhamento é emitido. Isso pode excluir quaisquer dados armazenados em cache por quaisquer aplicativos ou pelo sistema operacional.

### <a name="create-share-snapshot-using-portal"></a>Criar instantâneo de compartilhamento usando o portal  
Você pode simplesmente navegar para o compartilhamento de arquivos no portal e selecionar o botão `Create a Snapshot` para criar um instantâneo de compartilhamento de um ponto no tempo.

>   ![./media/storage-snapshots-create/portal-create-snapshot.png](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-share-snapshot-using-cli-20"></a>Criar instantâneo de compartilhamento usando a CLI 2.0
Você pode criar um instantâneo de compartilhamento usando o comando `az storage share snapshot`:

```azurecli-interactive
az storage share snapshot -n <share name>
```

Saída de Exemplo
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="create-share-snapshot-using-powershell"></a>Criar instantâneo de compartilhamento usando o PowerShell
Você pode criar um instantâneo de compartilhamento usando o comando `$share.Snapshot()`:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

```
## <a name="list-share-snapshots-browse-share-snapshot-contents-and-restore-from-snapshots"></a>Listar instantâneos de compartilhamento, procurar o conteúdo do instantâneo de compartilhamento e restaurar de instantâneos

Você pode enumerar os instantâneos de compartilhamento associados com o compartilhamento de arquivos usando a integração de "Versões Anteriores" no Windows, por meio do Portal, da biblioteca de cliente, do PowerShell e do REST. Quando o compartilhamento de Arquivos do Azure for montado, você poderá exibir todas as versões anteriores do arquivo usando a integração de "Versões anteriores" do SMB. Quando o compartilhamento de Arquivos do Azure for montado, você poderá exibir todas as versões anteriores do diretório usando a integração de "Versões anteriores" do SMB. Nas seções a seguir, você aprenderá a usar o Portal do Azure, o Windows e a CLI do Azure 2.0 para listar, procurar e restaurar instantâneos de compartilhamento.

### <a name="share-snapshot-operations-in-portal"></a>Operações de instantâneo de compartilhamento no Portal

Você pode examinar todos os instantâneos de compartilhamento de um compartilhamento de arquivos no Portal e procurar o instantâneo de compartilhamento desejado para exibir seu conteúdo

#### <a name="view-share-snapshot"></a>Exibir instantâneo de compartilhamento
Em seu compartilhamento de arquivos em um instantâneo, selecione **Exibir instantâneos**

![./media/storage-snapshots-list-browse/snapshot-view-portal.png](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-share-snapshot-content"></a>Listar e procurar o conteúdo do instantâneo de compartilhamento
Exiba a lista de instantâneos de compartilhamento e procure o respectivo conteúdo selecionando diretamente o compartilhamento de instantâneo por meio do carimbo de data/hora desejado.

![./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

Você também pode selecionar o botão **Conectar** na sua exibição de instantâneos de modo de exibição para obter o comando `net use` e o caminho do diretório para um instantâneo de compartilhamento específico, para o qual você pode navegar diretamente.


![./media/storage-snapshots-list-browse/snapshot-connect-portal.pngsnapshot-list-portal.png](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-share-snapshot"></a>Baixar ou restaurar de um instantâneo de compartilhamento
De dentro do Portal, baixe ou restaure o arquivo desejado de um instantâneo.

![./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="file-share-snapshot-operations-in-windows"></a>Operações de instantâneo de compartilhamento no Windows
Quando você já tiver tirado instantâneos de compartilhamento do seu compartilhamento de arquivos, você poderá exibir versões anteriores de um compartilhamento, diretório ou um arquivo específico do seu compartilhamento de arquivos do Azure montado no Windows. Por exemplo, eis aqui como você pode usar o recurso **Versões Anteriores** para exibir e restaurar uma versão anterior de um diretório específico no Windows:

> [!Note]  
> As mesmas operações podem ser feitas no nível de compartilhamento, bem como no nível de arquivo. Somente a versão que contém as alterações para esse diretório ou arquivo é mostrada na lista. Se um arquivo ou diretório específico não foi alterado entre dois instantâneos de compartilhamento, o instantâneo de compartilhamento só aparece na lista de versões anteriores do nível de compartilhamento, mas não na lista de versões anteriores do diretório ou do arquivo.

#### <a name="mount-file-share"></a>Montar compartilhamento de arquivos
Primeiro, monte o compartilhamento de arquivos usando o comando net use.

#### <a name="open-mounted-share-in-explorer"></a>Abrir compartilhamento montado no explorador
Vá para o Explorador de Arquivos e localize o compartilhamento montado.

![./media/storage-snapshots-list-browse/snapshot-windows-mount.png](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>Listar Versões Anteriores
 Navegue até o item ou o item pai que precisa ser restaurado. Clique duas vezes para navegar até o diretório desejado. Clique com o botão direito do mouse e selecione "Propriedades" no menu

![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

Selecione "**Versões Anteriores"** para ver a lista de instantâneos de compartilhamento para esse diretório. A listagem pode levar alguns segundos para carregar, dependendo da velocidade da rede e do número de instantâneos de compartilhamento.

 ![./media/storage-snapshots-list-browse/snapshot-windows-list.png](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

Você pode selecionar **Abrir** para procurar um instantâneo específico 

 ![./media/storage-snapshots-list-browse/snapshot-browse-windows.png](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Atualizar de uma Versão Anterior
**Restaure** para copiar o conteúdo do diretório inteiro recursivamente, no momento da criação do instantâneo de compartilhamento, para o local original.
 ![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="file-share-snapshot-operations-in-azure-cli-20"></a>Operações de instantâneo de compartilhamento na CLI do Azure 2.0
Você pode usar a CLI do Azure 2.0 para executar operações como procurar conteúdo de instantâneos de compartilhamento, restaurar ou baixar arquivos desses instantâneos, listar instantâneos de compartilhamento ou excluí-los.

#### <a name="list-share-snapshots"></a>Listar instantâneos de compartilhamento

Você pode listar instantâneos de compartilhamento de um determinado compartilhamento usando [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) com `--include-snapshots`

```azurecli-interactive 
az storage share list --include-snapshots
```

#### <a name="sample-output"></a>Saída de Exemplo
O comando lhe dará a lista de compartilhamento de instantâneos juntamente com todas as respectivas propriedades associadas.

```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

#### <a name="browse-share-snapshots"></a>Procurar instantâneos de compartilhamento
Você também pode procurar em um determinado instantâneo de compartilhamento para exibir seu conteúdo usando [`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). É necessário especificar o nome do compartilhamento `--share-name` e o carimbo de data/hora que desejamos procurar em `--snapshot '2017-10-04T19:45:18.0000000Z'`

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

#### <a name="sample-output"></a>Saída de Exemplo

Você verá que o conteúdo do instantâneo de compartilhamento é idêntico ao conteúdo do compartilhamento no ponto no tempo em que esse instantâneo de compartilhamento foi criado.

```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
#### <a name="restore-from-share-snapshots"></a>Restaurar de instantâneos de compartilhamento

Você pode restaurar um arquivo copiando ou baixando um arquivo de instantâneo de compartilhamento usando o comando `az storage file download`

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

#### <a name="sample-output"></a>Saída de Exemplo

Você verá que o conteúdo do arquivo baixado e suas propriedades é idêntico ao conteúdo e propriedades no ponto no tempo em que o instantâneo de compartilhamento foi criado.

```json
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```

## <a name="delete-azure-files-share-snapshot"></a>Excluir instantâneos de compartilhamento de Arquivos do Azure

Você pode excluir instantâneos de compartilhamento de arquivos usando o Portal do Azure, o PowerShell, a CLI, a API REST ou qualquer SDK de Armazenamento. As seguintes seções informarão como excluir um instantâneo de compartilhamento usando o Portal do Azure, a CLI e o PowerShell.

É possível procurar instantâneos de compartilhamento e exibir as diferenças entre dois deles usando qualquer ferramenta de comparação para determinar qual deles você deseja excluir. 

Você não pode excluir um compartilhamento que tem um instantâneo de compartilhamento. Primeiro, você deve excluir todos os instantâneos do compartilhamento para que seja possível excluí-lo.

### <a name="delete-share-snapshot-using-portal"></a>Excluir um instantâneo de compartilhamento usando o Portal  
Você pode navegar para a folha de compartilhamento de arquivos e selecionar o botão `delete` no Portal para excluir um ou mais instantâneos de compartilhamento.

>   ![./media/storage-snapshots-delete/portal-snapshots-delete.png](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-using-azure-cli-20"></a>Excluir um instantâneo de compartilhamento usando a CLI do Azure 2.0
Você pode excluir um instantâneo de compartilhamento usando o comando `[az storage share delete]` fornecendo o parâmetro `--snapshot '2017-10-04T23:28:35.0000000Z' ` com o carimbo de data/hora do instantâneo de compartilhamento:

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Saída de Exemplo
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-using-powershell"></a>Excluir um instantâneo de compartilhamento usando o PowerShell
Você pode criar um instantâneo de compartilhamento usando o comando `Remove-AzureStorageShare -Share`:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>Próximas etapas
* [Visão Geral do Instantâneo](storage-snapshots-files.md)
* [Perguntas Frequentes do Instantâneo](storage-files-faq.md)