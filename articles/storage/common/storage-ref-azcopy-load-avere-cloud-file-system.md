---
title: azcopy carregar CLFS | Microsoft Docs
titleSuffix: Azure Storage
description: Este artigo fornece informações de referência para o comando azcopy Load CLFS.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b36ea25180c31fef199aaacb10e46b3caa20f807
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878368"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Transfere dados locais para um contêiner e os armazena no formato CLFS (avere Cloud FileSystem) da Microsoft.

## <a name="synopsis"></a>Sinopse

O comando carregar copia dados em contêineres de armazenamento de BLOBs do Azure e, em seguida, armazena esses dados no formato CLFS (avere Cloud FileSystem) da Microsoft. O formato de CLFS proprietário é usado pelo cache do HPC do Azure e avere vFXT para produtos do Azure.

Para aproveitar esse comando, instale a extensão necessária via: pip3 install clfsload ~ = 1.0.23. Certifique-se de que CLFSLoad.py está em seu caminho. Para obter mais informações sobre esta etapa, visite [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) .

Esse comando é uma opção simples para mover os dados existentes para o armazenamento em nuvem para uso com produtos específicos de cache de computação de alto desempenho da Microsoft. 

Como esses produtos usam um formato de sistema de arquivos de nuvem proprietário para gerenciar dados, esses dados não podem ser carregados por meio do comando de cópia nativa. 

Em vez disso, os dados devem ser carregados por meio do próprio produto de cache ou por meio desse comando de carregamento, que usa o formato proprietário correto.
Esse comando permite transferir dados sem usar o cache. Por exemplo, para preencher previamente o armazenamento ou para adicionar arquivos a um conjunto de trabalho sem aumentar a carga do cache.

O destino é um contêiner de armazenamento do Azure vazio. Quando a transferência for concluída, o contêiner de destino poderá ser usado com uma instância de cache do HPC do Azure ou avere vFXT para o cluster do Azure.

> [!NOTE] 
> Esta é uma versão de visualização do comando carregar. Informe quaisquer problemas no repositório GitHub do AzCopy.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](./storage-use-azcopy-v10.md#transfer-data)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

Carregue um diretório inteiro em um contêiner com uma SAS no formato CLFS:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Opções

**--cadeia de caracteres de tipo de compactação** especifique o tipo de compactação a ser usado para as transferências. Os valores disponíveis são: `DISABLED` , `LZ4` . (padrão `LZ4` )

**--**    ajuda ajuda para o `azcopy load clfs` comando.

**--** cadeia de caracteres de nível de log defina o detalhamento de log para o arquivo de log, níveis disponíveis: `DEBUG` , `INFO` , `WARNING` , `ERROR` . (padrão `INFO` )

**--Max-erros** UInt32 especifique o número máximo de falhas de transferência a tolerar. Se ocorrerem erros suficientes, pare o trabalho imediatamente.

**--nova sessão**   Inicie um novo trabalho em vez de continuar um existente cujas informações de rastreamento sejam mantidas em `--state-path` . (padrão true)

**--preserve-links físicos**    Preservar relações de vínculo físico.

**--State-path** String caminho necessário para um diretório local para acompanhamento do estado do trabalho. O caminho deve apontar para um diretório existente a fim de retomar um trabalho. Ele deve estar vazio para um novo trabalho.

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps float|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|
|--Cadeia de caracteres de sufixos confiáveis da Microsoft   | Especifica sufixos de domínio adicionais onde Azure Active Directory tokens de logon podem ser enviados.  O padrão é '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Todos listados aqui são adicionados ao padrão. Por segurança, você só deve colocar Microsoft Azure domínios aqui. Separe várias entradas com ponto e vírgula.|

## <a name="see-also"></a>Confira também

- [azcopy](storage-ref-azcopy.md)
