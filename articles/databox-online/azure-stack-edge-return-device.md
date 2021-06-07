---
title: Retornar seu dispositivo pro Edge Azure Stack | Microsoft Docs
description: Saiba como apagar os dados e retornar seu Azure Stack dispositivo pro Edge e, em seguida, exclua o recurso associado ao dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.openlocfilehash: cb11d7d3b2da9ab793cb18814e4021ea7afeb806
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443583"
---
# <a name="return-your-azure-stack-edge-pro-device"></a>Retornar seu dispositivo pro Edge Azure Stack

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como apagar os dados e, em seguida, retornar o dispositivo pro Edge Azure Stack. Depois de retornar o dispositivo, você também pode excluir o recurso associado ao dispositivo.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
>
> * Apagar os dados dos discos de dados no dispositivo
> * Iniciar a devolução do dispositivo no portal do Azure
> * Empacotar o dispositivo e agendar uma retirada
> * Excluir o recurso no portal do Azure

## <a name="erase-data-from-the-device"></a>Apagar os dados do dispositivo

Para apagar os dados dos discos de dados do seu dispositivo, você precisa redefinir o dispositivo.

Antes de redefinir, crie uma cópia dos dados locais no dispositivo, se necessário. Você pode copiar os dados do dispositivo para um contêiner do Armazenamento do Microsoft Azure. 

Você pode iniciar o dispositivo retornar mesmo antes de o dispositivo ser redefinido.

