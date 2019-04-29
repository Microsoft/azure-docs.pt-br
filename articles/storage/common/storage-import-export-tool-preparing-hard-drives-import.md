---
title: Preparação de discos rígidos para um trabalho de importação da Importação/Exportação do Azure | Microsoft Docs
description: Saiba como preparar os discos rígidos usando a ferramenta WAImportExport para criar um trabalho de importação para o serviço Importação/Exportação do Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 777e0aac46dbffb1e491874b5889667a888aadf5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478498"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Preparação de discos rígidos para um trabalho de importação

A ferramenta WAImportExport é a ferramenta de preparação e reparo de unidade que pode ser usada com o serviço [Importação/Exportação do Microsoft Azure](../storage-import-export-service.md). Use essa ferramenta para copiar dados para os discos rígidos que você vai enviar para um datacenter do Azure. Após a conclusão de um trabalho de importação, é possível usar essa ferramenta para reparar os blobs corrompidos, ausentes ou que entraram em conflito com outros blobs. Depois de receber as unidades de um trabalho de exportação concluído, é possível usar essa ferramenta para reparar os arquivos corrompidos ou ausentes nas unidades. Neste artigo, explicamos o uso dessa ferramenta.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="requirements-for-waimportexportexe"></a>Requisitos de WAImportExport.exe

