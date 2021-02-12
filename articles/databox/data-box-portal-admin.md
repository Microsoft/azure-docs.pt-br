---
title: Gerenciar Azure Data Box, Azure Data Box Heavy via portal do Azure | Microsoft Docs
description: Descreve como usar o portal do Azure para administrar seu Azure Data Box e Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 46a18cb2b6e1682427d5674be28b240f35b120fe
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678654"
---
# <a name="use-the-azure-portal-to-administer-your-azure-data-box-and-azure-data-box-heavy"></a>Use o portal do Azure para administrar seu Azure Data Box e Azure Data Box Heavy

Este artigo se aplica a Azure Data Box e Azure Data Box Heavy. Este artigo descreve alguns dos fluxos de trabalho e tarefas de gerenciamento complexos que podem ser executados no dispositivo Azure Data Box. Você pode gerenciar o dispositivo Data Box por meio da portal do Azure ou por meio da interface do usuário da Web local.

Este artigo se concentra nas tarefas que você pode executar usando o portal do Azure. Use o portal do Azure para gerenciar pedidos, gerenciar Data Box dispositivo e acompanhar o status do pedido à medida que ele prossegue até a conclusão.

## <a name="cancel-an-order"></a>Cancelar um pedido

Talvez seja necessário cancelar um pedido por vários motivos depois de colocá-lo.

Para as ordens de importação e exportação, você só pode cancelar a ordem antes de ser processada. Depois que o pedido for processado e o dispositivo Data Box estiver preparado, você não poderá cancelar o pedido.

Execute as seguintes etapas para cancelar um pedido.

1.  Vá para **Visão Geral > Cancelar**.

    ![Comando cancelar na guia Visão geral de um pedido](media/data-box-portal-admin/portal-admin-cancel-command.png)

2.  Preencha um motivo para cancelar o pedido.  

    ![Caixa de diálogo cancelar pedido](media/data-box-portal-admin/portal-admin-cancel-order-dbox.png)

3.  Depois que o pedido for cancelado, o portal atualizará o status do pedido e o exibirá como **Cancelado**.

## <a name="clone-an-order"></a>Clonar um pedido

A clonagem é útil em determinadas situações. Por exemplo, você usou Data Box para transferir alguns dados. À medida que mais dados são gerados, você precisa usar outro dispositivo de Data Box para transferir esses dados para o Azure. Nesse caso, você pode apenas clonar a mesma ordem.

Execute as etapas a seguir para clonar uma ordem de importação.

1.  Vá para **Visão geral > Clonar**. 

    ![Clonar comando na guia Visão geral de um pedido](media/data-box-portal-admin/portal-admin-clone-command.png)

2.  Todos os detalhes do pedido permanecem inalterados. O nome do pedido é o nome original do pedido acrescido de *-Clone*. Marque a caixa de seleção para confirmar que você leu as informações de privacidade. Clique em **Criar**.

O clone é criado em alguns minutos e a portal é atualizado para mostrar o novo pedido.


## <a name="delete-order"></a>Excluir pedido

Você talvez queira excluir um pedido quando ele é concluído. O pedido contém suas informações pessoais, como nome, endereço e informações de contato. Essas informações pessoais são excluídas quando o pedido é excluído.

Só é possível excluir pedidos que tenham sido concluídos ou cancelados. Execute as etapas a seguir para excluir um pedido.

1. Vá para **todos os recursos**. Pesquise seu pedido.

2. Clique no pedido que você deseja excluir e vá para **Visão Geral**. Na barra de comandos, clique em **Excluir**.

    ![Comando delete na guia Visão geral de um pedido](media/data-box-portal-admin/portal-admin-delete-command.png)

3. Insira o nome do pedido quando solicitado a confirmar a exclusão de um pedido. Clique em **Excluir**.

## <a name="download-shipping-label"></a>Faça o download da etiqueta de remessa

Talvez seja necessário baixar o rótulo de envio se a exibição de E-Ink do seu Data Box não estiver funcionando e não exibir o rótulo de remessa de retorno. Não há nenhuma exibição de tinta na Data Box Heavy, portanto, esse fluxo de trabalho não se aplica a Data Box Heavy.

Execute as etapas a seguir para baixar uma etiqueta de remessa.

1.  Vá para **Visão geral > Baixar etiqueta de remessa**. Essa opção fica disponível somente depois que o dispositivo é enviado. 

    ![Faça o download da etiqueta de remessa](media/data-box-portal-admin/portal-admin-download-shipping-label.png)

