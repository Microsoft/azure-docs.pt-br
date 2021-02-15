---
title: cópia de azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy Copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c4e85195ace0a24aa11d4a03b8f429f2714399b0
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879149"
---
# <a name="azcopy-copy"></a>azcopy copy

Copia os dados de origem para um local de destino.

## <a name="synopsis"></a>Sinopse

Copia os dados de origem para um local de destino. As instruções com suporte são:

  - < local-> blob do Azure (autenticação SAS ou OAuth)
  - < locais-> arquivos do Azure (autenticação SAS de compartilhamento/diretório)
  - < local-> Azure Data Lake Storage Gen 2 (SAS, OAuth ou autenticação de chave compartilhada)
  - Blob do Azure (SAS ou público)-> blob do Azure (autenticação SAS ou OAuth)
  - Blob do Azure (SAS ou público)-> arquivos do Azure (SAS)
  - Arquivos do Azure (SAS)-> arquivos do Azure (SAS)
  - Arquivos do Azure (SAS)-> blob do Azure (autenticação SAS ou OAuth)
  - Amazon Web Services (AWS) S3 (chave de acesso)-> blob de blocos do Azure (autenticação SAS ou OAuth)

Para obter mais informações, consulte a seção exemplos deste artigo.

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](./storage-use-azcopy-v10.md#transfer-data)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Avançado

O AzCopy detecta automaticamente o tipo de conteúdo dos arquivos quando você os carrega do disco local. AzCopy detecta o tipo de conteúdo com base na extensão de arquivo ou no conteúdo (se nenhuma extensão for especificada).

A tabela de pesquisa interna é pequena, mas no UNIX, ela é aumentada pelos arquivos do sistema local `mime.types` se estiverem disponíveis em um ou mais desses nomes:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

No Windows, os tipos de MIME são extraídos do registro. Esse recurso pode ser desativado com a ajuda de um sinalizador. Para obter mais informações, consulte a seção sinalizador deste artigo.

Se você definir uma variável de ambiente usando a linha de comando, essa variável será legível no seu histórico de linha de comando. Considere limpar as variáveis que contêm credenciais do seu histórico de linha de comando. Para impedir que as variáveis apareçam no seu histórico, você pode usar um script para solicitar ao usuário suas credenciais e definir a variável de ambiente.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exemplos

Carregar um único arquivo usando a autenticação OAuth. Se você ainda não fez logon no AzCopy, execute o `azcopy login` comando antes de executar o comando a seguir.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
O mesmo que acima, mas desta vez, também calcula o hash MD5 do conteúdo do arquivo e o salva como a propriedade Content-MD5 do blob:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Carregar um único arquivo usando um token SAS:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Carregar um único arquivo usando um token e um pipe SAS (somente blobs de blocos):
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

Carregar um diretório inteiro usando um token SAS:
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

ou

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

Carregue um conjunto de arquivos usando um token SAS e caracteres curinga (*):
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Carregar arquivos e diretórios usando um token SAS e caracteres curinga (*):

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Carregue arquivos e diretórios na conta de armazenamento do Azure e defina as marcas codificadas da cadeia de caracteres de consulta no BLOB. 

- Para definir as marcas {Key = "bla bla", Val = "foo"} e {Key = "bla bla 2", Val = "bar"}, use a seguinte sintaxe: `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- Chaves e valores são codificados em URL e os pares chave-valor são separados por um e comercial (' & ')

- Ao definir marcas nos BLOBs, há permissões adicionais (' T' para marcas) em SAS sem a qual o serviço dará erro de autorização.

Baixe um único arquivo usando a autenticação OAuth. Se você ainda não fez logon no AzCopy, execute o `azcopy login` comando antes de executar o comando a seguir.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Baixar um único arquivo usando um token SAS:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Baixar um único arquivo usando um token SAS e, em seguida, canalizando a saída para um arquivo (somente blobs de blocos):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Baixe um diretório inteiro usando um token SAS:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

Uma observação sobre como usar um caractere curinga (*) em URLs:

Há apenas duas maneiras suportadas de usar um caractere curinga em uma URL. 

- Você pode usar um logo após a barra final (/) de uma URL. Esse uso do caractere curinga copia todos os arquivos em um diretório diretamente para o destino, sem colocá-los em um subdiretório. 

- Você também pode um caractere curinga no nome de um contêiner, desde que a URL se refere apenas a um contêiner e não a um blob. Você pode usar essa abordagem para obter arquivos de um subconjunto de contêineres. 

Baixe o conteúdo de um diretório sem copiar o próprio diretório recipiente.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Baixe uma conta de armazenamento inteira.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Baixe um subconjunto de contêineres em uma conta de armazenamento usando um símbolo curinga (*) no nome do contêiner.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Copiar um único blob para outro blob usando um token SAS.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copie um único blob para outro blob usando um token SAS e um token de autenticação. Você precisa usar um token SAS no final da URL da conta de origem, mas a conta de destino não precisará de uma se você fizer logon no AzCopy usando o `azcopy login` comando. 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Copie um diretório virtual de BLOB para outro usando um token SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Copie todos os contêineres de BLOB, diretórios e blobs da conta de armazenamento para outra usando um token SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copie um único objeto para o armazenamento de blobs de Amazon Web Services (AWS) S3 usando uma chave de acesso e um token SAS. Primeiro, defina a variável de ambiente `AWS_ACCESS_KEY_ID` e `AWS_SECRET_ACCESS_KEY` para a origem S3 AWS.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copie um diretório inteiro para o armazenamento de BLOBs do AWS S3 usando uma chave de acesso e um token SAS. Primeiro, defina a variável de ambiente `AWS_ACCESS_KEY_ID` e `AWS_SECRET_ACCESS_KEY` para a origem S3 AWS.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  Consulte https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html para entender melhor o espaço reservado [pasta].

Copie todos os buckets para o armazenamento de blobs de Amazon Web Services (AWS) usando uma chave de acesso e um token SAS. Primeiro, defina a variável de ambiente `AWS_ACCESS_KEY_ID` e `AWS_SECRET_ACCESS_KEY` para a origem S3 AWS.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copie todos os buckets para o armazenamento de blobs de uma região Amazon Web Services (AWS) usando uma chave de acesso e um token SAS. Primeiro, defina a variável de ambiente `AWS_ACCESS_KEY_ID` e `AWS_SECRET_ACCESS_KEY` para a origem S3 AWS.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copie um subconjunto de buckets usando um símbolo curinga (*) no nome do Bucket. Como nos exemplos anteriores, você precisará de uma chave de acesso e um token SAS. Certifique-se de definir a variável de ambiente `AWS_ACCESS_KEY_ID` e `AWS_SECRET_ACCESS_KEY` para a origem S3 AWS.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Transfira arquivos e diretórios para a conta de armazenamento do Azure e defina as marcas codificadas de cadeia de caracteres de consulta no BLOB. 

- Para definir as marcas {Key = "bla bla", Val = "foo"} e {Key = "bla bla 2", Val = "bar"}, use a seguinte sintaxe: `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- Chaves e valores são codificados em URL e os pares chave-valor são separados por um e comercial (' & ')
    
- Ao definir marcas nos BLOBs, há permissões adicionais (' T' para marcas) em SAS sem a qual o serviço dará erro de autorização.

## <a name="options"></a>Opções

**--backup** Ativa o Windows ' SeBackupPrivilege para carregamentos ou SeRestorePrivilege para downloads, para permitir que o AzCopy Veja e Leia todos os arquivos, independentemente de suas permissões do sistema de arquivos e restaure todas as permissões. Requer que a conta que executa o AzCopy já tenha essas permissões (por exemplo, tenha direitos de administrador ou seja um membro do `Backup Operators` grupo). Esse sinalizador ativa os privilégios que a conta já tem.

**--as marcas de blob** definem as marcas em BLOBs para categorizar os dados em sua conta de armazenamento.

**--blob-Type** cadeia de caracteres define o tipo de blob no destino. Isso é usado para carregar BLOBs e ao copiar entre contas (padrão `Detect` ). Os valores válidos incluem `Detect`, `BlockBlob`, `PageBlob` e `AppendBlob`. Ao copiar entre contas, um valor de `Detect` faz com que o AzCopy use o tipo de blob de origem para determinar o tipo do blob de destino. Ao carregar um arquivo, `Detect` determina se o arquivo é um VHD ou um arquivo VHDX com base na extensão de arquivo. Se o arquivo for ether a um VHD ou arquivo VHDX, AzCopy tratará o arquivo como um blob de páginas. (padrão "detectar")

**--Block-blob-camada** carregar blob de blocos para o armazenamento do Azure usando esta camada de BLOB. (padrão "None")

**--Block-size-MB** float Use esse tamanho de bloco (especificado na MIB) ao carregar no armazenamento do Azure e baixar do armazenamento do Azure. O valor padrão é calculado automaticamente com base no tamanho do arquivo. Frações decimais são permitidas (por exemplo: 0,25).

**--Cache-Control** String defina o cabeçalho Cache-Control. Retornado no download.

**--comprimento da verificação** Verifique o comprimento de um arquivo no destino após a transferência. Se houver uma incompatibilidade entre a origem e o destino, a transferência será marcada como com falha. (o valor padrão é `true` )

--a cadeia de caracteres **de verificação MD5** especifica como os hashes MD5 estritamente devem ser validados durante o download. Disponível somente ao baixar. Opções disponíveis: `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` . (padrão `FailIfDifferent` ) (padrão "FailIfDifferent")

**--** cadeia de caracteres de disposição de conteúdo defina o cabeçalho de disposição de conteúdo. Retornado no download.

**--** cadeia de codificação de conteúdo defina o cabeçalho Content-Encoding. Retornado no download.

**--** cadeia de caracteres de linguagem de conteúdo defina o cabeçalho de linguagem de conteúdo. Retornado no download.

**--Content-Type** String especifica o tipo de conteúdo do arquivo. Implica no tipo não-palpite-MIME. Retornado no download.

**--descompactar** Descompacte automaticamente os arquivos durante o download, se sua codificação de conteúdo indicar que eles estão compactados. Os valores de codificação de conteúdo com suporte são `gzip` e `deflate` . As extensões de arquivo do `.gz` / `.gzip` ou `.zz` não são necessárias, mas serão removidas se estiverem presentes.

**--Exclude-atributos** String (somente Windows) exclui os arquivos cujos atributos correspondem à lista de atributos. Por exemplo: A; & D

**--Exclude-blob-tipo** de cadeia de caracteres especifica opcionalmente o tipo de BLOB ( `BlockBlob` /  `PageBlob` /  `AppendBlob` ) a ser excluído ao copiar BLOBs do contêiner ou da conta. O uso desse sinalizador não é aplicável para copiar dados de serviço não Azure para o serviço. Mais de um blob deve ser separado por `;` . 

**--Exclude-cadeia de** caracteres de caminho exclua esses caminhos ao copiar. Essa opção não dá suporte a caracteres curinga (*). Verifica o prefixo de caminho relativo (por exemplo: `myFolder;myFolder/subDirName/file.pdf` ). Quando usado em combinação com passagem de conta, os caminhos não incluem o nome do contêiner.

**--a cadeia de caracteres de exclusão-padrão** exclui esses arquivos ao copiar. Essa opção dá suporte a caracteres curinga (*).

**--follow-symlinks**  Siga links simbólicos ao carregar do sistema de arquivos local.

**--Force-If-somente leitura** Ao substituir um arquivo existente em arquivos do Windows ou do Azure, force a substituição para funcionar mesmo que o arquivo existente tenha seu atributo somente leitura definido.

**--a cadeia de caracteres de-para** especifica opcionalmente a combinação de destino de origem. Por exemplo: `LocalBlob` , `BlobLocal` , `LocalBlobFS` .

**--**  ajuda ajuda para cópia.

--a cadeia de caracteres **include-After** inclui apenas os arquivos modificados em ou após a data/hora determinada. O valor deve estar no formato ISO8601. Se nenhum fuso horário for especificado, o valor será considerado no fuso horário local do computador que executa o AzCopy. por exemplo, `2020-08-19T15:04:00Z` para uma hora UTC ou `2020-08-19` para meia-noite (00:00) no fuso horário local. Como em AzCopy 10,5, esse sinalizador se aplica somente a arquivos, não a pastas, portanto, as propriedades de pasta não serão copiadas ao usar esse sinalizador com `--preserve-smb-info` ou `--preserve-smb-permissions` .

 --a cadeia de caracteres **include-before** inclui apenas os arquivos modificados antes ou na data/hora determinada. O valor deve estar no formato ISO8601. Se nenhum fuso horário for especificado, o valor será considerado no fuso horário local do computador que executa o AzCopy. Por ex.: `2020-08-19T15:04:00Z` por uma hora UTC ou `2020-08-19` para meia-noite (00:00) no fuso horário local. A partir do AzCopy 10,7, esse sinalizador se aplica somente a arquivos, não a pastas, portanto, as propriedades da pasta não serão copiadas ao usar esse sinalizador com `--preserve-smb-info` ou `--preserve-smb-permissions` .

**--include-** a cadeia de caracteres de atributos (somente Windows) inclui arquivos cujos atributos correspondem à lista de atributos. Por exemplo: A; & D

--a cadeia de caracteres **include-path** inclui apenas esses caminhos ao copiar. Essa opção não dá suporte a caracteres curinga (*). Verifica o prefixo de caminho relativo (por exemplo: `myFolder;myFolder/subDirName/file.pdf` ).

**--include-a cadeia de caracteres de padrão** inclui apenas esses arquivos ao copiar. Essa opção dá suporte a caracteres curinga (*). Separe arquivos usando um `;` .

**--a cadeia de caracteres da lista de versões** especifica um arquivo onde cada ID de versão é listada em uma linha separada. Verifique se a origem deve apontar para um único BLOB e se todas as IDs de versão especificadas no arquivo usando esse sinalizador devem pertencer somente ao blob de origem. O AzCopy baixará as versões especificadas na pasta de destino fornecida. Para obter mais informações, consulte [baixar versões anteriores de um blob](./storage-use-azcopy-v10.md#transfer-data).

**--** cadeia de caracteres de nível de log defina o detalhamento de log para o arquivo de log, níveis disponíveis: informações (todas as solicitações/respostas), aviso (respostas lentas), erro (somente solicitações com falha) e nenhum (nenhum log de saída). (padrão `INFO` ). 

**--** carregamento da cadeia de caracteres de metadados para o armazenamento do Azure com esses pares de chave-valor como metadados.

**--no-palpite-tipo MIME**  Impede que o AzCopy detecte o tipo de conteúdo com base na extensão ou no conteúdo do arquivo.

**--substituir** cadeia de caracteres substituirá os arquivos conflitantes e os BLOBs no destino se esse sinalizador for definido como true. (padrão `true` ) Os valores possíveis incluem `true` , `false` , `prompt` e `ifSourceNewer` . Para destinos que dão suporte a pastas, as propriedades de nível de pasta em conflito serão substituídas nesse sinalizador `true` ou se uma resposta positiva for fornecida para o prompt. (padrão "true")

**--Page-blob-camada – blob de** página de carregamento de cadeia de caracteres para o armazenamento do Azure usando essa camada de BLOB. (padrão `None` ). (padrão "None")

**--preservar-último-modificado-hora**  Disponível somente quando o destino é sistema de arquivos.

**--preserve-proprietário**    Só tem efeito em downloads e somente quando `--preserve-smb-permissions` é usado. Se true (o padrão), o proprietário e o grupo do arquivo serão preservados nos downloads. Se definido como false, o `--preserve-smb-permissions` ainda preservará ACLs, mas o proprietário e o grupo serão baseados no usuário que está executando AzCopy (padrão true)

**--preserve-SMB-info**   False por padrão. Preserva as informações da propriedade SMB (hora da última gravação, hora de criação, bits de atributo) entre recursos com reconhecimento de SMB (arquivos do Windows e do Azure). Somente os bits de atributo com suporte dos arquivos do Azure serão transferidos; quaisquer outras serão ignoradas. Esse sinalizador se aplica a arquivos e pastas, a menos que um filtro somente arquivo seja especificado (por exemplo, include-Pattern). As informações transferidas para pastas são as mesmas para arquivos, exceto para a hora da última gravação que nunca é preservada para pastas.

**--preserve-SMB-permissões**   False por padrão. Preserva ACLs SMB entre recursos de reconhecimento (arquivos do Windows e do Azure). Para downloads, você também precisará do `--backup` sinalizador para restaurar as permissões em que o novo proprietário não será o usuário que está executando o AzCopy. Esse sinalizador se aplica a arquivos e pastas, a menos que um filtro somente arquivo seja especificado (por exemplo, `include-pattern` ).

**--Put-MD5**    Crie um hash MD5 de cada arquivo e salve o hash como a propriedade Content-MD5 do BLOB ou arquivo de destino. (Por padrão, o hash não é criado.) Disponível somente ao carregar.

**--recursivo**    Examine os subdiretórios recursivamente ao carregar do sistema de arquivos local.

**--S2S-Detect-origem-alterado**   Detectar se o arquivo/blob de origem é alterado enquanto está sendo lido. (Esse parâmetro se aplica somente a cópias de serviço a serviço, pois a verificação correspondente é permanentemente habilitada para uploads e downloads.)

**--S2S-Handle-inválida-a cadeia de caracteres de metadados** especifica como as chaves de metadados inválidas são tratadas. Opções disponíveis: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (padrão `ExcludeIfInvalid` ). (padrão "ExcludeIfInvalid")

**--S2S-preserve-acesso-camada**   Preserve a camada de acesso durante a cópia de serviço para serviço. Consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](../blobs/storage-blob-storage-tiers.md) para garantir que a conta de armazenamento de destino dê suporte à configuração da camada de acesso. Nos casos em que não há suporte para a configuração da camada de acesso, use s2sPreserveAccessTier = false para ignorar a cópia da camada de acesso. (padrão `true` ).  (padrão "true")

**--S2S-preserve-Propriedades**   Preserve as propriedades completas durante a cópia de serviço para serviço. Para AWS S3 e fonte de arquivo não único de arquivo do Azure, a operação de lista não retorna propriedades completas de objetos e arquivos. Para preservar as propriedades completas, o AzCopy precisa enviar uma solicitação adicional por objeto ou arquivo. (padrão true)

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

**--Cap-Mbps float**   Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.

**--** formato da cadeia de caracteres do tipo de saída da saída do comando. As opções incluem: Text, JSON. O valor padrão é `text`. (padrão "texto")

**--a cadeia de caracteres Trusted-Microsoft-suffixs** especifica sufixos de domínio adicionais onde Azure Active Directory tokens de logon podem ser enviados.  O padrão é `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Todos listados aqui são adicionados ao padrão. Por segurança, você só deve colocar Microsoft Azure domínios aqui. Separe várias entradas com ponto e vírgula.

## <a name="see-also"></a>Confira também

- [azcopy](storage-ref-azcopy.md)