- **Configuração da máquina**
  - Windows 7, Windows Server 2008 R2 ou um sistema operacional Windows mais recente
  - O .NET Framework 4 deve estar instalado. Ver [perguntas frequentes sobre](#faq) sobre como verificar se o .NET Framework está instalado no computador.
- **Chave da conta de armazenamento** - É necessário ter pelo menos uma das chaves de conta para a conta de armazenamento.

### <a name="preparing-disk-for-import-job"></a>Preparação do disco para o trabalho de importação

- **BitLocker -** o BitLocker deve ser habilitado no computador executando a ferramenta WAImportExport. Confira nas [Perguntas frequentes](#faq) como habilitar o BitLocker.
- **Discos** acessíveis do computador no qual a ferramenta WAImportExport é executada. Confira nas [Perguntas frequentes](#faq) a especificação do disco.
- **Arquivos de origem** - Os arquivos que você pretende importar devem estar acessíveis no computador de cópia, estejam eles em um compartilhamento de rede ou um disco rígido local.

### <a name="repairing-a-partially-failed-import-job"></a>Reparação de um trabalho de importação com falha parcial

- **Copie o arquivo de log** gerado quando o serviço de Importação/Exportação do Azure copia dados entre a Conta de Armazenamento e o Disco. Eles estão localizados em sua conta de armazenamento de destino.

### <a name="repairing-a-partially-failed-export-job"></a>Reparação de um trabalho de exportação com falha parcial

- **Copie o arquivo de log** gerado quando o serviço de Importação/Exportação do Azure copia dados entre a Conta de Armazenamento e o Disco. Ele está localizado em sua conta de armazenamento de origem.
- **Arquivo de manifesto** -[opcional] localizado na unidade exportada retornada pela Microsoft.

## <a name="download-and-install-waimportexport"></a>Baixar e instalar a WAImportExport

Baixe a [versão mais recente do WAImportExport.exe](https://www.microsoft.com/download/details.aspx?id=55280). Extraia o conteúdo compactado em um diretório em seu computador.

A próxima tarefa é criar arquivos CSV.

## <a name="prepare-the-dataset-csv-file"></a>Preparar o arquivo CSV de conjunto de dados

### <a name="what-is-dataset-csv"></a>O que é o CSV de conjunto de dados

O arquivo CSV de conjunto de dados é o valor do sinalizador /dataset, um arquivo CSV contendo uma lista de diretórios e/ou arquivos de lista a serem copiados nas unidades de destino. A primeira etapa para criar um trabalho de importação é determinar quais diretórios e arquivos você pretende importar. Isso pode ser uma lista de diretórios, uma lista de arquivos exclusivos ou uma combinação dos dois. Quando um diretório é incluído, todos os arquivos no diretório e seus subdiretórios farão parte do trabalho de importação.

Para cada diretório ou arquivo a ser importado, você deve identificar um diretório virtual de destino ou um blob no serviço Blob do Azure. Você usará esses destinos como entradas para a ferramenta WAImportExport. Os diretórios devem ser delimitados com o caractere de barra "/".

A tabela a seguir mostra alguns exemplos de blobs de destino:

| Arquivo ou diretório de origem | Blob de destino ou diretório virtual |
| --- | --- |
| H:\Video | https://mystorageaccount.blob.core.windows.net/video |
| H:\Photo | https://mystorageaccount.blob.core.windows.net/photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.blob.core.windows.net/music |

### <a name="sample-datasetcsv"></a>Exemplo de dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>Campos do arquivo CSV de conjunto de dados

| Campo | DESCRIÇÃO |
| --- | --- |
| BasePath | **[Obrigatório]**<br/>O valor desse parâmetro representa a origem em que os dados a serem importados estão localizados. A ferramenta copiará recursivamente todos os dados localizados nesse caminho.<br><br/>**Valores Permitidos**: deve ser um caminho válido no computador local ou um caminho de compartilhamento válido e deve estar acessível para o usuário. O caminho do diretório deve ser um caminho absoluto (não um caminho relativo). Se o caminho termina com "\\", ele representa um diretório. Caso contrário, um caminho que termina sem "\\" representa um arquivo.<br/>Nenhuma regex é permitido neste campo. Se o caminho contiver espaços, coloque-os entre "".<br><br/>**Exemplo**: "c:\Directory\c\Directory\File.txt"<br>"\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[Obrigatório]**<br/> O caminho até o diretório virtual de destino em sua conta de armazenamento do Windows Azure. O diretório virtual pode ou não existir. Se não existir, o serviço de Importação/Exportação criará um.<br/><br/>Certifique-se de usar nomes de contêineres válidos ao especificar diretórios virtuais ou blobs de destino. Tenha em mente que os nomes de contêiner devem estar em minúsculas. Para conhecer as regras de nomenclatura de contêineres, consulte [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata). Se apenas a raiz for especificada, a estrutura do diretório de origem será replicada no contêiner de blob de destino. Caso deseje a uma estrutura de diretório diferente no código-fonte, várias linhas de mapeamento no CSV<br/><br/>Você pode especificar um contêiner ou um prefixo de blob como music/70s/. O diretório de destino deve começar com o nome do contêiner, seguido por uma barra "/" e, opcionalmente, pode incluir um diretório virtual de blob que termina com "/".<br/><br/>Quando o contêiner de destino for o contêiner raiz, especifique explicitamente o contêiner raiz, incluindo a barra, como $root/. Como os blobs no contêiner raiz não podem incluir "/" em seus nomes, quaisquer subdiretórios no diretório de origem não serão copiados quando o diretório de destino for o contêiner raiz.<br/><br/>**Exemplo**<br/>Se o caminho de blob de destino for https://mystorageaccount.blob.core.windows.net/video, o valor desse campo poderá ser video /  |
| BlobType | **[Opcional]** block &#124; page<br/>Atualmente, o serviço de Importação/Exportação oferece suporte dois tipos de Blobs. Blobs de página e Blobs de bloco. Por padrão, todos os arquivos serão importados como Blobs de blocos. E \*.vhd e \*.vhdx serão importados como Blobs de página. Há um limite para o tamanho permitido para o blob de bloco e o blob de página. Confira [Metas de escalabilidade do armazenamento](storage-scalability-targets.md) para saber mais.  |
| Disposition | **[Opcional]** rename &#124; no-overwrite &#124; overwrite <br/> Este campo especifica o comportamento de cópia durante a importação, ou seja Quando os dados estão sendo carregados do disco para a conta de armazenamento. As opções disponíveis são: rename&#124;overwrite&#124;no-overwrite.Se nada for especificado, o padrão é "rename". <br/><br/>**Renomear**: se um objeto com o mesmo nome estiver presente, isso criará uma cópia no destino.<br/>Overwrite: substitui o arquivo por um arquivo mais recente. O arquivo com a última modificação vence.<br/>**No-overwrite**: ignora a substituição do arquivo se ele já estiver presente.|
| MetadataFile | **[Opcional]** <br/>O valor para esse campo é o arquivo de metadados, que pode ser fornecido se for necessário preservar os metadados dos objetos ou fornecer metadados personalizados. Caminho até o arquivo de metadados para os blobs de destino. Confira [Formato de arquivo de propriedades e metadados de serviço de Importação/Exportação](../storage-import-export-file-format-metadata-and-properties.md) para saber mais |
| PropertiesFile | **[Opcional]** <br/>Caminho até o arquivo de propriedades para os blobs de destino. Confira [Formato de arquivo de propriedades e metadados de serviço de Importação/Exportação](../storage-import-export-file-format-metadata-and-properties.md) para saber mais. |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>Preparar o arquivo CSV AdditionalDriveSet ou InitialDriveSet

### <a name="what-is-driveset-csv"></a>O que é o CSV driveset

O valor do sinalizador /InitialDriveSet ou /AdditionalDriveSet é um arquivo CSV que contém a lista de discos para os quais as letras de unidade estão mapeadas, de modo que a ferramenta possa selecionar corretamente a lista de discos para preparação. Se o tamanho dos dados for maior do que o tamanho de um único disco, a ferramenta WAImportExport distribuirá os dados em vários discos inscritos nesse arquivo CSV de forma otimizada.

Não há limite para o número de discos nos quais os dados podem ser gravados em uma única sessão. A ferramenta distribuirá os dados com base no tamanho do disco e no tamanho da pasta. Ele selecionará o disco mais otimizado para o tamanho do objeto. Após serem carregados na conta de armazenamento, os dados serão ser convergidos novamente para a estrutura do diretório que foi especificada no arquivo do conjunto de dados. Para criar um CSV driveset, execute as etapas abaixo.

### <a name="create-basic-volume-and-assign-drive-letter"></a>Criar um volume básico e atribuir uma letra de unidade

Para criar um volume básico e atribuir uma letra de unidade, siga as instruções de "Criação de partição mais simples" fornecidas em [Visão geral do gerenciamento de disco](https://technet.microsoft.com/library/cc754936.aspx).

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>Exemplo de arquivo CSV InitialDriveSet e AdditionalDriveSet

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>Campos do arquivo CSV driveset

| Campos | Value |
| --- | --- |
| DriveLetter | **[Obrigatório]**<br/> Cada unidade fornecida à ferramenta como destino deve ter um volume NTFS simples e uma letra de unidade atribuída.<br/> <br/>**Exemplo**: R ou r |
| FormatOption | **[Obrigatório]** Format &#124; AlreadyFormatted<br/><br/> **Formatar**: isso formatará todos os dados no disco. <br/>**AlreadyFormatted**: a ferramenta ignorará a formatação quando esse valor for especificado. |
| SilentOrPromptOnFormat | **[Obrigatório]** SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: esse valor permitirá que o usuário execute a ferramenta no Modo Silencioso. <br/>**PromptOnFormat**: a ferramenta solicitará ao usuário a confirmação de que a ação realmente se destina a todos os formatos.<br/><br/>Se não for definido, o comando anulará e exibirá a mensagem de erro: "valor incorreto para SilentOrPromptOnFormat: none" |
| Criptografia | **[Obrigatório]** Encrypt &#124; AlreadyEncrypted<br/> O valor desse campo decide qual disco criptografar e qual não criptografar. <br/><br/>**Criptografar**: a ferramenta formatará a unidade. Se o valor do campo "FormatOption" for "Format", esse valor deverá ser "Encrypt". Se "AlreadyEncrypted" for especificado, o resultado será um erro "Quando Format for especificado, Encrypt também deverá ser especificado".<br/>**AlreadyEncrypted**: a ferramenta descriptografará a unidade usando o BitLockerKey fornecido no campo "ExistingBitLockerKey". Se o valor do campo "FormatOption" for "AlreadyFormatted", esse valor poderá ser "Encrypt" ou "AlreadyEncrypted" |
| ExistingBitLockerKey | **[Obrigatório]**  Se o valor do campo "Encryption" for "AlreadyEncrypted"<br/> O valor desse campo é a chave do BitLocker associada ao disco específico. <br/><br/>Esse campo deve ser deixado em branco se o valor do campo "Encryption" for "Encrypt".  Se a Chave do BitLocker for especificada nesse caso, o resultado será um erro "A Chave do BitLocker não deve ser especificada".<br/>  **Exemplo**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>Preparação do disco para o trabalho de importação

Para preparar unidades para um trabalho de importação, chame a ferramenta WAImportExport com o comando **PrepImport**. Os parâmetros que você inclui dependem se esta for a primeira sessão de cópia ou uma sessão de cópia subsequente.

### <a name="first-session"></a>Primeira sessão

Primeira Sessão de Cópia para copiar um único diretório/múltiplos diretórios em um único disco/múltiplos discos (dependendo do que é especificado no arquivo CSV) Comando PrepImport da ferramenta WAImportExport para a primeira sessão de cópia a fim de copiar diretórios e/ou arquivos com uma nova sessão de cópia:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] /DataSet:<dataset.csv>
```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>Adicionar dados em uma sessão subsequente

Em sessões de cópia subsequentes, não é necessário especificar os parâmetros iniciais. Use o mesmo arquivo de diário para que a ferramenta se lembre onde parou na sessão anterior. O estado da sessão de cópia é gravado no arquivo de diário. Esta é a sintaxe de uma sessão de cópia subsequente para copiar outros diretórios e/ou arquivos:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>Adicionar unidades à sessão mais recente

Se os dados não se ajustarem às unidades especificadas em InitialDriveset, será possível usar a ferramenta para adicionar outras unidades à mesma sessão de cópia. 

> [!NOTE]
> A identificação da sessão deve corresponder à identificação de sessão anterior. Arquivo de diário deve corresponder àquele especificado na sessão anterior.
> 
> ```
> WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
> ```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>Anular a sessão mais recente:

Se uma sessão de cópia for interrompida e não for possível retomá-la (por exemplo, se um diretório de origem ficar inacessível), será necessário anular a sessão atual para que ela possa ser revertida e novas sessões de cópia possam ser iniciadas:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

Somente a última sessão de cópia, se for finalizada de maneira anormal, poderá ser anulada. Observe que não é possível anular a primeira sessão de cópia de uma unidade. Em vez disso, reinicie a sessão de cópia com um novo arquivo de diário.

### <a name="resume-a-latest-interrupted-session"></a>Retomar a sessão interrompida mais recente

Se uma sessão de cópia for interrompida por qualquer motivo, você poderá retomá-la executando a ferramenta apenas com o arquivo de diário especificado:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> Ao retomar uma sessão de cópia, não modifique os diretórios e arquivos de dados de origem adicionando ou removendo arquivos.

## <a name="waimportexport-parameters"></a>Parâmetros de WAImportExport

| parâmetros | DESCRIÇÃO |
| --- | --- |
|     /j:&lt;JournalFile&gt;  | **Obrigatório**<br/> Caminho até o arquivo de diário. Um arquivo de diário acompanha um conjunto de unidades e registra o progresso da preparação dessas unidades. O arquivo de diário deve ser sempre especificado.  |
|     /logdir:&lt;LogDirectory&gt;  | **Opcional**. O diretório de log.<br/> Arquivos de log detalhado, bem como alguns arquivos temporários, serão gravados nesse diretório. Se nenhum for especificado, o diretório atual será usado como o diretório de log. O diretório de log pode ser especificado somente uma vez para o mesmo arquivo de diário.  |
|     /id:&lt;SessionId&gt;  | **Obrigatório**<br/> A ID da sessão é usada para identificar uma sessão de cópia. Ela é usada para garantir a recuperação correta de uma sessão de cópia interrompida.  |
|     /ResumeSession  | Opcional. Se a última sessão de cópia tiver sido finalizada de maneira anormal, esse parâmetro poderá ser especificado para retomar a sessão.   |
|     /AbortSession  | Opcional. Se a última sessão de cópia tiver sido finalizada de maneira anormal, esse parâmetro poderá ser especificado para anular a sessão.  |
|     /sn:&lt;StorageAccountName&gt;  | **Obrigatório**<br/> Aplicável somente para RepairImport e RepairExport. O nome da conta de armazenamento.  |
|     /sk:&lt;StorageAccountKey&gt;  | **Obrigatório**<br/> A chave da conta de armazenamento. |
|     /InitialDriveSet:&lt;driveset.csv&gt;  | **Obrigatório** Ao executar a primeira sessão de cópia<br/> Um arquivo CSV que contém uma lista de unidades para preparação.  |
|     /AdditionalDriveSet:&lt;driveset.csv&gt; | **Obrigatório**. Ao adicionar unidades à sessão de cópia atual. <br/> Um arquivo CSV que contém uma lista de unidades adicionais para adição.  |
|      /r:&lt;RepairFile&gt; | **Obrigatório** Aplicável somente para RepairImport e RepairExport.<br/> Caminho até o arquivo para acompanhar o progresso do reparo. Cada unidade deve ter um, e somente um, arquivo de reparo.  |
|     /d:&lt;TargetDirectories&gt; | **Obrigatório**. Aplicável somente para RepairImport e RepairExport. Para RepairImport, um ou mais diretórios separados por ponto e vírgula para reparação; para RepairExport, um diretório para reparação, por exemplo, diretório raiz da unidade.  |
|     /CopyLogFile:&lt;DriveCopyLogFile&gt; | **Obrigatório** Aplicável somente para RepairImport e RepairExport. Caminho até o arquivo de log de cópia da unidade (detalhado ou de erro).  |
|     /ManifestFile:&lt;DriveManifestFile&gt; | **Obrigatório** Aplicável somente para RepairExport.<br/> Caminho até o arquivo de manifesto da unidade.  |
|     /PathMapFile:&lt;DrivePathMapFile&gt; | **Opcional**. Aplicável somente para RepairImport.<br/> Caminho até o arquivo que contém mapeamentos dos caminhos de arquivo relativos à raiz da unidade até os locais de arquivos reais (delimitado por tabulação). Quando for especificado pela primeira vez, ele será preenchido com caminhos de arquivo com destinos vazios, o que significa que não será encontrado em TargetDirectories, terá o acesso negado, nome inválido ou existirá em vários diretórios. O arquivo de mapa de caminho pode ser editado manualmente para incluir os caminhos de destino corretos, e pode ser especificado novamente para que a ferramenta possa resolver os caminhos de arquivo corretamente.  |
|     /ExportBlobListFile:&lt;ExportBlobListFile&gt; | **Obrigatório**. Aplicável somente para PreviewExport.<br/> Caminho até o arquivo XML que contém a lista de caminhos de blob ou prefixos de caminhos de blob para os blobs a serem exportados. O formato de arquivo é o mesmo usado no formato de blob na lista de blobs na operação Put Job da API REST do serviço de Importação/Exportação.  |
|     /DriveSize:&lt;DriveSize&gt; | **Obrigatório**. Aplicável somente para PreviewExport.<br/>  Tamanho de unidades a serem usadas para exportação. Por exemplo, 500 GB, 1,5 TB. Observação: 1 GB = 1.000.000.000 bytes1 TB = 1.000.000.000.000 bytes  |
|     /DataSet:&lt;dataset.csv&gt; | **Obrigatório**<br/> Um arquivo CSV contendo uma lista de diretórios e/ou arquivos de lista a serem copiados nas unidades de destino.  |
|     /silentmode  | **Opcional**.<br/> Se não for especificado, você será lembrado sobre o requisito das unidades, e sua confirmação será necessária para continuar.  |

## <a name="tool-output"></a>Saída da ferramenta

### <a name="sample-drive-manifest-file"></a>Exemplo de arquivo de manifesto da unidade

```xml
<?xml version="1.0" encoding="UTF-8"?>
<DriveManifest Version="2011-MM-DD">
   <Drive>
      <DriveId>drive-id</DriveId>
      <StorageAccountKey>storage-account-key</StorageAccountKey>
      <ClientCreator>client-creator</ClientCreator>
      <!-- First Blob List -->
      <BlobList Id="session#1-0">
         <!-- Global properties and metadata that applies to all blobs -->
         <MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>
         <PropertiesPath Hash="md5-hash">global-properties-file-path</PropertiesPath>
         <!-- First Blob -->
         <Blob>
            <BlobPath>blob-path-relative-to-account</BlobPath>
            <FilePath>file-path-relative-to-transfer-disk</FilePath>
            <ClientData>client-data</ClientData>
            <Length>content-length</Length>
            <ImportDisposition>import-disposition</ImportDisposition>
            <!-- page-range-list-or-block-list -->
            <!-- page-range-list -->
            <PageRangeList>
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
            </PageRangeList>
            <!-- block-list -->
            <BlockList>
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
            </BlockList>
            <MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>
            <PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>
         </Blob>
      </BlobList>
   </Drive>
</DriveManifest>
```

### <a name="sample-journal-file-xml-for-each-drive"></a>Exemplo de arquivo de diário (XML) para cada unidade

```xml
[BeginUpdateRecord][2016/11/01 21:22:25.379][Type:ActivityRecord]
ActivityId: DriveInfo
DriveState: [BeginValue]
<?xml version="1.0" encoding="UTF-8"?>
<Drive>
   <DriveId>drive-id</DriveId>
   <BitLockerKey>*******</BitLockerKey>
   <ManifestFile>\DriveManifest.xml</ManifestFile>
   <ManifestHash>D863FE44F861AE0DA4DCEAEEFFCCCE68</ManifestHash> </Drive>
[EndValue]
SaveCommandOutput: Completed
[EndUpdateRecord]
```

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>Exemplo de arquivo de diário (JRN) para sessão que registra a trilha de sessões

```
[BeginUpdateRecord][2016/11/02 18:24:14.735][Type:NewJournalFile]
VocabularyVersion: 2013-02-01
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.749][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
LogDirectory: F:\logs
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.754][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
StorageAccountKey: *******
[EndUpdateRecord]
```

## <a name="faq"></a>Perguntas frequentes

### <a name="general"></a>Geral

#### <a name="what-is-waimportexport-tool"></a>O que é a ferramenta WAImportExport?

A ferramenta WAImportExport é a ferramenta de preparação e reparo de unidade que pode ser usada com o serviço de Importação/Exportação do Microsoft Azure. Use essa ferramenta para copiar dados para os discos rígidos que você vai enviar para um datacenter do Azure. Após a conclusão de um trabalho de importação, é possível usar essa ferramenta para reparar os blobs corrompidos, ausentes ou que entraram em conflito com outros blobs. Depois de receber as unidades de um trabalho de exportação concluído, é possível usar essa ferramenta para reparar os arquivos corrompidos ou ausentes nas unidades.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>Como funciona a ferramenta WAImportExport em vários diretórios de origem e discos?

Se o tamanho dos dados for maior do que o tamanho do disco, a ferramenta WAImportExport distribuirá os dados entre os discos de forma otimizada. A cópia dos dados em vários discos pode ser feita em paralelo ou sequencialmente. Não há limite para o número de discos nos quais os dados podem ser gravados simultaneamente. A ferramenta distribuirá os dados com base no tamanho do disco e no tamanho da pasta. Ele selecionará o disco mais otimizado para o tamanho do objeto. Após serem carregados na conta de armazenamento, os dados serão ser convergidos novamente para a estrutura do diretório especificada.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>Onde posso encontrar a versão anterior da ferramenta WAImportExport?

A ferramenta WAImportExport tem todas as funcionalidades que a ferramenta WAImportExport V1 tinha. A ferramenta WAImportExport permite aos usuários especificar várias fontes e gravar em várias unidades. Além disso, é possível gerenciar com facilidade vários locais de origem dos quais os dados precisam ser copiados em um único arquivo CSV. No entanto, caso você precise de suporte SAS ou deseje copiar uma única origem para um único disco, é possível [fazer o download da WAIportport V1 Tool](https://go.microsoft.com/fwlink/?LinkID=301900&amp;clcid=0x409) e consultar [Referência WAImportExport V1](storage-import-export-tool-how-to-v1.md) para obter ajuda sobre o uso do WAImportExport V1.

#### <a name="what-is-a-session-id"></a>O que é uma ID de sessão?

A ferramenta espera que o parâmetro (/id) da sessão de cópia seja o mesmo se a intenção for distribuir os dados entre vários discos. Manter o mesmo nome da sessão de cópia permitirá que o usuário copie dados de um ou vários locais de origem para um ou vários discos/diretórios de destino. A manutenção da mesma id de sessão permite que a ferramenta escolha a cópia dos arquivos onde ela foi deixada na última vez.

No entanto, a mesma sessão de cópia não pode ser usado para importar dados em contas de armazenamento diferentes.

Quando o nome de sessão de cópia for o mesmo entre várias execuções da ferramenta, o arquivo de log (/logdir) e a chave da conta de armazenamento (/sk) também deverá ser igual.

A SessionId pode conter letras, 0 a 9, sublinhado (\_), hífen (-) ou hash (#); e seu comprimento deve ser de 3 a 30.

Por exemplo, sessão-1 ou sessão#1 ou sessão\_1

#### <a name="what-is-a-journal-file"></a>O que é um arquivo de diário?

Sempre que você executar a ferramenta WAImportExport para copiar arquivos no disco rígido, a ferramenta criará uma sessão de cópia. O estado da sessão de cópia é gravado no arquivo de diário. Se uma sessão de cópia for interrompida (por exemplo, devido a uma perda de energia do sistema), ela poderá ser retomada executando a ferramenta novamente e especificando o arquivo de diário na linha de comando.

Para cada disco rígido que você preparar com a Ferramenta de Importação/Exportação do Azure, a ferramenta criará um único arquivo de diário com o nome "&lt;DriveID&gt;.xml", em que DriveID é o número de série associado à unidade lida pela ferramenta no disco. Você precisará dos arquivos de diário de todas as unidades para criar o trabalho de importação. O arquivo de diário também pode ser usado para retomar a preparação da unidade se a ferramenta for interrompida.

#### <a name="what-is-a-log-directory"></a>O que é um diretório de log?

O diretório de log especifica um diretório a ser usado para armazenar logs detalhados, bem como arquivos de manifesto temporários. Se nenhum for especificado, o diretório atual será usado como o diretório de log. Os logs são logs detalhados.

### <a name="prerequisites"></a>Pré-requisitos

#### <a name="what-are-the-specifications-of-my-disk"></a>Quais são as especificações de meu disco?

Um ou mais discos rígidos SATAII ou III ou SSD de 2,5 ou 3,5 polegadas vazios e conectados ao computador de cópia.

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>Como habilitar o BitLocker em meu computador?

Uma maneira simples de verificar é clicando com o botão direito na unidade do sistema. Ele mostrará opções para o BitLocker se o recurso estiver ativado. Se estiver desativado, você não o verá.

![Verificar o BitLocker](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

Este é um artigo sobre [como habilitar o BitLocker](https://technet.microsoft.com/library/cc766295.aspx)

É possível que seu computador não tenha o chip do TPM. Se você não obtiver uma saída usando tpm.msc, examine as próximas perguntas frequentes.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>Como desabilitar o TPM (Trusted Platform Module ) no BitLocker?
> [!NOTE]
> Apenas se não houver TPM nos servidores, você precisará desabilitar a política do TPM. Não é necessário desabilitar o TPM se há um TPM confiável no servidor do usuário. 
> 

Para desabilitar o TPM no BitLocker, consulte as seguintes etapas:<br/>
1. Inicie o **Editor de Política de Grupo** digitando gpedit.msc em um prompt de comando. Se parecer que o **Editor de Política de Grupo** não está disponível, ative o BitLocker primeiro. Consulte as perguntas frequentes anteriores.
2. Abra **Política do Computador Local &gt; Configuração do Computador &gt; Modelos Administrativos &gt; Componentes do Windows&gt; Criptografia de Unidade de Disco BitLocker &gt; Unidades do Sistema Operacional**.
3. Edite a política **Exigir autenticação adicional na inicialização**.
4. Configure a política como **Habilitado** e certifique-se de que **Permitir BitLocker sem um TPM compatível** esteja marcado.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>Como verificar se o .NET 4 ou uma versão posterior está instalada em meu computador?

Todas as versões do Microsoft .NET Framework são instaladas no seguinte diretório: %Windir%\Microsoft.NET\Framework\

Navegue até a parte mencionada acima no computador de destino onde a ferramenta precisa ser executada. Procure o nome da pasta que começa com "v4". A ausência de um diretório como esse significa que o .NET 4 não está instalado em seu computador. Você pode baixar o .NET 4 em seu computador usando [Microsoft .NET Framework 4 (instalador da Web)](https://www.microsoft.com/download/details.aspx?id=17851).

### <a name="limits"></a>limites

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>Quantas unidades eu posso preparar/enviar ao mesmo tempo?

Não há limite no número de discos que a ferramenta pode preparar. No entanto, a ferramenta espera letras de unidade como entradas. Isso causa uma limitação na preparação de 25 discos simultâneos. Um único trabalho pode lidar no máximo com 10 discos por vez. Se você preparar mais de 10 discos visando a mesma conta de armazenamento, os discos poderão ser distribuídos em vários trabalhos.

#### <a name="can-i-target-more-than-one-storage-account"></a>Posso visar mais de uma conta de armazenamento?

Apenas uma conta de armazenamento pode ser enviada por trabalho e em uma única sessão de cópia.

### <a name="capabilities"></a>Funcionalidades

#### <a name="does-waimportexportexe-encrypt-my-data"></a>O WAImportExport.exe criptografa meus dados?

Sim. A criptografia do BitLocker é habilitada e exigida para esse processo.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>Qual será a hierarquia dos meus dados quando ela for exibida na conta de armazenamento?

Embora os dados sejam distribuídos entre os discos, quando os dados são carregados na conta de armazenamento eles são convergidos novamente para a estrutura do diretório que foi especificada no arquivo CSV do conjunto de dados.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>Quantos discos de entrada terão E/S ativa em paralelo quando a cópia estiver em andamento?

A ferramenta distribui os dados entre os discos de entrada com base no tamanho dos arquivos de entrada. Dito isso, o número de discos ativos em paralelo depende completamente da natureza dos dados de entrada. Dependendo do tamanho dos arquivos individuais no conjunto de dados de entrada, um ou mais discos pode mostrar E/S ativa em paralelo. Confira a próxima pergunta para obter mais detalhes.

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>Como a ferramenta distribui os arquivos entre os discos?

A ferramenta de WAImportExport lê e grava arquivos, lote por lote, um lote contém no máximo 100000 arquivos. Isso significa que no máximo 100000 arquivos podem ser gravados em paralelo. Vários discos são gravados simultaneamente se esses 100000 arquivos forem distribuídos em várias unidades. No entanto, o fato de a ferramenta gravar em vários discos simultaneamente ou um único disco depende do tamanho cumulativo do lote. Por exemplo, no caso de arquivos menores, se todos os 10.0000 arquivos couberem em uma única unidade, a ferramenta gravará apenas um disco durante o processamento deste lote.

### <a name="waimportexport-output"></a>Saída de WAImportExport

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>Existem dois arquivos de diário, qual deles devo carregar no Portal do Azure?

**.xml**: para cada disco rígido que você preparar com a Ferramenta WAImportExport, a ferramenta criará um único arquivo de diário com o nome `<DriveID>.xml`, em que DriveID é o número de série associado à unidade lida pela ferramenta no disco. Você precisará dos arquivos de diário de todas as unidades para criar o trabalho de importação no Portal do Azure. Esse arquivo de diário também pode ser usado para retomar a preparação da unidade se a ferramenta for interrompida.

**.jrn**: o arquivo de diário com o sufixo `.jrn` contém o status de todas as sessões de cópia de um disco rígido. Ele também contém as informações necessárias para criar o trabalho de importação. Você deve sempre especificar um arquivo de diário ao executar a ferramenta WAImportExport, bem como uma ID da sessão de cópia.

## <a name="next-steps"></a>Próximas etapas

* [Configurando a Ferramenta de Importação/Exportação do Azure](storage-import-export-tool-setup.md)
* [Definindo propriedades e metadados durante o processo de importação](storage-import-export-tool-setting-properties-metadata-import.md)
* [Fluxo de trabalho de exemplo para preparar discos rígidos para um trabalho de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [Referência rápida para comandos usados frequentemente](storage-import-export-tool-quick-reference.md) 
* [Revisão do status do trabalho com arquivos de log de cópia](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparação de um trabalho de importação](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparação de um trabalho de exportação](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Solucionando problemas da Ferramenta de Importação/Exportação do Azure](storage-import-export-tool-troubleshooting-v1.md)
