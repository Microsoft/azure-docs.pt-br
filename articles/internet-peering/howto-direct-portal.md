---
title: Criar ou modificar um emparelhamento direto usando o portal do Azure
titleSuffix: Azure
description: Criar ou modificar um emparelhamento Direto usando o portal do Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 1a89ce873c53e94036aa4f8ac2c2870365924187
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537201"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Criar ou modificar um emparelhamento Direto usando o portal do Azure

Este artigo descreve como criar um emparelhamento Direto da Microsoft para um provedor de serviços de Internet ou um provedor de Internet usando o portal do Azure. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, conclua este guia usando o [Azure PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [Guia passo a passo de emparelhamento Direto](walkthrough-direct-all.md) antes de começar a configuração.
* Se você já tiver conexões de emparelhamento Direto com a Microsoft que não são convertidas nos recursos do Azure, confira [Converter um emparelhamento Direto herdado para um recurso do Azure usando o portal](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Criar e provisionar um emparelhamento Direto

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Criar um emparelhamento Direto

Como um provedor de serviços de Internet ou um provedor de Internet, você pode criar uma solicitação de emparelhamento direto [Criando um Emparelhamento]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Na página **Criar um Emparelhamento**, na guia **Noções Básicas**, preencha as caixas conforme mostrado aqui:


    ![Captura de tela mostra a guia criar um nível básico da página de emparelhamento com o link criar novo chamado.](./media/setup-basics-tab.png)

2. Selecione sua Assinatura do Azure.

3. Para o grupo de recursos, você pode escolher um grupo de recursos existente na lista suspensa ou criar um selecionando Criar. Para este exemplo, vamos criar um grupo de recursos.

4. O nome corresponde ao nome do recurso e pode ser qualquer um de sua escolha.

5. A região será selecionada automaticamente se você escolher um grupo de recursos existente. Se você optar por criar um novo grupo de recursos, também precisará escolher a região do Azure na qual deseja que o recurso resida.

    >[!NOTE]
    > A região em que um grupo de recursos reside é independente do local em que você deseja criar o emparelhamento com a Microsoft. Porém, é uma prática recomendada organizar seus recursos de emparelhamento em grupos de recursos que residem nas regiões do Azure mais próximas. Por exemplo, para emparelhamentos em Ashburn, você pode criar um grupo de recursos no Leste dos EUA ou no Leste dos EUA 2.

6. Selecione seu ASN na caixa **PeerASN**.

    >[!IMPORTANT]
    >Você só pode escolher um ASN com ValidationState como Aprovado antes de enviar uma solicitação de emparelhamento. Se você tiver acabado de enviar sua solicitação PeerAsn, aguarde cerca de 12 horas para que a associação do ASN seja aprovada. Se o ASN selecionado tiver validação pendente, você verá uma mensagem de erro. Se não vir o ASN que precisa escolher, verifique se selecionou a assinatura correta. Neste caso, verifique se você já criou o PeerAsn usando **[Associar o ASN Par à assinatura do Azure](https://go.microsoft.com/fwlink/?linkid=2129592)** .

7. Selecione **Avançar: Configuração** para continuar.



    ![Captura de tela mostra a guia criar um nível básico da página de emparelhamento com todos os valores inseridos.](./media/setup-direct-basics-filled-tab.png)


#### <a name="configure-connections-and-submit"></a>Configurar conexões e enviar
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar emparelhamento Direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificar um emparelhamento Direto
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Desprovisionar um emparelhamento Direto
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um emparelhamento do Exchange usando o portal](howto-exchange-portal.md)
* [Converter um emparelhamento do Exchange herdado para um recurso do Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, confira [Perguntas frequentes sobre emparelhamento de Internet](faqs.md).
