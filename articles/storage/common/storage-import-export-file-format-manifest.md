---
title: Formato do arquivo de manifesto da Importação/Exportação do Azure | Microsoft Docs
description: Saiba mais sobre o formato do arquivo de manifesto da unidade que descreve o mapeamento entre blobs no Armazenamento de Blobs do Azure e arquivo em uma unidade em um trabalho de importação ou exportação no serviço de Importação/Exportação.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ee53cc3a639a79e1b29ac6cd537bfb04e05b1bca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478616"
---
# <a name="azure-importexport-service-manifest-file-format"></a>Formato de arquivo de manifesto do serviço de Importação/Exportação do Azure
O arquivo de manifesto da unidade descreve o mapeamento entre blobs no Armazenamento de Blobs do Azure e os arquivos na unidade que compõem um trabalho de importação ou exportação. Para uma operação de importação, o arquivo de manifesto é criado como parte do processo de preparação da unidade e é armazenado na unidade antes de ser enviado ao datacenter do Azure. Durante uma operação de exportação, o manifesto é criado e armazenado na unidade pelo serviço de Importação/Exportação do Azure.  
  
Para os trabalhos de importação e exportação, o arquivo de manifesto da unidade é armazenado na unidade de importação ou exportação; ele não é transmitido ao serviço por meio de qualquer operação de API.  
  
Veja a seguir uma descrição do formato geral de um arquivo de manifesto da unidade:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveManifest Version="2014-11-01">  
  <Drive>  
    <DriveId>drive-id</DriveId>  
    import-export-credential  
  
    <!-- First Blob List -->  
    <BlobList>  
      <!-- Global properties and metadata that applies to all blobs -->  
      [<MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>]  
      [<PropertiesPath   
        Hash="md5-hash">global-properties-file-path</PropertiesPath>]  
  
      <!-- First Blob -->  
      <Blob>  
        <BlobPath>blob-path-relative-to-account</BlobPath>  
        <FilePath>file-path-relative-to-transfer-disk</FilePath>  
        [<ClientData>client-data</ClientData>]  
        [<Snapshot>snapshot</Snapshot>]  
        <Length>content-length</Length>  
        [<ImportDisposition>import-disposition</ImportDisposition>]  
        page-range-list-or-block-list          
        [<MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>]  
        [<PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>]  
      </Blob>  
  
      <!-- Second Blob -->  
      <Blob>  
      . . .  
      </Blob>  
    </BlobList>  
  
    <!-- Second Blob List -->  
    <BlobList>  
    . . .  
    </BlobList>  
  </Drive>  
</DriveManifest>  
  
import-export-credential ::=   
  <StorageAccountKey>storage-account-key</StorageAccountKey> | <ContainerSas>container-sas</ContainerSas>  
  
page-range-list-or-block-list ::=   
  page-range-list | block-list  
  
page-range-list ::=   
    <PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
    </PageRangeList>  
  
block-list ::=  
    <BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       Hash="md5-hash"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       Hash="md5-hash"/>]  
    </BlockList>  

