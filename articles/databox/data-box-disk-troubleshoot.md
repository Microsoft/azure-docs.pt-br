---
title: Solução de problemas do Azure Data Box Disk | Microsoft Docs
description: Saiba como usar logs para solucionar problemas de validação que podem surgir ao implantar Disco do Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: fa8a6643f1b7bd60fbf6e5950234e0381666177e
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605217"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Use logs para solucionar problemas de validação no Disco do Azure Data Box

Este artigo se aplica a Microsoft Azure Disco do Data Box. O artigo descreve como usar os logs para solucionar os problemas de validação que você pode ver ao implantar essa solução.

## <a name="validation-tool-log-files"></a>Arquivos de log da ferramenta de validação

Quando você valida os dados nos discos usando a [ferramenta de validação](data-box-disk-deploy-copy-data.md#validate-data), um *error.xml* é gerado para registrar quaisquer erros. O arquivo de log está localizado na  `Drive:\DataBoxDiskImport\logs` pasta da unidade. Um link para o log de erros é fornecido quando você executa a validação.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Se você executar várias sessões para validação, um log de erros será gerado por sessão.

- Aqui está um exemplo do log de erros quando os dados carregados na `PageBlob` pasta não são 512-bytes alinhados. Todos os dados carregados em PageBlob devem ter 512-bytes alinhados, por exemplo, um VHD ou VHDX. Os erros neste arquivo estão nos `<Errors>` avisos e em `<Warnings>` .

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

- Aqui está um exemplo do log de erros quando o nome do contêiner não é válido. A pasta que você cria no `BlockBlob` , `PageBlob` ou `AzureFile` pastas no disco, torna-se um contêiner em sua conta de armazenamento do Azure. O nome do contêiner deve seguir as [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

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

## <a name="validation-tool-errors"></a>Erros da ferramenta de validação

Os erros contidos no *error.xml* com as ações recomendadas correspondentes são resumidos na tabela a seguir.

| Código do erro| Descrição                       | Ações recomendadas               |
|------------|--------------------------|-----------------------------------|
| `None` | Dados validados com êxito. | Nenhuma ação é necessária. |
| `InvalidXmlCharsInPath` |Não foi possível criar um arquivo de manifesto, pois o caminho do arquivo tem caracteres inválidos. | Remova esses caracteres para continuar.  |
| `OpenFileForReadFailed`| Não foi possível processar o arquivo. Isso pode ser devido a um problema de acesso ou corrupção do sistema de arquivos.|Não foi possível ler o arquivo devido a um erro. Os detalhes do erro estão na exceção. |
| `Not512Aligned` | Este arquivo não está em um formato válido para a pasta PageBlob.| Carregue somente os dados que são 512 bytes alinhados à `PageBlob` pasta. Remova o arquivo da pasta PageBlob ou mova-o para a pasta BlockBlob. Repita a validação.|
| `InvalidBlobPath` | O caminho do arquivo não é mapeado para um caminho de blob válido na nuvem de acordo com as convenções de nomenclatura do blob do Azure.|Siga as diretrizes de nomenclatura do Azure para renomear o caminho do arquivo. |
| `EnumerationError` | Não foi possível enumerar o arquivo para validação. |Pode haver várias razões para esse erro. Um motivo mais provável é o acesso ao arquivo. |
| `ShareSizeExceeded` | Esse arquivo fez com que o tamanho do compartilhamento de arquivos do Azure exceda o limite do Azure de 5 TB.|Reduza o tamanho dos dados no compartilhamento para que ele esteja em conformidade com os [limites de tamanho de objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação. |
| `AzureFileSizeExceeded` | O tamanho do arquivo excede os limites de tamanho do arquivo do Azure.| Reduza o tamanho do arquivo ou dos dados para que ele esteja em conformidade com os [limites de tamanho de objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação.|
| `BlockBlobSizeExceeded` | O tamanho do arquivo excede os limites de tamanho do blob de blocos do Azure. | Reduza o tamanho do arquivo ou dos dados para que ele esteja em conformidade com os [limites de tamanho de objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação. |
| `ManagedDiskSizeExceeded` | O tamanho do arquivo excede os limites de tamanho do disco gerenciado do Azure. | Reduza o tamanho do arquivo ou dos dados para que ele esteja em conformidade com os [limites de tamanho de objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação. |
| `PageBlobSizeExceeded` | O tamanho do arquivo excede os limites de tamanho do disco gerenciado do Azure. | Reduza o tamanho do arquivo ou dos dados para que ele esteja em conformidade com os [limites de tamanho de objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação. |
| `InvalidShareContainerFormat`  |Os nomes de diretório não estão em conformidade com as convenções de nomenclatura do Azure para contêineres ou compartilhamentos.         |A primeira pasta criada nas pastas pré-existentes no disco torna-se um contêiner em sua conta de armazenamento. Esse nome de compartilhamento ou contêiner não está de acordo com as convenções de nomenclatura do Azure. Renomeie o arquivo para que ele esteja em conformidade com as [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repita a validação.   |
| `InvalidBlobNameFormat` | O caminho do arquivo não é mapeado para um caminho de blob válido na nuvem de acordo com as convenções de nomenclatura do blob do Azure.|Renomeie o arquivo para que ele esteja em conformidade com as [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repita a validação. |
| `InvalidFileNameFormat` | O caminho do arquivo não é mapeado para um caminho de arquivo válido na nuvem de acordo com as convenções de nomenclatura do arquivo do Azure. |Renomeie o arquivo para que ele esteja em conformidade com as [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repita a validação. |
| `InvalidDiskNameFormat` | O caminho do arquivo não é mapeado para um nome de disco válido na nuvem de acordo com as convenções de nomenclatura do disco gerenciado do Azure. |Renomeie o arquivo para que ele esteja em conformidade com as [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repita a validação.       |
| `NotPartOfFileShare` | O caminho de carregamento dos arquivos não é válido. Carregue os arquivos em uma pasta nos arquivos do Azure.   | Remova os arquivos com erro e carregue esses arquivos em uma pasta criada. Repita a validação. |
| `NonVhdFileNotSupportedForManagedDisk` | Um arquivo não VHD não pode ser carregado como um disco gerenciado. |Remova os arquivos não-VHD da `ManagedDisk` pasta, pois eles não têm suporte ou mova esses arquivos para uma `PageBlob` pasta. Repita a validação. |


## <a name="next-steps"></a>Próximas etapas

- Solucionar [erros de upload de dados](data-box-disk-troubleshoot-upload.md).
