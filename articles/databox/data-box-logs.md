---
title: Rastrear e registrar Azure Data Box, Azure Data Box Heavy eventos para a ordem de importação | Microsoft Docs
description: Descreve como rastrear e registrar eventos em vários estágios de sua Azure Data Box e Azure Data Box Heavy ordem de importação.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: a9304936f746b82b59550d62e8b60a9e0035d188
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147931"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-import-order"></a>Rastreamento e log de eventos para seu pedido de importação do Azure Data Box e Azure Data Box Heavy

Uma ordem de importação Data Box ou Data Box Heavy passa pelas seguintes etapas: ordem, configuração, cópia de dados, retorno, carregamento no Azure e verificação e eliminação de dados. Correspondendo a cada etapa na ordem, você pode executar várias ações para controlar o acesso ao pedido, auditar os eventos, acompanhar o pedido e interpretar os vários logs gerados.

A tabela a seguir mostra um resumo das etapas de pedidos de importação do Data Box ou do Data Box Heavy, bem como as ferramentas disponíveis para acompanhá-los e auditá-los durante cada etapa.

| Etapa de pedido de importação do Data Box       | Ferramenta para acompanhar e auditar                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Criar pedido               | [Configurar o controle de acesso na ordem por meio do RBAC do Azure](#set-up-access-control-on-the-order)                                                    |
| Pedido processado            | [Acompanhar o pedido](#track-the-order) <ul><li> Portal do Azure </li><li> Site da transportadora de envio </li><li>Notificações por email</ul> |
| Configurar o dispositivo              | Acesso de credenciais de dispositivo [logs de atividade](#query-activity-logs-during-setup) registrados                                              |
| Cópia de dados para o dispositivo        | [Exibir arquivos de *error.xml*](#view-error-log-during-data-copy) para cópia de dados                                                             |
| Preparar para o envio            | [Inspecione os arquivos da bom](#inspect-bom-during-prepare-to-ship) ou os arquivos de manifesto no dispositivo                                      |
| Upload de dados no Azure       | [Examinar os logs de cópia](#review-copy-log-during-upload-to-azure) em busca de erros durante o carregamento de dados no datacenter do Azure                         |
| Eliminação de dados do dispositivo   | [Exibir cadeia de logs de custódia](#get-chain-of-custody-logs-after-data-erasure) , incluindo logs de auditoria e histórico de pedidos                |

Este artigo descreve detalhadamente os vários mecanismos ou ferramentas disponíveis para rastrear e auditar Data Box ou Data Box Heavy ordem de importação. As informações neste artigo se aplicam a ordens de importação, Data Box e Data Box Heavy. Nas seções subsequentes, todas as referências a Data Box também se aplicam a Data Box Heavy.

## <a name="set-up-access-control-on-the-order"></a>Configurar o controle de acesso na ordem

Você pode controlar quem pode acessar seu pedido quando a ordem é criada pela primeira vez. Configure as funções do Azure em vários escopos para controlar o acesso ao pedido de Data Box. Uma função do Azure determina o tipo de acesso – leitura-gravação, somente leitura e gravação de leitura para um subconjunto de operações.

As duas funções que podem ser definidas para o serviço de Azure Data Box são:

- **Leitor de data Box** -tem acesso somente leitura a uma (s) ordem (ões), conforme definido pelo escopo. Eles só podem exibir detalhes de um pedido. Eles não podem acessar nenhum outro detalhe relacionado às contas de armazenamento ou editar os detalhes do pedido, como endereço e assim por diante.
- **Colaborador de data Box** – só poderá criar um pedido para transferir dados para uma determinada conta de armazenamento *se eles já tiverem acesso de gravação a uma conta de armazenamento*. Se eles não tiverem acesso a uma conta de armazenamento, eles não poderão nem mesmo criar uma ordem de Data Box para copiar dados para a conta. Essa função não define nenhuma permissão relacionada à conta de armazenamento nem concede acesso a contas de armazenamento.  

Para restringir o acesso a um pedido, você pode:

- Atribua uma função em um nível de pedido. O usuário só tem essas permissões, conforme definido pelas funções para interagir com essa Data Box ordem específica apenas e nada mais.
- Atribua uma função no nível do grupo de recursos, o usuário tem acesso a todos os Data Box pedidos em um grupo de recursos.

Para obter mais informações sobre o uso do RBAC do Azure sugerido, consulte [práticas recomendadas para o RBAC do Azure](../role-based-access-control/best-practices.md).

## <a name="track-the-order"></a>Acompanhar o pedido

Você pode acompanhar o seu pedido por meio do portal do Azure e do site da transportadora. Os mecanismos a seguir estão em vigor para controlar a ordem de Data Box a qualquer momento:

- Para acompanhar o pedido quando o dispositivo estiver no datacenter do Azure ou em seu local, acesse sua **ordem de Data Box > visão geral** em portal do Azure.

    ![Exibir status e acompanhamento do pedido](media/data-box-logs/overview-view-status-1.png)

- Para acompanhar o pedido enquanto o dispositivo estiver em trânsito, acesse o site da operadora regional, por exemplo, o site do UPS nos EUA. Forneça o número de rastreamento associado ao seu pedido.
- Data Box também envia notificações por email sempre que o status do pedido é alterado com base nos emails fornecidos quando o pedido foi criado. Para obter uma lista de todos os status do pedido de Data Box, consulte [Exibir status da ordem](data-box-portal-admin.md#view-order-status). Para alterar as configurações de notificação associadas ao pedido, consulte [Editar detalhes da notificação](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Consultar logs de atividade durante a instalação

- Seu Data Box chega em seu local em um estado bloqueado. Você pode usar as credenciais de dispositivo disponíveis no portal do Azure para seu pedido.  

    Quando um Data Box é configurado, talvez seja necessário saber quem todos acessaram as credenciais do dispositivo. Para descobrir quem acessou a folha **credenciais do dispositivo** , você pode consultar os logs de atividade.  Qualquer ação que envolva o acesso aos **detalhes do dispositivo > folha credenciais** será registrada nos logs de atividade como `ListCredentials` ação.

    ![Consultar logs de atividade](media/data-box-logs/query-activity-log-1.png)

- Cada logon no Data Box é registrado em tempo real. No entanto, essas informações só estarão disponíveis nos [logs de auditoria](#audit-logs) depois que o pedido for concluído com êxito.

## <a name="view-error-log-during-data-copy"></a>Exibir log de erros durante a cópia de dados

Durante a cópia de dados para Data Box ou Data Box Heavy, um arquivo de erro será gerado se houver algum problema com os dados que estão sendo copiados.

### <a name="errorxml-file"></a>Arquivo de Error.xml

Certifique-se de que os trabalhos de cópia foram concluídos sem erros. Se houver erros durante o processo de cópia, faça o download dos logs na página **Conectar e copiar**.

- Se você copiou um arquivo que não é 512 bytes alinhado a uma pasta de disco gerenciado no seu Data Box, o arquivo não será carregado como blob de páginas para sua conta de armazenamento de preparo. Você verá um erro nos logs. Remova o arquivo e copiar um arquivo que tenha 512 bytes alinhados.
- Se você copiou um VHDX, ou um VHD dinâmico, ou um VHD diferencial (não há suporte para esses arquivos), verá um erro nos logs.

Aqui está um exemplo de *error.xml* para erros diferentes ao copiar para discos gerenciados.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Aqui está um exemplo de *error.xml* para erros diferentes ao copiar para BLOBs de página.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Aqui está um exemplo de *error.xml* para erros diferentes ao copiar para BLOBs de blocos.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Aqui está um exemplo de *error.xml* para erros diferentes ao copiar para arquivos do Azure.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

Em cada um dos casos acima, resolva os erros antes de prosseguir para a próxima etapa. Para obter mais informações sobre os erros recebidos durante a cópia de dados para Data Box por meio de protocolos SMB ou NFS, vá para [solucionar problemas data box e data Box Heavy problemas](data-box-troubleshoot.md). Para obter informações sobre erros recebidos durante a cópia de dados para Data Box por meio do REST, acesse [solucionar problemas data Box o armazenamento de BLOBs](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Inspecionar a BOM durante a preparação para o envio

Durante a preparação para o envio, é criada uma lista de arquivos conhecidos como a BOM (lista de materiais) ou o arquivo de manifesto.

- Use esse arquivo para verificar os nomes reais e o número de arquivos que foram copiados para o Data Box.
- Use esse arquivo para verificar os tamanhos reais dos arquivos.
- Verifique se *crc64* corresponde a uma cadeia de caracteres diferente de zero. <!--A null value for crc64 indicates that there was a reparse point error)-->

Para obter mais informações sobre os erros recebidos durante a preparação para o envio, vá para [solucionar problemas data box e data Box Heavy problemas](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>BOM ou arquivo de manifesto

O arquivo de manifesto ou BOM contém a lista de todos os arquivos que são copiados para o dispositivo Data Box. O arquivo da BOM tem nomes de arquivo e os tamanhos correspondentes, bem como a soma de verificação. Um arquivo de BOM separado é criado para os blobs de blocos, blobs de páginas, arquivos do Azure, para cópia por meio das APIs REST e para a cópia para discos gerenciados no Data Box. Você pode baixar os arquivos da BOM da interface do usuário da Web local do dispositivo durante a preparação para o envio.

Esses arquivos também residem no dispositivo Data Box e são carregados para a conta de armazenamento associada no datacenter do Azure.

### <a name="bom-file-format"></a>Formato de arquivo da BOM

O arquivo de manifesto ou BOM tem o seguinte formato geral:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Aqui está um exemplo de um manifesto gerado quando os dados foram copiados para o compartilhamento de blobs de blocos no Data Box.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

Os arquivos de BOM ou de manifesto também são copiados para a conta de armazenamento do Azure. Você pode usar os arquivos de BOM ou de manifesto para verificar se os arquivos carregados no Azure correspondem aos dados que foram copiados para o Data Box.

## <a name="review-copy-log-during-upload-to-azure"></a>Examinar o log de cópia durante o carregamento para o Azure

Durante o carregamento de dados no Azure, um log de cópia é criado.

### <a name="copy-log"></a>Copiar log

Para cada pedido que é processado, o serviço de Data Box cria o log de cópia na conta de armazenamento associada. O log de cópia tem o número total de arquivos que foram carregados e o número de arquivos que tiveram um erro durante a cópia de dados do Data Box para sua conta de armazenamento do Azure.

Uma computação de CRC (verificação de redundância cíclica) é feita durante o carregamento no Azure. O CRCs da cópia de dados e depois do carregamento de dados são comparados. Uma incompatibilidade de CRC indica que os arquivos correspondentes não puderam ser carregados.

Por padrão, os logs são gravados em um contêiner denominado `copylog`. Os logs são armazenados com a seguinte convenção de nomenclatura:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

O caminho do log de cópia também é exibido na folha **visão geral** do Portal.

![Caminho para copiar o log na folha de visão geral quando concluído](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Upload concluído com êxito 

O exemplo a seguir descreve o formato geral de um log de cópia para um upload de Data Box que foi concluído com êxito:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Carregamento concluído com erros 

O upload para o Azure também pode ser concluído com erros.

![Caminho para copiar o log na folha de visão geral quando concluído com erros](media/data-box-logs/copy-log-path-2.png)

Aqui está um exemplo de um log de cópia em que o upload foi concluído com erros:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
### <a name="upload-completed-with-warnings"></a>Carregamento concluído com avisos

O upload para o Azure é concluído com avisos se seus dados tivessem nomes de contêiner/blob/arquivo que não estavam em conformidade com as convenções de nomenclatura do Azure e os nomes foram modificados para carregar os dados no Azure.

![Caminho para copiar o log na folha de visão geral quando concluído com avisos](media/data-box-logs/copy-log-path-3.png)

Aqui está um exemplo de um log de cópia em que os contêineres que não estavam em conformidade com as convenções de nomenclatura do Azure foram renomeados durante o carregamento de dados para o Azure.

Os novos nomes exclusivos para contêineres estão no formato `DataBox-GUID` e os dados do contêiner são colocados no novo contêiner renomeado. O log de cópia especifica o antigo e o novo nome do contêiner para o contêiner.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Aqui está um exemplo de um log de cópia em que os BLOBs ou arquivos que não estão em conformidade com as convenções de nomenclatura do Azure foram renomeados durante o carregamento de dados para o Azure. Os novos nomes de BLOB ou arquivo são convertidos para o resumo SHA256 do caminho relativo para o contêiner e são carregados para o caminho com base no tipo de destino. O destino pode ser blobs de blocos, blobs de páginas ou arquivos do Azure.

O `copylog` especifica o antigo e o novo nome de arquivo ou BLOB, e o caminho no Azure.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Obter cadeia de logs de custódia após a eliminação de dados

Depois que os dados são apagados dos discos de Data Box de acordo com as diretrizes NIST SP 800-88 revisão 1, a cadeia de logs de custódia está disponível. Esses logs incluem os logs de auditoria e o histórico do pedido. Os arquivos de BOM ou de manifesto também são copiados com os logs de auditoria.

### <a name="audit-logs"></a>Logs de auditoria

Os logs de auditoria contêm informações sobre como ligar e acessar compartilhamentos no Data Box ou Data Box Heavy quando estiver fora do datacenter do Azure. Esses logs estão localizados em: `storage-account/azuredatabox-chainofcustodylogs`

Aqui está um exemplo do log de auditoria de um Data Box:

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Baixar histórico de pedidos

O histórico de pedidos está disponível em portal do Azure. Se o pedido estiver concluído e a limpeza do dispositivo (eliminação de dados dos discos) estiver concluída, vá para a ordem do dispositivo e navegue até **detalhes do pedido**. A opção **Baixar histórico de pedidos** está disponível. Para obter mais informações, consulte [baixar histórico de pedidos](data-box-portal-admin.md#download-order-history).

Se você rolar o histórico do pedido, verá:

- Informações de rastreamento de portador para seu dispositivo.
- Eventos com a atividade *SecureErase* . Esses eventos correspondem à eliminação dos dados no disco.
- Data Box links de log. Os caminhos para os *logs de auditoria*, *os logs de cópia* e os arquivos de *bom* são apresentados.

Aqui está um exemplo do log do histórico de pedidos do portal do Azure:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Próximas etapas

- Saiba como [solucionar problemas em seu data box e data Box Heavy](data-box-troubleshoot.md).
