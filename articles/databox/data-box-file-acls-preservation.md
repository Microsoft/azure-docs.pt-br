---
title: Preservando ACLs de arquivo, atributos e carimbos de data/hora com Azure Data Box
description: ACLs, carimbos de data/hora e atributos preservados durante a cópia de dados via SMB para Azure Data Box. Copiando metadados com ferramentas de cópia de dados do Windows e do Linux.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: e8df77356b6b5b1b40e2abd772e13c2e811413ae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950305"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Preservando ACLs de arquivo, atributos e carimbos de data/hora com Azure Data Box

Azure Data Box permite preservar listas de controle de acesso (ACLs), carimbos de data/hora e atributos de arquivo ao enviar dados para o Azure. Este artigo descreve os metadados que você pode transferir ao copiar dados para Data Box por meio do protocolo SMB para carregá-los nos arquivos do Azure. 

São fornecidas etapas específicas para copiar metadados com ferramentas de cópia de dados do Windows e do Linux. Os metadados não são preservados ao transferir dados para o armazenamento de BLOBs.

Neste artigo, as ACLs, os carimbos de data e hora e os atributos de arquivo que são transferidos são chamados coletivamente de *metadados*.

## <a name="transferred-metadata"></a>Metadados transferidos

Os metadados a seguir são transferidos quando os dados da Data Box são carregados nos arquivos do Azure.

#### <a name="timestamps"></a>Carimbos de data/hora

Os seguintes carimbos de data/hora são transferidos:
- CreationTime
- LastWriteTime

O carimbo de data/hora a seguir não é transferido:
- LastAccessTime
  
#### <a name="file-attributes"></a>Atributos do arquivo

Os atributos de arquivo em arquivos e diretórios são transferidos, salvo indicação em contrário.

Os seguintes atributos de arquivo são transferidos:
- FILE_ATTRIBUTE_READONLY (somente arquivo)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY (somente diretório)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY (somente arquivo)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

Os seguintes atributos de arquivo não são transferidos:
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
Os atributos somente leitura em diretórios não são transferidos.

#### <a name="acls"></a>ACLs

Todas as ACLs de diretórios e arquivos que você copia para seu Data Box sobre SMB são copiadas e transferidas. As transferências incluem DACLs (ACLs condicionais) e SACLs (ACLs de sistema). Para o Linux, somente as ACLs do Windows NT são transferidas.

As ACLs não são transferidas durante cópias de dados no sistema de arquivos de rede (NTS) e quando você usa o serviço de cópia de dados para transferir seus dados. O serviço de cópia de dados lê os dados diretamente de seus compartilhamentos e não pode ler ACLs.

Mesmo que a ferramenta de cópia de dados não copie ACLs, as ACLs padrão em diretórios e arquivos serão transferidas para os arquivos do Azure. As ACLs padrão têm permissões para a conta de administrador interna, a conta do sistema e a conta de usuário de compartilhamento SMB que foi usada para montar e copiar dados no Data Box.

As ACLs contêm descritores de segurança com as seguintes propriedades: ACLs, proprietário, grupo, SACL.

A transferência de ACLs é habilitada por padrão. Talvez você queira desabilitar essa configuração na interface do usuário da Web local no seu Data Box. Para obter mais informações, consulte [usar a interface do usuário da Web local para administrar seu data box e data Box Heavy](./data-box-local-web-ui-admin.md).

> [!NOTE]
> Arquivos com ACLs que contêm cadeias de caracteres de entrada de controle de acesso condicional (ACE) não são copiados. Este é um problema conhecido. Para contornar isso, copie esses arquivos para o compartilhamento de arquivos do Azure manualmente, montando o compartilhamento e, em seguida, usando uma ferramenta de cópia que dá suporte à cópia de ACLs.

## <a name="copying-data-and-metadata"></a>Copiando dados e metadados

Para transferir ACLs, carimbos de data/hora e atributos para seus dados, use os procedimentos a seguir para copiar dados para o Data Box. 

### <a name="windows-data-copy-tool"></a>Ferramenta de cópia de dados do Windows

Para copiar dados para o Data Box via SMB, use uma ferramenta de cópia de arquivo compatível com SMB, como `robocopy` . O comando de exemplo a seguir copia todos os arquivos e diretórios, transferindo metadados junto com os dados.

Ao usar a `/copyall` `/dcopy:DAT` opção ou, verifique se os privilégios de operador de backup necessários não estão desabilitados. Para obter mais informações, consulte [usar a interface do usuário da Web local para administrar seu data box e data Box Heavy](./data-box-local-web-ui-admin.md). 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

where

|Opção |Descrição |
|------------------- | ----- |
|`/copyall` |Copia todos os atributos.|
|`/e`      |Copia subdiretórios, incluindo diretórios vazios.         |
|`/dcopy:DAT`  |Copia dados, atributos e carimbos de data/hora. Observação: a opção/DCOPY: DAT deve ser usada para transferir `CreationTime` em diretórios. |
|`/r:3`    |Especifica três tentativas em cópias com falha.         |
|`/w:60`   |Especifica um tempo de espera de 60 segundos entre as repetições.         |
|`/is`     |Inclui os mesmos arquivos.         |
|`/nfl`    |Não registra nomes de arquivo.         |
|`/ndl`    |Não registra em log os nomes de diretório.        |
|`/np`     |Não exibe o progresso da operação de cópia.         |
|`/MT:32 or 64`  |Usa multithreading, com threads 32 ou 64.           |
|`/fft`    |Reduz a granularidade do carimbo de data/hora de qualquer sistema de arquivos.        |
|`/log+:<LogFile>`  |Acrescenta a saída ao arquivo de log existente.|

Para obter mais informações sobre esses `robocopy` parâmetros, consulte [tutorial: copiar dados para Azure data Box via SMB](./data-box-deploy-copy-data.md)

### <a name="linux-data-copy-tool"></a>Ferramenta de cópia de dados do Linux

A transferência de metadados no Linux é um processo de duas etapas. Primeiro, você copia os dados de origem usando uma ferramenta como `rsync` , que não copia metadados. Depois de copiar os dados, você pode copiar os metadados usando uma ferramenta como `smbcacls` ou `cifsacl` . 

Os comandos de exemplo a seguir fazem a primeira etapa, copiando os dados usando `rsync` . 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>Próximas etapas

- [Copiar dados para o Azure Data Box por SMB](./data-box-deploy-copy-data.md)