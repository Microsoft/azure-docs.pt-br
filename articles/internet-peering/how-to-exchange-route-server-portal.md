---
title: Conexão de emparelhamento para parceiros do Exchange com o servidor de rota usando o portal
titleSuffix: Azure
description: Criar ou modificar um emparelhamento do Exchange com o servidor de rota usando o portal do Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 7e10cd91eadd338217845b1504e8e9160bccfc98
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537184"
---
# <a name="create-or-modify-an-exchange-peering-with-route-server-in-azure-portal"></a>Criar ou modificar um emparelhamento do Exchange com o servidor de rota no portal do Azure

Este artigo descreve como criar um emparelhamento do Microsoft Exchange com um servidor de rota usando o portal do Azure. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.


## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [Guia passo a passo de emparelhamento do Exchange](walkthrough-exchange-all.md) antes de começar a configuração.
* Se você já tiver emparelhamentos do Exchange com a Microsoft que não são convertidos em recursos do Azure, confira [Converter um emparelhamento do Exchange herdado em um recurso do Azure usando o portal](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Criar e provisionar um emparelhamento do Exchange

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering-with-route-server"></a><a name=create></a>Criar um emparelhamento do Exchange com o servidor de rota


Como um Provedor de Internet do Exchange, você pode criar uma solicitação de emparelhamento do Exchange [Criando um Emparelhamento]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Na página **Criar um Emparelhamento**, na guia **Noções Básicas**, preencha as caixas conforme mostrado aqui:

    > [!div class="mx-imgBorder"] 
    > ![Registrar Serviço de Emparelhamento](./media/setup-basics-tab.png)

* Selecione sua Assinatura do Azure.

* Para o grupo de recursos, você pode escolher um grupo de recursos existente na lista suspensa ou criar um selecionando Criar. Para este exemplo, vamos criar um grupo de recursos.

* O nome corresponde ao nome do recurso e pode ser qualquer um de sua escolha.

* A região será selecionada automaticamente se você escolher um grupo de recursos existente. Se você optar por criar um novo grupo de recursos, também precisará escolher a região do Azure na qual deseja que o recurso resida.

    >[!NOTE]
    >A região em que um grupo de recursos reside é independente do local em que você deseja criar o emparelhamento com a Microsoft. Porém, é uma prática recomendada organizar seus recursos de emparelhamento em grupos de recursos que residem nas regiões do Azure mais próximas. Por exemplo, para emparelhamentos em Ashburn, você pode criar um grupo de recursos no Leste dos EUA ou no Leste dos EUA 2.

* Selecione seu ASN na caixa **PeerASN**.

    >[!IMPORTANT] 
    >Você só pode escolher um ASN com ValidationState como Aprovado antes de enviar uma solicitação de emparelhamento. Se você tiver acabado de enviar sua solicitação PeerAsn, aguarde cerca de 12 horas para que a associação do ASN seja aprovada. Se o ASN selecionado tiver validação pendente, você verá uma mensagem de erro. Se não vir o ASN que precisa escolher, verifique se selecionou a assinatura correta. Neste caso, verifique se você já criou o PeerAsn usando **[Associar o ASN Par à assinatura do Azure](https://go.microsoft.com/fwlink/?linkid=2129592)** .

* Selecione **Avançar: Configuração** para continuar.

#### <a name="configure-connections-and-submit"></a>Configurar conexões e enviar

1. Na página criar um emparelhamento, na guia configuração, preencha as caixas como mostrado aqui:

    > [!div class="mx-imgBorder"]
    > ![Configurar servidor de rota](./media/setup-exchange-conf-tab-routeserver.png)
 
    * Para tipo de emparelhamento, selecione **direto**
    * Para Microsoft Network, selecione **AS8075 com servidor de rota do Exchange**. 
    * Selecione SKU como **básico gratuito**. Não selecione Premium gratuitamente, pois ele é reservado para aplicativos especiais.
    * Selecione o local do **Metro** onde você deseja configurar o emparelhamento.

1. Em **conexões de emparelhamento**, selecione **criar novo**

1.  Em **conexão de emparelhamento direto**, preencha os seguintes detalhes de sessão BGP:

    > [!div class="mx-imgBorder"]
    > ![Captura de tela mostra o painel conexão direta de emparelhamento com detalhes adicionados.](./media/setup-exchange-conf-tab-direct-route.png)


     * Instalação de emparelhamento, selecione o local físico apropriado para o emparelhamento
     * Provedor de endereços de sessão, selecione par
     * O prefixo IPv4 da sessão será fornecido pelo par do provedor do Exchange
     * Endereço IPv4 da sessão par, será selecionado pelo ponto do Exchange para o servidor de rota de seu intervalo de prefixo IP.
     * Endereço IPv4 da sessão da Microsoft, será o IP do roteador alocado do intervalo de prefixo de IP.
     * A sessão IPv6 é opcional no momento.
     * O prefixo IPv4 anunciado máximo pode ser de até 20000. 
     * O uso para o serviço de emparelhamento está desabilitado por padrão. Ela pode ser habilitada quando o provedor do Exchange tiver assinado um contrato de serviço de emparelhamento com a Microsoft.

1. Após a conclusão, clique em **salvar**. 

1. Em criar um emparelhamento, você verá a validação aprovada. Depois que a validação for aprovada, clique em **criar**

    > [!div class="mx-imgBorder"]
    > ![Validação de configurações](./media/setup-exchange-conf-tab-validation.png)

    >[!NOTE]
    >Para provedores de serviços de Internet normais (ISP) que são um parceiro de serviço de emparelhamento da Microsoft, o registro de prefixos de IP do cliente é necessário. No entanto, no caso de parceiros do Exchange com um servidor de rota, é necessário registrar o cliente ASNs e não os prefixos. A mesma chave ASN seria válida para o registro de prefixo do cliente.

1. Selecione **ASNs registradas** na seção Configurações.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela mostra o painel de emparelhamento com o item de menu S NS registrado chamado.](./media/setup-exchange-registered-asn.png)

1. Selecione **Adicionar ASN registrado** para criar um novo cliente ASN em sua assinatura do Exchange.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela mostra o painel registrar um S N com nome e as caixas de texto S N.](./media/setup-exchange-register-new-asn.png)

1. Em registrar um ASN, selecione um nome, preencha o cliente ASN e clique em salvar.

1. Em ASNs registrados, haverá uma chave de prefixo associada atribuída a cada ASN. Como um provedor do Exchange, você precisará fornecer essa chave de prefixo ao cliente para que ele possa registrar o serviço de emparelhamento em sua assinatura.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela mostra o registro de um painel S NS com chaves de prefixo.](./media/setup-exchange-register-asn-prefixkey.png)




### <a name="verify-an-exchange-peering"></a><a name=get></a>Verificar um emparelhamento do Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificar um emparelhamento do Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Desprovisionar um emparelhamento do Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um Emparelhamento direto usando o portal](howto-direct-portal.md)
* [Converter um Emparelhamento direto herdado para o recurso do Azure usando o portal](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, confira [Perguntas frequentes sobre emparelhamento de Internet](faqs.md).
