---
title: Solucionar problemas de carregamentos de dados usando logs
titleSuffix: Azure Data Box Disk
description: Descreve como usar os logs e solucionar problemas encontrados ao carregar dados no Disco do Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 4b53cf607bdf60c785c7324d9ede526a0983b7e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97605268"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Entenda os logs para solucionar problemas de carregamento de dados no Disco do Azure Data Box

Este artigo se aplica a Microsoft Azure Disco do Data Box e descreve os problemas que você vê ao carregar dados no Azure.

## <a name="about-upload-logs"></a>Sobre os logs de carregamento

Quando os dados são carregados no Azure no datacenter e os `_error.xml` `_verbose.xml` arquivos são gerados para cada conta de armazenamento. Esses logs são carregados para a mesma conta de armazenamento que foi usada para carregar dados. 

Ambos os logs estão no mesmo formato e contêm descrições XML dos eventos que ocorreram ao copiar os dados do disco para a conta de armazenamento do Azure.

O log detalhado contém informações completas sobre o status da operação de cópia para cada BLOB ou arquivo, enquanto o log de erros contém apenas as informações de BLOBs ou arquivos que encontraram erros durante o carregamento.

O log de erros tem a mesma estrutura que o log detalhado, mas filtra as operações bem-sucedidas.

## <a name="download-logs"></a>Baixar logs

Execute as etapas a seguir para localizar os logs de carregamento.

1. Se houver erros ao carregar os dados no Azure, o portal exibirá um caminho para a pasta onde os logs de diagnóstico estão localizados.

    ![Vincular a logs no portal](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Vá para **waies**.

    ![logs detalhados e de erro](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

Em cada caso, você vê os logs de erros e os logs detalhados. Selecione cada log e Baixe uma cópia local.

## <a name="sample-upload-logs"></a>Logs de upload de exemplo

Uma amostra do `_verbose.xml` é mostrada abaixo. Nesse caso, o pedido foi concluído com êxito sem erros.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

Para a mesma ordem, um exemplo de `_error.xml` é mostrado abaixo.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

Uma amostra do `_error.xml` é mostrada abaixo onde o pedido foi concluído com erros. 

Nesse caso, o arquivo de erro tem uma `Summary` seção e outra seção que contém todos os erros de nível de arquivo. 

O `Summary` contém o `ValidationErrors` e o `CopyErrors` . Nesse caso, 8 arquivos ou pastas foram carregados no Azure e não houve erros de validação. Quando os dados foram copiados para a conta de armazenamento do Azure, cinco arquivos ou pastas são carregados com êxito. Os três arquivos ou pastas restantes foram renomeados de acordo com as convenções de nomenclatura de contêiner do Azure e, em seguida, foram carregadas com êxito no Azure.

O status de nível de arquivo está em, `BlobStatus` que descreve as ações executadas para carregar os BLOBs. Nesse caso, três contêineres são renomeados porque as pastas nas quais os dados foram copiados não estão em conformidade com as convenções de nomenclatura do Azure para contêineres. Para os BLOBs carregados nesses contêineres, o novo nome do contêiner, o caminho do blob no Azure, o caminho de arquivo original inválido e o tamanho do blob são incluídos.
  
```xml
 <?xml version="1.0" encoding="utf-8"?>
  <DriveLog Version="2018-10-01">
    <DriveId>18041C582D7E</DriveId>
    <Summary>
     <!--Summary for validation and data copy to Azure -->
      <ValidationErrors>
        <None Count="8" />
      </ValidationErrors>
      <CopyErrors>
        <Completed Count="5" Description="No errors encountered" />
        <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
      </CopyErrors>
    </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
      <SizeInBytes>71528448</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Status>CompletedWithErrors</Status>
  </DriveLog>
```

## <a name="data-upload-errors"></a>Erros de upload de dados

Os erros gerados ao carregar os dados no Azure são resumidos na tabela a seguir.

| Código do erro | Descrição                   |
|-------------|------------------------------|
|`None` |  Concluído com êxito.           |
|`Renamed` | O blob foi renomeado com êxito.   |
|`CompletedWithErrors` | Carregamento concluído com erros. Os detalhes dos arquivos em erro são incluídos no arquivo de log.  |
|`Corrupted`|A CRC computada durante a ingestão de dados não corresponde à CRC computada durante o carregamento.  |  
|`StorageRequestFailed` | Falha na solicitação de armazenamento do Azure.   |     
|`LeasePresent` | Este item é concedido e está sendo usado por outro usuário. |
|`StorageRequestForbidden` |Não foi possível carregar devido a problemas de autenticação. |
|`ManagedDiskCreationTerminalFailure` | Não foi possível carregar como discos gerenciados. Os arquivos estão disponíveis na conta de armazenamento de preparo como BLOBs de páginas. Você pode converter manualmente os blobs de página em discos gerenciados.  |
|`DiskConversionNotStartedTierInfoMissing` | Como o arquivo VHD foi copiado fora das pastas da camada criada, um disco gerenciado não foi criado. O arquivo é carregado como blob de páginas para a conta de armazenamento de preparo conforme especificado durante a criação do pedido. Você pode convertê-lo manualmente em um disco gerenciado.|
|`InvalidWorkitem` | Não foi possível carregar os dados, pois eles não estão em conformidade com as convenções de nomenclatura e limites do Azure.|
|`InvalidPageBlobUploadAsBlockBlob` | Carregados como BLOBs de blocos em um contêiner com prefixo `databoxdisk-invalid-pb-` .|
|`InvalidAzureFileUploadAsBlockBlob` | Carregados como BLOBs de blocos em um contêiner com prefixo `databoxdisk-invalid-af` -.|
|`InvalidManagedDiskUploadAsBlockBlob` | Carregados como BLOBs de blocos em um contêiner com prefixo `databoxdisk-invalid-md` -.|
|`InvalidManagedDiskUploadAsPageBlob` |Carregados como BLOBs de páginas em um contêiner com prefixo `databoxdisk-invalid-md-` . |
|`MovedToOverflowShare` |Arquivos carregados em um novo compartilhamento, pois o tamanho do compartilhamento original excedeu o limite máximo de tamanho do Azure. O nome do novo compartilhamento de arquivos tem o nome original com sufixo `-2` .   |
|`MovedToDefaultAzureShare` |Arquivos carregados que não fazem parte de nenhuma pasta para um compartilhamento padrão. O nome do compartilhamento começa com `databox-` . |
|`ContainerRenamed` |O contêiner para esses arquivos não estava em conformidade com as convenções de nomenclatura do Azure e foi renomeado. O novo nome começa com `databox-` e é sufixado com o hash SHA1 do nome original |
|`ShareRenamed` |O compartilhamento desses arquivos não estava em conformidade com as convenções de nomenclatura do Azure e foi renomeado. O novo nome começa com `databox-` e é sufixado com o hash SHA1 do nome original. |
|`BlobRenamed` |Esses arquivos não estavam em conformidade com as convenções de nomenclatura do Azure e foram renomeados. Verifique o `BlobPath` novo nome no campo. |
|`FileRenamed` |Esses arquivos não estavam em conformidade com as convenções de nomenclatura do Azure e foram renomeados. Verifique o `FileStoragePath` novo nome no campo. |
|`DiskRenamed` |Esses arquivos não estavam em conformidade com as convenções de nomenclatura do Azure e foram renomeados. Verifique o `BlobPath` novo nome no campo. |


## <a name="next-steps"></a>Próximas etapas

- [Abra um tíquete de suporte para problemas de disco do data Box](data-box-disk-contact-microsoft-support.md).
