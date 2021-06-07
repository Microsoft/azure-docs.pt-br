---
title: Rastrear e registrar Azure Data Box, Azure Data Box Heavy eventos para a ordem de exportação | Microsoft Docs
description: Descreve como rastrear e registrar eventos em vários estágios de sua Azure Data Box e Azure Data Box Heavy ordem de exportação.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 3a915ac8de83a5e183660ec4a3d05044eafff4a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94337501"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-export-orders"></a>Rastreamento e log de eventos para seus Azure Data Box e Azure Data Box Heavy ordens de exportação

Uma ordem de exportação Data Box ou Data Box Heavy passa pelas seguintes etapas: ordem, configuração, cópia de dados, retorno e eliminação de dados. Correspondendo a cada etapa na ordem, você pode executar várias ações para controlar o acesso ao pedido, auditar os eventos, acompanhar o pedido e interpretar os vários logs gerados.

Este artigo descreve detalhadamente os vários mecanismos ou ferramentas disponíveis para rastrear e auditar ordens de exportação para Data Box ou Data Box Heavy. As informações neste artigo se aplicam a ambos, Data Box e Data Box Heavy. Nas seções subsequentes, todas as referências a Data Box também se aplicam a Data Box Heavy.

A tabela a seguir mostra um resumo das etapas Data Box ordem de exportação e as ferramentas disponíveis para acompanhar e auditar o pedido durante cada etapa.

