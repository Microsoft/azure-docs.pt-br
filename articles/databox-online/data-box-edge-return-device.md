---
title: Retornar ou substituir seu dispositivo de Azure Data Box Edge | Microsoft Docs
description: Descreve como retornar ou substituir o dispositivo Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 07/19/2019
ms.author: alkohli
ms.openlocfilehash: a5fee604a529e9ca6153f6c189f199577ae65426
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356154"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>Retornar ou substituir seu dispositivo Azure Data Box Edge

Este artigo descreve como apagar os dados e, em seguida, retornar o dispositivo Azure Data Box Edge. Depois de retornar o dispositivo, você também pode excluir o recurso associado ao dispositivo ou solicitar um dispositivo de substituição.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Apagar os dados dos discos de dados no dispositivo
> * Abrir um tíquete de suporte para retornar seu dispositivo
> * Empacotar o dispositivo e agendar uma retirada
> * Excluir o recurso no portal do Azure
> * Obter um dispositivo de substituição

## <a name="erase-data-from-the-device"></a>Apagar dados do dispositivo

Para apagar os dados dos discos de dados do seu dispositivo, você precisa redefinir o dispositivo. Você pode redefinir o seu dispositivo usando a interface do usuário da Web local ou do PowerShell.

Antes de redefinir, crie uma cópia dos dados locais no dispositivo, se necessário. Você pode copiar os dados do dispositivo para um contêiner de armazenamento do Azure.

Para redefinir o dispositivo usando a interface do usuário da Web local, execute as etapas a seguir.

1. Na interface do usuário da Web local, vá para **manutenção > dispositivo redefinir**.
2. Selecione **Redefinir dispositivo**.

    ![Redefinir dispositivo](media/data-box-edge-return-device/device-reset-1.png)

3. Quando a confirmação for solicitada, examine o aviso e selecione **Sim** para continuar.

    ![Confirmar redefinição](media/data-box-edge-return-device/device-reset-2.png)  

A redefinição apaga os dados dos discos de dados do dispositivo. Dependendo da quantidade de dados em seu dispositivo, esse processo leva cerca de 30-40 minutos.