Você pode redefinir seu dispositivo na interface do usuário da Web local ou no PowerShell. Para obter instruções sobre o PowerShell, consulte [redefinir seu dispositivo](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

> [!NOTE]
> - Se estiver trocando ou atualizando para um novo dispositivo, recomendamos definir seu dispositivo somente depois de receber o novo dispositivo.
> - A redefinição do dispositivo apenas exclui todos os dados locais do dispositivo. Os dados que estão na nuvem não são excluídos e recebe [cobranças](https://azure.microsoft.com/pricing/details/storage/). Esses dados precisam ser excluídos separadamente com uma ferramenta de gerenciamento de armazenamento em nuvem, como o [Gerenciador do Armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="initiate-device-return"></a>Iniciar o retorno do dispositivo

Para iniciar o processo de devolução, execute as etapas a seguir.

1. Acesse o recurso Azure Stack Edge pro/Gateway do Data Box no portal do Azure. Na **visão geral**, vá para a barra de comandos no painel direito e selecione **retornar dispositivo**. 

    ![Retornar o dispositivo 1](media/azure-stack-edge-return-device/return-device-1.png)  

2. Na folha do **dispositivo de retorno** , em **detalhes básicos**:

    1. Forneça o número de série do dispositivo. Para obter o número de série do dispositivo, acesse a interface do usuário da Web local do dispositivo e vá para **visão geral**.  
    
       ![Número de série do dispositivo 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. Insira o número da etiqueta de serviço. O número da marca de serviço é um identificador com cinco ou mais caracteres, que é exclusivo para seu dispositivo. A marca de serviço está localizada no canto inferior direito do dispositivo (à medida que você enfrenta o dispositivo). Retire a marca de informações (é um painel de rótulo de deslizamento). Esse painel contém informações do sistema, como a marca de serviço, NIC, endereço MAC e assim por diante. 
    
       ![Rótulo de serviço número 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. Na lista suspensa, escolha um motivo para o retorno.

       ![Dispositivo de retorno 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. Em **detalhes de envio**:

    1. Forneça seu nome, nome da empresa e endereço completo da empresa. Insira um telefone comercial, incluindo o código de área e uma ID de email para notificação.
    2. Peça uma caixa de envio para devolução se precisar de uma. Responda **Sim** à pergunta **Preciso de uma caixa vazia para devolução**.

    ![Retornar dispositivo 3](media/azure-stack-edge-return-device/return-device-3.png)

4. Examine os **termos de privacidade** e marque a caixa de seleção que você examinou e concorde com os termos de privacidade.

5. Selecione **Iniciar retornar**.

    ![Retornar dispositivo 4](media/azure-stack-edge-return-device/return-device-4.png) 

6. Depois que os detalhes de retorno do dispositivo forem capturados, você poderá notificar a equipe de operações do Azure Stack Edge pro por meio de um email. Você pode usar seu aplicativo de email supondo que o aplicativo de email esteja instalado e configurado. Você também pode copiar os dados para criar e enviar um email.

    ![Retornar dispositivo 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Depois que a equipe de operações do Azure Stack Edge pro receber o email, ele enviará um rótulo de remessa inversa. Ao receber esse rótulo, você pode agendar a retirada do dispositivo com a transportadora. 

## <a name="schedule-a-pickup"></a>Agendar uma retirada

Para agendar uma retirada, execute as etapas a seguir.

1. Desligar o dispositivo. Na interface do usuário de web local, vá para **manutenção > configurações de energia**.
2. Selecione **Desligar**. Quando precisar confirmar, clique em **Sim** para continuar. Para obter mais informações, confira [Gerenciar energia](../databox-gateway/data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Desconecte os cabos de alimentação e remova todos os cabos de rede do dispositivo.
4. Use sua própria caixa ou uma vazia recebida da Azure para embalar o equipamento para envio. Coloque o dispositivo e os cabos de alimentação que foram enviados com o dispositivo na caixa.
5. Cole a etiqueta de remessa recebida da Azure no pacote.
6. Agende uma retirada com sua transportadora regional. Se devolver o dispositivo nos EUA, sua transportadora será a UPS ou a FedEx. Agendar uma retirada com a UPS:

    1. Ligue para a UPS local (linha gratuita específica do país/região).
    2. Em sua chamada, mencione o número de rastreamento de envio reverso, conforme mostrado na etiqueta impressa.
    3. Se o número de rastreamento não estiver entre aspas, o no break exigirá que você pague um encargo extra durante a retirada.

    Em vez de agendar a retirada, você também pode cancelar o Azure Stack o Edge pro no local de distribuição mais próximo.

## <a name="delete-the-resource"></a>Excluir o recursos

Depois que o dispositivo é recebido no datacenter do Azure, o dispositivo é inspecionado quanto a danos ou sinais de violação.

- Se o dispositivo chegar intacto e estiver em bom formato, o medidor de cobrança será interrompido para esse recurso. Azure Stack equipe de operações do Edge Pro irá contatá-lo para confirmar que o dispositivo foi retornado. Em seguida, você pode excluir o recurso associado ao dispositivo no portal do Azure.
- Se o dispositivo chegar significativamente danificado, multas poderão ser aplicadas. Para mais informações, consulte [Perguntas frequentes sobre dispositivo perdido ou danificado](https://azure.microsoft.com/pricing/details/databox/edge/) e os [Termos de serviço do produto](https://www.microsoft.com/licensing/product-licensing/products).  


Você pode excluir o dispositivo no portal do Azure:

- Depois de fazer um pedido e antes de o dispositivo ser preparado pela Microsoft.
- Depois de retornar um dispositivo à Microsoft, a equipe de operações do Azure Stack Edge pro foi chamada para confirmar que o dispositivo foi retornado. A equipe de operações não é chamada até que o dispositivo retornado passe pela inspeção física no datacenter do Azure.

Se você ativou o dispositivo em outra assinatura ou local, a Microsoft moverá seu pedido para a nova assinatura ou local em até um dia útil. Depois que o pedido for movido, você poderá excluir esse recurso.


Siga as etapas abaixo para excluir o dispositivo e o recurso em portal do Azure.

1. Na portal do Azure, vá para o recurso e, em seguida, para **Visão geral**. Na barra de comandos, selecione **Excluir**.

    ![Selecione excluir](media/azure-stack-edge-return-device/delete-resource-1.png)

2. Na folha **Excluir dispositivo**, digite o nome do dispositivo que quer excluir e selecione **Excluir**.

    ![Confirmar exclusão](media/azure-stack-edge-return-device/delete-resource-2.png)

Você será notificado após o dispositivo e o recurso associado for excluído com êxito.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [obter uma substituição Azure Stack dispositivo pro Edge](azure-stack-edge-replace-device.md).