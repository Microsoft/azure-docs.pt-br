---
title: Tutorial sobre como devolver o Azure Data Box | Microsoft Docs
description: Neste tutorial, aprenda a devolver o Azure Data Box, incluindo a preparação para o envio, o envio do Data Box, a verificação do upload de dados e o apagamento de dados do Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 267094ea6a7295a65b93fb7700c97e4280da341d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539104"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutorial: Devolver o Azure Data Box e verificar o upload de dados para o Azure

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>Devolver o Data Box e verificar o upload de dados para o Azure

::: zone-end

::: zone target="docs"

Este tutorial descreve como devolver o Azure Data Box e verificar os dados carregados no Azure.

Neste tutorial, você aprenderá sobre tópicos como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Preparar para o envio
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

* Você concluiu o [Tutorial: Copiar dados para o Azure Data Box e verificá-los](data-box-deploy-copy-data.md).
* Os trabalhos de cópia estão concluídos e não há erros na página **Conectar e copiar**. O comando **preparar para o envio** não poderá ser executado se os trabalhos de cópia estiverem em andamento ou houver erros na página **Conectar e copiar**.

## <a name="prepare-to-ship"></a>Preparar para o envio

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Após a conclusão da cópia dos dados, você prepara e envia o dispositivo. Quando o dispositivo chega ao datacenter do Azure, os dados são carregados automaticamente para o Azure.

## <a name="prepare-to-ship"></a>Preparar para o envio

Antes da preparação para o envio, verifique se os trabalhos de cópia foram concluídos.

1. Acesse a página **Preparação para o envio** na interface do usuário da Web local e inicie a preparação da remessa.
2. Desligue o dispositivo por meio da interface do usuário da Web local. Remova os cabos do dispositivo.

As próximas etapas são determinadas pelo local em você está devolvendo o dispositivo.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Devolver o Data Box

Verifique se a cópia de dados no dispositivo foi concluída e se a execução do comando **preparar para o envio** foi bem-sucedida. De acordo com a região para a qual você está enviando o dispositivo, o procedimento é diferente.

::: zone-end

## <a name="us-canada-europe"></a>[EUA, Canadá, Europa](#tab/in-us-canada-europe)

Execute as etapas a seguir se estiver devolvendo o dispositivo nos EUA, no Canadá ou na Europa.

1. Verifique se o dispositivo está desligado e se os cabos foram removidos.
2. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte posterior do dispositivo.
3. Certifique-se de que a etiqueta de remessa esteja aparecendo no papel eletrônico e agende uma retirada com a operadora. Se a etiqueta for danificada ou perdida ou não for exibida na tela do E-ink, contate o Suporte da Microsoft. Se o Suporte sugerir, acesse **Visão Geral > Baixar etiqueta de remessa** no portal do Azure. Baixe a etiqueta de remessa e afixe-a ao dispositivo. 
4. Agende uma retirada com a UPS se estiver devolvendo o dispositivo. Para agendar uma retirada:

    * Ligue para a UPS local (linha gratuita específica do país/região).
    * Em sua chamada, mencione a remessa inversa, conforme mostrado na exibição E-ink ou sua etiqueta impressa de número de controle. Se você não citar o número de rastreamento, o no-break exigirá uma cobrança adicional durante a retirada.
    * Se algum problema surgir enquanto você estiver agendando a retirada ou for solicitado a pagar valores adicionais, entre em contato com a equipe de Operações do Azure Data Box. Envie um email para [adbops@microsoft.com](mailto:adbops@microsoft.com).

    Em vez de agendar a retirada, você também pode descartar o Data Box no local mais próximo de redistribuição.
4. Após a coleta e registro do Data Box por sua operadora, o status do pedido no portal será atualizado para **Recolhido**. Uma ID de rastreamento também é exibida.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminar dados do Data Box