```

## <a name="manifest-xml-elements-and-attributes"></a>Elementos e atributos XML do manifesto

Os elementos de dados e os atributos do formato XML de manifesto da unidade são especificados na tabela a seguir.  
  
|Elemento XML|Type|DESCRIÇÃO|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Elemento raiz|O elemento raiz do arquivo de manifesto. Todos os outros elementos no arquivo estão abaixo desse elemento.|  
|`Version`|Atributo, cadeia de caracteres|A versão do arquivo de manifesto.|  
|`Drive`|Elemento XML aninhado|Contém o manifesto de cada unidade.|  
|`DriveId`|Cadeia de caracteres|O identificador de unidade exclusivo. O identificador de unidade pode ser encontrado consultando o número de série da unidade. O número de série da unidade também é normalmente impresso na parte externa da unidade. O elemento `DriveID` deve aparecer antes de qualquer elemento `BlobList` no arquivo de manifesto.|  
|`StorageAccountKey`|Cadeia de caracteres|Obrigatório para os trabalhos de importação se, e somente se, `ContainerSas` não for especificado. A chave de conta da conta de armazenamento do Azure associada ao trabalho.<br /><br /> Esse elemento é omitido do manifesto em uma operação de exportação.|  
|`ContainerSas`|Cadeia de caracteres|Obrigatório para os trabalhos de importação se, e somente se, `StorageAccountKey` não for especificado. O SAS do contêiner para acessar os blobs associados ao trabalho. Consulte [Put Job](/rest/api/storageimportexport/jobs) para saber o formato. Esse elemento é omitido do manifesto em uma operação de exportação.|  
|`ClientCreator`|Cadeia de caracteres|Especifica o cliente que criou o arquivo XML. Esse valor não é interpretado pelo serviço de Importação/Exportação.|  
|`BlobList`|Elemento XML aninhado|Contém uma lista de blobs que fazem parte do trabalho de importação ou exportação. Cada blob em uma lista de blobs compartilha os mesmos metadados e propriedades.|  
|`BlobList/MetadataPath`|Cadeia de caracteres|Opcional. Especifica o caminho relativo de um arquivo no disco que contém os metadados padrão que serão definidos em blobs na lista de blobs para uma operação de importação. Opcionalmente, esses metadados podem ser substituídos de blob em blob.<br /><br /> Esse elemento é omitido do manifesto em uma operação de exportação.|  
|`BlobList/MetadataPath/@Hash`|Atributo, cadeia de caracteres|Especifica o valor de hash MD5 codificado com Base16 do arquivo de metadados.|  
|`BlobList/PropertiesPath`|Cadeia de caracteres|Opcional. Especifica o caminho relativo de um arquivo no disco que contém as propriedades padrão que serão definidos em blobs na lista de blobs para uma operação de importação. Opcionalmente, essas propriedades podem ser substituídas de blob em blob.<br /><br /> Esse elemento é omitido do manifesto em uma operação de exportação.|  
|`BlobList/PropertiesPath/@Hash`|Atributo, cadeia de caracteres|Especifica o valor de hash MD5 codificado com Base16 do arquivo de propriedades.|  
|`Blob`|Elemento XML aninhado|Contém informações sobre cada blob em cada lista de blobs.|  
|`Blob/BlobPath`|Cadeia de caracteres|O URI relativo do blob, começando com o nome do contêiner. Se o blob estiver no contêiner raiz, ele deverá começar com `$root`.|  
|`Blob/FilePath`|Cadeia de caracteres|Especifica o caminho relativo até o arquivo na unidade. Para trabalhos de exportação, o caminho do blob será usado para o caminho do arquivo, se for possível; *, por exemplo,*, `pictures/bob/wild/desert.jpg` serão exportados para `\pictures\bob\wild\desert.jpg`. No entanto, devido a limitações de nomes NTFS, um blob pode ser exportado para um arquivo com um caminho que não lembra o caminho do blob.|  
|`Blob/ClientData`|Cadeia de caracteres|Opcional. Contém comentários do cliente. Esse valor não é interpretado pelo serviço de Importação/Exportação.|  
|`Blob/Snapshot`|DateTime|Opcional para trabalhos de exportação. Especifica o identificador de instantâneo de um instantâneo de blob exportado.|  
|`Blob/Length`|Número inteiro|Especifica o comprimento total do blob em bytes. O valor pode ser de até 200 GB para um blob de blocos e de até 1 TB para um blob de páginas. Para um blob de páginas, esse valor deve ser um múltiplo de 512.|  
|`Blob/ImportDisposition`|Cadeia de caracteres|Opcional para trabalhos de importação, omitida para trabalhos de exportação. Isso especifica como o serviço de Importação/Exportação deve tratar o caso para um trabalho de importação no qual já existe um blob com o mesmo nome. Se esse valor for omitido do manifesto de importação, o valor padrão será `rename`.<br /><br /> Os valores para esse elemento incluem:<br /><br /> -   `no-overwrite`: Se já há um blob de destino com o mesmo nome, a operação de importação ignora a importação desse arquivo.<br />-   `overwrite`: Qualquer blob de destino existente será substituído completamente pelo arquivo recém-importado.<br />-   `rename`: O novo blob será carregado com um nome modificado.<br /><br /> A regra de renomeação é a seguinte:<br /><br /> - Se o nome do blob não contiver um ponto, um novo nome será gerado anexando `(2)` ao nome original do blob; se esse novo nome também entrar em conflito com um nome de blob existente, `(3)` será anexado no lugar de `(2)`; e assim por diante.<br />- Se o nome do blob contiver um ponto, a parte após o último ponto será considerada o nome da extensão. Semelhante ao procedimento anterior, `(2)` é inserido antes do último ponto a fim de gerar um novo nome; se o novo nome ainda estiver em conflito com um nome de blob existente, o serviço tentará `(3)`, `(4)` e assim por diante, até que encontre um nome não conflitante.<br /><br /> Alguns exemplos:<br /><br /> O blob `BlobNameWithoutDot` será renomeado como:<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> O blob `Seattle.jpg` será renomeado como:<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Elemento XML aninhado|Obrigatório para um blob de páginas.<br /><br /> Para uma operação de importação, especifica uma lista de intervalos de bytes de um arquivo a ser importado. Cada intervalo de páginas é descrito por um deslocamento e comprimento no arquivo de origem que descreve o intervalo de páginas, junto com um hash MD5 da região. O atributo `Hash` de um intervalo de páginas é obrigatório. O serviço validará se o hash dos dados no blob corresponde ao hash MD5 calculado do intervalo de páginas. É possível usar qualquer quantidade de intervalos de páginas para descrever um arquivo para uma importação, com o tamanho total podendo chegar até 1 TB. Todos os intervalos de página devem ser ordenados por deslocamento, e não há permissão para sobreposições.<br /><br /> Para uma exportação operação, especifique um conjunto de intervalos de bytes de um blob que foi exportado para a unidade.<br /><br /> Os intervalos de página juntos podem cobrir apenas subintervalos de um blob ou arquivo.  A parte restante do arquivo não coberto por qualquer intervalo de página é esperada, e seu conteúdo pode ser indefinido.|  
|`PageRange`|Elemento XML|Representa um intervalo de páginas.|  
|`PageRange/@Offset`|Atributo, inteiro|Especifica o deslocamento no arquivo de transferência e o blob onde o intervalo de páginas especificado começa. Esse valor deve ser um múltiplo de 512.|  
|`PageRange/@Length`|Atributo, inteiro|Especifica o comprimento do intervalo de páginas. Esse valor deve ser um múltiplo de 512 e não ultrapassar 4 MB.|  
|`PageRange/@Hash`|Atributo, cadeia de caracteres|Especifica o valor de hash MD5 codificado com Base16 do intervalo de páginas.|  
|`BlockList`|Elemento XML aninhado|Obrigatório para um blob de blocos com blocos nomeados.<br /><br /> Para uma operação de importação, a lista de blocos especifica um conjunto de blocos que será importado para o Armazenamento do Azure. Para uma operação de exportação, a lista de blocos especifica onde cada bloco foi armazenado no arquivo no disco de exportação. Cada bloco é descrito por um deslocamento no arquivo e um cumprimento de bloco; cada bloco ainda recebe um nome de um atributo de ID de bloco, e contém um hash MD5 para o bloco. É possível usar até 50.000 blocos para descrever um blob.  Todos os blocos deverão ser ordenados por deslocamento e, juntos, devem cobrir o intervalo completo do arquivo, *ou seja*, não deve haver nenhum espaço entre os blocos. Se o blob não ultrapassar 64 MB, todas as IDs de bloco deverão estar todas ausentes ou todas presentes. As IDs de bloco devem ser cadeias de caracteres codificadas em Base64. Consulte [Put Block](/rest/api/storageservices/put-block) para ver mais requisitos para IDs de bloco.|  
|`Block`|Elemento XML|Representa um bloco.|  
|`Block/@Offset`|Atributo, inteiro|Especifica o deslocamento no qual o bloco especificado começa.|  
|`Block/@Length`|Atributo, inteiro|Especifica o número de bytes no bloco; esse valor não deve ultrapassar 4 MB.|  
|`Block/@Id`|Atributo, cadeia de caracteres|Especifica uma cadeia de caracteres que representa a ID do bloco.|  
|`Block/@Hash`|Atributo, cadeia de caracteres|Especifica o hash MD5 codificado para Base16 do bloco.|  
|`Blob/MetadataPath`|Cadeia de caracteres|Opcional. Especifica o caminho relativo de um arquivo de metadados. Durante uma importação, os metadados são definidos no blob de destino. Durante uma operação de exportação, os metadados de blob são armazenados no arquivo de metadados na unidade.|  
|`Blob/MetadataPath/@Hash`|Atributo, cadeia de caracteres|Especifica o hash MD5 codificado em Base16 do arquivo de metadados do blob.|  
|`Blob/PropertiesPath`|Cadeia de caracteres|Opcional. Especifica o caminho relativo de um arquivo de propriedades. Durante uma importação, as propriedades são definidas no blob de destino. Durante uma operação de exportação, as propriedades do blob são armazenadas no arquivo de propriedades na unidade.|  
|`Blob/PropertiesPath/@Hash`|Atributo, cadeia de caracteres|Especifica o hash MD5 codificado em Base16 do arquivo de propriedades do blob.|  
  
## <a name="next-steps"></a>Próximas etapas
 
* [API REST de Importação/Exportação do Armazenamento](/rest/api/storageimportexport/)
