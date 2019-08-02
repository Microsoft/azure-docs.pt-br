---
title: Problemas usando logs de carregar dados de solução de problemas de disco da caixa de dados do Azure | Microsoft Docs
description: Descreve como usar os logs e solucionar problemas encontrados ao carregar dados para o disco do Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: deaa9a220ee4d765650779b40742225e300ffdb7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807517"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Compreender os logs para solucionar problemas de carregamento de dados no disco do Azure Data Box

Este artigo se aplica ao disco do Microsoft Azure Data Box e descreve os problemas que você vê quando você carrega dados no Azure.

## <a name="about-upload-logs"></a>Sobre como carregar logs

Quando os dados são carregados para o Azure no datacenter, `_error.xml` e `_verbose.xml` arquivos são gerados para cada conta de armazenamento. Esses logs são carregados para a mesma conta de armazenamento que foi usada para carregar dados. 

Os dois os logs estão no mesmo formato e contêm descrições XML dos eventos ocorridos ao copiar os dados do disco para a conta de armazenamento do Azure.

O log detalhado contém informações completas sobre o status da operação de cópia para cada blob ou arquivo, enquanto que o log de erros contém apenas as informações para blobs ou arquivos que encontraram erros durante o carregamento.

O log de erros tem a mesma estrutura que o log detalhado, mas filtra operações bem-sucedidas.

## <a name="download-logs"></a>baixar logs

Execute as seguintes etapas para localizar os logs de upload.

1. Se houver erros ao carregar os dados para o Azure, o portal exibe um caminho para a pasta onde se encontram os logs de diagnóstico.

    ![Link logs no portal](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Vá para **waies**.

    ![erro e logs detalhados](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

Em cada caso, você pode ver os logs de erros e os logs detalhados. Selecione cada log e baixe uma cópia local.

## <a name="sample-upload-logs"></a>Carregar logs de exemplo

Um exemplo da `_verbose.xml` é mostrado abaixo. Nesse caso, o pedido foi concluído com êxito sem erros.

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

Para a mesma ordem, uma amostra do `_error.xml` é mostrado abaixo.

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

Um exemplo da `_error.xml` é mostrada abaixo de onde a ordem em que foi concluída com erros. 

Neste caso, o arquivo de erro tem um `Summary` erros de nível de seção e outra seção que contém todos os arquivos. 

O `Summary` contém o `ValidationErrors` e o `CopyErrors`. Nesse caso, 8 arquivos ou pastas foram carregadas para o Azure e não há erros de validação. Quando os dados foram copiados para a conta de armazenamento do Azure, 5 pastas ou arquivos carregados com êxito. O restante de 3 arquivos ou pastas foram renomeadas, de acordo com as convenções de nomenclatura do contêiner do Azure e carregadas com êxito no Azure.

O status do nível de arquivo estão em `BlobStatus` que descreve as ações tomadas para carregar os blobs. Nesse caso, três contêineres são renomeados porque não cumpre as pastas para o qual os dados foram copiados com as convenções de nomenclatura do Azure para contêineres. Para os blobs carregados nesses contêineres, o nome do novo contêiner, caminho do blob no Azure, o caminho de arquivo inválido original e o tamanho do blob são incluídos.
    
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

| Código do erro | DESCRIÇÃO                   |
|-------------|------------------------------|
|`None` |  Foi concluída com êxito.           |
|`Renamed` | O blob foi renomeado com êxito.   |
|`CompletedWithErrors` | Upload concluído com erros. Os detalhes dos arquivos de erro são incluídos no arquivo de log.  |
|`Corrupted`|CRC calculado durante a ingestão de dados não corresponde ao CRC calculado durante o carregamento.  |  
|`StorageRequestFailed` | Falha na solicitação de armazenamento do Azure.   |     
|`LeasePresent` | Este item é concedido e está sendo usado por outro usuário. |
|`StorageRequestForbidden` |Não foi possível carregar devido a problemas de autenticação. |
|`ManagedDiskCreationTerminalFailure` | Não foi possível carregar como discos gerenciados. Os arquivos estão disponíveis na conta de armazenamento temporário, como blobs de página. Você pode converter manualmente blobs de página em discos gerenciados.  |
|`DiskConversionNotStartedTierInfoMissing` | Depois que o arquivo VHD foi copiado fora as pastas de camada de pré-criados, um disco gerenciado não tiver sido criado. O arquivo é carregado como um blob de páginas para a conta de armazenamento temporário, conforme especificado durante a criação do pedido. Você pode convertê-la manualmente em um disco gerenciado.|
|`InvalidWorkitem` | Não foi possível carregar os dados, pois ele não estiver de acordo com a nomenclatura do Azure e limita as convenções.|
|`InvalidPageBlobUploadAsBlockBlob` | Carregados como blobs de blocos em um contêiner com o prefixo `databoxdisk-invalid-pb-`.|
|`InvalidAzureFileUploadAsBlockBlob` | Carregados como blobs de blocos em um contêiner com o prefixo `databoxdisk-invalid-af`-.|
|`InvalidManagedDiskUploadAsBlockBlob` | Carregados como blobs de blocos em um contêiner com o prefixo `databoxdisk-invalid-md`-.|
|`InvalidManagedDiskUploadAsPageBlob` |Carregados como blobs de página em um contêiner com o prefixo `databoxdisk-invalid-md-`. |
|`MovedToOverflowShare` |Arquivos carregados para um novo compartilhamento de tamanho do compartilhamento original excederam o limite de tamanho máximo do Azure. O novo nome de compartilhamento de arquivo tem o nome do original com o sufixo `-2`.   |
|`MovedToDefaultAzureShare` |Arquivos carregados que não estavam uma parte de qualquer pasta em um compartilhamento de padrão. O nome do compartilhamento começa com `databox-`. |
|`ContainerRenamed` |O contêiner para esses arquivos não estão em conformidade com as convenções de nomenclatura do Azure e é renomeado. O novo nome começa com `databox-` e é sufixado com o hash SHA1 do nome original |
|`ShareRenamed` |O compartilhamento desses arquivos não estão em conformidade com as convenções de nomenclatura do Azure e é renomeado. O novo nome começa com `databox-` e é sufixado com o hash SHA1 do nome do original. |
|`BlobRenamed` |Esses arquivos não estão em conformidade com as convenções de nomenclatura do Azure e foram renomeados. Verifique o `BlobPath` campo para o novo nome. |
|`FileRenamed` |Esses arquivos não estão em conformidade com as convenções de nomenclatura do Azure e foram renomeados. Verifique o `FileStoragePath` campo para o novo nome. |
|`DiskRenamed` |Esses arquivos não estão em conformidade com as convenções de nomenclatura do Azure e foram renomeados. Verifique o `BlobPath` campo para o novo nome. |


## <a name="next-steps"></a>Próximas etapas

- [Abra um tíquete de suporte para problemas de disco Data Box](data-box-disk-contact-microsoft-support.md).