2.  Essa opção baixa a etiqueta de remessa de devolução a seguir. Salve o rótulo e imprima-o. Dobre e insira o rótulo na manga vazia do dispositivo. Verifique se a etiqueta está visível. Remova todos os adesivos do dispositivo referentes ao envio anterior.

    ![Etiqueta de remessa de exemplo](media/data-box-portal-admin/portal-admin-example-shipping-label.png)

## <a name="edit-shipping-address"></a>Editar endereço para entrega

Talvez seja necessário editar o endereço de envio depois que o pedido é feito. Essa opção só está disponível até o envio do disco. Depois que o disco é enviado, essa opção não fica mais disponível.

Execute as etapas a seguir para editar o pedido.

1. Vá para **Detalhes do pedido > Editar endereço para entrega**.

    ![Editar comando de endereço de envio em detalhes da ordem](media/data-box-portal-admin/portal-admin-edit-shipping-address-command.png)

2. Edite e valide o endereço para entrega e, em seguida, salve as alterações.

    ![Caixa de diálogo Editar endereço de envio](media/data-box-portal-admin/portal-admin-edit-shipping-address-dbox.png)

## <a name="edit-notification-details"></a>Editar detalhes da notificação

Talvez seja necessário alterar os usuários que recebem os emails de status do pedido. Por exemplo, um usuário precisa ser informado quando o dispositivo é entregue ou recolhido. Outro usuário talvez precise ser informado quando a cópia de dados é concluída para que possa verificar se os dados estão na conta de armazenamento do Azure antes de excluí-los da origem. Nesses casos, você pode editar os detalhes da notificação.

Execute as etapas a seguir para editar os detalhes de notificação.

1. Vá para **Detalhes do pedido > Editar detalhes da notificação**.

    ![Editar o comando de detalhes de notificação nos detalhes da ordem](media/data-box-portal-admin/portal-admin-edit-notification-details-command.png)

2. Agora você pode editar os detalhes de notificação e salvar as alterações.
 
    ![Caixa de diálogo Editar detalhes da notificação](media/data-box-portal-admin/portal-admin-edit-notification-details-dbox.png)


## <a name="download-order-history"></a>Baixar histórico de pedidos

Depois que o pedido do Data Box estiver completo, os dados nos discos do dispositivo serão apagados. Quando a limpeza de dispositivo for concluída, você poderá baixar o histórico de pedidos no portal do Azure.

Execute as etapas a seguir para baixar o histórico de pedidos.

1. No seu pedido do Data Box, vá para **Visão Geral**. Verifique se o pedido está completo. Se o pedido estiver completo e a limpeza for concluída, vá para **Detalhes da ordem**. A opção **Baixar histórico de pedidos** está disponível.

    ![Baixar histórico de pedidos](media/data-box-portal-admin/portal-admin-download-order-history.png)

2. Clique em **Baixar histórico de pedidos**. O histórico baixado inclui um registro de logs de controle da transportadora. Haverá dois conjuntos de log correspondentes aos dois nós em um dispositivo Data Box Heavy. Se você rolar para baixo até a parte inferior desse log, poderá ver os links para:
    
   - **Copiar logs** – tenha a lista de arquivos que apresentaram erros durante a cópia de dados do data box para sua conta de armazenamento do Azure (ordem de importação) ou da sua conta de armazenamento para a data Box (ordem de exportação).
   - **Logs de auditoria** -contêm informações sobre como ligar os compartilhamentos de data box e acesso quando o data Box está fora do datacenter do Azure.
   - **Arquivos da bom na ordem de importação** -tenha a lista de arquivos (também conhecido como o manifesto do arquivo) que você pode baixar durante a **preparação para o envio** e tem nomes de arquivo, tamanhos de arquivo e as somas de verificação de arquivo.
   - **Logs detalhados na ordem de exportação** -tenha a lista de arquivos com nomes de arquivo, tamanhos de arquivo e computação de soma de verificação quando os dados foram copiados das contas de armazenamento do Azure para o data box.

   Aqui está um exemplo de um histórico de pedidos de uma ordem de importação.

    ```output
    -------------------------------
    Microsoft Data Box Order Report
    -------------------------------
    Name                                               : DataBoxTestOrder                              
    StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
    DeviceType                                         : DataBox                                           
    -------------------
    Data Box Activities
    -------------------
    Time(UTC)                 | Activity                       | Status          | Description  
    
    10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
    11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
    11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
    11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
    11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
    11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
    11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
    11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
    11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
    11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
    11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
    11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
    11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
    11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
    11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
    11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
    1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
    1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
    1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
    1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
    1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
    1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
    1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
    1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
    1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
    1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
    1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
    1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
    1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
    1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
    1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
    1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
    1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
    1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
    2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
    2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
    2/4/2019 8:01:10 PM       | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

    ------------------
    Data Box Log Links
    ------------------

    Account Name         : Gus                                                       
    Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
    Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
    BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
    ```

    Você pode, em seguida, ir para sua conta de armazenamento e exibir os logs de cópia.

   ![Os logs de cópia de uma conta de armazenamento](media/data-box-portal-admin/portal-admin-storage-account-copy-logs.png)

   Você também pode exibir a cadeia de logs de custódia, que incluem os logs de auditoria e os arquivos da BOM.

   ![Cadeia de logs de custódia para uma conta de armazenamento](media/data-box-portal-admin/portal-admin-storage-account-chain-of-custody-logs.png)

