---
title: Retornar seu dispositivo pro Edge Azure Stack | Microsoft Docs
description: Saiba como apagar os dados e retornar seu Azure Stack dispositivo pro Edge e, em seguida, exclua o recurso associado ao dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/27/2020
ms.author: alkohli
ms.openlocfilehash: 0c35bde40cac9629f084d69d52f119651b5655f7
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784470"
---
# <a name="return-your-azure-stack-edge-pro-device"></a>Retornar seu dispositivo pro Edge Azure Stack

Este artigo descreve como apagar os dados e, em seguida, retornar o dispositivo pro Edge Azure Stack. Depois de retornar o dispositivo, você também pode excluir o recurso associado ao dispositivo.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
>
> * Apagar os dados dos discos de dados no dispositivo
> * Iniciar a devolução do dispositivo no portal do Azure
> * Empacotar o dispositivo e agendar uma retirada
> * Excluir o recurso no portal do Azure

## <a name="erase-data-from-the-device"></a>Apagar os dados do dispositivo

Para apagar os dados dos discos de dados do seu dispositivo, você precisa redefinir o dispositivo. Você pode redefinir o seu dispositivo usando a IU da Web local ou a interface do PowerShell.

Antes de redefinir, crie uma cópia dos dados locais no dispositivo, se necessário. Você pode copiar os dados do dispositivo para um contêiner do Armazenamento do Microsoft Azure.

Você pode iniciar o dispositivo retornar mesmo antes de o dispositivo ser redefinido. 

Para redefinir o dispositivo usando a IU da Web local, execute as etapas a seguir.

1. Na IU da Web local, acesse **Manutenção > Redefinição do dispositivo**.
2. Selecione **Redefinir dispositivo**.

    ![Redefinir dispositivo](media/azure-stack-edge-return-device/device-reset-1.png)

3. Quando a confirmação for solicitada, examine o aviso e selecione **Sim** para continuar.

    ![Confirmar redefinição](media/azure-stack-edge-return-device/device-reset-2.png)  

A redefinição apaga os dados dos discos de dados do dispositivo. Dependendo da quantidade de dados em seu dispositivo, esse processo leva cerca de 30 a 40 minutos.

Como alternativa, conecte-se à interface do PowerShell do dispositivo e use o cmdlet `Reset-HcsAppliance` para apagar os dados dos discos de dados. Para obter mais informações, consulte [Redefinir seu dispositivo](azure-stack-edge-connect-powershell-interface.md#reset-your-device).

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

    2. Insira o número da marca de serviço que é um identificador de cinco ou mais caracteres que é exclusivo para o seu dispositivo. A marca de serviço está localizada no canto inferior direito do dispositivo (à medida que você enfrenta o dispositivo). Retire a marca de informações (é um painel de rótulo de deslizamento). Esse painel contém informações do sistema, como a marca de serviço, NIC, endereço MAC e assim por diante. 
    
    ![Rótulo de serviço número 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. Na lista suspensa, escolha um motivo para o retorno.

    ![Dispositivo de retorno 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. Em **detalhes de envio**:

    1. Forneça seu nome, nome da empresa e endereço completo da empresa. Insira um telefone comercial, incluindo o código de área e uma ID de email para notificação.
    2. Peça uma caixa de envio para devolução se precisar de uma. Responda **Sim** à pergunta **Preciso de uma caixa vazia para devolução**.

    ![Retornar dispositivo 3](media/azure-stack-edge-return-device/return-device-3.png)

4. Revise os **termos de privacidade** e marque a caixa de seleção em relação à observação que você examinou e concorda com os termos de privacidade.

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
    3. Se o número de rastreamento não for mencionado, a UPS exigirá que você pague uma taxa adicional durante a retirada.

    Em vez de agendar a retirada, você também pode cancelar o Azure Stack o Edge pro no local de distribuição mais próximo.

## <a name="delete-the-resource"></a>Excluir o recursos

Depois que o dispositivo é recebido no datacenter do Azure, o dispositivo é inspecionado quanto a danos ou sinais de violação.

- Se o dispositivo chegar intacto e estiver em bom formato, o medidor de cobrança será interrompido para esse recurso. Azure Stack equipe de operações do Edge Pro irá contatá-lo para confirmar que o dispositivo foi retornado. Em seguida, você pode excluir o recurso associado ao dispositivo no portal do Azure.
- Se o dispositivo chegar significativamente danificado, multas poderão ser aplicadas. Para mais informações, consulte [Perguntas frequentes sobre dispositivo perdido ou danificado](https://azure.microsoft.com/pricing/details/databox/edge/) e os [Termos de serviço do produto](https://www.microsoft.com/licensing/product-licensing/products).  


Você pode excluir o dispositivo no portal do Azure:

- Depois de ter feito o pedido e antes de o dispositivo ser preparado pela Microsoft.
- Depois de ter retornado o dispositivo para a Microsoft, ele passa a inspeção física no datacenter do Azure e Azure Stack a equipe de operações do Edge pro para confirmar que o dispositivo foi retornado.

Se você ativou o dispositivo em outra assinatura ou local, a Microsoft moverá seu pedido para a nova assinatura ou local em até um dia útil. Depois que o pedido for movido, você poderá excluir esse recurso.


Siga as etapas abaixo para excluir o dispositivo e o recurso em portal do Azure.

1. Na portal do Azure, vá para o recurso e, em seguida, para **Visão geral**. Na barra de comandos, selecione **Excluir**.

    ![Selecione excluir](media/azure-stack-edge-return-device/delete-resource-1.png)

2. Na folha **Excluir dispositivo**, digite o nome do dispositivo que quer excluir e selecione **Excluir**.

    ![Confirmar exclusão](media/azure-stack-edge-return-device/delete-resource-2.png)

Você será notificado após o dispositivo e o recurso associado for excluído com êxito.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [obter uma substituição Azure Stack dispositivo pro Edge](azure-stack-edge-replace-device.md).