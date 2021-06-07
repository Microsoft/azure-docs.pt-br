---
title: Tutorial sobre como devolver o Azure Data Box na ordem de exportação | Microsoft Docs
description: Saiba como enviar o Azure Data Box para a Microsoft após a conclusão da ordem de exportação
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/03/2021
ms.author: alkohli
ms.openlocfilehash: 6c2eed9aac614b0765582d5cf373caf50323ef7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99537350"
---
# <a name="tutorial-return-azure-data-box"></a>Tutorial: Devolução do Azure Data Box

Este tutorial descreve como retornar o Azure Data Box e os dados são apagados depois que o dispositivo é recebido nos dados do Azure.

Neste tutorial, você aprenderá sobre tópicos como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Preparar para o envio
> * Enviar o Data Box para a Microsoft
> * Eliminar dados do Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

* Você concluiu o [Tutorial: Copiar dados do Azure Data Box](data-box-deploy-export-copy-data.md).
* Trabalhos de cópia estão concluídos. A Preparação para o envio não poderá ser executada se os trabalhos de cópia estiverem em andamento.

## <a name="prepare-to-ship"></a>Preparar para o envio

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

As próximas etapas são determinadas pelo local em você está devolvendo o dispositivo.

## <a name="ship-data-box-back"></a>Devolver o Data Box

Verifique se a cópia de dados do dispositivo foi concluída e se a execução de **Preparar para o envio** foi bem-sucedida. De acordo com a região para a qual você está enviando o dispositivo, o procedimento é diferente.

## <a name="us-canada-europe"></a>[EUA, Canadá, Europa](#tab/in-us-canada-europe)

Execute as etapas a seguir se estiver devolvendo o dispositivo nos EUA, no Canadá ou na Europa.

1. Verifique se o dispositivo está desligado e se os cabos foram removidos. 
2. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte posterior do dispositivo.
3. Verifique se o rótulo de remessa está aparecendo no E-ink e agende uma retirada com a operadora. Se o rótulo for danificado ou perdido ou não for exibida na tela do E-ink, entre em contato com o Suporte da Microsoft. Se o Suporte sugerir, acesse **Visão Geral > Baixar etiqueta de remessa** no portal do Azure. Baixe a etiqueta de remessa e afixe-a ao dispositivo. 
4. Agende uma retirada com a UPS se estiver devolvendo o dispositivo. Para agendar uma retirada:

   - Ligue para a UPS local (linha gratuita específica do país/região).
   - Em sua chamada, mencione a remessa inversa, conforme mostrado na exibição E-ink ou sua etiqueta impressa de número de controle. Se você não citar o número de rastreamento, o no-break exigirá uma cobrança adicional durante a retirada.
   - Se algum problema surgir enquanto você estiver agendando a retirada ou for solicitado a pagar valores adicionais, entre em contato com a equipe de Operações do Azure Data Box. Envie um email para [adbops@microsoft.com](mailto:adbops@microsoft.com).

    Em vez de agendar a retirada, você também pode descartar o Data Box no local mais próximo de redistribuição.
4. Após a coleta e registro do Data Box por sua operadora, o status do pedido no portal será atualizado para **Recolhido**. Uma ID de rastreamento também é exibida.

## <a name="australia"></a>[Austrália](#tab/in-australia)

Datacenters do Azure na Austrália têm uma notificação de segurança adicional. Todas as remessas de entrada devem ter uma notificação avançada. Execute as etapas a seguir para o envio na Austrália.

