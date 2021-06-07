---
title: Reparando um trabalho de importação do serviço de Importação/Exportação do Azure — v1 | Microsoft Docs
description: Saiba como reparar um trabalho de importação criado e executado usando o serviço de Importação/Exportação do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: b0eeb73ffb5436284f4f23cef943db0a3307a2b2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564561"
---
# <a name="repairing-an-import-job"></a>Reparação de um trabalho de importação
O serviço de Importação/Exportação do Microsoft Azure pode não copiar alguns arquivos ou partes de um arquivo para o serviço Blob do Windows Azure. Alguns motivos para as falhas incluem:  
  
-   Arquivos corrompidos  
  
-   Unidades danificadas  
  
-   A chave da conta de armazenamento mudou durante a transferência do arquivo.  
  
Você pode executar a ferramenta de Importação/Exportação do Microsoft Azure com os arquivos de log de cópia do trabalho de importação. A ferramenta carrega os arquivos ausentes ou partes de um arquivo para sua conta de armazenamento do Windows Azure para concluir o trabalho de importação.  
  
## <a name="repairimport-parameters"></a>Parâmetros de RepairImport

Os seguintes parâmetros podem ser especificados com **RepairImport**: 
  
| Parâmetro | Descrição |  
|-|-|  
|**/r:**<repairfile\>|**Necessário.** Caminho até o arquivo de reparo, que controla o progresso do reparo e permite que você retome um reparo interrompido. Cada unidade deve ter um, e somente um, arquivo de reparo. Ao iniciar um reparo para uma determinada unidade, passe o caminho para um arquivo de reparo, que ainda não existe. Para retomar um reparo interrompido, você deve passar no nome de um arquivo de reparo existente. Sempre especifique o arquivo de reparo correspondente à unidade de destino.|  
|**/logdir:**<LogDirectory\>|**Opcional.** O diretório de log. Os arquivos de log detalhados são gravados nesse diretório. Se nenhum diretório de log for especificado, o diretório atual será usado como o diretório de log.|  
|**/d:**<TargetDirectories\>|**Necessário.** Um ou mais diretórios separados por ponto e vírgula que contêm os arquivos originais que foram importados. A unidade de importação também pode ser usada, mas não será necessária se locais alternativos de arquivos originais estiverem disponíveis.|  
|**/BK:**<BitLockerKey\>|**Opcional.** Especifique a chave do BitLocker se desejar que a ferramenta desbloqueie uma unidade criptografada onde os arquivos originais estão disponíveis.|  
|**/sn:**<StorageAccountName\>|**Necessário.** O nome da conta de armazenamento do trabalho de importação.|  
|**/SK:**<StorageAccountKey\>|**Necessário** se e somente se uma SAS do contêiner não for especificada. A chave de conta da conta de armazenamento do trabalho de importação.|  
|**/csas:**<ContainerSas\>|**Necessário** se e somente se a chave da conta de armazenamento não for especificada. O SAS do contêiner para acessar os blobs associados ao trabalho de importação.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Necessário.** Caminho até o arquivo de log de cópia da unidade (log detalhado ou de erro). O arquivo é gerado pelo serviço de Importação/Exportação do Windows Azure e pode ser baixado do armazenamento de blobs associado ao trabalho. O arquivo de log de cópia contém informações sobre arquivos ou blobs com falha que devem ser reparados.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Opcional.** Caminho para um arquivo de texto usado para resolver ambiguidades se você tiver vários arquivos com o mesmo nome que você estava importando no mesmo trabalho. Na primeira vez que a ferramenta é executada, ela pode preencher esse arquivo com todos os nomes ambíguos. As execuções posteriores da ferramenta usam esse arquivo para resolver as ambiguidades.|  
  
## <a name="using-the-repairimport-command"></a>Usando o comando RepairImport  
Para reparar a dados de importação transmitindo os dados pela rede, você deve especificar os diretórios que contêm os arquivos originais importados usando o parâmetro `/d`. Especifique também o arquivo de log de cópia que você baixou de sua conta de armazenamento. Uma linha de comando típica para reparar um trabalho de importação com falhas parciais é semelhante a:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
No exemplo a seguir de um arquivo de log de cópia, uma parte de 64 K de um arquivo foi corrompida na unidade enviada para o trabalho de importação. Como essa falha é a única indicada, o restante dos BLOBs no trabalho foi importado com êxito.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Quando esse log de cópia for passado para a Ferramenta de Importação/Exportação do Azure, a ferramenta tenta concluir a importação desse arquivo copiando o conteúdo ausente pela rede. Seguindo o exemplo acima, a ferramenta procura o arquivo original `\animals\koala.jpg` nos dois diretórios `C:\Users\bob\Pictures` e `X:\BobBackup\photos`. Se o arquivo `C:\Users\bob\Pictures\animals\koala.jpg` existir, a Ferramenta de Importação/Exportação do Azure copiará o intervalo de dados ausente no blob correspondente `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Resolvendo conflitos ao usar RepairImport  
Em algumas situações, a ferramenta pode não conseguir localizar ou abrir o arquivo necessário por um dos seguintes motivos: o arquivo não pôde ser encontrado, o arquivo não está acessível, o nome do arquivo é ambíguo ou o conteúdo do arquivo não está mais correto.  
  
Um erro ambíguo pode ocorrer se a ferramenta estiver tentando localizar `\animals\koala.jpg` e houver um arquivo com esse nome em `C:\Users\bob\pictures` e `X:\BobBackup\photos` . Ou seja, `C:\Users\bob\pictures\animals\koala.jpg` e `X:\BobBackup\photos\animals\koala.jpg` existem nas unidades do trabalho de importação.  
  
A opção `/PathMapFile` permite que você resolva esses erros. Você pode especificar o nome do arquivo, que contém a lista de arquivos que a ferramenta não pôde identificar corretamente. O exemplo de linha de comando a seguir preenche `9WM35C2V_pathmap.txt`:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Em seguida, a ferramenta gravará os caminhos do arquivo problemático em `9WM35C2V_pathmap.txt`, um em cada linha. Por exemplo, o arquivo pode conter as seguintes entradas após a execução do comando:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Para cada arquivo na lista, você deve tentar localizar e abrir o arquivo para garantir que ele esteja disponível para a ferramenta. Se você quiser dizer à ferramenta explicitamente onde encontrar um arquivo, modifique o arquivo de mapa de caminho e adicione o caminho a cada arquivo na mesma linha, separado por um caractere de tabulação:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Depois de disponibilizar os arquivos necessários para a ferramenta, ou atualizar o arquivo de mapa do caminho, execute novamente a ferramenta para concluir o processo de importação.  
  
## <a name="next-steps"></a>Próximas etapas
 
* [Configurando a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup-v1.md)   
* [Preparando discos rígidos para um trabalho de importação](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
* [Revisão do status do trabalho com arquivos de log de cópia](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de um trabalho de exportação](./storage-import-export-tool-repairing-an-export-job-v1.md)