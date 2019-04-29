---
title: Formato do arquivo de log da Importação/Exportação do Azure | Microsoft Docs
description: Saiba mais sobre o formato dos arquivos de log criados quando etapas são executadas para um trabalho do serviço de Importação/Exportação.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 00e226134039d29efd744290c4bc63abd50adc89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478599"
---
# <a name="azure-importexport-service-log-file-format"></a>Formato de arquivo de log de serviço de Importação/Exportação do Azure
Quando o serviço de importação/exportação do Microsoft Azure executa uma ação em uma unidade como parte de um trabalho de importação ou exportação, os logs são gravados para bloquear blobs na conta de armazenamento associado ao trabalho.  
  
Há dois logs que podem ser gravados pelo serviço de importação/exportação:  
  
-   O log de erros sempre é gerado se ocorrer um erro.  
  
-   O log detalhado não é habilitado por padrão, mas pode ser habilitado configurando a propriedade `EnableVerboseLog` em um [Trabalho Put Job](/rest/api/storageimportexport/jobs) ou em uma operação de [Atualização de Propriedades do Trabalho](/rest/api/storageimportexport/jobs).  
  
## <a name="log-file-location"></a>Local do arquivo de log  
Os logs são gravados para bloquear blobs no contêiner ou diretório virtual especificado pela configuração `ImportExportStatesPath`, que pode ser definida em um operação `Put Job`. O local no qual os logs são gravados depende de como a autenticação é especificada para o trabalho, junto com o valor especificado para `ImportExportStatesPath`. A autenticação para o trabalho pode ser especificada por meio de uma chave de conta de armazenamento ou um contêiner SAS (assinatura de acesso compartilhado).  
  
O nome do contêiner ou diretório virtual pode ser o nome padrão de `waimportexport` ou outro nome de contêiner ou diretório virtual que você especificar.  
  
A tabela a seguir mostra as opções possíveis:  
  
|Método de autenticação|Valor de `ImportExportStatesPath`Elemento|Local dos Blobs de Log|  
|---------------------------|----------------------------------------------|---------------------------|  
|Chave da conta de armazenamento|Valor padrão|Um contêiner denominado `waimportexport`, que é o contêiner padrão. Por exemplo: <br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Chave da conta de armazenamento|Valores especificados pelo usuário|Um contêiner nomeado pelo usuário. Por exemplo: <br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|SAS do contêiner|Valor padrão|Um diretório virtual chamado `waimportexport`, que é o nome padrão, sob o contêiner especificado na SAS.<br /><br /> Por exemplo, se a SAS especificada para o trabalho é `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, então o local do log será`https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`|  
|SAS do contêiner|Valores especificados pelo usuário|Um diretório virtual nomeado pelo usuário, sob o contêiner especificado na SAS.<br /><br /> Por exemplo, se a SAS especificada para o trabalho é `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, e o diretório virtual especificado for denominado `mylogblobs`, então o local do log será `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
Você pode recuperar a URL de erro e logs detalhados chamando a operação [Obter Trabalho](/rest/api/storageimportexport/jobs). Os logs estão disponíveis após a conclusão do processamento da unidade.  
  
## <a name="log-file-format"></a>Formato do arquivo de log  
O formato para ambos os logs é o mesmo: um blob que contém descrições XML dos eventos ocorridos ao copiar blobs entre o disco rígido e a conta do cliente.  
  
O log detalhado contém informações completas sobre o status da operação de cópia para cada blob (para um trabalho de importação) ou arquivo (para um trabalho de exportação), enquanto que o log de erros contém apenas as informações para blobs ou arquivos que tiveram erros durante o trabalho de importação ou exportação.  
  
O formato de log detalhado é mostrado abaixo. O log de erros tem a mesma estrutura, mas filtra operações bem-sucedidas.  

```xml
<DriveLog Version="2014-11-01">  
  <DriveId>drive-id</DriveId>  
  [<Blob Status="blob-status">  
   <BlobPath>blob-path</BlobPath>  
   <FilePath>file-path</FilePath>  
   [<Snapshot>snapshot</Snapshot>]  
   <Length>length</Length>  
   [<LastModified>last-modified</LastModified>]  
   [<ImportDisposition Status="import-disposition-status">import-disposition</ImportDisposition>]  
   [page-range-list-or-block-list]  
   [metadata-status]  
   [properties-status]  
  </Blob>]  
  [<Blob>  
    . . .  
  </Blob>]  
  <Status>drive-status</Status>  