Após a conclusão do upload no Azure, o Data Box apaga os dados nos discos de acordo com as [diretrizes NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="australia"></a>[Austrália](#tab/in-australia)

Datacenters do Azure na Austrália têm uma notificação de segurança adicional. Todas as remessas de entrada devem ter uma notificação avançada. Execute as etapas a seguir para o envio na Austrália.

1. Mantenha a caixa original usada para enviar o dispositivo para a remessa de devolução.
2. Verifique se a cópia de dados no dispositivo foi concluída e se a execução de **Preparação para o envio** foi bem-sucedida.
3. Desligue o dispositivo e remova os cabos.
4. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte traseira do dispositivo.
5. Agendar uma coleta online no [Link da DHL](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminar dados do Data Box

Após a conclusão do upload no Azure, o Data Box apaga os dados nos discos de acordo com as [diretrizes NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

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

6. Você receberá um email de confirmação da Quantium Solutions depois de agendar uma retirada. O email de confirmação também inclui informações sobre a nota de consignação do Chakubarai.

Se necessário, você poderá contatar o Suporte da Quantium Solutions (em japonês) com as seguintes informações:

* Email：Customerservice.JP@quantiumsolutions.com 
* Telefone：03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminar dados do Data Box
 
Após a conclusão do upload no Azure, o Data Box apaga os dados nos discos de acordo com as [diretrizes NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="singapore"></a>[Singapura](#tab/in-singapore)

1. Mantenha a caixa original usada para enviar o dispositivo para a remessa de devolução.
2. Anote o número de acompanhamento (exibido como um número de referência na página **Preparar para o Envio** da IU da Web local do Data Box). O número de rastreamento estará disponível após a conclusão bem-sucedida da etapa **Preparar para o Envio**. Baixe a etiqueta de remessa desta página e cole na caixa de embalagem.
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
   > * Antes das 15:00, a retirada ocorrerá no dia útil a seguir, entre 9:00 e 13:00.
   > * Após às 15:00, a retirada ocorrerá no dia útil a seguir, entre 14:00 e 18:00.  

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminar dados do Data Box

Após a conclusão do upload no Azure, o Data Box apaga os dados nos discos de acordo com as [diretrizes NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="south-africa"></a>[África do Sul](#tab/in-sa)

1. Empacote o dispositivo para a remessa de devolução na caixa original.
2. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte traseira do dispositivo.
3. Anote o número de acompanhamento (exibido como um número de referência na página **Preparação para o Envio** da IU da Web local do Data Box). O número de rastreamento estará disponível após a conclusão bem-sucedida da etapa "Preparar para o Envio". Baixe a etiqueta de remessa desta página e cole-a na caixa de embalagem.
4. Solicite um código de retorno à equipe de Operações do Azure Data Box. Um código de retorno é necessário para entregar o pacote de volta ao datacenter. Envie um email para [adbops@microsoft.com](mailto:adbops@microsoft.com). Anote esse código na etiqueta de remessa ao lado do endereço de retorno, em que ele fica bem visível.
5. Agende a retirada com a DHL usando um dos seguintes métodos:
 
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

6. Se você encontrar problemas, envie um email para [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) com detalhes dos problemas e coloque o número da nota de entrega na linha Assunto:. Você também pode ligar para +27 (0) 119213902.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminar dados do Data Box

Após a conclusão do upload no Azure, o Data Box apaga os dados nos discos de acordo com as [diretrizes NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="hong-kong"></a>[RAE de Hong Kong](#tab/in-hk)

1. Empacote o dispositivo para a remessa de devolução na caixa original.
2. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte traseira do dispositivo.
3. Ligue para a linha direta da **Quantium Solutions** no número **(852) 2318 1213** durante o horário de expediente (das 9h às 18h, de segunda a sexta-feira).  
4. Mencione a retirada do Microsoft Azure, o número de referência e o número de rastreamento (acima do código de barras) na etiqueta de remessa de devolução para providenciar a retirada.
5. Você receberá uma confirmação verbal para a agenda da retirada. Se a transportadora não chegar para a coleta, ligue para a linha direta da Quantium Solutions para providências alternativas.
6. Quando você reservar uma coleta com a Quantium Solutions, compartilhe a confirmação com a equipe de [Operações do Microsoft Data Box na Ásia](mailto:adbo@microsoft.com) usando o seguinte modelo:

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium Solutions.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

Se você encontrar problemas, envie um email para a equipe de Operações do Data Box na Ásia [adbo@microsoft.com](mailto:adbo@microsoft.com) com detalhes dos problemas e coloque o número do trabalho na linha Assunto:.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminar dados do Data Box
 

::: zone-end

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminar dados do Data Box
 
Após a conclusão do upload no Azure, o Data Box apaga os dados nos discos de acordo com as [diretrizes NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

<!--## [In Korea](#tab/in-korea) 

1. Retain the original box used to ship the device for return shipment.
2. Note down the tracking number (shown as reference number on the **Prepare to Ship** page of the Data Box local web UI). The tracking number is available after the **Prepare to ship** step successfully completes. Download the shipping label from this page and paste on the packing box. 
3. Power off the device and remove the cables.
4. Spool and securely place the power cord that was provided with the device in the back of the device. 

Request pickup  
If consignment note is present:  

1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number to arrange for a collection.
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.  
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.  
4. You will receive an email confirmation for the pickup schedule.  

Exception process
If the consignment note is not present:
1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number. Specify that you need a new consignment note to arrange for a collection. Provide sender (customer), receiver information (Azure datacenter), and reference number (service request number).
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.
4. You get a verbal confirmation if request is made via telephone.  
::: zone target="chromeless"

## Verify data upload to Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## Erasure of data from Data Box
 
Once the upload to Azure is complete, the Data Box erases the data on its disks as per the [NIST SP 800-88 Revision 1 guidelines](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end
-->

## <a name="self-managed"></a>[Autogerenciado](#tab/in-selfmanaged)

Se você estiver usando o Data Box no US Government, no Japão, em Singapura, na Coreia do Sul, na Índia, na África do Sul, no Reino Unido, no Oeste da Europa ou na Austrália e tiver selecionado a remessa autogerenciada durante a criação do pedido, siga estas instruções.

1. Escreva o código de Autorização mostrado na página **Preparar para o Envio** da IU da Web local para o Data Box depois que a etapa for concluída com êxito.
2. Desligue o dispositivo e remova os cabos. Enrole o cabo de alimentação fornecido com o dispositivo e coloque-o com segurança na parte traseira do dispositivo.
3. Quando você estiver pronto para devolver o dispositivo, envie um email para a equipe de Operações do Azure Data Box usando o modelo abaixo.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminar dados do Data Box
 
Após a conclusão do upload no Azure, o Data Box apaga os dados nos discos de acordo com as [diretrizes NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

---

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

