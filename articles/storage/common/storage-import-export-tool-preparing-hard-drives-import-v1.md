---
title: Preparando discos rígidos para um trabalho de importação da Importação/Exportação do Azure — v1 | Microsoft Docs
description: Saiba como preparar os discos rígidos usando a ferramenta WAImportExport v1 para criar um trabalho de importação para o serviço de Importação/Exportação do Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 03b504524b2f489f1ee042c6e825ccffe0a60bb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478463"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Preparando discos rígidos para um trabalho de importação
Para preparar um ou mais discos rígidos para um trabalho de importação, execute estas etapas:

- Identificar os dados para importar no serviço Blob

- Identificar os diretórios virtuais de destino e blobs no serviço Blob

- Determinar quantas unidades você precisará

- Copiar os dados para cada um de seus discos rígidos

  Para obter um exemplo de fluxo de trabalho, confira [Exemplo de fluxo de trabalho para preparar discos rígidos para um trabalho de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md).

## <a name="identify-the-data-to-be-imported"></a>Identificar os dados a serem importados
 A primeira etapa para criar um trabalho de importação é determinar quais diretórios e arquivos você pretende importar. Isso pode ser uma lista de diretórios, uma lista de arquivos exclusivos ou uma combinação dos dois. Quando um diretório é incluído, todos os arquivos no diretório e seus subdiretórios farão parte do trabalho de importação.

> [!NOTE]
>  Como os subdiretórios são incluídos recursivamente quando um diretório pai é incluído, especifique apenas o diretório pai. Não especifique nenhum de seus subdiretórios.
>
>  Atualmente, a Ferramenta de Importação/Exportação do Microsoft Azure tem a seguinte limitação: se um diretório contiver mais dados do que um disco rígido pode conter, o diretório precisará ser dividido em diretórios menores. Por exemplo, se um diretório contiver 2,5 TB de dados e a capacidade do disco rígido for apenas 2 TB, será necessário dividir o diretório de 2,5 TB em diretórios menores. Essa limitação será corrigida em uma versão mais recente da ferramenta.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>Identificar os locais de destino no serviço Blob
 Para cada diretório ou arquivo que será importado, você precisa identificar um diretório virtual de destino ou um blob no serviço Blob do Azure. Você usará esses destinos como entradas para a Ferramenta de Importação/Exportação do Azure. Observe que os diretórios devem ser delimitados com o caractere de barra "/".

 A tabela a seguir mostra alguns exemplos de blobs de destino:

|Arquivo ou diretório de origem|Blob de destino ou diretório virtual|
|------------------------------|-------------------------------------------|
|H:\Video|https:\//mystorageaccount.blob.core.windows.net/video|
|H:\Photo|https:\//mystorageaccount.blob.core.windows.net/photo|
|K:\Temp\FavoriteVideo.ISO|https:\//mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https:\//mystorageaccount.blob.core.windows.net/music|

## <a name="determine-how-many-drives-are-needed"></a>Determinar quantas unidades serão necessárias
 Em seguida, será necessário determinar:

- O número de discos rígidos necessários para armazenar os dados.

- Os diretórios e/ou os arquivos independentes que serão copiados em cada disco rígido.

  Verifique se você tem o número de discos rígidos necessários para armazenar os dados que você está transferindo.

## <a name="copy-data-to-your-hard-drive"></a>Copiar dados para o disco rígido
 Esta seção descreve como chamar a Ferramenta de Importação/Exportação do Azure para copiar seus dados em um ou mais discos rígidos. Sempre que você chama a Ferramenta de Importação/Exportação do Azure, uma nova *sessão de cópia* é criada. Crie pelo menos uma sessão de cópia para cada unidade na qual você copia dados; em alguns casos, talvez seja necessário mais de uma sessão de cópia para copiar todos os dados em uma única unidade. Veja alguns motivos pelos quais talvez você precise de várias sessões de cópia:

-   Você deve criar uma sessão de cópia separada para cada unidade na qual você copia.

-   Uma sessão de cópia pode copiar um único diretório ou um único blob na unidade. Se você estiver copiando vários diretórios, vários blobs ou uma combinação de ambos, será necessário criar várias sessões de cópia.

-   Você pode especificar propriedades e metadados que serão definidos nos blobs importados como parte de um trabalho de importação. As propriedades ou metadados que você especifica para uma sessão de cópia serão aplicados a todos os blobs especificados por essa sessão de cópia. Se você quiser especificar propriedades ou metadados diferentes para alguns blobs, será necessário criar uma sessão de cópia separada. Confira [Configuração de propriedades e metadados durante o processo de importação](storage-import-export-tool-setting-properties-metadata-import-v1.md) para saber mais.