| Estágio de ordem de exportação Data Box       | Ferramenta para acompanhar e auditar                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Criar pedido               | [Configurar o controle de acesso na ordem por meio do RBAC do Azure](#set-up-access-control-on-the-order) <br> [Habilitar log detalhado na ordem](#enable-verbose-log-in-the-order)                                                    |
| Pedido processado            | [Acompanhar o pedido](#track-the-order) <ul><li> Portal do Azure </li><li> Site da transportadora de envio </li><li>Notificações por email</ul> |
| Configurar o dispositivo              | Acesso de credenciais de dispositivo [logs de atividade](#query-activity-logs-during-setup) registrados              |
| Cópia de dados do dispositivo        | [Examinar logs de cópia](#copy-log) <br> [Examine os logs detalhados](#verbose-log) antes de copiar dados            |
| Eliminação de dados do dispositivo   | [Exibir cadeia de logs de custódia](#get-chain-of-custody-logs-after-data-erasure) , incluindo logs de auditoria e histórico de pedidos                |


## <a name="set-up-access-control-on-the-order"></a>Configurar o controle de acesso na ordem

Você pode controlar quem pode acessar seu pedido quando a ordem é criada pela primeira vez. Configure as funções do Azure em vários escopos para controlar o acesso ao pedido de Data Box. Uma função do Azure determina o tipo de acesso – leitura-gravação, somente leitura e gravação de leitura para um subconjunto de operações.

As duas funções que podem ser definidas para o serviço de Azure Data Box são:

- **Leitor de data Box** -tem acesso somente leitura a uma (s) ordem (ões), conforme definido pelo escopo. Eles só podem exibir detalhes de um pedido. Eles não podem acessar nenhum outro detalhe relacionado às contas de armazenamento ou editar os detalhes do pedido, como endereço e assim por diante.
- **Colaborador de data Box** – só poderá criar um pedido para transferir dados para uma determinada conta de armazenamento *se eles já tiverem acesso de gravação a uma conta de armazenamento*. Se eles não tiverem acesso a uma conta de armazenamento, eles não poderão nem mesmo criar uma ordem de Data Box para copiar dados para a conta. Essa função não define nenhuma permissão relacionada à conta de armazenamento nem concede acesso a contas de armazenamento.  

Para restringir o acesso a um pedido, você pode:

- Atribua uma função em um nível de pedido. O usuário só tem essas permissões, conforme definido pelas funções para interagir com essa Data Box ordem específica apenas e nada mais.
- Atribua uma função no nível do grupo de recursos, o usuário tem acesso a todos os Data Box pedidos em um grupo de recursos.

Para obter mais informações sobre o uso do RBAC do Azure sugerido, consulte [práticas recomendadas para o RBAC do Azure](../role-based-access-control/best-practices.md).

## <a name="enable-verbose-log-in-the-order"></a>Habilitar log detalhado na ordem

Ao colocar uma ordem de exportação para Data Box, você tem a opção de habilitar a coleta de um log detalhado. Aqui está a tela do pedido em que você pode habilitar o log detalhado:

![Selecionar opção de exportação](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

Quando você seleciona a opção **incluir log detalhado** , um arquivo de log detalhado é gerado ao copiar os dados da sua conta de armazenamento do Azure. Esse log contém uma lista de todos os arquivos que foram exportados com êxito.

Para obter mais informações sobre a ordem de exportação, consulte [criar uma ordem de exportação para data Box](data-box-deploy-export-ordered.md)

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

- Cada logon no Data Box é registrado em tempo real. No entanto, essas informações só estarão disponíveis na [cadeia de logs de auditoria de custódia](#chain-of-custody-audit-logs) depois que o pedido for concluído com êxito.

## <a name="view-logs-during-data-copy"></a>Exibir logs durante a cópia de dados

Antes de copiar dados do seu Data Box, você pode baixar e examinar o log de *cópia* e o *log detalhado* para os dados que foram copiados para o data box. Esses logs são gerados quando os dados são copiados de sua conta de armazenamento no Azure para seu Data Box. 

### <a name="copy-log"></a>Copiar log

Antes de copiar os dados de seu Data Box, baixe o log de cópia da página **conectar e copiar** .

Aqui está um exemplo de saída do *log de cópias* quando não houve erros e todos os arquivos foram copiados durante a cópia de dados do Azure para o dispositivo data box.

```output
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
  <TotalFiles_Blobs>5521</TotalFiles_Blobs>
  <FilesErrored>0</FilesErrored>
</CopyLog>
``` 
    
Aqui está um exemplo de saída quando o *log de cópia* tem erros e alguns dos arquivos falharam ao copiar do Azure.

```output
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>    
```

Você tem as seguintes opções para ajudar a exportar esses arquivos: 

- É possível transferir os arquivos que não puderam ser copiados pela rede. 
- Se o tamanho dos dados for maior do que a capacidade utilizável do dispositivo, ocorrerá uma cópia parcial e todos os arquivos que não tiverem sido copiados serão listados nesse log. Você pode usar esse log como um XML de entrada para criar uma ordem de Data Box e copiar esses arquivos.

### <a name="verbose-log"></a>Log detalhado

O *log detalhado* contém uma lista de todos os arquivos que foram exportados com êxito da conta de Armazenamento do Azure. O log também contém o tamanho do arquivo e a computação da soma de verificação.

O log detalhado tem as informações no seguinte formato:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Aqui está um exemplo de saída do log detalhado. 

```powershell
  <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
  </File>
  ``````

Os logs detalhados também são copiados para a conta de armazenamento do Azure. Por padrão, os logs são gravados em um contêiner denominado `copylog`. Os logs são armazenados com a seguinte convenção de nomenclatura:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

O caminho do log de cópia também é exibido na folha **visão geral** do Portal.

<!-- add a screenshot-->

Você pode usar esses logs para verificar se os arquivos copiados do Azure correspondem aos dados que foram copiados para o servidor local. 

Use o arquivo de log detalhado:

- Para verificar os nomes reais e o número de arquivos que foram copiados do Data Box.
- Para verificar os tamanhos reais dos arquivos.
- Para verificar se o *crc64* corresponde a uma cadeia de caracteres diferente de zero. Uma computação de CRC (verificação de redundância cíclica) é feita durante a exportação do Azure. O CRCs da exportação e depois que os dados são copiados de Data Box para o servidor local podem ser comparados. Uma incompatibilidade de CRC indica que os arquivos correspondentes não foram copiados corretamente.


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Obter cadeia de logs de custódia após a eliminação de dados

Depois que os dados são apagados dos discos de Data Box de acordo com as diretrizes NIST SP 800-88 revisão 1, a cadeia de logs de custódia está disponível. Esses logs incluem a cadeia de logs de auditoria de custódia e o histórico do pedido. Os arquivos de BOM ou de manifesto também são copiados com os logs de auditoria.

### <a name="chain-of-custody-audit-logs"></a>Cadeia de logs de auditoria de custódia

A cadeia de logs de auditoria de custódia contém informações sobre como ligar e acessar compartilhamentos no Data Box ou Data Box Heavy quando ele está fora do datacenter do Azure. Esses logs estão localizados em: `storage-account/azuredatabox-chainofcustodylogs`

Aqui está um exemplo do log de auditoria de um Data Box:

```output
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