## <a name="view-order-status"></a>Exibir status do pedido

Quando o status do dispositivo for alterado no portal, você será notificado por email.

### <a name="statuses-for-import-order"></a>Status da ordem de importação

Aqui estão os status de uma ordem de importação.

|Status do pedido |Descrição |
|---------|---------|
|Encomendado     | Pedido feito com êxito. <br>Se o dispositivo estiver disponível, a Microsoft identificará um dispositivo para envio e o preparará. <br> Se o dispositivo não estiver disponível imediatamente, a ordem será processada quando o dispositivo ficar disponível. O pedido pode levar de vários dias a alguns meses para ser processado. Se o pedido não puder ser atendido em 90 dias, o pedido será cancelado e você será notificado.         |
|Processado     | O processamento de pedidos foi concluído. De acordo com seu pedido, o dispositivo está preparado para o envio no data center.         |
|Expedido     | O pedido foi enviado. Use a ID de acompanhamento exibida no pedido no portal para acompanhar a remessa.        |
|Entregue     | A remessa foi entregue no endereço especificado no pedido.        |
|Recolhido     |A remessa de devolução foi recolhida e verificada pela transportadora.         |
|Recebido     | O dispositivo foi recebido e verificado no datacenter do Azure. <br> Depois que a remessa for inspecionada, o upload do dispositivo será iniciado.      |
|Cópia de dados     | Cópia de dados em andamento. Acompanhe o progresso de cópia do pedido no portal do Azure. <br> Aguarde até que a cópia de dados seja concluída. |
|Concluído       |Pedido concluído com êxito.<br> Verifique se os dados estão no Azure antes de excluir os dados locais dos servidores.         |
|Concluído com erros| A cópia de dados foi concluída, mas apresentou erros. <br> Examine os logs de cópia usando o caminho fornecido no portal do Azure. Consulte [exemplos de logs de cópia quando o upload for concluído com erros](./data-box-logs.md#upload-completed-with-errors).   |
|Concluído com avisos| A cópia de dados foi concluída, mas os dados foram modificados. Os dados tinham erros não críticos de BLOB ou de nome de arquivo que foram corrigidos alterando os nomes de arquivo ou BLOB. <br> Examine os logs de cópia usando o caminho fornecido no portal do Azure. Anote as modificações em seus dados. Consulte [exemplos de logs de cópia quando o upload for concluído com avisos](./data-box-logs.md#upload-completed-with-warnings).   |
|Canceled            |Pedido cancelado. <br> Você cancelou a ordem ou o serviço cancelou a ordem após um erro. Se o pedido não puder ser atendido em 90 dias, o pedido também será cancelado e você será notificado.     |
|Limpar | Os dados nos discos do dispositivo foram apagados. A limpeza do dispositivo é considerada concluída quando o histórico de pedidos fica disponível no portal do Azure.|

### <a name="statuses-for-export-order"></a>Status da ordem de exportação

Aqui estão os status de uma ordem de exportação.

|Status do pedido |Descrição |
|---------|---------|
|Encomendado     | Uma ordem de exportação foi colocada com êxito. <br>Se o dispositivo estiver disponível, a Microsoft identificará um dispositivo para envio e o preparará. <br> Se o dispositivo não estiver disponível imediatamente, o pedido apenas será processado quando o dispositivo estiver disponível. O pedido pode levar de vários dias a alguns meses para ser processado. Se o pedido não puder ser processado em até 90 dias, ele será cancelado e você será notificado.         |
|Canceled            |Pedido cancelado. <br> Você cancelou o pedido (você pode cancelar somente antes de o pedido ser processado) ou um erro foi encontrado e o serviço cancelou o pedido. Se o pedido não puder ser processado em até 90 dias, ele também será cancelado e você será notificado.     |
|Processado     | O processamento de pedidos foi concluído. De acordo com seu pedido, o dispositivo está preparado para cópia de dados no datacenter. Os compartilhamentos de dispositivo são criados.         |
|Cópia de dados em andamento     | A cópia de dados das contas de armazenamento do Azure especificadas para o dispositivo está em andamento. Acompanhe o progresso de cópia do pedido no portal do Azure. <br> Aguarde até que a cópia de dados seja concluída. |
|Cópia concluída     | A cópia de dados das contas de armazenamento do Azure especificadas para o dispositivo foi concluída. Um arquivo de log detalhado (se a opção tiver sido habilitada na ordem) e um log de cópia será criado na sua conta de armazenamento. O log detalhado contém as informações sobre todos os arquivos (nome, caminho, soma de verificação de computação) que são copiados para o dispositivo. O log de cópia contém o resumo do processo de cópia, incluindo uma lista de arquivos que não puderam ser copiados devido a erros. <br> Os dados da conta de armazenamento permanecem como estão. |
|Cópia concluída com erros| A cópia de dados foi concluída, mas apresentou erros. <br> Examine os logs de cópia na conta de armazenamento do Azure usando o caminho fornecido na portal do Azure. Consulte [exemplos de logs de cópia quando o download foi concluído com erros](./data-box-logs.md#upload-completed-with-errors).   |
|Cópia concluída com avisos| A cópia de dados da conta de armazenamento do Azure foi concluída, mas os dados tinham erros não críticos. <br> Examine os logs de cópia usando o caminho fornecido no portal do Azure. Anote os erros não críticos. Consulte [exemplos de logs de cópia quando o download foi concluído com avisos](./data-box-logs.md#upload-completed-with-warnings).   |
|A cópia falhou com erros| Falha na cópia de dados da conta de armazenamento do Azure e a ordem é encerrada. Um dispositivo não será enviado. <br> Examine os logs de cópia na conta de armazenamento do Azure usando o caminho fornecido na portal do Azure. Consulte [exemplos de logs de cópia quando o download falhou com erros](./data-box-logs.md#upload-completed-with-errors).   |
|Expedido     |O pedido foi enviado. Use a ID de acompanhamento exibida no pedido no portal para acompanhar a remessa.        |
|Entregue     |A remessa foi entregue no endereço especificado no pedido.        |
|Recolhido     |A remessa de devolução foi recolhida e verificada pela transportadora.         |
|Recebido     | O dispositivo foi recebido e verificado no datacenter do Azure. <br> A remessa é inspecionada.      |
|Concluído           |A ordem foi concluída.     |
|Limpar | Os dados nos discos do dispositivo foram apagados. A limpeza do dispositivo é considerada concluída quando o histórico de pedidos fica disponível no portal do Azure.|

> [!NOTE]
> Se o trabalho de cópia para exportar dados das contas de armazenamento do Azure para Data Box for concluído com erros ou avisos, o dispositivo ainda será enviado. Somente no caso de uma falha de cópia, a ordem é encerrada e o dispositivo não é enviado.


Se você estiver usando o envio autogerenciado, depois que a cópia for concluída e antes de receber o dispositivo, você verá os seguintes Estados (em vez daqueles mencionados na tabela anterior):

|Status do pedido |Descrição |
|---------|---------|
|Pronto para retirada no datacenter do Azure      |O dispositivo está pronto para ser selecionado no datacenter do Azure.        |
|Recolhido    |Você selecionou o dispositivo.         |
|Pronto para receber no datacenter do Azure     |O dispositivo está pronto para ser recebido no datacenter do Azure.        |
|Recebido     |O dispositivo foi recebido no datacenter do Azure.      |





## <a name="next-steps"></a>Próximas etapas

- Saiba como [solucionar problemas de data box e data Box Heavy](data-box-troubleshoot.md).