1. Mantenha a caixa original usada para enviar o dispositivo para a remessa de devolução.
2. Verifique se a cópia de dados no dispositivo foi concluída e se a execução de **Preparação para o envio** foi bem-sucedida.
3. Desligue o dispositivo e remova os cabos.
4. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte traseira do dispositivo.
5. Agendar uma coleta online no [Link da DHL](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

## <a name="japan"></a>[Japão](#tab/in-japan)

1. Mantenha a caixa original usada para enviar o dispositivo para a remessa de devolução.
2. Desligue o dispositivo e remova os cabos.
3. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte traseira do dispositivo.
4. Escreva as informações de nome e endereço da sua empresa na nota de consignação como suas informações de remetente.
5. Envie um email para a Quantium Solutions usando o modelo de email a seguir.

    * Se a nota de consignação do Japan Post Chakubarai não tiver sido incluída ou estiver ausente, indique isso nesse email. A Quantium Solutions Japan solicitará ao Japan Post que traga a nota de consignação após a retirada.
    * Caso você tenha vários pedidos, envie um email para garantir a retirada individual.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

6. Receba um email de confirmação da Quantium Solutions depois de agendar uma retirada. O email de confirmação também inclui informações sobre a nota de consignação do Chakubarai.

Se necessário, você poderá contatar o Suporte da Quantium Solutions (em japonês) com as seguintes informações: 

* Email：Customerservice.JP@quantiumsolutions.com
* Telefone：03-5755-0150

## <a name="singapore"></a>[Singapura](#tab/in-singapore)

1. Mantenha a caixa original usada para enviar o dispositivo para a remessa de devolução.
2. Anote o número de acompanhamento (exibido como um número de referência na página Preparar para o Envio da interface de usuário da Web local do Data Box). Isso estará disponível depois que a etapa preparar para o envio for concluída. Baixe a etiqueta de remessa desta página e cole na caixa de embalagem.
3. Desligue o dispositivo e remova os cabos.
4. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte traseira do dispositivo. 
5. Envie um email para o Serviço de atendimento ao consumidor da SingPost usando este modelo de email com o número de acompanhamento.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > Para solicitações de reserva recebidas em um dia útil:
   >
   > * Antes das 15:00, a retirada ocorrerá no dia útil a seguir, entre 9:00 e 13:00.
   > * Após às 15:00, a retirada ocorrerá no dia útil a seguir, entre 14:00 e 18:00.  

## <a name="south-africa"></a>[África do Sul](#tab/in-sa)

1. Guarde a caixa original usada para empacotar o dispositivo para a remessa de devolução.
2. Anote o número de referência (número da nota de entrega) mostrado na IU da Web local do dispositivo. Esse número é exibido depois que a **executada de Preparação para o envio** é bem-sucedida.
3. Baixe e imprima a etiqueta de remessa que está disponível na IU da Web local do dispositivo e afixe-a ao pacote de remessa.
4. Para agendar uma retirada com a DHL, escolha uma das seguintes opções:

    * Ligue para o centro de contato do atendimento ao cliente às 14h em **+27(0) 11 9213600**, selecione a opção 1 e especifique o número da nota de entrega.
    * Envie um email para [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) usando o seguinte modelo:

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * Em vez disso, você pode deixar o pacote no ponto de serviço da DHL mais próximo.

5. Se você encontrar algum problema, envie um email para [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) com detalhes dos problemas encontrados e coloque o número da carta de porte na linha Assunto:. Você também pode ligar para +27 (0) 119213902.

## <a name="hong-kong"></a>[RAE de Hong Kong](#tab/in-hk)

1. Empacote o dispositivo para a remessa de devolução na caixa original.
2. Anote o número de referência (número de acompanhamento para remessa reversa) mostrado na IU da Web local do dispositivo. Esse número é exibido depois que a **executada de Preparação para o envio** é bem-sucedida.
3. Baixe e imprima a etiqueta de remessa que está disponível na IU da Web local do dispositivo e afixe-a ao pacote de remessa.
4. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte traseira do dispositivo.
5. Ligue para a linha direta da **Quantium Solutions** no número **(852) 2318 1213** durante o horário de expediente (das 9h às 18h, de segunda a sexta-feira).  
6. Mencione a retirada do Microsoft Azure, o número de referência e o número de rastreamento (acima do código de barras) na etiqueta de remessa de devolução para providenciar a retirada.
7. Você receberá uma confirmação verbal para o agendamento da retirada. Se a transportadora não chegar para a coleta, ligue para a linha direta da Quantium Solutions para providências alternativas.
8. Ao reservar uma coleta com a Quantium, compartilhe a confirmação com a [Microsoft Data Box Operations Ásia](mailto:adbo@microsoft.com) usando o seguinte modelo:

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

Se tiver problemas, envie um email para a equipe de Operações do Data Box na Ásia [adbo@microsoft.com](mailto:adbo@microsoft.com) fornecendo o nome do trabalho no cabeçalho de assunto e o problema encontrado.

## <a name="united-arab-emirates"></a>[Emirados Árabes Unidos](#tab/in-uae)

1. Mantenha a caixa original usada para enviar o dispositivo para a remessa de devolução.
2. Verifique se a cópia de dados para o dispositivo foi concluída e se a etapa **Preparação para o envio** foi concluída com êxito.
3. Anote o número de referência na página **Preparação para o envio** da IU da Web local do dispositivo.
4. Desligue o dispositivo e remova os cabos. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte traseira do dispositivo.
6. Empacote o dispositivo para a remessa de devolução na caixa original.
7. Envie um email para [Operações do Azure Data Box](mailto:adbops@microsoft.com) para obter uma ID que será usada para identificar o pacote quando ele chegar ao datacenter.
8. Anote essa ID no rótulo de envio impresso, ao lado do endereço de retorno, de modo que fique claramente visível.  
9. Agende uma retirada online acessando [DHL Express UAE](https://mydhl.express.dhl/ae/en/home.html#/schedulePickupTab) > **Agendar uma retirada**.
   - Insira o número de referência da página **Preparação para o envio** da IU da Web local do dispositivo no campo de número do conhecimento de embarque.
   - Reservas são aceitas das 9h às 14h, seis dias por semana (exceto às sextas-feiras e feriados públicos).
   - As solicitações de retirada devem ser feitas pelo menos 90 minutos antes do horário de encerramento do atendimento a clientes.
10. Se tiver algum problema com a ferramenta de reservas da DHL, entre em contato com a DHL por meio de um destes métodos:
    - Telefone para 04-2924545.
    - Envie um email para [ecom.ae@dhl.com](mailto:ecom.ae@dhl.com) com detalhes dos problemas e coloque o número da nota de entrega na linha Assunto:.
    - Entre em contato com o atendimento ao cliente da DHL pelo número 600 567567.

## <a name="self-managed"></a>[Autogerenciado](#tab/in-selfmanaged)

Se estiver usando o Data Box no Japão, em Singapura, na Coreia do Sul, na Índia, na África do Sul, no Reino Unido, no Oeste da Europa ou na Austrália e tiver selecionado a opção de remessa autogerenciada durante a criação do pedido, siga estas instruções.

1. Após a conclusão dessa etapa, anote o código de Autorização mostrado na página Preparar para o Envio da IU da Web local do Data Box.
2. Desligue o dispositivo e remova os cabos. Enrole o cabo de alimentação fornecido com o dispositivo e coloque-o com segurança na parte traseira do dispositivo.
3. Envie um email para a equipe de Operações do Azure Data Box usando o modelo abaixo quando estiver pronto para devolver o dispositivo.
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

---

## <a name="erasure-of-data-from-data-box"></a>Eliminar dados do Data Box

Após o dispositivo chegar ao datacenter do Azure, o Data Box apaga os dados nos respectivos discos de acordo com as [diretrizes do NIST SP 800-88 Revisão 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para o envio
> * Enviar o Data Box para a Microsoft
> * Eliminar dados do Data Box

Avance para o próximo artigo para saber como gerenciar seu Data Box.

> [!div class="nextstepaction"]
> [Gerenciar Data Box via portal do Azure](./data-box-portal-admin.md)