</DriveLog>  
  
page-range-list-or-block-list ::= 
  page-range-list | block-list  
  
page-range-list ::=   
<PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
</PageRangeList>  
  
block-list ::=  
<BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       [Hash="md5-hash"] Status="block-status"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       [Hash="md5-hash"] Status="block-status"/>]  
</BlockList>  
  
metadata-status ::=  
<Metadata Status="metadata-status">  
   [<GlobalPath Hash="md5-hash">global-metadata-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">metadata-file-path</Path>]  
</Metadata>  
  
properties-status ::=  
<Properties Status="properties-status">  
   [<GlobalPath Hash="md5-hash">global-properties-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">properties-file-path</Path>]  
</Properties>  
```

A tabela a seguir descreve os elementos do arquivo de log.  
  
|Elemento XML|Type|DESCRIÇÃO|  
|-----------------|----------|-----------------|  
|`DriveLog`|Elemento XML|Representa um log de unidade.|  
|`Version`|Atributo, cadeia de caracteres|A versão do formato do log.|  
|`DriveId`|Cadeia de caracteres|Número de série do hardware da unidade.|  
|`Status`|Cadeia de caracteres|Status do processamento da unidade. Consulte a tabela `Drive Status Codes` abaixo para obter mais informações.|  
|`Blob`|Elemento XML aninhado|Representa um blob.|  
|`Blob/BlobPath`|Cadeia de caracteres|O URI do blob.|  
|`Blob/FilePath`|Cadeia de caracteres|Caminho relativo até o arquivo na unidade.|  
|`Blob/Snapshot`|DateTime|A versão de instantâneo do blob, apenas para um trabalho de exportação.|  
|`Blob/Length`|Número inteiro|O comprimento total do blob em bytes.|  
|`Blob/LastModified`|DateTime|A data/hora em que o blob foi modificado pela última vez, apenas para um trabalho de exportação.|  
|`Blob/ImportDisposition`|Cadeia de caracteres|A disposição de importação do blob, para um trabalho de importação.|  
|`Blob/ImportDisposition/@Status`|Atributo, cadeia de caracteres|O status de disposição de importação.|  
|`PageRangeList`|Elemento XML aninhado|Representa uma lista de intervalos de página para um blobs de página.|  
|`PageRange`|Elemento XML|Representa um intervalo de páginas.|  
|`PageRange/@Offset`|Atributo, inteiro|Iniciando o deslocamento do intervalo de página no blob.|  
|`PageRange/@Length`|Atributo, inteiro|Comprimento em bytes do intervalo de página.|  
|`PageRange/@Hash`|Atributo, cadeia de caracteres|Hash de MD5 codificado para Base16 do intervalo de página.|  
|`PageRange/@Status`|Atributo, cadeia de caracteres|Status do processamento do intervalo de páginas.|  
|`BlockList`|Elemento XML aninhado|Representa uma lista de blocos para um blobs de bloco.|  
|`Block`|Elemento XML|Representa um bloco.|  
|`Block/@Offset`|Atributo, inteiro|Iniciando o deslocamento do bloco no blob.|  
|`Block/@Length`|Atributo, inteiro|Comprimento em bytes do bloco.|  
|`Block/@Id`|Atributo, cadeia de caracteres|ID do bloco.|  
|`Block/@Hash`|Atributo, cadeia de caracteres|Hash de MD5 codificado para Base16 do bloco.|  
|`Block/@Status`|Atributo, cadeia de caracteres|Status do processamento do bloco.|  
|`Metadata`|Elemento XML aninhado|Representa os metadados do blob.|  
|`Metadata/@Status`|Atributo, cadeia de caracteres|Status de processamento dos metadados do blob.|  
|`Metadata/GlobalPath`|Cadeia de caracteres|Caminho relativo para o arquivo de metadados globais.|  
|`Metadata/GlobalPath/@Hash`|Atributo, cadeia de caracteres|Hash de MD5 codificado para Base16 do arquivo global de metadados.|  
|`Metadata/Path`|Cadeia de caracteres|Caminho relativo para o arquivo de metadados.|  
|`Metadata/Path/@Hash`|Atributo, cadeia de caracteres|Hash de MD5 codificado para Base16 do arquivo de metadados.|  
|`Properties`|Elemento XML aninhado|Representa as propriedades do blob.|  
|`Properties/@Status`|Atributo, cadeia de caracteres|Status do processamento de propriedades de blob, por exemplo, arquivo não encontrado, concluído.|  
|`Properties/GlobalPath`|Cadeia de caracteres|Caminho relativo para o arquivo global de propriedades.|  
|`Properties/GlobalPath/@Hash`|Atributo, cadeia de caracteres|Hash de MD5 codificado para Base16 do arquivo global de propriedades.|  
|`Properties/Path`|Cadeia de caracteres|Caminho relativo para o arquivo de propriedades.|  
|`Properties/Path/@Hash`|Atributo, cadeia de caracteres|Hash de MD5 codificado para Base16 do arquivo de propriedades.|  
|`Blob/Status`|Cadeia de caracteres|Status do processamento do blob.|  
  
## <a name="drive-status-codes"></a>Códigos de status da unidade  
A tabela a seguir lista os códigos de status para o processamento de uma unidade.  
  
|Código de status|DESCRIÇÃO|  
|-----------------|-----------------|  
|`Completed`|A unidade concluiu o processamento sem erros.|  
|`CompletedWithWarnings`|A unidade concluiu o processamento com avisos em um ou mais blobs de acordo com as disposições de importação especificadas para os blobs.|  
|`CompletedWithErrors`|A unidade foi concluída com erros em um ou mais blobs ou partes.|  
|`DiskNotFound`|Nenhum disco foi encontrado na unidade.|  
|`VolumeNotNtfs`|O primeiro volume de dados no disco não está no formato NTFS.|  
|`DiskOperationFailed`|Uma falha desconhecida ocorreu ao executar operações na unidade.|  
|`BitLockerVolumeNotFound`|Nenhum volume criptografável BitLocker foi encontrado.|  
|`BitLockerNotActivated`|O BitLocker não está habilitado no volume.|  
|`BitLockerProtectorNotFound`|O protetor de chave de senha numérica não existe no volume.|  
|`BitLockerKeyInvalid`|A senha numérica fornecida não é pode desbloquear o volume.|  
|`BitLockerUnlockVolumeFailed`|Uma falha desconhecida ocorreu ao tentar desbloquear o volume.|  
|`BitLockerFailed`|Uma falha desconhecida ocorreu ao executar operações BitLocker.|  
|`ManifestNameInvalid`|O nome do arquivo de manifesto é inválido.|  
|`ManifestNameTooLong`|O nome do arquivo de manifesto é muito longo.|  
|`ManifestNotFound`|O arquivo de manifesto não foi encontrado.|  
|`ManifestAccessDenied`|Acesso ao arquivo de manifesto foi negado.|  
|`ManifestCorrupted`|O arquivo de manifesto está corrompido (o conteúdo não corresponde ao seu hash).|  
|`ManifestFormatInvalid`|O conteúdo do manifesto não corresponde ao formato necessário.|  
|`ManifestDriveIdMismatch`|A ID de unidade no arquivo de manifesto não corresponde à leitura do disco.|  
|`ReadManifestFailed`|Ocorreu uma falha de E/S de disco durante a leitura do manifesto.|  
|`BlobListFormatInvalid`|A lista de blob de exportação não corresponde ao formato necessário.|  
|`BlobRequestForbidden`|O acesso aos blobs na conta de armazenamento é proibido. Isso pode ser devido a uma chave da conta de armazenamento inválida ou SAS do contêiner.|  
|`InternalError`|Ocorreu um erro interno durante o processamento da unidade.|  
  
## <a name="blob-status-codes"></a>Códigos de status de blobs  
A tabela a seguir lista os códigos de status para o processamento de um blob.  
  
|Código de status|DESCRIÇÃO|  
|-----------------|-----------------|  
|`Completed`|O blob concluiu o processamento sem erros.|  
|`CompletedWithErrors`|O blob concluiu o processamento com erros em um ou mais intervalos de páginas ou blocos, metadados ou propriedades.|  
|`FileNameInvalid`|O nome do arquivo é inválido.|  
|`FileNameTooLong`|O nome do arquivo é muito longo.|  
|`FileNotFound`|O arquivo não foi encontrado.|  
|`FileAccessDenied`|Acesso ao arquivo foi negado.|  
|`BlobRequestFailed`|A solicitação de serviço Blob para acessar o blob falhou.|  
|`BlobRequestForbidden`|A solicitação de serviço Blob para acessar o blob é proibida. Isso pode ser devido a uma chave da conta de armazenamento inválida ou SAS do contêiner.|  
|`RenameFailed`|Falha ao renomear o blob (para um trabalho de importação) ou o arquivo (para um trabalho de exportação).|  
|`BlobUnexpectedChange`|Ocorreu uma alteração inesperada com o blob (para um trabalho de exportação).|  
|`LeasePresent`|Há uma concessão no blob.|  
|`IOFailed`|Uma falha de E/S de disco ou rede ocorreu durante o processamento de blob.|  
|`Failed`|Uma falha desconhecida ocorreu durante o processamento de blob.|  
  
## <a name="import-disposition-status-codes"></a>Códigos de status de disposição de importação  
A tabela a seguir lista os códigos de status para resolver uma disposição de importação.  
  
|Código de status|DESCRIÇÃO|  
|-----------------|-----------------|  
|`Created`|O blob foi criado.|  
|`Renamed`|O blob foi renomeado de acordo com a disposição de importação de renomeação. O elemento `Blob/BlobPath` contém o URI para o blob renomeado.|  
|`Skipped`|O blob foi ignorado de acordo com a disposição de importação `no-overwrite`.|  
|`Overwritten`|O blob substituiu um blob existente de acordo com a disposição de importação `overwrite`.|  
|`Cancelled`|Uma falha anterior interrompeu o processamento da disposição de importação.|  
  
## <a name="page-rangeblock-status-codes"></a>Códigos de status do intervalo de página/bloco  
A tabela a seguir lista os códigos de status para o processamento de um intervalo de páginas ou de um bloco.  
  
|Código de status|DESCRIÇÃO|  
|-----------------|-----------------|  
|`Completed`|O intervalo de páginas ou bloco concluiu o processamento sem erros.|  
|`Committed`|O bloco foi confirmado, mas não na lista completa de blocos porque outros blocos têm falha ou a própria lista de blocos falhou.|  
|`Uncommitted`|O bloco foi carregado, mas não foi confirmado.|  
|`Corrupted`|O intervalo de páginas ou bloco está corrompido (o conteúdo não corresponde ao seu hash).|  
|`FileUnexpectedEnd`|Fim de arquivo inesperado foi encontrado.|  
|`BlobUnexpectedEnd`|Fim de blob inesperado foi encontrado.|  
|`BlobRequestFailed`|A solicitação de serviço Blob para acessar o intervalo de páginas ou bloco falhou.|  
|`IOFailed`|Uma falha de E/S de disco ou rede ocorreu durante o processamento do intervalo de páginas ou bloco.|  
|`Failed`|Uma falha desconhecida ocorreu durante o processamento do intervalo de páginas ou bloco.|  
|`Cancelled`|Uma falha anterior interrompeu o processamento do intervalo de páginas ou bloco.|  
  
## <a name="metadata-status-codes"></a>Códigos de status de metadados  
A tabela a seguir lista os códigos de status para o processamento dos metadados do blob.  
  
|Código de status|DESCRIÇÃO|  
|-----------------|-----------------|  
|`Completed`|Os metadados concluíram o processamento sem erros.|  
|`FileNameInvalid`|O nome do arquivo de metadados é inválido.|  
|`FileNameTooLong`|O nome do arquivo de metadados é muito longo.|  
|`FileNotFound`|O arquivo de metadados não foi encontrado.|  
|`FileAccessDenied`|Acesso ao arquivo de metadados foi negado.|  
|`Corrupted`|O arquivo de metadados está corrompido (o conteúdo não corresponde ao seu hash).|  
|`XmlReadFailed`|O conteúdo dos metadados não corresponde ao formato necessário.|  
|`XmlWriteFailed`|A gravação de metadados XML falhou.|  
|`BlobRequestFailed`|A solicitação de serviço Blob para acessar os metadados falhou.|  
|`IOFailed`|Uma falha de E/S de disco ou rede ocorreu durante o processamento dos metadados.|  
|`Failed`|Uma falha desconhecida ocorreu durante o processamento dos metadados.|  
|`Cancelled`|Uma falha anterior interrompeu o processamento dos metadados.|  
  
## <a name="properties-status-codes"></a>Códigos de status das propriedades  
A tabela a seguir lista os códigos de status para o processamento das propriedades do blob.  
  
|Código de status|DESCRIÇÃO|  
|-----------------|-----------------|  
|`Completed`|As propriedades concluíram o processamento sem erros.|  
|`FileNameInvalid`|O nome do arquivo de propriedades é inválido.|  
|`FileNameTooLong`|O nome do arquivo de propriedades é muito longo.|  
|`FileNotFound`|O arquivo de propriedades não foi encontrado.|  
|`FileAccessDenied`|O acesso ao arquivo de propriedades foi negado.|  
|`Corrupted`|O arquivo de propriedades está corrompido (o conteúdo não corresponde ao seu hash).|  
|`XmlReadFailed`|O conteúdo das propriedades não corresponde ao formato necessário.|  
|`XmlWriteFailed`|Falha ao gravar as propriedades XML.|  
|`BlobRequestFailed`|A solicitação de serviço Blob para acessar as propriedades falhou.|  
|`IOFailed`|Uma falha de E/S de disco ou rede ocorreu durante o processamento das propriedades.|  
|`Failed`|Uma falha desconhecida ocorreu durante o processamento das propriedades.|  
|`Cancelled`|Uma falha anterior interrompeu o processamento das propriedades.|  
  
## <a name="sample-logs"></a>Logs de exemplo  
A seguir está um exemplo de log detalhado.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV123456</DriveId>  
    <Blob Status="Completed">  
       <BlobPath>pictures/bob/wild/desert.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\wild\desert.jpg</FilePath>  
       <Length>98304</Length>  
       <ImportDisposition Status="Created">overwrite</ImportDisposition>  
       <BlockList>  
          <Block Offset="0" Length="65536" Id="AAAAAA==" Hash=" 9C8AE14A55241F98533C4D80D85CDC68" Status="Completed"/>  
          <Block Offset="65536" Length="32768" Id="AQAAAA==" Hash=" DF54C531C9B3CA2570FDDDB3BCD0E27D" Status="Completed"/>  
       </BlockList>  
       <Metadata Status="Completed">  
          <GlobalPath Hash=" E34F54B7086BCF4EC1601D056F4C7E37">\Users\bob\Pictures\wild\metadata.xml</GlobalPath>  
       </Metadata>  
    </Blob>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="0" Length="65536" Hash="19701B8877418393CB3CB567F53EE225" Status="Completed"/>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
          <PageRange Offset="131072" Length="4096" Hash="9BA552E1C3EEAFFC91B42B979900A996" Status="Completed"/>  
       </PageRangeList>  
       <Properties Status="Completed">  
          <Path Hash="38D7AE80653F47F63C0222FEE90EC4E7">\Users\bob\Pictures\animals\koala.jpg.properties</Path>  
       </Properties>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
O log de erros correspondente é mostrado abaixo.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV6965824</DriveId>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
       </PageRangeList>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

 O log de erros a seguir para um trabalho de importação contém um erro sobre um arquivo não encontrado na unidade de importação. Observe que o status dos componentes subsequentes é `Cancelled`.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="FileNotFound">  
    <BlobPath>pictures/animals/koala.jpg</BlobPath>  
    <FilePath>\animals\koala.jpg</FilePath>  
    <Length>30310</Length>  
    <ImportDisposition Status="Cancelled">rename</ImportDisposition>  
    <BlockList>  
      <Block Offset="0" Length="6062" Id="MD5/cAzn4h7VVSWXf696qp5Uaw==" Hash="700CE7E21ED55525977FAF7AAA9E546B" Status="Cancelled" />  
      <Block Offset="6062" Length="6062" Id="MD5/PEnGwYOI8LPLNYdfKr7kAg==" Hash="3C49C6C18388F0B3CB35875F2ABEE402" Status="Cancelled" />  
      <Block Offset="12124" Length="6062" Id="MD5/FG4WxqfZKuUWZ2nGTU2qVA==" Hash="146E16C6A7D92AE5166769C64D4DAA54" Status="Cancelled" />  
      <Block Offset="18186" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
      <Block Offset="24248" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

O log de erros a seguir para um trabalho de exportação indica que o conteúdo do blob foi gravado com êxito na unidade, mas ocorreu um erro ao exportar as propriedades do blob.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C3U</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
    <FilePath>\pictures\wild\canyon.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList />  
    <Properties Status="Failed" />  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
## <a name="next-steps"></a>Próximas etapas
 
* [API REST de Importação/Exportação do Armazenamento](/rest/api/storageimportexport/)
