---
title: Tutorial sobre como devolver o Microsoft Azure Data Box Disk | Microsoft Docs
description: Usar este tutorial para aprender como enviar o Azure Data Box Disk para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: ca42532fe6ba954180ce06ed0e3561e42f1fb447
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424241"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Tutorial: Devolver o Azure Data Box Disk e verificar o upload de dados para o Azure

Este é o último tutorial da série: Implantar o Azure Data Box Disk. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box Disk

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você concluiu o [Tutorial: copiar dados para o Azure Data Box Disk e verificar](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Devolver o Data Box Disk

1. Após a conclusão da validação de dados, desconecte os discos. Remova os cabos de conexão.
2. Encapsule todos os discos e os cabos de conexão com plástico bolha e coloque-os na caixa de remessa. Poderá haver encargos se os acessórios estiverem ausentes.
    - Reutilize a embalagem da remessa inicial.  
    - É recomendável que empacotar os discos usando um plástico bolha bem ajustado.
    - Verifique se que o ajuste é firme para reduzir qualquer movimentos dentro da caixa.

As próximas etapas são determinadas pelo local em você está devolvendo o dispositivo.

### <a name="pick-up-in-us-canada"></a>Retirada nos EUA, Canadá

Execute as seguintes etapas se estiver devolvendo o dispositivo nos EUA ou Canadá.

1. Use a etiqueta de remessa de devolução localizada na capa de plástico transparente que está afixada à caixa. Se o rótulo estiver danificado ou tiver sido perdido:
    - Vá para **Visão geral > Baixar etiqueta de remessa**.

        ![Faça o download da etiqueta de remessa](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Essa ação faz o download de uma etiqueta de remessa de devolução, conforme mostrado abaixo.

        ![Etiqueta de remessa de exemplo](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Afixe o rótulo ao dispositivo.

2. Lacre a caixa de envio e verifique se a etiqueta de remessa de devolução está visível.
3. Agende uma retirada com a UPS. Para agendar uma retirada:

    - Ligue para a UPS local (linha gratuita específica do país/região).
    - Em sua chamada, mencione a remessa inversa, conforme mostrado na etiqueta impressa de número de controle.
    - Se o número de controle não está entre aspas, o serviço de no-break exigirá que você pague um encargo adicional durante a retirada.
    - Em vez de agendar a retirada, você também pode descartar o Data Box Disk no local mais próximo de redistribuição.


### <a name="pick-up-in-europe"></a>Retirada na Europa

Execute as seguintes etapas se estiver devolvendo o dispositivo na Europa.

1. Use a etiqueta de remessa de devolução localizada na capa de plástico transparente que está afixada à caixa. Se o rótulo estiver danificado ou tiver sido perdido:
    - Vá para **Visão geral > Baixar etiqueta de remessa**.

        ![Faça o download da etiqueta de remessa](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Essa ação faz o download de uma etiqueta de remessa de devolução, conforme mostrado abaixo.

        ![Etiqueta de remessa de exemplo](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Afixe o rótulo ao dispositivo.

2. Lacre a caixa de envio e verifique se a etiqueta de remessa de devolução está visível.
3. Se você estiver devolvendo o dispositivo na Europa com a DHL, solicite a retirada da DHL visitando o site e especificando o número da carta de porte aéreo.
4. Acesse o site da DHL Express do país/região e escolha **Agendar a recolha pelo serviço de correio > Remessa de eReturn**.

    ![Devolução de remessa da DHL](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Especifique o número da carta de porte e clique em **Agendar retirada** para organizar a retirada.

      ![Agendar retirada](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Retirada na região do Pacífico Asiático

Essa região inclui instruções para retirada no Japão, na Coreia, na Austrália e em Singapura.

#### <a name="pick-up-in-australia"></a>Retirada na Austrália

Datacenters do Azure na Austrália têm uma notificação de segurança adicional. Todas as remessas de entrada devem ter uma notificação avançada. Execute as seguintes etapas para retirada na Austrália.

1. Envie um email para `adbops@microsoft.com` para solicitar a etiqueta de remessa com a ID exclusiva de entrada ou o código TAU. Faça a solicitação com pelo menos 3 dias com antecedência à data de remessa planejada para receber a etiqueta a tempo.
2. O assunto do email deve ser – *Solicitação de etiqueta de remessa reversa com o código TAU*. Inclua os seguintes detalhes no email: 

    - Nome do pedido
    - Endereço
    - Nome de contato

#### <a name="pick-up-in-japan"></a>Retirada no Japão

1. Escreva as informações de nome e endereço da sua empresa na nota de consignação como suas informações de remetente.
2. Envie um email para a Quantium Solutions usando o modelo de email a seguir.

    - Se a nota de consignação do Japan Post Chakubarai não tiver sido incluída ou estiver ausente, indique isso nesse email. A Quantium Solutions Japan solicitará ao Japan Post que traga a nota de consignação após a retirada.
    - Caso você tenha vários pedidos, envie um email para garantir a retirada individual.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```

3. Receba um email de confirmação da Quantium Solutions depois de agendar uma retirada. O email de confirmação também inclui informações sobre a nota de consignação do Chakubarai.

Se necessário, você poderá contatar o Suporte da Quantium Solutions (em japonês) com as seguintes informações: 

- Email：Customerservice.JP@quantiumsolutions.com 
- Telefone: 03-5755-0150 

#### <a name="pick-up-in-korea"></a>Retirada na Coreia do Sul

1. Inclua a nota de consignação de devolução.
2. Para solicitar a retirada quando houver nota de consignação:
    1. Ligue para a linha direta da *Quantium Solutions International* em 070-8231 1418 durante o horário de expediente (das 10h às 17h, de segunda a sexta-feira). Mencione *Retirada do Microsoft Azure* e o número da solicitação de serviço para providenciar uma retirada.  
    2. Se a linha direta estiver ocupada, envie um email para `microsoft@rocketparcel.com` com o assunto *Retirada do Microsoft Azure* e o número da solicitação de serviço como referência.
    3. Se a transportadora não chegar para a coleta, ligue para a linha direta da *Quantium Solutions International* para providências alternativas. 
    4. Você receberá um email de confirmação para o agendamento da retirada.
3. Siga esta etapa somente se não houver nota de consignação. Para solicitar retirada:
    1. Ligue para a linha direta da *Quantium Solutions International* em 070-8231 1418 durante o horário de expediente (das 10h às 17h, de segunda a sexta-feira). Mencione *Retirada do Microsoft Azure* e o número da solicitação de serviço para providenciar uma retirada. Especifique que você precisa de uma nova nota de consignação para providenciar uma retirada. Forneça o remetente (cliente), as informações de destinatário (datacenter do Azure) e o número de referência (número da solicitação de serviço). 
    2. Se a linha direta estiver ocupada, envie um email para `microsoft@rocketparcel.com` com o assunto *Retirada do Microsoft Azure* e o número da solicitação de serviço como referência.
    3. Se a transportadora não chegar para a coleta, ligue para a linha direta da *Quantium Solutions International* para providências alternativas. 
    4. Você recebe uma confirmação verbal se a solicitação for feita por telefone.

### <a name="pick-up-in-singapore"></a>Retirada em Singapura

1. Imprima a etiqueta de remessa e anexe à caixa. Se o rótulo estiver danificado ou tiver sido perdido:
    - Vá para **Visão geral > Baixar etiqueta de remessa**.

        ![Faça o download da etiqueta de remessa](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Essa ação faz o download de uma etiqueta de remessa de devolução, conforme mostrado abaixo.

        ![Etiqueta de remessa de exemplo](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Afixe o rótulo ao dispositivo. Certifique-se que a etiqueta está visível.

2. Para solicitar retirada:
    - Ligue para a linha direta do **SingPost** pelo número **6845 6485** no horário comercial (das 9h às 17h, de segunda-feira a sexta-feira).  
    - Mencione *Retirada do Microsoft Azure* e o número da solicitação de serviço (número de rastreamento na etiqueta de remessa de devolução) para providenciar uma retirada. 
    - Você receberá uma confirmação verbal para o agendamento da retirada. 
    - Se a transportadora não chegar para a coleta, ligue para o **SingPost** pelo número **6845 6485** para providências alternativas. 
3. Passe para a transportadora. 


## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Depois que os discos forem recolhidos pelo serviço de correio, o status do pedido no portal de atualizações será exibido como **Recolhidos**. Uma ID de rastreamento também é exibida.

![Discos escolhidos](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Quando a Microsoft receber e verificar o disco, o status do trabalho será atualizado para **Recebido**. 

![Discos recebidos](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Os dados são copiados automaticamente quando os discos são conectados a um servidor no datacenter do Azure. Dependendo do tamanho dos dados, a operação de cópia poderá demorar de algumas horas a dias para ser concluída. Você pode monitorar o andamento do trabalho de cópia no portal.

Depois que a cópia for concluída, o status do pedido será atualizado para **Concluído**.

![Cópia de dados concluída](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Se a cópia for concluída com erros, confira [Solucionar problemas de upload](data-box-disk-troubleshoot-upload.md).

Verifique se seus dados estão nas contas de armazenamento antes de excluí-los da fonte. Seus dados podem estar em:

- Suas contas de Armazenamento do Microsoft Azure. Quando você copia os dados para o Data Box, dependendo do tipo, eles são carregados em um dos caminhos a seguir em sua conta do Armazenamento do Azure.

  - Para blobs de página e blobs de bloco: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Arquivos do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Como alternativa, você pode acessar sua conta de Armazenamento do Azure no portal do Azure e navegar de lá.

- Seus grupos de recursos de disco gerenciado. Ao criar discos gerenciados, os VHDs são carregados como blob de páginas e, em seguida, convertidos em discos gerenciados. Os discos gerenciados são anexados aos grupos de recursos especificados no momento da criação do pedido.

  - Se a cópia para discos gerenciados no Azure tiver êxito, você poderá acessar os **Detalhes do pedido** no portal do Azure e anotar o grupo de recursos especificado para discos gerenciados.

      ![Exibir detalhes do pedido](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Vá para o grupo de recursos anotado e localize os discos gerenciados.

      ![Grupo de recursos para discos gerenciados](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Se você copiou um VHDX, ou um VHD diferencial/dinâmico, o VHDX/VHD será carregado para a conta de armazenamento de preparo como um blob de blocos. Acesse sua **conta de armazenamento de preparo > Blobs** e selecione o contêiner apropriado - StandardSSD, StandardHDD ou PremiumSSD. Os VHDX/VHDs devem aparecer como blobs de blocos na conta de armazenamento de preparo.

Para verificar se os dados foram carregados no Azure, execute as seguintes etapas:

1. Vá para a conta de armazenamento associada ao pedido do disco.
2. Vá para **serviço Blob > Procurar blobs**. A lista de contêineres é apresentada. Correspondente à subpasta que você criou nas pastas *BlockBlob* e *PageBlob*, contêineres com o mesmo nome são criados em sua conta de armazenamento.
    Se os nomes das pastas não estiverem em conformidade com as convenções de nomenclatura do Azure, o carregamento de dados para o Azure falhará.

4. Para verificar se todo o conjunto de dados foi carregado, use o Gerenciador de Armazenamento do Microsoft Azure. Anexe a conta de armazenamento correspondente ao pedido de aluguel do disco e, em seguida, observe a lista de contêineres de blob. Escolha um contêiner, clique em **...Mais** e, em seguida, clique em **Estatísticas da pasta**. No painel **Atividades**, as estatísticas dessa pasta, incluindo o número de blobs e o tamanho total do blob são exibidos. O tamanho total do blob em bytes deve corresponder ao tamanho do conjunto de dados.

    ![Estatísticas da pasta no Gerenciador de Armazenamento](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Eliminar dados do Data Box Disk

Depois que a cópia for concluída e você tiver verificado que os dados estão na conta de armazenamento do Azure, os discos serão apagados com segurança seguindo o padrão do NIST.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box Disk


Avance para a próxima instrução para saber como gerenciar o Data Box Disk por meio do portal do Azure.

> [!div class="nextstepaction"]
> [Usar o portal do Azure para administrar o Azure Data Box Disk](./data-box-portal-ui-admin.md)

::: zone-end


::: zone target="chromeless"

# <a name="return-the-disk-and-verify-upload-to-azure"></a>Devolver o disco e verificar o upload no Azure

## <a name="ship-to-azure"></a>Enviar para o Azure

1. Após a conclusão da validação de dados, desconecte os discos. Remova os cabos de conexão.
2. Encapsule todos os discos e os cabos de conexão com plástico bolha e coloque-os na caixa de remessa. Poderá haver encargos se os acessórios estiverem ausentes.
    - Reutilize a embalagem da remessa inicial.  
    - É recomendável que empacotar os discos usando um plástico bolha bem ajustado.
    - Verifique se que o ajuste é firme para reduzir qualquer movimentos dentro da caixa.
3. As próximas etapas são determinadas pelo local em você está devolvendo o dispositivo.
    - [Agende uma retirada com a UPS se estiver devolvendo o dispositivo nos EUA e no Canadá](data-box-disk-deploy-picked-up.md#pick-up-in-us-canada).
    - [Agende uma retirada com a DHL para a Europa](data-box-disk-deploy-picked-up.md#pick-up-in-europe) visitando seu site e especificando o número de conhecimento aéreo.
    - [Agende uma retirada para países na região Austrália/Pacífico](data-box-disk-deploy-picked-up.md#pick-up-in-asia-pacific-region), como Austrália, Japão, Coreia do Sul e Singapura.
4. Depois que os discos forem retirados pela transportadora, o status do pedido no portal será atualizado e uma ID de rastreamento será exibida.

## <a name="verify-upload-to-azure"></a>Verificar o upload no Azure

Depois que os dados forem carregados no Azure, verifique se os seus dados estão nas contas de armazenamento antes de excluí-los da fonte. Seus dados podem estar em:

- Suas contas de Armazenamento do Microsoft Azure. Quando você copia os dados para o Data Box, dependendo do tipo, eles são carregados em um dos caminhos a seguir em sua conta do Armazenamento do Azure.

    - **Para blobs de blocos e blobs de páginas**: https://<nome_da_conta_de_armazenamento>.blob.core.windows.net/<containername>/files/a.txt

    - **Para Arquivos do Azure**: https://<nome_da_conta_de_armazenamento>.file.core.windows.net/<sharename>/files/a.txt

    Como alternativa, você pode acessar sua conta de Armazenamento do Azure no portal do Azure e navegar de lá.

- Seus grupos de recursos de disco gerenciado. Ao criar discos gerenciados, os VHDs são carregados como blob de páginas e, em seguida, convertidos em discos gerenciados. Os discos gerenciados são anexados aos grupos de recursos especificados no momento da criação do pedido.

::: zone-end


