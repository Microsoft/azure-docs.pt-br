---
title: Tutorial sobre como devolver o Microsoft Azure Data Box Disk | Microsoft Docs
description: Neste tutorial, aprenda a devolver seu Azure Data Box Disk. As instruções de retirada dependem da localização em que o dispositivo é devolvido.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 542eed34f2be05cce3829baab2f07a5c58413ba0
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526289"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Tutorial: Devolução do Azure Data Box Disk

Este tutorial descreve como fazer a devolução do seu Azure Data Box Disk. As instruções de retirada dependem da localização em que o dispositivo é devolvido.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Enviar o Data Box Disk para a Microsoft

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você concluiu o [Tutorial: copiar dados para o Azure Data Box Disk e verificar](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Devolver o Data Box Disk

::: zone-end

::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>Devolução do Azure Data Box Disk

::: zone-end

1. Após a conclusão da validação de dados, desconecte os discos. Remova os cabos de conexão.
2. Encapsule todos os discos e os cabos de conexão com plástico bolha e coloque-os na caixa de remessa. Poderá haver encargos se os acessórios estiverem ausentes.
    - Reutilize a embalagem da remessa inicial.  
    - É recomendável que empacotar os discos usando um plástico bolha bem ajustado.
    - Verifique se que o ajuste é firme para reduzir qualquer movimentos dentro da caixa.

As próximas etapas são determinadas pelo local em você está devolvendo o dispositivo. As instruções são diferentes para EUA/Canadá, UE (União Europeia), Austrália e países/regiões na Ásia.

### <a name="us-or-canada"></a>[EUA ou Canadá](#tab/in-us-or-canada)

Execute as seguintes etapas se estiver devolvendo o dispositivo nos EUA ou Canadá.

1. Use a etiqueta de remessa de devolução que tem a capa de plástico transparente afixada à caixa. Se o rótulo estiver danificado ou tiver sido perdido:
    - Acesse **Visão Geral > Baixar etiqueta de remessa** e baixe uma etiqueta de remessa de devolução.
    - Afixe o rótulo ao dispositivo.

2. Lacre a caixa de envio e verifique se a etiqueta de remessa de devolução está visível.
3. Agende uma retirada com a UPS. Para agendar uma retirada:

    - Ligue para a UPS local (linha gratuita específica do país/região).
    - Em sua chamada, mencione a remessa inversa, conforme mostrado na etiqueta impressa de número de controle.
    - Se o número de rastreamento não for mencionado, a UPS exigirá que você pague uma taxa adicional durante a retirada.
    - Em vez de agendar a retirada, você também pode descartar o Data Box Disk no local mais próximo de redistribuição.


### <a name="europe-or-uk"></a>[Europa ou Reino Unido](#tab/in-europe-or-uk)

Execute as etapas a seguir se estiver devolvendo o dispositivo na Europa ou no Reino Unido.

1. Use a etiqueta de remessa de devolução que tem a capa de plástico transparente afixada à caixa. Se o rótulo estiver danificado ou tiver sido perdido:
    - Acesse **Visão Geral > Baixar etiqueta de remessa** e baixe uma etiqueta de remessa de devolução.
    - Afixe o rótulo ao dispositivo.

2. Lacre a caixa de envio e verifique se a etiqueta de remessa de devolução está visível.
3. Acesse o site da DHL Express do país/da região e selecione **Agendar uma Retirada**. Em **Você precisa de uma etiqueta de remessa?** , selecione **Não** > **Tenho um número de carta de porte da DHL**.
4. Especifique o número da carta de porte e clique em **Agendar Retirada** para organizar a retirada.

### <a name="australia"></a>[Austrália](#tab/in-australia)

Datacenters do Azure na Austrália têm uma notificação de segurança adicional. Todas as remessas de entrada devem ter uma notificação avançada. Execute as seguintes etapas para retirada na Austrália.

1. Use a etiqueta de remessa de devolução fornecida e verifique se o código TAU (número de referência) está escrito nela. Caso não tenha mais a etiqueta de remessa fornecida ou tenha qualquer outro problema, envie um email para [Operações do Data Box na Ásia](mailto:adbo@microsoft.com). Forneça o nome do pedido no assunto do cabeçalho e os detalhes do problema.
2. Afixe a etiqueta à caixa.
3. Agende online no link https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference uma retirada.

### <a name="japan"></a>[Japão](#tab/in-japan)

1. Escreva as informações de nome e endereço da sua empresa na nota de consignação como suas informações de remetente.
2. Envie um email para a Quantium Solutions usando o modelo de email a seguir.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00
        b. 13：00-15：00
        c. 15：00-17：00
        d. 17：00-19：00
    ```
    - **Se estiver retirando em Osaka**, modifique o assunto no modelo de email para: `Pickup request for Microsoft Azure OSA`.
    - Se a nota de consignação do Japan Post Chakubarai não tiver sido incluída ou estiver ausente, faça uma observação sobre isso no email. A Quantium Solutions Japan solicitará ao Japan Post que traga a nota de consignação após a retirada.
    - Caso você tenha vários pedidos, envie um email para garantir a retirada individual.

3. Receba um email de confirmação da Quantium Solutions depois de agendar uma retirada. O email de confirmação também inclui informações sobre a nota de consignação do Chakubarai.

Se necessário, você poderá contatar o Suporte da Quantium Solutions (em japonês) com as seguintes informações: 

- Email：[Customerservice.JP@quantiumsolutions.com](mailto:Customerservice.JP@quantiumsolutions.com)
- Telefone：03-5755-0150 

### <a name="korea"></a>[Coreia do Sul](#tab/in-korea)

1. Inclua a nota de consignação de devolução.
2. Para solicitar a retirada quando houver nota de consignação:
    1. Ligue para a linha direta da *Quantium Solutions International* em 070-8231 1418 durante o horário de expediente (das 10h às 17h, de segunda a sexta-feira). Mencione *Retirada do Microsoft Azure* e o número da solicitação de serviço para providenciar uma retirada.  
    2. Se a linha direta estiver ocupada, envie um email para [microsoft@rocketparcel.com](mailto:microsoft@rocketparcel.com) com o assunto *Retirada do Microsoft Azure* e o número da solicitação de serviço para referência.
    3. Se a transportadora não chegar para a coleta, ligue para a linha direta da *Quantium Solutions International* para providências alternativas.
    4. Você receberá um email de confirmação para o agendamento da retirada.
3. Siga esta etapa somente se não houver uma nota de consignação. Para solicitar retirada:
    1. Ligue para a linha direta da *Quantium Solutions International* em 070-8231 1418 durante o horário de expediente (das 10h às 17h, de segunda a sexta-feira). Mencione *Retirada do Microsoft Azure* e o número da solicitação de serviço para providenciar uma retirada. Especifique que você precisa de uma nova nota de consignação para providenciar uma retirada. Forneça o remetente (cliente), as informações de destinatário (datacenter do Azure) e o número de referência (número da solicitação de serviço).
    2. Se a linha direta estiver ocupada, envie um email para [microsoft@rocketparcel.com](mailto:microsoft@rocketparcel.com) com o assunto *Retirada do Microsoft Azure* e o número da solicitação de serviço como referência.
    3. Se a transportadora não chegar para a coleta, ligue para a linha direta da *Quantium Solutions International* para providências alternativas.
    4. Você recebe uma confirmação verbal se a solicitação for feita por telefone.

### <a name="singapore"></a>[Singapura](#tab/in-singapore)

1. Imprima a etiqueta de remessa e anexe-a à caixa. Se o rótulo estiver danificado ou tiver sido perdido:
    - Acesse **Visão Geral > Baixar etiqueta de remessa** e obtenha uma etiqueta de remessa de devolução.
    - Afixe o rótulo ao dispositivo. Certifique-se que a etiqueta está visível.

2. Para solicitar a retirada, envie um email para o Atendimento ao Cliente do SingPost usando o modelo a seguir com o número de controle (esse número pode ser encontrado no rótulo de retorno fornecido no pacote entregue).

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - XZ00001234567
    Body:
     a.    Requestor name
     b.    Requestor contact number
     c.    Requestor collection address
     d.    Preferred collection date
    ```

   > [!NOTE]
   > Para solicitações de reserva recebidas em um dia útil:
   >
   > * Antes das 15:00, a retirada ocorrerá no dia útil a seguir, entre 9:00 e 13:00.
   > * Após às 15:00, a retirada ocorrerá no dia útil a seguir, entre 14:00 e 18:00.

   Caso você tenha problemas, entre em contato com as Operações do Data Box na Ásia pelo email [adbo@microsoft.com](mailto:adbo@microsoft.com). Forneça o nome do trabalho no cabeçalho de assunto e o problema encontrado.

3. Passe para a transportadora.

### <a name="south-africa"></a>[África do Sul](#tab/in-sa)

Siga as etapas a seguir se estiver devolvendo o dispositivo na África do Sul.

1. Anexe à caixa a etiqueta de remessa fornecida. Essa etiqueta contém o número de rastreamento. Se a etiqueta de remessa estiver ausente, você poderá baixar uma nova em **Visão geral > Baixar etiqueta de remessa**.

2. Lacre a caixa de envio e verifique se a etiqueta de remessa de devolução está visível.

3. Solicite um código de retorno à equipe de Operações do Azure Data Box. Um código de retorno é necessário para entregar o pacote de volta ao datacenter. Envie um email para [adbops@microsoft.com](mailto:adbops@microsoft.com). Anote esse código na etiqueta de remessa ao lado do endereço de retorno para que ele fique bem visível.

4. Agende a retirada com a DHL usando um dos seguintes métodos:
   * Agende uma retirada online acessando [DHL Express South Africa, **Agendar uma Retirada**](https://mydhl.express.dhl/za/en/schedule-pickup.html#/schedule-pickup#label-reference).
   * Envie um email para [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) usando o seguinte modelo:

     ```output
     To: Priority.Support@dhl.com
     Subject: Pickup request for Microsoft Azure
     Body: Need pick up for the below shipment
       *  DHL tracking number: (reference number/waybill number)
       *  Requested pickup date: yyyy/mm/dd;time:HH MM
       *  Shipper contact: (company name)
       *  Contact person: 
       *  Phone number: 
       *  Full physical address: 
       *  Item to be collected: Azure Dt
     ```

    * Ou deixe o pacote no ponto de serviço da DHL mais próximo.

5. Se você encontrar problemas, envie um email para [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) com detalhes dos problemas e coloque o número da nota de entrega na linha Assunto:. Você também pode ligar para +27 (0) 119213902.

### <a name="china"></a>[China](#tab/in-china)

Siga as etapas a seguir se estiver devolvendo o dispositivo na China.

1. Anexe a etiqueta de remessa fornecida e cole-a na caixa. Essa etiqueta contém o número de rastreamento. Se a etiqueta de remessa estiver ausente, você poderá baixar uma nova em **Visão geral > Baixar etiqueta de remessa**.

2. Envie um email para o Atendimento ao Cliente Premier FedEx e forneça o número de rastreamento (número de referência da remessa) para organizar a retirada usando o seguinte modelo de email:  

   ```output
   To: ying.bao@fedex.com;739951@fedex.com
   Subject: Pickup request for Microsoft Azure : Order Name
   Body: Need pick up for the below shipment
   * FedEx tracking number (reference number)
   * Requested pickup date：yyyy/mm/dd; time: HH MM
   ```

3. Receba uma confirmação de email do FedEx após a conclusão da retirada da reserva.  

4. Caso você tenha problemas, envie um email para [DL-DC-SHA@oe.21vianet.com](mailto:DL-DC-SHA@oe.21vianet.com) com detalhes dos problemas e coloque o nome do pedido na linha Assunto:.

#### <a name="premier-customer-care-contact-information"></a>Informações de contato do Atendimento ao Cliente Premier

<ins>Primário</ins>

| Informações de contato | Detalhes |
|---|---|
|Nome:       | Bao Ying|
|Designação | Representante sênior da OneCall |
|Telefone:      | 400.889.6066 ext. 3693 |
|Email:     | [ying.bao@fedex.com](mailto:ying.bao@fedex.com) |

<ins>Backup</ins>

| Informações de contato | Detalhes |
|---|---|
|Nome:       | He Xun|
|Designação | Representante da OneCall |
|Telefone:      | 400.889.6066 ext. 3603 |
|Email:     | [739951@fedex.com](mailto:739951@fedex.com) |

### <a name="self-managed"></a>[Autogerenciado](#tab/in-selfmanaged)

Se estiver usando o Data Box Disk no US Government, no Japão, em Singapura, na Coreia do Sul, no Reino Unido, no Oeste da Europa, na Austrália, na África do Sul ou na Índia e tiver selecionado a opção de remessa autogerenciada durante a criação do pedido, siga estas instruções.

1. Vá para a folha **Visão geral** de seu pedido no portal do Azure. Examine as instruções que são exibidas ao selecionar **Agendar retirada**. Você deverá ver um código de Autorização, que será usado no momento de entrega do pedido.

2. Envie um email para a equipe de Operações do Azure Data Box usando o modelo a seguir quando estiver pronto para devolver o dispositivo.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box Disk drop-off for order: 'orderName'
    Body:
     a. Order name
     b. Contact name of the person dropping off. You will need to display a Government approved ID during the drop-off.
    ```
3. A equipe de Operações do Azure Data Box trabalhará com você para marcar a devolução para o datacenter do Azure.

---

::: zone target="docs"

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
>
> * Enviar o Data Box Disk para a Microsoft

Avance para as próximas instruções para saber como verificar o upload de dados do Data Box Disk na conta de armazenamento do Azure.

> [!div class="nextstepaction"]
> [Verificar o upload de dados do Azure Data Box Disk](./data-box-disk-deploy-upload-verify.md)

::: zone-end
