---
title: Rastrear e registrar o Azure Data Box, eventos pesada de caixa de dados do Azure | Microsoft Docs
description: Descreve como controlar e registrar eventos em vários estágios do seu pedido do Azure Data Box e pesada de caixa de dados do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: ba08cd7fdecda99c04d5bb1007b3e5f61cd1bd5c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446761"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Acompanhamento e log de eventos para o Azure Data Box e pesada de caixa de dados do Azure

Uma ordem de caixa de dados ou dados caixa pesada percorre as etapas a seguir: pedidos, configurar, dados copiam, retornam, carregar no Azure e verifique se e eliminação de dados. Correspondendo a cada etapa na ordem, você pode executar várias ações para controlar o acesso à ordem, os eventos de auditoria, controlar a ordem e interpretar os vários logs que são gerados.

A tabela a seguir mostra um resumo de como as etapas de ordem de caixa de dados ou dados caixa pesada e as ferramentas disponíveis para controlar e auditar a ordem durante cada etapa.

| Estágio de ordem de caixa de dados       | Ferramenta para controlar e auditar                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Criar pedido               | [Configurar o controle de acesso na ordem por meio do RBAC](#set-up-access-control-on-the-order)                                                    |
| Ordem de processamento            | [Controlar a ordem](#track-the-order) por meio de <ul><li> Portal do Azure </li><li> Site da transportadora de envio </li><li>Notificações por email</ul> |
| Configurar o dispositivo              | Credenciais de dispositivo acesso conectado [os logs de atividade](#query-activity-logs-during-setup)                                              |
| Cópia de dados para o dispositivo        | [Modo de exibição *error.xml* arquivos](#view-error-log-during-data-copy) para cópia de dados                                                             |
| Preparar para o envio            | [Inspecione os arquivos de BOM](#inspect-bom-during-prepare-to-ship) ou os arquivos de manifesto no dispositivo                                      |
| Carregamento de dados do Azure       | [Revisão *copylogs* ](#review-copy-log-during-upload-to-azure) para erros durante a dados de carregamento no data center do Azure                         |
| Eliminação de dados do dispositivo   | [Exibir a cadeia de custódia logs](#get-chain-of-custody-logs-after-data-erasure) incluindo logs de auditoria e histórico de pedidos                |

Este artigo descreve em detalhes os vários mecanismos ou ferramentas disponíveis para controlar e auditar o Data Box caixa de dados pesados, a ordem. As informações neste artigo se aplica, caixa de dados tanto dados caixa pesada. Nas seções subsequentes, todas as referências a caixa de dados também se aplicam a caixa de dados pesados.

## <a name="set-up-access-control-on-the-order"></a>Configurar o controle de acesso na ordem de

Você pode controlar quem pode acessar seu pedido quando a ordem é criada. Configure funções de controle de acesso baseado em função (RBAC) em vários escopos para controlar o acesso para o pedido do Data Box. Uma função RBAC determina o tipo de acesso – leitura / gravação, somente leitura, leitura / gravação a um subconjunto de operações.

As duas funções que podem ser definidas para o serviço do Azure Data Box são:

- **Leitor de dados do caixa** -têm acesso somente leitura a um pedido, conforme definido pelo escopo. Eles só podem exibir detalhes de um pedido. Eles não podem acessar todos os outros detalhes relacionados às contas de armazenamento ou editar os detalhes do pedido, como endereço e assim por diante.
- **Colaborador da caixa de dados** -só pode criar um pedido para transferir dados para uma determinada conta de armazenamento *se eles já têm acesso de gravação a uma conta de armazenamento*. Se não tiverem acesso a uma conta de armazenamento, eles ainda não é possível criar um pedido do Data Box para copiar dados para a conta. Essa função não define qualquer conta de armazenamento relacionados a permissões, nem concede acesso a contas de armazenamento.  

Para restringir o acesso a um pedido, você pode:

- Atribua uma função em um nível de ordem. O usuário tem apenas essas permissões, conforme definido pelas funções de interagir com esse pedido do Data Box específico somente e nada mais.
- Atribuir uma função no nível do grupo de recursos, o usuário tem acesso a todas as ordens de caixa de dados dentro de um grupo de recursos.

Para obter mais informações sobre o uso do RBAC sugerida, consulte [práticas recomendadas para RBAC](../role-based-access-control/overview.md#best-practice-for-using-rbac).

## <a name="track-the-order"></a>Acompanhar o pedido

Você pode acompanhar o seu pedido por meio do portal do Azure e o site da transportadora envio. Os seguintes mecanismos estão em vigor para controlar a ordem de caixa de dados a qualquer momento:

- Para acompanhar o pedido quando o dispositivo estiver no seu local ou datacenter do Azure, vá para seu **pedido do Data Box > Visão geral** no portal do Azure.

    ![Exibir status do pedido e o número de controle](media/data-box-logs/overview-view-status-1.png)

- Para controlar a ordem, enquanto o dispositivo está em trânsito, vá para o site de operadora regional, por exemplo, site de no-break nos EUA. Forneça o número de controle associado ao seu pedido.
- Caixa de dados também envia notificações por email sempre que as alterações de status de ordem com base em emails fornecidos quando o pedido foi criado. Para obter uma lista de todos os status de ordem de caixa de dados, consulte [exibir o status do pedido](data-box-portal-admin.md#view-order-status). Para alterar as configurações de notificação associadas ao pedido, consulte [editar detalhes da notificação](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Logs de atividade de consulta durante a instalação

- O Data Box é recebido em sua empresa em um estado bloqueado. Você pode usar as credenciais do dispositivo disponíveis no portal do Azure para o pedido.  

    Quando uma caixa de dados estiver configurada, você precisa saber quem todos acessou as credenciais do dispositivo. Para descobrir quem acessou o **as credenciais do dispositivo** folha, você pode consultar os logs de atividade.  Qualquer ação que envolve acessar **detalhes do dispositivo > credenciais** folha é registrada nos logs de atividade como `ListCredentials` ação.

    ![Consultar logs de atividade](media/data-box-logs/query-activity-log-1.png)

- Cada entrada na caixa de dados é registrada em tempo real. No entanto, essas informações só estão disponíveis na [logs de auditoria](#audit-logs) depois que o pedido for concluído com êxito.

## <a name="view-error-log-during-data-copy"></a>Exibir o log de erros durante a cópia de dados

Durante a cópia de dados a caixa de dados ou dados caixa pesada, um arquivo de erro é gerado se houver algum problema com os dados que estão sendo copiados.

### <a name="errorxml-file"></a>Error.xml file

Certifique-se de que os trabalhos de cópia tem concluído sem erros. Se houver erros durante o processo de cópia, baixe os logs a partir de **Connect e copie** página.

- Se você copiou um arquivo que não tem 512 bytes alinhados em uma pasta de disco gerenciado em sua caixa de dados, o arquivo não é carregado como um blob de páginas para sua conta de armazenamento de preparo. Você verá um erro nos logs. Remova o arquivo e copiar um arquivo que tenha 512 bytes alinhados.
- Se você copiou um VHDX, ou um VHD dinâmico ou um VHD diferencial (não há suporte para esses arquivos), você verá um erro nos logs.

Aqui está um exemplo do *error.xml* para erros diferentes ao copiar para discos gerenciados.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Aqui está um exemplo do *error.xml* para erros diferentes ao copiar blobs de página.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Aqui está um exemplo do *error.xml* para erros diferentes ao copiar para blobs de blocos.

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

Aqui está um exemplo do *error.xml* para erros diferentes ao copiar arquivos do Azure.

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

Em cada um dos casos acima, resolva os erros antes de prosseguir para a próxima etapa. Para obter mais informações sobre os erros recebidos durante a cópia de dados para Data Box por meio de protocolos SMB ou NFS, acesse [problemas de caixa de solucionar problemas de dados e dados caixa pesada](data-box-troubleshoot.md). Para obter informações sobre erros recebidos durante a cópia de dados para a caixa de dados por meio de REST, acesse [problemas de armazenamento de Blob de caixa de dados solucionar](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Inspecionar BOM durante a preparação para o envio

Durante a preparação para o envio, uma lista de arquivos conhecida como lista de materiais (BOM) ou arquivo de manifesto é criado.

- Use esse arquivo para verificar em relação os nomes reais e o número de arquivos que foram copiados para o Data Box.
- Use esse arquivo para verificar em relação os tamanhos reais dos arquivos.
- Verifique se que o *crc64* corresponde a uma cadeia de caracteres diferente de zero. <!--A null value for crc64 indicates that there was a reparse point error)-->

Para obter mais informações sobre os erros recebidos durante a preparação enviar, vá para [problemas de caixa de solucionar problemas de dados e dados caixa pesada](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>Arquivo de manifesto ou BOM

A BOM ou o arquivo de manifesto contém a lista de todos os arquivos são copiados para o dispositivo Data Box. O arquivo da BOM tem nomes de arquivo e os tamanhos correspondentes, bem como a soma de verificação. Um arquivo BOM separado é criado para blobs de blocos, blobs de páginas, arquivos do Azure, para cópia por meio das APIs REST e para a cópia de discos gerenciados, a na caixa de dados. Você pode baixar os arquivos BOM da web local da interface do usuário do dispositivo durante a preparação para o envio.

Esses arquivos também residem no dispositivo Data Box e são carregados para a conta de armazenamento associada no data center do Azure.

### <a name="bom-file-format"></a>Formato de arquivo da BOM

Arquivo de manifesto ou BOM tem o seguinte formato geral:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Aqui está um exemplo de um manifesto gerado quando os dados foram copiados para o compartilhamento de blob de bloco na caixa de dados.

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

A BOM ou arquivos de manifesto também são copiados para a conta de armazenamento do Azure. Você pode usar a BOM ou arquivos para verificar se os arquivos carregados para o Azure coincidem com os dados que foi copiados para a caixa de dados de manifesto.

## <a name="review-copy-log-during-upload-to-azure"></a>Examine o log de cópia durante o carregamento do Azure

Durante o carregamento de dados do Azure, uma *copylog* é criado.

### <a name="copylog"></a>Copylog

Para cada ordem processada, o serviço do Data Box cria *copylog* na conta de armazenamento associada. O *copylog* tem o número total de arquivos que foram carregados e o número de arquivos que apresentaram durante os dados copiar na caixa de dados de sua conta de armazenamento do Azure.

Uma computação de redundância cíclica (CRC) é feita durante o carregamento no Azure. O CRC da cópia de dados e depois que o upload de dados são comparados. Uma incompatibilidade CRC indica que os arquivos correspondentes falhou ao carregar.

Por padrão, os logs são gravados em um contêiner denominado `copylog`. Os logs são armazenados com a seguinte convenção de nomenclatura:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

O caminho copylog também é exibido na **visão geral** folha para o portal.

![Caminho para copylog na folha de visão geral, quando concluído](media/data-box-logs/copy-log-path-1.png)

O exemplo a seguir descreve o formato geral de um arquivo copylog para carregar de uma caixa de dados que foi concluída com êxito:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

Upload para o Azure também pode ser concluída com erros.

![Caminho para copylog na folha de visão geral, quando foi concluída com erros](media/data-box-logs/copy-log-path-2.png)

Aqui está um exemplo de um copylog em que o upload foi concluído com erros:

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
Aqui está um exemplo de um `copylog` onde os contêineres que não cumpre as convenções de nomenclatura do Azure foram renomeados durante o carregamento de dados do Azure.

Os novos nomes exclusivos para contêineres estão no formato `DataBox-GUID` e os dados para o contêiner serão colocados no novo contêiner renomeado. O `copylog` Especifica o antigo e o novo nome de contêiner para contêiner.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Aqui está um exemplo de um `copylog` onde os blobs ou arquivos que não cumpre as convenções de nomenclatura do Azure, foram renomeados durante o carregamento de dados do Azure. O novo blob ou nomes de arquivo são convertidos em digest de SHA256 de caminho relativo ao contêiner e são carregados para o caminho com base no tipo de destino. O destino pode ser blobs de blocos, blobs de página ou arquivos do Azure.

O `copylog` Especifica o antigo e o novo nome de blob ou arquivo e o caminho no Azure.

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

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Cadeia de logs de custódia é exibida após a eliminação de dados

Depois que os dados são apagados dos discos do Data Box, de acordo com as diretrizes do NIST SP 800-88 revisão 1, a cadeia de custódia logs estão disponíveis. Esses logs incluem os logs de auditoria e o histórico de pedidos. A BOM ou arquivos de manifesto também são copiados com os logs de auditoria.

### <a name="audit-logs"></a>Logs de auditoria

Os logs de auditoria contêm informações sobre como ligar e compartilhar o acesso na caixa de dados ou dados caixa pesada quando ele estiver fora do datacenter do Azure. Esses logs estão localizados em: `storage-account/azuredatabox-chainofcustodylogs`

Aqui está um exemplo de log de auditoria de uma caixa de dados:

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

Histórico de pedidos está disponível no portal do Azure. Se o pedido for concluído e a limpeza de dispositivo (eliminação dos discos de dados) for concluída, vá para o seu pedido de dispositivo e navegue até **detalhes da ordem**. ** Baixe o histórico de pedidos** opção está disponível. Para obter mais informações, consulte [baixar o histórico de pedidos](data-box-portal-admin.md#download-order-history).

Se você rolar o histórico de pedidos, você verá:

- Operadora de informações de controle de seu dispositivo.
- Eventos com *SecureErase* atividade. Esses eventos correspondem a eliminação dos dados no disco.
- Links de log de caixa de dados. Os caminhos para o *logs de auditoria*, *copylogs*, e *BOM* arquivos são apresentados.

Aqui está um exemplo do log de histórico do pedido do portal do Azure:

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

- Saiba como [solucionar problemas em sua caixa de dados e dados caixa pesada](data-box-troubleshoot.md).
