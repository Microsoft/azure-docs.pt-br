---
title: "Reparando um trabalho de exportação do serviço de Importação/Exportação do Azure — v1 | Microsoft Docs"
description: "Saiba como reparar um trabalho de exportação criado e executado usando o serviço de Importação/Exportação do Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 728e2a42-04ce-4be8-9375-e9e2bc6827a5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 57ab58fa1fd8371d0b6f019f94bb162bcc1e0e43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="repairing-an-export-job"></a>Reparação de um trabalho de exportação
Após a conclusão de um trabalho de exportação, você poderá executar a Ferramenta de Importação/Exportação do Microsoft Azure local para:  
  
1.  Baixar todos os arquivos que o serviço de Importação/Exportação do Azure não conseguiu exportar.  
  
2.  Validar se os arquivos na unidade foram exportados corretamente.  
  
Você deve ter conectividade com o Armazenamento do Azure para usar essa funcionalidade.  
  
O comando para reparar um trabalho de importação é **RepairExport**.

## <a name="repairexport-parameters"></a>Parâmetros de RepairExport

Os seguintes parâmetros podem ser especificados com **RepairExport**:  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|**/r:<RepairFile\>**|Obrigatório. Caminho até o arquivo de reparo, que controla o progresso do reparo e permite que você retome um reparo interrompido. Cada unidade deve ter um, e somente um, arquivo de reparo. Ao iniciar o reparo de uma determinada unidade, você passará no caminho até um arquivo de reparo que ainda não existe. Para retomar um reparo interrompido, você deve passar no nome de um arquivo de reparo existente. O arquivo de reparo que corresponde à unidade de destino deve sempre ser especificado.|  
|**/logdir:<LogDirectory\>**|Opcional. O diretório de log. Os arquivos de log detalhados serão gravados nesse diretório. Se nenhum diretório de log for especificado, o diretório atual será usado como o diretório de log.|  
|**/d:<TargetDirectory\>**|Obrigatório. O diretório a ser validado e reparado. Normalmente é o diretório raiz da unidade de exportação, mas também pode ser um compartilhamento de arquivos de rede que contém uma cópia dos arquivos exportados.|  
|**/bk:<BitLockerKey\>**|Opcional. Você deve especificar a chave do BitLocker se quiser que a ferramenta desbloqueie uma unidade criptografada na qual os arquivos exportados foram armazenados.|  
|**/sn:<StorageAccountName\>**|Obrigatório. O nome da conta de armazenamento do trabalho de exportação.|  
|**/sk:<StorageAccountKey\>**|**Necessário** somente se uma SAS do contêiner não for especificada. A chave de conta da conta de armazenamento do trabalho de exportação.|  
|**/csas:<ContainerSas\>**|**Necessário** somente se a chave da conta de armazenamento não for especificada. O SAS do contêiner para acessar os blobs associados ao trabalho de exportação.|  
|**/CopyLogFile:<DriveCopyLogFile\>**|Obrigatório. O caminho até o arquivo de log de cópia da unidade. O arquivo é gerado pelo serviço de Importação/Exportação do Windows Azure e pode ser baixado do armazenamento de blobs associado ao trabalho. O arquivo de log de cópia contém informações sobre blobs com falha ou arquivos que devem ser reparados.|  
|**/ManifestFile:<DriveManifestFile\>**|Opcional. O caminho até o arquivo de manifesto da unidade de exportação. Esse arquivo é gerado pelo serviço de Importação/Exportação do Windows Azure e armazenado na unidade de exportação e, opcionalmente, em um blob na conta de armazenamento associada ao trabalho.<br /><br /> O conteúdo dos arquivos na unidade de exportação será verificado com os hashes MD5 contidos nesse arquivo. Todos os arquivos determinados como corrompidos serão baixados e reescritos nos diretórios de destino.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Usando o modo RepairExport para corrigir exportações com falha  
Você pode usar a Ferramenta de Importação/Exportação do Azure para baixar arquivos cuja exportação falhou. O arquivo de log da cópia conterá uma lista de arquivos que não foram exportados devido a alguma falha.  
  
As causas das falhas de exportação incluem as seguintes possibilidades:  
  
-   Unidades danificadas  
  
-   A chave da conta de armazenamento mudou durante o processo de transferência  
  
Para executar a ferramenta no modo **RepairExport**, primeiro você precisa conectar a unidade que contém os arquivos exportados ao seu computador. Em seguida, execute a Ferramenta de Importação/Exportação do Azure, especificando o caminho até essa unidade com o parâmetro `/d`. Você também precisa especificar o caminho até arquivo de log de cópia da unidade que você baixou. O exemplo de linha de comando abaixo executa a ferramenta para reparar todos os arquivos que não foram exportados devido a alguma falha:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Veja a seguir um exemplo de um arquivo de log de cópia mostrando que um bloco no blob falhou durante a exportação:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
O arquivo de log da cópia indica que ocorreu uma falha enquanto o serviço de Importação/Exportação do Windows Azure baixava um dos blocos de blob para o arquivo na unidade de exportação. Os outros componentes do arquivo foram baixados com êxito, e o tamanho do arquivo foi definido corretamente. Nesse caso, a ferramenta abrirá o arquivo na unidade, baixará o bloco da conta de armazenamento e o gravará no intervalo de arquivos, a partir do deslocamento 65536 com comprimento 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Usando o RepairExport para validar o conteúdo da unidade  
Você também pode usar a Importação/Exportação do Azure com a opção **RepairExport** para validar se o conteúdo na unidade está correto. O arquivo de manifesto em cada unidade de exportação contém MD5s para o conteúdo da unidade.  
  
O serviço de Importação/Exportação do Azure também pode salvar os arquivos de manifesto em uma conta de armazenamento durante o processo de exportação. O local dos arquivos de manifesto está disponível por meio da operação [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) após a conclusão do trabalho. Consulte [Formato de arquivo de manifesto do serviço de Importação/Exportação](storage-import-export-file-format-metadata-and-properties.md) para saber mais sobre o formato de um arquivo de manifesto da unidade.  
  
O seguinte exemplo mostra como executar a Ferramenta de Importação/Exportação do Azure com os parâmetros **/ManifestFile** e **/CopyLogFile**:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Veja a seguir um exemplo de um arquivo de manifesto:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
Depois de concluir o processo de reparo, a ferramenta lerá cada arquivo referenciado no arquivo de manifesto e verificará a integridade do arquivo com os hashes MD5. No caso do manifesto acima, ele percorrerá os seguintes componentes.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

Qualquer componente que falhar na verificação será baixado pela ferramenta e reescrito no mesmo arquivo na unidade.  
  
## <a name="next-steps"></a>Próximas etapas
 
* [Configurando a Ferramenta de Importação/Exportação do Azure](storage-import-export-tool-setup-v1.md)   
* [Preparação de discos rígidos para um trabalho de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisão do status do trabalho com arquivos de log de cópia](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de um trabalho de importação](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Solucionando problemas da Ferramenta de Importação/Exportação do Azure](storage-import-export-tool-troubleshooting-v1.md)
