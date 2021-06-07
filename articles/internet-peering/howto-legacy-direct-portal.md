---
title: Converter um Emparelhamento direto herdado no recurso do Azure usando o portal do Azure
titleSuffix: Azure
description: Converter um Emparelhamento direto herdado no recurso do Azure usando o portal do Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 496774f034c861131624b552b1acefca9f399ea8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84700256"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Converter um Emparelhamento direto herdado no recurso do Azure usando o portal do Azure

Este artigo descreve como converter um emparelhamento direto herdado existente em um recurso do Azure usando o portal do Azure.

Se preferir, conclua este guia usando o [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [Guia passo a passo de emparelhamento direto](walkthrough-direct-all.md) antes de começar a configuração.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Converter um emparelhamento direto herdado em um recurso do Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Converter um emparelhamento direto herdado

Como um Provedor de serviços da Internet, você pode converter conexões de Emparelhamento direto herdadas usando [Criar um emparelhamento]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Na página **Criar um Emparelhamento**, na guia **Noções Básicas**, preencha as caixas conforme mostrado aqui:

    > [!div class="mx-imgBorder"] 
    > ![Registrar Serviço de Emparelhamento](./media/setup-basics-tab.png)

*    Selecione sua Assinatura do Azure.

* Para o grupo de recursos, você pode escolher um grupo de recursos existente na lista suspensa ou criar um selecionando Criar. Para este exemplo, vamos criar um grupo de recursos.

* O nome corresponde ao nome do recurso e pode ser qualquer um de sua escolha.

* A região será selecionada automaticamente se você escolher um grupo de recursos existente. Se você optar por criar um novo grupo de recursos, também precisará escolher a região do Azure na qual deseja que o recurso resida.

>[!NOTE]
>A região em que um grupo de recursos reside é independente do local em que você deseja criar o emparelhamento com a Microsoft. Porém, é uma prática recomendada organizar seus recursos de emparelhamento em grupos de recursos que residem nas regiões do Azure mais próximas. Por exemplo, para emparelhamentos em Ashburn, você pode criar um grupo de recursos no Leste dos EUA ou no Leste dos EUA 2.

* Selecione seu ASN na caixa **PeerASN**.

>[!IMPORTANT] 
>Você só pode escolher um ASN com ValidationState como Aprovado antes de enviar uma solicitação de emparelhamento. Se você tiver acabado de enviar sua solicitação PeerAsn, aguarde cerca de 12 horas para que a associação do ASN seja aprovada. Se o ASN selecionado tiver validação pendente, você verá uma mensagem de erro. Se não vir o ASN que precisa escolher, verifique se selecionou a assinatura correta. Neste caso, verifique se você já criou o PeerAsn usando **[Associar o Peer ASN à assinatura do Azure](https://go.microsoft.com/fwlink/?linkid=2129592)** .

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Iniciar o recurso e definir as configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar conexões e enviar
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar Emparelhamento direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, confira [Perguntas frequentes sobre emparelhamento de Internet](faqs.md).

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um Emparelhamento direto usando o portal](howto-direct-portal.md)
