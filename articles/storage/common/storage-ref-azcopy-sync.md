---
title: sincronização de azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy Sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: dc3451a4b46a317dccda0e4292dcb1712b4171f0
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878300"
---
# <a name="azcopy-sync"></a>azcopy sync

Replica o local de origem para o local de destino.

## <a name="synopsis"></a>Sinopse

As horas da última modificação são usadas para comparação. O arquivo será ignorado se a hora da última modificação no destino for mais recente.

Os pares com suporte são:

- < local-> blob do Azure (a autenticação SAS ou OAuth pode ser usada)
- Blob do Azure <-> blob do Azure (a origem deve incluir uma SAS ou está acessível publicamente; a autenticação SAS ou OAuth pode ser usada para o destino)
- Arquivo do Azure <-> arquivo do Azure (a origem deve incluir uma SAS ou pode ser acessado publicamente; A autenticação SAS deve ser usada para o destino)

O comando de sincronização difere do comando de cópia de várias maneiras:

1. Por padrão, o sinalizador recursivo é true e a sincronização copia todos os subdiretórios. Sincronizar somente copiará os arquivos de nível superior dentro de um diretório se o sinalizador recursivo for false.
2. Ao sincronizar entre diretórios virtuais, adicione uma barra à direita no caminho (consulte exemplos) se houver um blob com o mesmo nome de um dos diretórios virtuais.
3. Se o `deleteDestination` sinalizador for definido como true ou prompt, a sincronização excluirá arquivos e blobs no destino que não estão presentes na origem.

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](./storage-use-azcopy-v10.md#transfer-data)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Avançado

Se você não especificar uma extensão de arquivo, o AzCopy detectará automaticamente o tipo de conteúdo dos arquivos ao carregar do disco local, com base na extensão de arquivo ou no conteúdo (se nenhuma extensão for especificada).

A tabela de pesquisa interna é pequena, mas no UNIX, ela é aumentada pelos arquivos MIME. Types do sistema local, se disponíveis em um ou mais desses nomes:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

No Windows, os tipos de MIME são extraídos do registro.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Exemplos

Sincronizar um único arquivo:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

O mesmo que acima, mas também calcula um hash MD5 do conteúdo do arquivo e, em seguida, salva o hash MD5 como a propriedade Content-MD5 do blob. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Sincronize um diretório inteiro, incluindo seus subdiretórios (Observe que recursivo é por padrão em):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

ou

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Sincronizar somente os arquivos dentro de um diretório, mas não os subdiretórios ou os arquivos dentro dos subdiretórios:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Sincronizar um subconjunto de arquivos em um diretório (por exemplo: somente arquivos jpg e PDF, ou se o nome do arquivo for `exactName` ):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

Sincronize um diretório inteiro, mas exclua determinados arquivos do escopo (por exemplo: cada arquivo que inicia com foo ou termina com bar):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

Sincronizar um único blob:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Sincronizar um diretório virtual:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Sincronizar um diretório virtual que tem o mesmo nome de um blob (adicione uma barra à direita no caminho para eliminar a ambiguidade):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Sincronizar um diretório de arquivos do Azure (mesma sintaxe que BLOB):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Se os sinalizadores de inclusão/exclusão forem usados juntos, somente os arquivos correspondentes aos padrões de inclusão serão examinados, mas aqueles que correspondem aos padrões de exclusão seriam sempre ignorados.

## <a name="options"></a>Opções

**--Block-size-MB** float Use esse tamanho de bloco (especificado na MIB) ao carregar no armazenamento do Azure ou baixar do armazenamento do Azure. O padrão é calculado automaticamente com base no tamanho do arquivo. Frações decimais são permitidas (por exemplo: `0.25` ).

--a cadeia de caracteres **de verificação MD5** especifica como os hashes MD5 estritamente devem ser validados durante o download. Essa opção só está disponível durante o download. Os valores disponíveis incluem: `NoCheck` ,, `LogOnly` `FailIfDifferent` , `FailIfDifferentOrMissing` . (padrão `FailIfDifferent` ). (padrão `FailIfDifferent` )

**--delete –** a cadeia de caracteres de destino define se é para excluir arquivos extras do destino que não estão presentes na origem. Pode ser definido como `true` , `false` ou `prompt` . Se definido como `prompt` , será feita uma pergunta ao usuário antes de agendar arquivos e BLOBs para exclusão. (padrão `false` ). (padrão `false` )

**--Exclude-atributos** String (somente Windows) exclui os arquivos cujos atributos correspondem à lista de atributos. Por exemplo: `A;S;R`

**--Exclude-String de caminho** exclui esses caminhos ao comparar a origem com o destino. Essa opção não dá suporte a caracteres curinga (*). Verifica o prefixo de caminho relativo (por exemplo: `myFolder;myFolder/subDirName/file.pdf` ).

**--Exclude-Pattern** cadeia de caracteres excluir arquivos onde o nome corresponde à lista padrão. Por exemplo: `*.jpg;*.pdf;exactName`

**--**    ajuda ajuda para sincronização.

**--include-** a cadeia de caracteres de atributos (somente Windows) inclui apenas arquivos cujos atributos correspondem à lista de atributos. Por exemplo: `A;S;R`

**--include-** a cadeia de caracteres de padrão inclui apenas arquivos em que o nome corresponde à lista de padrões. Por exemplo: `*.jpg;*.pdf;exactName`

**--** cadeia de caracteres de nível de log defina o detalhamento de log para o arquivo de log, níveis disponíveis: `INFO` (todas as solicitações e respostas) `WARNING` (respostas lentas), `ERROR` (somente solicitações com falha) e `NONE` (nenhum log de saída). (padrão `INFO` ). 

**--preserve-SMB-info**   False por padrão. Preserva as informações da propriedade SMB (hora da última gravação, hora de criação, bits de atributo) entre recursos com reconhecimento de SMB (arquivos do Windows e do Azure). Esse sinalizador se aplica a arquivos e pastas, a menos que um filtro somente arquivo seja especificado (por exemplo, include-Pattern). As informações transferidas para pastas são as mesmas para arquivos, exceto para a hora da última gravação que não é preservada para pastas.

**--preserve-SMB-permissões**   False por padrão. Preserva ACLs SMB entre recursos de reconhecimento (arquivos do Windows e do Azure). Esse sinalizador se aplica a arquivos e pastas, a menos que um filtro somente arquivo seja especificado (por exemplo, `include-pattern` ).

**--Put-MD5**     Crie um hash MD5 de cada arquivo e salve o hash como a propriedade Content-MD5 do BLOB ou arquivo de destino. (Por padrão, o hash não é criado.) Disponível somente ao carregar.

**--recursivo** `True` Por padrão, examine os subdiretórios recursivamente ao sincronizar entre diretórios.     (padrão `True` ). 

**--S2S-preserve-acesso-camada**  Preserve a camada de acesso durante a cópia de serviço para serviço. Consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](../blobs/storage-blob-storage-tiers.md) para garantir que a conta de armazenamento de destino dê suporte à configuração da camada de acesso. Nos casos em que não há suporte para a configuração da camada de acesso, use s2sPreserveAccessTier = false para ignorar a cópia da camada de acesso. (padrão `true` ). 

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|
|--Cadeia de caracteres de sufixos confiáveis da Microsoft   |Especifica sufixos de domínio adicionais onde Azure Active Directory tokens de logon podem ser enviados.  O padrão é '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Todos listados aqui são adicionados ao padrão. Por segurança, você só deve colocar Microsoft Azure domínios aqui. Separe várias entradas com ponto e vírgula.|

## <a name="see-also"></a>Confira também

- [azcopy](storage-ref-azcopy.md)
