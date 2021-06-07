---
title: azcopy copy| Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 03/08/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7c1e265f473c1c6fb70fd97416722e7b863c429b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503551"
---
# <a name="azcopy-copy"></a>azcopy copy

Copia os dados de origem para um local de destino.

## <a name="synopsis"></a>Sinopse

Copia os dados de origem para um local de destino. As instruções suportadas são:

  - local <-> Azure Blob (Autenticação SAS ou OAuth)
  - local <-> Arquivos do Azure (Compartilhar/diretório de autenticação SAS)
  - local <-> Azure Data Lake Storage Gen 2 (autenticação SAS, OAuth, ou chave compartilhada)
  - Azure Blob (SAS ou pública) -> Azure Blob (autenticação SAS ou OAuth)
  - Azure Blob (SAS ou pública) -> Arquivos do Azure (SAS)
  - Arquivos do Azure (SAS) -> Arquivos do Azure (SAS)
  - Arquivos do Azure (SAS) -> Azure Blob (Autenticação SAS ou OAuth)
  - Amazon Web Services (AWS) S3 (Chave de Acesso) -> blob de blocos do Azure (autenticação SAS ou OAuth)
  - Google Cloud Storage (Chave de conta de serviço) -> blob de blocos do Azure (autenticação SAS ou OAuth) [Pré-visualização]

