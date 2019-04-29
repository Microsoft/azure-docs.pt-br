---
title: Diagnóstico e recuperação de erro para os trabalhos de Importação/Exportação do Azure | Microsoft Docs
description: Saiba como habilitar o log detalhado para os trabalhos do serviço de Importação/Exportação do Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 306b94fbe23e0ae92dcd59f7a87b7bb58ef7c3b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478803"
---
# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Diagnóstico e recuperação de erro para trabalhos de Importação/Exportação do Azure
Para cada unidade processada, o serviço de Importação/Exportação do Azure cria um log de erros na conta de armazenamento associada. Também é possível habilitar o log detalhado definindo a propriedade `LogLevel` como `Verbose` ao chamar as operações [Put Job](/rest/api/storageimportexport/jobs) ou [Update Job Properties](/rest/api/storageimportexport/jobs).

 Por padrão, os logs são gravados em um contêiner denominado `waimportexport`. Você pode especificar outro nome definindo a propriedade `DiagnosticsPath` ao chamar as operações `Put Job` ou `Update Job Properties`. Os logs são armazenados como blobs de blocos com a seguinte convenção de nomenclatura: `waies/jobname_driveid_timestamp_logtype.xml`.

 É possível recuperar o URI dos logs de um trabalho chamando a operação [Get Job](/rest/api/storageimportexport/jobs). O URI do log detalhado é retornado na propriedade `VerboseLogUri` de cada unidade, enquanto o URI do log de erros é retornado na propriedade `ErrorLogUri`.

É possível usar os dados de log para identificar os problemas a seguir.

## <a name="drive-errors"></a>Erros de unidade

Os seguintes itens são classificados como erros de unidade:

-   Erros ao acessar ou ler o arquivo de manifesto

-   Chaves incorretas do BitLocker

-   Erros de leitura/gravação da unidade

## <a name="blob-errors"></a>Erros de blob

Os seguintes itens são classificados como erros de blob:

-   Nomes de blob incorretos ou conflitantes

-   Arquivos ausentes

-   Blob não encontrado

-   Arquivos truncados (os arquivos no disco são menores que os especificados no manifesto)

-   Conteúdo do arquivo corrompido (para trabalhos de importação, detectado com uma soma de verificação MD5 incompatível)

-   Arquivos de metadados e propriedades de blob corrompidos (detectados com uma soma de verificação MD5 incompatível)

-   Esquema incorreto para os arquivos de propriedades e/ou metadados de blob

Pode haver casos em que algumas partes de um trabalho de importação ou exportação não são concluídas com êxito, enquanto o trabalho geral ainda é concluído. Nesse caso, é possível carregar ou baixar as partes ausentes dos dados pela rede ou criar um novo trabalho para transferir os dados. Consulte a [Referência da Ferramenta de Importação/Exportação do Azure](storage-import-export-tool-how-to-v1.md) para saber como reparar os dados pela rede.

## <a name="next-steps"></a>Próximas etapas

* [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)
