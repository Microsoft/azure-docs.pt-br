---
title: Solução de problemas do Azure Data Box Disk | Microsoft Docs
description: Descreve como solucionar os problemas observados no Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805718"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Usar logs para solucionar problemas de validação no disco do Azure Data Box

Este artigo se aplica ao disco do Microsoft Azure Data Box. O artigo descreve como usar os logs para solucionar os problemas de validação, que você pode ver quando você implanta essa solução.

## <a name="validation-tool-log-files"></a>Arquivos de log da ferramenta de validação

Quando você validar os dados nos discos usando o [ferramenta de validação](data-box-disk-deploy-copy-data.md#validate-data), um *error.xml* é gerado para registrar erros. O arquivo de log está localizado no `Drive:\DataBoxDiskImport\logs` pasta da unidade. Um link para o log de erros é fornecido quando você executar a validação.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Se você executar várias sessões para validação, um log de erro é gerado por sessão.

- Aqui está um exemplo de log de erros quando os dados carregados no `PageBlob` pasta não é de 512 bytes alinhados. Todos os dados carregados para PageBlob devem ser de 512 bytes alinhados, por exemplo, um VHD ou VHDX. Os erros nesse arquivo estão na `<Errors>` e avisos no `<Warnings>`.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Aqui está um exemplo de log de erros quando o nome do contêiner não é válido. A pasta que você cria em `BlockBlob`, `PageBlob`, ou `AzureFile` pastas no disco se torna um contêiner em sua conta de armazenamento do Azure. O nome do contêiner deve seguir a [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Erros de validação de ferramenta

Os erros contidos na *error.xml* com as ações recomendadas correspondentes são resumidos na tabela a seguir.

| Código do erro| DESCRIÇÃO                       | Ações recomendadas               |
|------------|--------------------------|-----------------------------------|
| `None` | Validado com êxito os dados. | Nenhuma ação é necessária. |
| `InvalidXmlCharsInPath` |Não foi possível criar um arquivo de manifesto, como o caminho do arquivo tem caracteres que não são válidos. | Remova esses caracteres para continuar.  |
| `OpenFileForReadFailed`| Não foi possível processar o arquivo. Isso pode ser devido a uma corrupção de sistema de problema ou arquivo de acesso.|Não foi possível ler o arquivo devido a um erro. Os detalhes do erro estão na exceção. |
| `Not512Aligned` | Esse arquivo não está em um formato válido para a pasta PageBlob.| Somente dados de carregamento que é de 512 bytes alinhado à `PageBlob` pasta. Remova o arquivo da pasta PageBlob ou movê-lo para a pasta BlockBlob. Repita a validação.|
| `InvalidBlobPath` | Caminho do arquivo não é mapeado para um caminho de blob válido na nuvem de acordo com as convenções de nomenclatura de BLOBs do Azure.|Siga as diretrizes de nomenclatura do Azure para renomear o caminho do arquivo. |
| `EnumerationError` | Não foi possível enumerar o arquivo para validação. |Pode haver vários motivos para esse erro. Um motivo mais provável é o acesso ao arquivo. |
| `ShareSizeExceeded` | Esse arquivo causado o tamanho do compartilhamento de arquivos do Azure para exceder o limite do Azure de 5 TB.|Reduzir o tamanho dos dados no compartilhamento de forma que ele está em conformidade com o [limites de tamanho do objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação. |
| `AzureFileSizeExceeded` | Tamanho do arquivo excede os limites de tamanho de arquivos do Azure.| Reduzir o tamanho do arquivo ou os dados para que ele está em conformidade com o [limites de tamanho do objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação.|
| `BlockBlobSizeExceeded` | Tamanho do arquivo excede os limites de tamanho do Blob de blocos do Azure. | Reduzir o tamanho do arquivo ou os dados para que ele está em conformidade com o [limites de tamanho do objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação. |
| `ManagedDiskSizeExceeded` | Tamanho do arquivo excede os limites de tamanho de disco gerenciado do Azure. | Reduzir o tamanho do arquivo ou os dados para que ele está em conformidade com o [limites de tamanho do objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação. |
| `PageBlobSizeExceeded` | Tamanho do arquivo excede os limites de tamanho de disco gerenciado do Azure. | Reduzir o tamanho do arquivo ou os dados para que ele está em conformidade com o [limites de tamanho do objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação. |
| `InvalidShareContainerFormat`  |Os nomes de diretório não está em conformidade com as convenções de nomenclatura do Azure para contêineres ou compartilhamentos.         |A primeira pasta criada sob as pastas já existentes no disco se torna um contêiner em sua conta de armazenamento. Esse nome de compartilhamento ou um contêiner não estiver de acordo com as convenções de nomenclatura do Azure. Renomeie o arquivo para que ele está em conformidade com [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repita a validação.   |
| `InvalidBlobNameFormat` | Caminho do arquivo não é mapeado para um caminho de blob válido na nuvem de acordo com as convenções de nomenclatura de BLOBs do Azure.|Renomeie o arquivo para que ele está em conformidade com [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repita a validação. |
| `InvalidFileNameFormat` | Caminho do arquivo não é mapeado para um caminho de arquivo válido na nuvem, de acordo com as convenções de nomenclatura de arquivo do Azure. |Renomeie o arquivo para que ele está em conformidade com [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repita a validação. |
| `InvalidDiskNameFormat` | Caminho do arquivo não é mapeado para um nome de disco válido na nuvem, de acordo com as convenções de nomenclatura de disco gerenciado do Azure. |Renomeie o arquivo para que ele está em conformidade com [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repita a validação.       |
| `NotPartOfFileShare` | O caminho de carregamento de arquivos não é válido. Carregue os arquivos para uma pasta de arquivos do Azure.   | Remova os arquivos no erro e carregar esses arquivos em uma pasta pré-criada. Repita a validação. |
| `NonVhdFileNotSupportedForManagedDisk` | Um arquivo não VHD não pode ser carregado como um disco gerenciado. |Remover os arquivos não-VHD `ManagedDisk` pasta que elas não têm suporte ou mover esses arquivos para um `PageBlob` pasta. Repita a validação. |


## <a name="next-steps"></a>Próximas etapas

- Solucionar problemas [erros de upload de dados](data-box-disk-troubleshoot-upload.md).