> [!NOTE]
>  Se você tiver vários computadores que atendem aos requisitos descritos em [Configurando a Ferramenta de Importação/Exportação do Azure ](storage-import-export-tool-setup-v1.md), copie os dados em vários discos rígidos em paralelo executando uma instância dessa ferramenta em cada computador.

 Para cada disco rígido que você prepara com a Ferramenta de Importação/Exportação do Azure, a ferramenta criará um único arquivo de diário. Você precisará dos arquivos de diário de todas as unidades para criar o trabalho de importação. O arquivo de diário também pode ser usado para retomar a preparação da unidade se a ferramenta for interrompida.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Sintaxe da Ferramenta de Importação/Exportação do Azure para um trabalho de importação
 Para preparar unidades para um trabalho de importação, chame a Ferramenta de Importação/Exportação do Azure com o comando **PrepImport**. Os parâmetros que você inclui dependem se esta for a primeira sessão de cópia ou uma sessão de cópia subsequente.

 A primeira sessão de cópia para uma unidade exige alguns parâmetros adicionais para especificar a chave da conta de armazenamento; a letra da unidade de destino; se a unidade deve ser formatada; se a unidade deve ser criptografada e, em caso positivo, a chave do BitLocker; e o diretório do log. Veja a sintaxe de uma sessão de cópia inicial para copiar um diretório ou um único arquivo:

 **Primeira sessão de cópia para copiar um único diretório**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Primeira sessão de cópia para copiar um único arquivo**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 Em sessões de cópia subsequentes, não é necessário especificar os parâmetros iniciais. Veja a sintaxe de uma sessão de cópia subsequente para copiar um diretório ou um único arquivo:

 **Sessões de cópia subsequentes para copiar um único diretório**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Sessões de cópia subsequentes para copiar um único arquivo**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Parâmetros para a primeira sessão de cópia de um disco rígido
 Sempre que você executar a Ferramenta de Importação/Exportação do Azure para copiar arquivos no disco rígido, a ferramenta criará uma sessão de cópia. Cada sessão de cópia copia um único diretório ou um único arquivo em um disco rígido. O estado da sessão de cópia é gravado no arquivo de diário. Se uma sessão de cópia for interrompida (por exemplo, devido a uma perda de energia do sistema), ela poderá ser retomada executando a ferramenta novamente e especificando o arquivo de diário na linha de comando.

> [!WARNING]
>  Se você especificar o parâmetro **/format** para a primeira sessão de cópia, a unidade será formatada e todos os dados no disco serão apagados. Recomendamos o uso de unidades em branco apenas para a sessão de cópia.

 O comando usado para a primeira sessão de cópia de cada unidade exige parâmetros diferentes dos comandos para sessões de cópia subsequentes. A tabela a seguir lista os parâmetros adicionais disponíveis para a primeira sessão de cópia:

|Parâmetro de linha de comando|DESCRIÇÃO|
|-----------------------------|-----------------|
|**/sk:**<StorageAccountKey\>|`Optional.` A chave da conta de armazenamento na qual os dados serão importados. Você deve incluir **/sk:**<StorageAccountKey\> ou **/csas:**<ContainerSas\> no comando.|
|**/csas:**<ContainerSas\>|`Optional`. O SAS do contêiner a ser usado para importar dados na conta de armazenamento. Você deve incluir **/sk:**<StorageAccountKey\> ou **/csas:**<ContainerSas\> no comando.<br /><br /> O valor desse parâmetro deve começar com o nome do contêiner, seguido por um ponto de interrogação (?) e o token SAS. Por exemplo: <br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> As permissões, especificadas na URL ou em uma política de acesso armazenada, devem incluir Leitura, Gravação e Exclusão para trabalhos de importação, e Leitura, Gravação e Lista para trabalhos de exportação.<br /><br /> Quando este parâmetro for especificado, todos os blobs a serem importados ou exportados deverão estar dentro do contêiner especificado na assinatura de acesso compartilhado.|
|**/t:**<TargetDriveLetter\>|`Required.` A letra da unidade do disco rígido de destino para a sessão de cópia atual, sem os dois-pontos no final.|
|**/format**|`Optional.` Especifique esse parâmetro quando for necessário formatar a unidade; caso contrário, omita-o. Antes de a ferramenta formatar a unidade, ela solicitará uma confirmação no console. Para suprimir a confirmação, especifique o parâmetro /silentmode.|
|**/silentmode**|`Optional.` Especifique esse parâmetro para suprimir a confirmação para formatar a unidade de destino.|
|**/encrypt**|`Optional.` Especifique esse parâmetro quando a unidade ainda não tiver sido criptografada com o BitLocker e precisar ser criptografada pela ferramenta. Se a unidade já tiver sido criptografada com o BitLocker, omita esse parâmetro e especifique o parâmetro `/bk`, fornecendo a chave do BitLocker existente.<br /><br /> Se você especificar o parâmetro `/format`, também deverá especificar o parâmetro `/encrypt`.|
|**/bk:**<BitLockerKey\>|`Optional.` Se `/encrypt` for especificado, omita este parâmetro. Se `/encrypt` for omitido, você já precisa ter criptografado a unidade com o BitLocker. Use esse parâmetro para especificar a chave do BitLocker. A criptografia do BitLocker é exigida em todos os discos rígidos para trabalhos de importação.|
|**/logdir:**<LogDirectory\>|`Optional.` O diretório de log especifica um diretório a ser usado para armazenar logs detalhados, bem como arquivos de manifesto temporários. Se nenhum for especificado, o diretório atual será usado como o diretório de log.|