Como alternativa, conecte-se à interface do PowerShell do dispositivo e use `Reset-HcsAppliance` o cmdlet para apagar os dados dos discos de dados. Para obter mais informações, consulte [redefinir seu dispositivo](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Se você estiver trocando ou atualizando para um novo dispositivo, recomendamos que você redefina seu dispositivo somente depois de receber o novo dispositivo.
> - A redefinição do dispositivo somente exclui todos os dados locais do dispositivo. Os dados que estão na nuvem não são excluídos e coleta [cobranças](https://azure.microsoft.com/pricing/details/storage/). Esses dados precisam ser excluídos separadamente usando uma ferramenta de gerenciamento de armazenamento em nuvem como [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Abrir um tíquete de suporte

Para iniciar o processo de retorno, execute as etapas a seguir.

1. Abra um tíquete de suporte com Suporte da Microsoft indicando que você deseja retornar o dispositivo. Selecione o tipo de problema como **Data Box Edge hardware**.

    ![Abrir tíquete de suporte](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Um engenheiro de Suporte da Microsoft entrará em contato com você. Forneça os detalhes de envio.
3. Se você precisar de uma caixa de envio de retorno, poderá solicitá-la. Responda **Sim** para a pergunta **precisa de uma caixa vazia para retornar**.


## <a name="schedule-a-pickup"></a>Agendar uma retirada

1. Desligar o dispositivo. Na interface do usuário de web local, vá para **manutenção > configurações de energia**.
2. Selecione **desligar**. Quando a confirmação for solicitada, clique em **Sim** para continuar. Para obter mais informações, consulte [Manage Power](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Desconecte os cabos de alimentação e remova todos os cabos de rede do dispositivo.
4. Prepare o pacote de remessa usando sua própria caixa ou a caixa vazia que você recebeu do Azure. Coloque o dispositivo e os cabos de alimentação que foram enviados com o dispositivo na caixa.
5. Fixar o rótulo de envio recebido do Azure no pacote.
6. Agende uma retirada com sua transportadora regional. Se retornar o dispositivo nos EUA, sua operadora será UPS. Para agendar uma retirada:

    1. Ligue para o serviço de no-break local (linha gratuita específica do país).
    2. Em sua chamada, entre em contato com o número de rastreamento de remessa reversa, conforme mostrado no seu rótulo impresso.
    3. Se o número de rastreamento não estiver entre aspas, o no break exigirá que você pague um encargo adicional durante a retirada.

    Em vez de agendar a retirada, você também pode remover o Data Box Edge no local de distribuição mais próximo.

## <a name="delete-the-resource"></a>Excluir o recurso

Depois que o dispositivo é recebido no datacenter do Azure, o dispositivo é inspecionado quanto a danos ou sinais de violação.

- Se o dispositivo chegar intacto e em bom formato, o medidor de cobrança será interrompido para esse recurso. Suporte da Microsoft entrará em contato com você para confirmar que o dispositivo foi retornado. Em seguida, você pode excluir o recurso associado ao dispositivo no portal do Azure.
- Se o dispositivo chegar significativamente danificado, as multas poderão ser aplicadas. Para obter detalhes, consulte as [perguntas frequentes sobre o dispositivo perdido ou danificado](https://azure.microsoft.com/pricing/details/databox/edge/) e os [termos de serviço do produto](https://www.microsoft.com/licensing/product-licensing/products).  


Você pode excluir o dispositivo no portal do Azure:
-   Depois de ter colocado o pedido e antes de o dispositivo ser preparado pela Microsoft.
-   Depois de retornar o dispositivo à Microsoft, ele passa a inspeção física no datacenter do Azure e Suporte da Microsoft chamadas para confirmar que o dispositivo foi retornado.

Se você ativou o dispositivo em outra assinatura ou local, a Microsoft moverá seu pedido para a nova assinatura ou local dentro de um dia útil. Depois que a ordem for movida, você poderá excluir esse recurso.


Execute as etapas a seguir para excluir o dispositivo e o recurso em portal do Azure.

1. Na portal do Azure, vá para o recurso e, em seguida, para **visão geral**. Na barra de comandos, selecione **excluir**.

    ![Selecione excluir](media/data-box-edge-return-device/delete-resource-1.png)

2. Na folha **excluir dispositivo** , digite o nome do dispositivo que você deseja excluir e selecione **excluir**.

    ![Confirmar exclusão](media/data-box-edge-return-device/delete-resource-2.png)

Você será notificado após o dispositivo e o recurso associado for excluído com êxito.

## <a name="get-a-replacement-device"></a>Obter um dispositivo de substituição

Um dispositivo de substituição é necessário quando o dispositivo existente tem uma falha de hardware ou precisa de uma atualização. Execute as seguintes etapas quando o dispositivo tiver um problema de hardware:

1. [Abra um tíquete de suporte para o problema de hardware](#open-a-support-ticket). Suporte da Microsoft determinará se uma FRU (unidade de substituição de campo) não está disponível para esta instância ou se o dispositivo precisa de uma atualização de hardware. Em ambos os casos, o suporte solicitará um dispositivo de substituição.
2. [Crie um novo recurso](data-box-edge-deploy-prep.md#create-a-new-resource) para o dispositivo de substituição. Certifique-se de marcar a caixa de seleção com **um dispositivo data Box Edge**. 
3. Depois de receber um dispositivo de substituição, [Instale](data-box-edge-deploy-install.md) e [ative](data-box-edge-deploy-connect-setup-activate.md) o dispositivo de substituição no novo recurso.
4. Siga todas as etapas para retornar o dispositivo original:
    1. Abra outro tíquete para retornar o dispositivo original.
    2. [Apague os dados no dispositivo](#erase-data-from-the-device).
    3. [Agendar uma retirada](#schedule-a-pickup).
    5. [Exclua o recurso](#delete-the-resource) associado ao dispositivo retornado.



## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar a largura de banda](data-box-edge-manage-bandwidth-schedules.md).