Para saber mais, consulte a seção de exemplos deste artigo.

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Tutorial: migrar dados do local para a nuvem com o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Transferir dados com o AzCopy e o Armazenamento de blobs](./storage-use-azcopy-v10.md#transfer-data)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)

## <a name="advanced"></a>Avançado

O AzCopy detecta automaticamente o tipo de conteúdo dos arquivos quando você os carrega a partir do disco local. AzCopy detecta o tipo de conteúdo com base na extensão do arquivo ou no conteúdo (caso não seja especificada a extensão).

A tabela de pesquisa interna é pequena, mas no UNIX, ela é aumentada pelos `mime.types`arquivos do sistema local, caso estejam disponíveis em um ou mais desses nomes:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

No Windows, os tipos MIME são extraídos do registro. Esse recurso pode ser desativado com a ajuda de um sinalizador. Para saber mais, consulte a seção sinalizada deste artigo.

Se você definir uma variável de ambiente por meio de uma linha de comando, essa variável será legível no seu histórico de linha de comando. Considere limpar as variáveis que contêm credenciais do seu histórico de linha de comando. Para impedir que as variáveis apareçam no seu histórico, você pode usar um script para solicitar ao usuário que ele forneça as credenciais dele e defina a variável de ambiente.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exemplos

Carregar um único arquivo usando a autenticação OAuth. Se você ainda não fez logon no AzCopy, execute o `azcopy login` comando antes de executar o comando a seguir.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
Faz o mesmo que mencionado acima e também calcula o hash MD5 do conteúdo do arquivo e o salva como a propriedade Content-MD5 do blob:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Carregar um único arquivo usando um token SAS:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Carregar um único arquivo usando um token e um redirecionamento SAS (somente blobs de blocos):
  
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

Carregar um conjunto de arquivos usando um token SAS e caracteres curinga (*):
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Carregar arquivos e diretórios usando um token SAS e caracteres curinga (*):

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Carregar arquivos e diretórios na conta de Armazenamento do Microsoft Azure e definir as marcas codificadas da cadeia de caracteres de consulta no blob. 

- Para definir as marcas {key = "bla bla", val = "foo"} e {key = "bla bla 2", val = "bar"}, use a sintaxe a seguir: `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- As chaves e valores são codificados em URLs e os pares de chave-valor são separados por um e comercial ('&')

- Ao definir marcas nos blobs, existem permissões adicionais ('t' para marcas) no SAS sem as quais o serviço apresentará erro de autorização.

Baixar um único arquivo usando uma autenticação OAuth. Se você ainda não fez logon no AzCopy, execute o `azcopy login` comando antes de executar o comando a seguir.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Baixar um único arquivo usando um token SAS:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Baixar um único arquivo usando um token SAS e redirecionar a sua saída para um arquivo (somente blobs de blocos):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Baixar um diretório inteiro usando um token SAS:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

Observação sobre como usar um caractere curinga (*) em URLs:

Há apenas duas formas possíveis de usar um caractere curinga em uma URL. 

- Você pode usar um caractere logo após a barra final (/) de uma URL. Tal uso do caractere curinga copia todos os arquivos em um diretório diretamente para o destino, sem colocá-los em um subdiretório. 

- Você também pode usar um caractere curinga no nome de um contêiner, desde que a URL se refira a apenas a um contêiner e não a um blob. Você pode usar essa abordagem para obter arquivos de um subconjunto de contêineres. 

Baixar os conteúdos de um diretório sem copiar o próprio diretório.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Baixar uma conta de armazenamento inteira.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Baixar um subconjunto de contêineres em uma conta de armazenamento usando um caractere curinga (*) no nome do contêiner.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Copiar um único blob para outro blob usando um token SAS.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copiar um blob único para outro blob usando um token SAS e um token de autenticação. Você precisa usar um token SAS no final da URL da conta de origem, mas a conta de destino não precisa de uma se você fizer logon no AzCopy usando o `azcopy login` comando. 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Copiar um diretório virtual de blob para outro usando um token SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Copiar todos os contêineres de blob, diretórios e blobs da conta de armazenamento para outra usando um token SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copiar apenas um objeto para o Armazenamento de blobs do Amazon Web Services (AWS) S3 usando uma chave de acesso e um token SAS. Primeiro, definir a variável de ambiente `AWS_ACCESS_KEY_ID` e `AWS_SECRET_ACCESS_KEY` para a origem AWS S3.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copiar um diretório inteiro para o Armazenamento de blobs do AWS S3 usando uma chave de acesso e um token SAS. Primeiro, definir a variável de ambiente `AWS_ACCESS_KEY_ID` e `AWS_SECRET_ACCESS_KEY` para a origem AWS S3.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  Consultar https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html para entender melhor o espaço reservado [pasta].

Copiar todos os buckets para o armazenamento de blobs do AWS (Amazon Web Services) usando uma chave de acesso e um token SAS. Primeiro, definir a variável de ambiente `AWS_ACCESS_KEY_ID` e `AWS_SECRET_ACCESS_KEY` para a origem AWS S3.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copiar todos os buckets para o armazenamento de blobs de uma região AWS (Amazon Web Services) usando uma chave de acesso e um token SAS. Primeiro, definir a variável de ambiente `AWS_ACCESS_KEY_ID` e `AWS_SECRET_ACCESS_KEY` para a origem AWS S3.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copiar um subconjunto de buckets usando um caractere curinga (*) no nome do bucket. Como nos exemplos anteriores, você precisará de uma chave de acesso e um token SAS. Não deixe de definir a variável de ambiente `AWS_ACCESS_KEY_ID` e `AWS_SECRET_ACCESS_KEY` para a origem AWS S3.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Transferir arquivos e diretórios para a conta de Armazenamento do Microsoft Azure e definir as marcas codificadas da cadeia de caracteres de consulta fornecidas no blob. 

- Para definir as marcas {key = "bla bla", val = "foo"} e {key = "bla bla 2", val = "bar"}, use a sintaxe a seguir: `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- As chaves e valores são codificados em URLs e os pares de chave-valor são separados por um e comercial ('&')
    
- Ao definir marcas nos blobs, existem permissões adicionais ('t' para marcas) no SAS sem as quais o serviço apresentará erro de autorização.

Copiar apenas um objeto para o armazenamento de blobs do Google Cloud Storage usando uma chave de conta de serviço e um token SAS. Primeiro, defina a variável de ambiente GOOGLE_APPLICATION_CREDENTIALS para a fonte do Google Cloud Storage.
  
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copiar um diretório inteiro para o armazenamento de blobs do Google Cloud Storage usando uma chave de conta de serviço e um token SAS. Primeiro, defina a variável de ambiente GOOGLE_APPLICATION_CREDENTIALS para a fonte do Google Cloud Storage.
 
```azcopy
  - azcopy cp "https://storage.cloud.google.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Copiar um bucket inteiro para o armazenamento de blobs do Google Cloud Storage usando uma chave de conta de serviço e um token SAS. Primeiro, defina a variável de ambiente GOOGLE_APPLICATION_CREDENTIALS para a fonte do Google Cloud Storage.

```azcopy 
azcopy cp "https://storage.cloud.google.com/[bucket]" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Copiar todos os buckets para o armazenamento de blobs do Google Cloud Storage usando uma chave de conta de serviço e um token SAS. Primeiro, defina as variáveis de ambiente GOOGLE_APPLICATION_CREDENTIALS e GOOGLE_CLOUD_PROJECT=<project-id> para a fonte do GCS

```azcopy
  - azcopy cp "https://storage.cloud.google.com/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Copie um subconjunto de buckets usando um caractere curinga (*) no nome do bucket do Google Cloud Storage usando uma chave de conta de serviço e um token SAS para o destino. Primeiro, defina as variáveis de ambiente GOOGLE_APPLICATION_CREDENTIALS e GOOGLE_CLOUD_PROJECT=<project-id> para a fonte do Google Cloud Storage.
 
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

## <a name="options"></a>Opções

**--backup** Ativa o SeBackupPrivilege do Windows para carregamentos ou o SeRestorePrivilege para downloads, para permitir que o AzCopy consulte e leia todos os arquivos, independentemente de suas permissões do sistema de arquivos e restaure todas as permissões. Requer que a conta que executa o AzCopy já tenha essas permissões (por exemplo, tenha direitos de administrador ou seja um membro do `Backup Operators` grupo). Esse sinalizador ativa os privilégios que a conta já tem.

A cadeia de caracteres **--marcas de blob** definem as marcas em blobs para categorizar os dados em sua conta de armazenamento.

A cadeia de caracteres **--blob-Type** define o tipo de blob no destino. Isso é usado para carregar blobs e ao copiar entre contas (padrão `Detect`). Os valores válidos incluem `Detect`, `BlockBlob`, `PageBlob` e `AppendBlob`. Ao copiar entre contas, um valor de `Detect` faz com que o AzCopy use o tipo de blob de origem para determinar o tipo do blob de destino. Ao carregar um arquivo, `Detect` determina se o arquivo é um VHD ou um arquivo VHDX com base na extensão de arquivo. Mesmo que o arquivo seja um VHD ou arquivo VHDX o AzCopy tratará o arquivo como um blob de páginas. (padrão "Detectar")

A cadeia de caracteres **--Block-blob-camada** carrega um blob de blocos para o Armazenamento do Microsoft Azure usando esta camada de blob. (padrão “Nenhum”)

Um float **--Block-size-MB** usa esse tamanho de bloco (especificado na MIB) ao carregar no Armazenamento do Microsoft Azure e ao baixar do armazenamento do Azure. O valor padrão é calculado automaticamente com base no tamanho do arquivo. Frações decimais são permitidas (por exemplo: 0,25).

A cadeia de caracteres **--cache-control** define o cabeçalho de controle de cache. Devolução no download.

**--check-length** Verifica o comprimento de um arquivo no destino após a transferência. Se houver incompatibilidade entre a origem e o destino, a transferência será marcada como falha. (o valor padrão é `true`)

A cadeia de caracteres **--check-md5** Especifica o nível de restrição com o qual os hashes MD5 devem ser validados durante o download. Disponível somente durante o download. Opções disponíveis: `NoCheck`, `LogOnly`, `FailIfDifferent`, `FailIfDifferentOrMissing`. (padrão `FailIfDifferent`) (padrão "FailIfDifferent")

A cadeia de caracteres **--content-disposition** define o cabeçalho de disposição de conteúdo. Devolução no download.

A cadeia de caracteres **--content-encoding** define o cabeçalho de codificação de conteúdo. Devolução no download.

A cadeia de caracteres **--content-language** define o cabeçalho de linguagem de conteúdo. Devolução no download.

A cadeia de caracteres **--content-type** especifica o tipo de conteúdo do arquivo. Sugere o tipo MIME sem estimativa. Devolução no download.

**--decompress** Descompacta automaticamente os arquivos durante o download, se a codificação de conteúdo indicar que eles estão compactados. Os valores de codificação de conteúdo com suporte são `gzip` e `deflate`. As extensões de arquivo `.gz` / `.gzip` ou `.zz` não são necessárias, mas serão removidas se estiverem presentes.

A cadeia de caracteres (somente do Windows) **--exclude-attributtes** exclui os arquivos cujos atributos correspondem à lista de atributos. Por exemplo: A;S;R

A cadeia de caracteres **--exclude-blob-type** especifica opcionalmente o tipo de blob (`BlockBlob` /  `PageBlob` /  `AppendBlob`) para excluir ao copiar blobs do contêiner ou da conta. O uso desse sinalizador não é aplicável para copiar dados de serviços não Azure para o serviço. Mais de um blob deve ser separado por `;`. 

A cadeia de caracteres **--exclude-path** exclui esses caminhos ao copiar. Essa opção não dá suporte a caracteres curinga (*). Verifica o prefixo de caminho relativo (por exemplo: `myFolder;myFolder/subDirName/file.pdf`). Os caminhos não incluem o nome do contêiner quando usados junto a passagem de conta.

A cadeia de caracteres **--exclude-pattern** exclui esses arquivos ao copiar. Essa opção dá suporte a caracteres curinga (*).

**--follow-symlinks** Segue links simbólicos ao carregar do sistema de arquivos local.

**--force-if-read-only** Ao substituir um arquivo existente em arquivos do Windows ou do Azure, force a substituição para funcionar mesmo que o arquivo existente tenha seu próprio atributo de somente leitura definido.

A cadeia de caracteres **--from-to** especifica opcionalmente a combinação de destino de origem. Por exemplo: `LocalBlob`, `BlobLocal`, `LocalBlobFS`.

**--help** ajuda a fazer a cópia.

A cadeia de caracteres **--include-after** inclui apenas os arquivos modificados após ou na data/hora determinada. O valor deve estar no formato ISO8601. Se não for especificado um fuso horário, o valor será considerado de acordo com o fuso horário local do computador que está executando o AzCopy. por exemplo, `2020-08-19T15:04:00Z` para um horário UTC ou `2020-08-19` para às 0h (meia-noite) no fuso horário local. Como no AzCopy 10.5, esse sinalizador é aplicável apenas em arquivos, não em pastas, as propriedades da pasta não serão copiadas ao usar esse sinalizador com `--preserve-smb-info` ou `--preserve-smb-permissions`.

 A cadeia de caracteres **--include-before** inclui apenas os arquivos modificados antes ou na data/hora determinada. O valor deve estar no formato ISO8601. Se não for especificado um fuso horário, o valor será considerado de acordo com o fuso horário local do computador que está executando o AzCopy. Por ex.: `2020-08-19T15:04:00Z`para um horário UTC ou`2020-08-19` para às 0h (meia-noite) no fuso horário local. Como o AzCopy 10.7, esse sinalizador é aplicável apenas em arquivos, não em pastas, as propriedades da pasta não serão copiadas ao usar esse sinalizador com `--preserve-smb-info` ou `--preserve-smb-permissions`.

A cadeia de caracteres (somente do Windows) **--include-attributtes** inclui os arquivos cujos atributos correspondem à lista de atributos. Por exemplo: A;S;R

A cadeia de caracteres **--include-path** inclui apenas esses caminhos ao copiar. Essa opção não dá suporte a caracteres curinga (*). Verifica o prefixo de caminho relativo (por exemplo: `myFolder;myFolder/subDirName/file.pdf`).

A cadeia de caracteres **--include-pattern** inclui apenas esses arquivos ao copiar. Essa opção dá suporte a caracteres curinga (*). Separe arquivos usando um `;`.

A cadeia de caracteres **--list-of-versions** especifica um arquivo no qual cada ID de versão é listada em uma linha separada. Verifique se a origem deve apontar para um único blob e se todas as IDs de versão especificadas no arquivo usando esse sinalizador devem pertencer somente ao blob de origem. O AzCopy baixará as versões especificadas na pasta de destino fornecida. Para saber mais, consulte [Baixar versões anteriores de um blob](./storage-use-azcopy-v10.md#transfer-data).

A cadeia de caracteres **--log-level** define o detalhamento de log para o arquivo de log, níveis disponíveis: INFORMAÇÕES (todas as solicitações/respostas), AVISO (respostas lentas), ERRO (somente solicitações com falha) e NENHUM (sem logs de saída). (padrão `INFO`). 

A cadeia de caracteres **--metadata** carrega para o Armazenamento do Microsoft Azure esses pares de chave-valor como metadados.

**--no-guess-mime-type** Impede que o AzCopy detecte o tipo de conteúdo com base na extensão ou no conteúdo do arquivo.

A cadeia de caracteres **--overwrite** substituirá os arquivos conflitantes e os blobs no destino se esse sinalizador for definido como true. (padrão `true`) Os valores possíveis incluem `true`, `false`, `prompt` e `ifSourceNewer`. Para os destinos que dão suporte a pastas, as propriedades de nível de pasta conflitante serão substituídas nesse sinalizador `true` ou se uma resposta positiva for fornecida ao prompt. (padrão “true”)

A cadeia de caracteres **--page-blob-tier** carrega uma página de blob no armazenamento do Microsoft Azure usando esta camada de blob. (padrão `None`). (padrão “Nenhum”)

**--preserve-last-modified-time**  Disponível somente quando o destino é sistema de arquivos.

**--preserve-owner** Só tem efeito em downloads e somente quando `--preserve-smb-permissions` é usado. Se true (o padrão), o proprietário e o grupo do arquivo forem preservados nos downloads. Se definido como false, o `--preserve-smb-permissions` ainda preservará as ACLs, mas o Proprietário e o Grupo serão baseados no usuário que está executando AzCopy (padrão true)

**--preserve-SMB-info**   False por padrão. Preserva as informações de propriedade SMB (hora da última gravação, hora de criação, bits de atributo) entre recursos com reconhecimento de SMB (arquivos do Windows e do Azure). Somente os bits de atributo com suporte dos arquivos do Azure serão transferidos; quaisquer outros serão ignorados. Esse sinalizador é aplicável a arquivos e pastas, a menos que um filtro de apenas arquivo seja especificado (por exemplo, incluir padrão). As informações transferidas para pastas são as mesmas para arquivos, exceto a Hora da última gravação que nunca é preservada para pastas.

**--preserve-smb-permissions**   False por padrão. Preserva as ACLs de SMB entre recursos de reconhecimento (arquivos do Windows e do Azure). Para baixar, você também precisará do `--backup` sinalizador para restaurar as permissões nas quais o novo proprietário não será o usuário que vai executar o AzCopy. Esse sinalizador é aplicável a arquivos e pastas, a menos que um filtro de apenas arquivo seja especificado (por exemplo, `include-pattern`).

**--put-md5** Crie um hash MD5 de cada arquivo e salve o hash como a propriedade Content-MD5 do blob ou arquivo de destino. (Por padrão, o hash NÃO é criado.) Disponível somente ao carregar.

**--recursive** Examine os subdiretórios recursivamente ao carregar a partir do sistema de arquivos local.

**--s2s-detect-source-changed**   Detecta se o arquivo/blob de origem é alterado enquanto está sendo lido. (Esse parâmetro se aplica somente a cópias de serviço a serviço, pois a verificação correspondente é permanentemente habilitada para uploads e downloads.)

A cadeia de caracteres **--s2s-handle-invalid-metadata** especifica como são tratadas as chaves de metadados inválidas. Opções disponíveis: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (padrão `ExcludeIfInvalid`). (padrão "ExcludeIfInvalid")

**--s2s-preserve-access-tier** para preserva a camada de acesso durante a cópia de serviço para serviço. Consulte o [Armazenamento de blobs do Azure: camadas de acesso frequentes, esporádicos e do arquivo](../blobs/storage-blob-storage-tiers.md) para garantir que a conta de armazenamento de destino dê suporte à configuração da camada de acesso. Nos casos em que não há suporte para a configuração da camada de acesso, use s2sPreserveAccessTier=false para ignorar a cópia da camada de acesso. (padrão `true`).  (padrão “true”)

**--s2s-preserve-properties** Preserva todas as propriedades durante a cópia de serviço para serviço. Para os AWS S3 e fonte de arquivo não único de arquivo do Azure, a operação de lista não retorna todas as propriedades de objetos e arquivos. Para preservar todas as propriedades, o AzCopy precisa enviar uma solicitação adicional por objeto ou arquivo. (padrão true)

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

O float **--cap-mbps** Limita a taxa de transferência, em Mbps. A taxa de transferência por minuto pode variar um pouco do limite. Se essa opção for definida para zero ou for omitida, a taxa de transferência não será limitada.

A cadeia de caracteres **--output-type** formata o tipo de saída do comando. As opções incluem: text, JSON. O valor padrão é `text`. (padrão “text”)

A cadeia de caracteres **--trusted-microsoft-suffixes** especifica sufixos de domínio adicionais para os quais os tokens de logon do Azure Active Director  podem ser enviados.  O padrão é `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Todos listados aqui são adicionados ao padrão. Por segurança, você deve colocar apenas domínios do Microsoft Azure aqui. Separar várias entradas com ponto e vírgula.

## <a name="see-also"></a>Confira também

- [azcopy](storage-ref-azcopy.md)