### <a name="parameters-required-for-all-copy-sessions"></a>Parâmetros obrigatórios para todas as sessões de cópia
 O arquivo de diário contém o status de todas as sessões de cópia de um disco rígido. Ele também contém as informações necessárias para criar o trabalho de importação. Você sempre deve especificar um arquivo de diário ao executar a Ferramenta de Importação/Exportação do Azure, bem como uma ID da sessão de cópia:

|||
|-|-|
|Parâmetro de linha de comando|DESCRIÇÃO|
|**/j:**<JournalFile\>|`Required.` O caminho para o arquivo de diário. Cada unidade deve ter exatamente um arquivo de diário. Observe que o arquivo de diário não deve residir na unidade de destino. A extensão do arquivo de diário é `.jrn`.|
|**/id:**<SessionId\>|`Required.` A ID de sessão identifica uma sessão de cópia. Ela é usada para garantir a recuperação correta de uma sessão de cópia interrompida. Os arquivos copiados em uma sessão de cópia são armazenados em um diretório nomeado como a ID de sessão na unidade de destino.|

### <a name="parameters-for-copying-a-single-directory"></a>Parâmetros para copiar um único diretório
 Ao copiar um único diretório, os seguintes parâmetros obrigatórios e opcionais serão aplicáveis:

|Parâmetro de linha de comando|DESCRIÇÃO|
|----------------------------|-----------------|
|**/srcdir:**<SourceDirectory\>|`Required.` O diretório de origem que contém os arquivos a serem copiados para a unidade de destino. O caminho do diretório deve ser um caminho absoluto (não um caminho relativo).|
|**/dstdir:**<DestinationBlobVirtualDirectory\>|`Required.` O caminho até o diretório virtual de destino em sua conta de armazenamento do Microsoft Azure. O diretório virtual pode ou não existir.<br /><br /> Você pode especificar um contêiner ou um prefixo de blob como `music/70s/`. O diretório de destino deve começar com o nome do contêiner, seguido por uma barra "/" e, opcionalmente, pode incluir um diretório virtual de blob que termina com "/".<br /><br /> Quando o contêiner de destino for o contêiner raiz, especifique explicitamente o contêiner raiz, incluindo a barra, como `$root/`. Como os blobs no contêiner raiz não podem incluir "/" em seus nomes, quaisquer subdiretórios no diretório de origem não serão copiados quando o diretório de destino for o contêiner raiz.<br /><br /> Certifique-se de usar nomes de contêineres válidos ao especificar diretórios virtuais ou blobs de destino. Tenha em mente que os nomes de contêiner devem estar em minúsculas. Para conhecer as regras de nomenclatura de contêineres, consulte [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).|
|**/Disposition:**<rename&#124;no-overwrite&#124;overwrite>|`Optional.` Especifica o comportamento quando um blob com o endereço especificado já existe. Os valores válidos para este parâmetro são: `rename`, `no-overwrite` e `overwrite`. Observe que esses valores diferenciam maiúsculas de minúsculas. Se nenhum valor for especificado, o padrão será `rename`.<br /><br /> O valor especificado para esse parâmetro afeta todos os arquivos no diretório especificado pelo parâmetro `/srcdir`.|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` Especifica o tipo de blob para os blobs de destino. Os valores válidos são: `BlockBlob` e `PageBlob`. Observe que esses valores diferenciam maiúsculas de minúsculas. Se nenhum valor for especificado, o padrão será `BlockBlob`.<br /><br /> Na maioria dos casos, `BlockBlob` é recomendado. Se você especificar `PageBlob`, o comprimento de cada arquivo no diretório deverá ser um múltiplo de 512, o tamanho de uma página para blobs de página.|
|**/PropertyFile:**<PropertyFile\>|`Optional.` Caminho até o arquivo de propriedades para os blobs de destino. Confira [Formato de arquivo de propriedades e metadados de serviço de Importação/Exportação](../storage-import-export-file-format-metadata-and-properties.md) para saber mais.|
|**/MetadataFile:**<MetadataFile\>|`Optional.` Caminho até o arquivo de metadados para os blobs de destino. Confira [Formato de arquivo de propriedades e metadados de serviço de Importação/Exportação](../storage-import-export-file-format-metadata-and-properties.md) para saber mais.|

