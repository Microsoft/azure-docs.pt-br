---
title: Solucionar problemas em seu Azure Data Box, pesada de caixa de dados do Azure | Microsoft Docs
description: Descreve como solucionar problemas encontrados no Azure Data Box e pesada de caixa de dados do Azure ao copiar dados para esses dispositivos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: bc0681a8ea15f736a7b253d6bd7ba2f7928d2a32
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439392"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Solucionar problemas relacionados ao Azure Data Box e pesada de caixa de dados do Azure

Este artigo fornece detalhes para obter informações sobre como solucionar problemas que você pode ver ao usar o Azure Data Box ou pesada de caixa de dados do Azure. O artigo inclui a lista de possíveis erros visto quando dados são copiados para o Data Box ou quando os dados são carregados na caixa de dados.

## <a name="error-classes"></a>Classes de erro

Os erros na caixa de dados e dados de caixa pesada são resumidos da seguinte maneira:

| Categoria de erro *        | DESCRIÇÃO        | Ação recomendada    |
|----------------------------------------------|---------|--------------------------------------|
| Nomes de contêiner ou de compartilhamento | Os nomes de contêiner ou o compartilhamento não seguem as regras de nomenclatura do Azure.  |Baixe as listas de erro. <br> Renomear os contêineres ou compartilhamentos. [Saiba mais](#container-or-share-name-errors).  |
| Limite de tamanho do contêiner ou de compartilhamento | O total de dados em contêineres ou compartilhamentos excede o limite do Azure.   |Baixe as listas de erro. <br> Reduza os dados geral no contêiner ou compartilhamento. [Saiba mais](#container-or-share-size-limit-errors).|
| Limite de tamanho de arquivo ou um objeto | O objeto ou arquivos em compartilhamentos ou contêineres excede o limite do Azure.|Baixe as listas de erro. <br> Reduza o tamanho do arquivo no contêiner ou compartilhamento. [Saiba mais](#object-or-file-size-limit-errors). |    
| Tipo de dados ou arquivo | Não há suporte para o formato de dados ou o tipo de arquivo. |Baixe as listas de erro. <br> Para blobs de página ou discos gerenciados, verifique se que os dados são de 512 bytes alinhado e copiados para as pastas criadas previamente. [Saiba mais](#data-or-file-type-errors). |
| Erros de blob ou arquivo não-crítico  | Os nomes de blob ou arquivo não seguem as regras de nomenclatura do Azure ou o tipo de arquivo não é suportado. | Esses BLOBs ou arquivos não podem ser copiados ou os nomes podem ser alterados. [Saiba como corrigir esses erros](#non-critical-blob-or-file-errors). |

\* Categorias de quatro erro primeiras são erros críticos e devem ser corrigidas antes de prosseguir para a preparação para o envio.


## <a name="container-or-share-name-errors"></a>Erros de nome de contêiner ou de compartilhamento

Esses são erros relacionados aos nomes de contêiner e compartilhamento.

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Descrição do erro:** O nome do compartilhamento ou do contêiner deve ter entre 3 e 63 caracteres. 

**Resolução sugerida:** A pasta sob o share(SMB/NFS) Data Box ou pesada de caixa de dados ao qual você copiou os dados se torna um contêiner do Azure em sua conta de armazenamento. 

- Sobre o **conectar e copiar** página da web local do dispositivo da interface do usuário, download e examine os nomes de arquivos de erros para identificar a pasta com problemas.
- Altere o nome da pasta no compartilhamento de caixa de dados ou dados caixa pesada para certificar-se de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes podem ter apenas letras, números e hifens.
    - Os nomes não podem começar ou terminar com hifens.
    - Os nomes não podem ter hifens consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para obter mais informações, consulte as convenções de nomenclatura do Azure para [nomes de contêiner](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de compartilhamento](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="errorcontainerorsharenamealphanumericdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Descrição do erro:** O nome do compartilhamento ou do contêiner deve consistir apenas em letras, números ou hifens.

**Resolução sugerida:** A pasta sob o share(SMB/NFS) Data Box ou pesada de caixa de dados ao qual você copiou os dados se torna um contêiner do Azure em sua conta de armazenamento. 

- Sobre o **conectar e copiar** página da web local do dispositivo da interface do usuário, download e examine os nomes de arquivos de erros para identificar a pasta com problemas.
- Altere o nome da pasta no compartilhamento de caixa de dados ou dados caixa pesada para certificar-se de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes podem ter apenas letras, números e hifens.
    - Os nomes não podem começar ou terminar com hifens.
    - Os nomes não podem ter hifens consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para obter mais informações, consulte as convenções de nomenclatura do Azure para [nomes de contêiner](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de compartilhamento](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenameimproperdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Descrição do erro:** Os nomes de contêiner e nomes de compartilhamento não é possível iniciar ou terminar com hifens e não pode ter hifens consecutivos.

**Resolução sugerida:** A pasta sob o share(SMB/NFS) Data Box ou pesada de caixa de dados ao qual você copiou os dados se torna um contêiner do Azure em sua conta de armazenamento. 

- Sobre o **conectar e copiar** página da web local do dispositivo da interface do usuário, download e examine os nomes de arquivos de erros para identificar a pasta com problemas.
- Altere o nome da pasta no compartilhamento de caixa de dados ou dados caixa pesada para certificar-se de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes podem ter apenas letras, números e hifens.
    - Os nomes não podem começar ou terminar com hifens.
    - Os nomes não podem ter hifens consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para obter mais informações, consulte as convenções de nomenclatura do Azure para [nomes de contêiner](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de compartilhamento](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

## <a name="container-or-share-size-limit-errors"></a>Erros de limite de tamanho de contêiner ou de compartilhamento

Esses são erros relacionados a dados que excedem o tamanho dos dados permitido em um contêiner ou um compartilhamento.

### <a name="errorcontainerorsharecapacityexceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Descrição do erro:** Compartilhamento de arquivos do Azure limita a um compartilhamento de 5 TB de dados. Esse limite foi excedido para alguns compartilhamentos.

**Resolução sugerida:** Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.

Identifique as pastas que apresentam esse problema dos logs de erro e certifique-se de que os arquivos nessa pasta são abaixo de 5 TB.


## <a name="object-or-file-size-limit-errors"></a>Erros de limite de tamanho de arquivo ou um objeto

Esses são erros relacionados a dados que excedem o tamanho máximo do objeto ou o arquivo que é permitido no Azure. 

### <a name="errorbloborfilesizelimit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Descrição do erro:** O tamanho do arquivo excede o tamanho máximo do arquivo para upload.

**Resolução sugerida:** O blob ou o tamanho dos arquivos excede o limite máximo permitido para carregamento.

- Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.
- Certifique-se de que os tamanhos de blob e arquivo não excedam os limites de tamanho do objeto do Azure.

## <a name="data-or-file-type-errors"></a>Erros de tipo de dados ou arquivo

Esses são erros relacionados ao tipo de arquivo sem suporte ou tipo de dados encontrado no contêiner ou compartilhamento. 

### <a name="errorbloborfilesizealignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Descrição do erro:** O blob ou arquivo está alinhado incorretamente.

**Resolução sugerida:** O compartilhamento de blob de página na caixa de dados ou dados caixa pesada apenas dá suporte a arquivos que são 512 bytes alinhado (por exemplo, VHD/VHDX). Todos os dados copiados para o compartilhamento de blob de página são carregados no Azure como blobs de página.

Remova todos os dados não-VHD/VHDX do compartilhamento de blob de página. Você pode usar compartilhamentos de blob de blocos ou os arquivos do Azure para dados genéricos.

Para obter mais informações, consulte [blobs de página de visão geral de](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="errorbloborfiletypeunsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Descrição do erro:** Um tipo de arquivo sem suporte está presente em um compartilhamento de disco gerenciado. Somente VHDs fixos são permitidos.

**Resolução sugerida:**

- Certifique-se de que você só pode carregar os VHDs fixos para criar discos gerenciados.
- Arquivos VHDX ou **dinâmica** e **diferenciais** VHDs não têm suporte.

### <a name="errordirectorydisallowedfortype"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Descrição do erro:** Um diretório não é permitido em qualquer uma das pastas já existentes para os discos gerenciados. Somente VHDs fixos são permitidos nessas pastas.

**Resolução sugerida:** Para discos gerenciados, dentro de cada compartilhamento, as três pastas a seguir são criadas que correspondem aos contêineres na conta de armazenamento: SSD Premium, padrão HDD e SSD Standard. Essas pastas correspondem à camada de desempenho para o disco gerenciado.

- Certifique-se de que você copie seus dados de blob de página (VHDs) em uma dessas pastas existentes.
- Uma pasta ou diretório não é permitido nessas pastas existentes. Remova todas as pastas que você criou dentro das pastas já existentes.

Para obter mais informações, consulte [cópia em discos gerenciados](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparsepointerror"></a>REPARSE_POINT_ERROR

**Descrição do erro:** Links simbólicos não são permitidos em Linux. 

**Resolução sugerida:** Os links simbólicos são geralmente links, pipes e outros esses arquivos. Remover os links, ou resolver os links e copiar os dados.


## <a name="non-critical-blob-or-file-errors"></a>Erros de blob ou arquivo não-crítico

Todos os erros que são vistos durante a cópia de dados são resumidos nas seções a seguir.

### <a name="errorbloborfilenamecharactercontrol"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Descrição do erro:** Os nomes de blob ou arquivo contêm caracteres de controle sem suporte.

**Resolução sugerida:** Os blobs ou os arquivos que você copiou contêm nomes com caracteres sem suporte.

Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.
Remova ou renomeie os arquivos para remover caracteres sem suporte.

Para obter mais informações, consulte as convenções de nomenclatura do Azure para [nomes de blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="errorbloborfilenamecharacterillegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Descrição do erro:** Os nomes de blob ou arquivo contêm caracteres inválidos.

**Resolução sugerida:** Os blobs ou os arquivos que você copiou contêm nomes com caracteres sem suporte.

Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.
Remova ou renomeie os arquivos para remover caracteres sem suporte.

Para obter mais informações, consulte as convenções de nomenclatura do Azure para [nomes de blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenameending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Descrição do erro:** Os nomes de blob ou arquivo estão terminando com caracteres inválidos.

**Resolução sugerida:** Os blobs ou os arquivos que você copiou contêm nomes com caracteres sem suporte.

Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.
Remova ou renomeie os arquivos para remover caracteres sem suporte.

Para obter mais informações, consulte as convenções de nomenclatura do Azure para [nomes de blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenamesegmentcount"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Descrição do erro:** O nome de arquivo ou blob contém muitos segmentos de caminho.

**Resolução sugerida:** Os blobs ou os arquivos que você copiou excederem o número máximo de segmentos de caminho. Um segmento de caminho é a cadeia de caracteres entre os caracteres delimitadores consecutivos, por exemplo, a barra invertida /.

- Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.
- Certifique-se de que o [nomes de blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) estão em conformidade com as convenções de nomenclatura do Azure.

### <a name="errorbloborfilenameaggregatelength"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Descrição do erro:** O nome de blob ou do arquivo é muito longo.

**Resolução sugerida:** Os nomes de arquivo ou blob excederem o comprimento máximo.

- Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.
- O nome do blob não deve exceder 1.024 caracteres.
- Remova ou renomeie os arquivos ou um blob, para que os nomes não excederem 1.024 caracteres.

Para obter mais informações, consulte as convenções de nomenclatura do Azure para nomes de arquivo e nomes de blob.

### <a name="errorbloborfilenamecomponentlength"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Descrição do erro:** Um dos segmentos no nome do blob ou do arquivo é muito longo.

**Resolução sugerida:** Um dos segmentos de caminho no nome do blob ou arquivo excede o número máximo de caracteres. Um segmento de caminho é a cadeia de caracteres entre os caracteres delimitadores consecutivos, por exemplo, a barra invertida /.

- Sobre o **conectar e copiar** página da web local da interface do usuário, baixe e examine os arquivos de erro.
- Certifique-se de que o [nomes de blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) estão em conformidade com as convenções de nomenclatura do Azure.


### <a name="errorcontainerorsharenamedisallowedfortype"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Descrição do erro:** Os nomes de contêiner inadequada são especificados para os compartilhamentos de disco gerenciado.

**Resolução sugerida:** Para discos gerenciados, dentro de cada compartilhamento, as seguintes pastas são criadas que correspondem aos contêineres na conta de armazenamento: SSD Premium, padrão HDD e SSD Standard. Essas pastas correspondem à camada de desempenho para o disco gerenciado.

- Certifique-se de que você copie seus dados de blob de página (VHDs) em uma dessas pastas existentes. Somente os dados de um desses contêineres existentes são carregados no Azure.
- Qualquer outra pasta que é criada no mesmo nível como SSD Premium, padrão HDD e SSD Standard não corresponde a um nível de desempenho válido e não pode ser usada.
- Remova arquivos ou pastas criadas fora de níveis de desempenho.

Para obter mais informações, consulte [cópia em discos gerenciados](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [requisitos de sistema de armazenamento de Blob de caixa de dados](data-box-system-requirements-rest.md).