### <a name="parameters-for-copying-a-single-file"></a>Parâmetros para copiar um único arquivo
 Ao copiar um único arquivo, os seguintes parâmetros obrigatórios e opcionais serão aplicáveis:

|Parâmetro de linha de comando|DESCRIÇÃO|
|----------------------------|-----------------|
|**/srcfile:**<SourceFile\>|`Required.` O caminho completo até o arquivo a ser copiado. O caminho do diretório deve ser um caminho absoluto (não um caminho relativo).|
|**/dstblob:**<DestinationBlobPath\>|`Required.` O caminho até o blob de destino em sua conta de armazenamento do Windows Azure. O blob pode ou não existir.<br /><br /> Especifique o nome do blob, começando com o nome do contêiner. O nome do blob não pode começar com "/" ou com o nome da conta de armazenamento. Para conhecer as regras de nomenclatura de blobs, consulte [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).<br /><br /> Quando o contêiner de destino for o contêiner raiz, especifique explicitamente `$root` como o contêiner, por exemplo `$root/sample.txt`. Observe que os blobs no contêiner raiz não podem incluir "/" em seus nomes.|
|**/Disposition:**<rename&#124;no-overwrite&#124;overwrite>|`Optional.` Especifica o comportamento quando um blob com o endereço especificado já existe. Os valores válidos para este parâmetro são: `rename`, `no-overwrite` e `overwrite`. Observe que esses valores diferenciam maiúsculas de minúsculas. Se nenhum valor for especificado, o padrão será `rename`.|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` Especifica o tipo de blob para os blobs de destino. Os valores válidos são: `BlockBlob` e `PageBlob`. Observe que esses valores diferenciam maiúsculas de minúsculas. Se nenhum valor for especificado, o padrão será `BlockBlob`.<br /><br /> Na maioria dos casos, `BlockBlob` é recomendado. Se você especificar `PageBlob`, o comprimento de cada arquivo no diretório deverá ser um múltiplo de 512, o tamanho de uma página para blobs de página.|
|**/PropertyFile:**<PropertyFile\>|`Optional.` Caminho até o arquivo de propriedades para os blobs de destino. Confira [Formato de arquivo de propriedades e metadados de serviço de Importação/Exportação](../storage-import-export-file-format-metadata-and-properties.md) para saber mais.|
|**/MetadataFile:**<MetadataFile\>|`Optional.` Caminho até o arquivo de metadados para os blobs de destino. Confira [Formato de arquivo de propriedades e metadados de serviço de Importação/Exportação](../storage-import-export-file-format-metadata-and-properties.md) para saber mais.|

### <a name="resuming-an-interrupted-copy-session"></a>Retomando uma sessão de cópia interrompida
 Se uma sessão de cópia for interrompida por qualquer motivo, você poderá retomá-la executando a ferramenta apenas com o arquivo de diário especificado:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 Somente a sessão de cópia mais recente, se for finalizada de maneira anormal, poderá ser retomada.

> [!IMPORTANT]
>  Ao retomar uma sessão de cópia, não modifique os diretórios e arquivos de dados de origem adicionando ou removendo arquivos.

### <a name="aborting-an-interrupted-copy-session"></a>Anulando uma sessão de cópia interrompida
 Se uma sessão de cópia for interrompida e não for possível retomá-la (por exemplo, se um diretório de origem ficar inacessível), será necessário anular a sessão atual para que ela possa ser revertida e novas sessões de cópia possam ser iniciadas:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 Somente a última sessão de cópia, se for finalizada de maneira anormal, poderá ser anulada. Observe que não é possível anular a primeira sessão de cópia de uma unidade. Em vez disso, reinicie a sessão de cópia com um novo arquivo de diário.

## <a name="next-steps"></a>Próximas etapas

* [Configurando a Ferramenta de Importação/Exportação do Azure](storage-import-export-tool-setup-v1.md)
* [Definindo propriedades e metadados durante o processo de importação](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [Fluxo de trabalho de exemplo para preparar discos rígidos para um trabalho de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [Referência rápida para comandos usados frequentemente](storage-import-export-tool-quick-reference-v1.md) 
* [Revisão do status do trabalho com arquivos de log de cópia](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparação de um trabalho de importação](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparação de um trabalho de exportação](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Solucionando problemas da Ferramenta de Importação/Exportação do Azure](storage-import-export-tool-troubleshooting-v1.md)
