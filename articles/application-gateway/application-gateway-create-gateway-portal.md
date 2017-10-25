---
title: Criar um Gateway de Aplicativo - Portal do Azure | Microsoft Docs
description: Saiba como criar um Gateway de Aplicativo usando o portal
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 17d09ce98c40717d1db0927f791a7c97ea7835e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-the-portal"></a>Criar um gateway de aplicativo com o portal

O [Gateway de Aplicativo](application-gateway-introduction.md) é uma solução de virtualização dedicada que fornece o ADC (controlador de entrega de aplicativos) como serviço, oferecendo várias funcionalidades de balanceamento de carga de camada 7 para o aplicativo. Este artigo o orienta durante as etapas para criar um Gateway de Aplicativo usando o portal do Azure e adicionar um servidor existente como um membro de back-end.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no portal do Azure em [http://portal.azure.com](http://portal.azure.com)

## <a name="create-application-gateway"></a>Criar um gateway de aplicativo

Para criar um gateway de aplicativo, conclua as etapas a seguir. O gateway de aplicativo exige sua própria sub-rede. Ao criar uma rede virtual, certifique-se de deixar espaço de endereço suficiente para ter várias sub-redes. Depois que o gateway de aplicativo for implantado em uma sub-rede, apenas outros gateways de aplicativo poderão ser adicionados a ele.

1. No painel Favoritos do portal, clique em **Novo**
1. Na folha **Novo**, clique em **Rede**. Na folha **Rede**, clique em **Gateway de Aplicativo**, conforme mostrado na seguinte imagem:

    ![Criação de um gateway de aplicativo][1]

1. Na folha **Informações Básicas** exibida, insira os seguintes valores e clique em **OK**:

   | **Configuração** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|AdatumAppGateway|O nome do gateway de aplicativo|
   |**Camada**|Standard|Os valores disponíveis são Standard e WAF. Visite a página [Firewall do aplicativo Web](application-gateway-web-application-firewall-overview.md) para saber mais sobre o WAF.|
   |**Tamanho do SKU**|Média|As opções ao escolher a camada Standard são Pequeno, Médio e Grande. Ao escolher a camada do WAF, as opções são apenas Médio e Grande.|
   |**Contagem de instâncias**|2|Número de instâncias do gateway de aplicativo para alta disponibilidade. Contagens de instância iguais a 1 devem ser usadas apenas para fins de teste.|
   |**Assinatura**|[Sua assinatura]|Selecione uma assinatura na qual o gateway de aplicativo será criado.|
   |**Grupo de recursos**|**Criar novo:** AdatumAppGatewayRG|Crie um grupos de recursos. O nome do grupo de recursos deve ser exclusivo na assinatura selecionada. Para saber mais sobre grupos de recursos, leia o artigo [Visão geral do Gerenciador de Recursos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Localidade**|Oeste dos EUA||

1. Na folha **Configurações** exibida em **Rede virtual**, clique em **Escolher uma rede virtual**. A folha **Escolher rede virtual** é aberta.  Clique em **Criar nova** para abrir a folha **Criar rede virtual**.

   ![escolher uma rede virtual][2]

1. Na folha **Criar rede virtual**, insira os valores a seguir e clique em **OK**. As folhas **Criar rede virtual** e **Escolher rede virtual** são fechadas. Esta etapa popula o campo **Sub-rede** na folha **Configurações** com a sub-rede escolhida.

   | **Configuração** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|AdatumAppGatewayVNET|Nome do gateway de aplicativo|
   |**Espaço de Endereço**|10.0.0.0/16|Esse é o espaço de endereço da rede virtual|
   |**Nome da sub-rede**|AppGatewaySubnet|Nome da sub-rede do gateway de aplicativo|
   |**Intervalo de endereços da sub-rede**|10.0.0.0/28|Essa sub-rede permite sub-redes adicionais na rede virtual para membros do pool de back-end|

1. Na folha **Configurações** em **Configuração de IP de front-end**, escolha **Público** como o **Tipo de endereço IP**

1. Na folha **Configurações** em **Endereço IP público**, clique em **Escolher um endereço IP público**; a folha **Escolher o endereço IP público** abrirá, clique em **Criar novo**.

   ![escolher o IP público][3]

1. Na folha **Criar endereço IP público**, aceite o valor padrão e clique em **OK**. A folha fecha e popula o **Endereço IP público** com o endereço IP público escolhido.

1. Na folha **Configurações** em **Configuração do ouvinte**, clique em **HTTP** sob **Protocolo**. Insira a porta a ser usada no campo **Porta**.

2. Clique em **OK** na folha **Configurações** para continuar.

1. Examine as configurações na folha **Resumo** e clique em **OK** para iniciar a criação do gateway de aplicativo. A criação de um gateway de aplicativo é uma tarefa de execução longa e levará muito tempo para ser concluída.

## <a name="add-servers-to-backend-pools"></a>Adicionar servidores aos pools de back-end

Depois que você criar o gateway de aplicativo, os sistemas que hospedam o aplicativo que terá a carga balanceada ainda precisará ser adicionado ao gateway de aplicativo. Os endereços IP, o FQDN ou as NICs desses servidores são adicionados aos pools de endereços de back-end.

### <a name="ip-address-or-fqdn"></a>Endereço IP ou FQDN

1. Com o gateway de aplicativo criado, no painel **Favoritos** do portal do Azure, clique em **Todos os recursos**. Clique no gateway de aplicativo **AdatumAppGateway** na folha Todos os recursos. Se a assinatura selecionada já contiver vários recursos, você poderá inserir **AdatumAppGateway** na caixa **Filtrar por nome...** para acessar facilmente o gateway de aplicativo.

1. O gateway de aplicativo criado é exibido. Clique em **Pools de back-end** e selecione o pool de back-end atual **appGatewayBackendPool**. Isso abrirá a folha **appGatewayBackendPool**.

   ![Pools de back-end do Gateway de Aplicativo][4]

1. Clique em **Adicionar Destino** para adicionar endereços IP de valores de FQDN. Escolha **Endereço IP ou FQDN** como o **Tipo** e insira o endereço IP ou o FQDN no campo. Repita esta etapa para outros membros do pool de back-end. Quando terminar, clique em **Salvar**.

### <a name="virtual-machine-and-nic"></a>Máquina virtual e NIC

Também é possível adicionar NICs de máquina virtual como membros do pool de back-end. Somente as máquinas virtuais que estão dentro da mesma rede virtual que o Gateway de Aplicativo ficarão disponíveis na lista suspensa.

1. Com o gateway de aplicativo criado, no painel **Favoritos** do portal do Azure, clique em **Todos os recursos**. Clique no gateway de aplicativo **AdatumAppGateway** na folha Todos os recursos. Se a assinatura selecionada já contiver vários recursos, você poderá inserir **AdatumAppGateway** na caixa **Filtrar por nome...** para acessar facilmente o gateway de aplicativo.

1. O gateway de aplicativo criado é exibido. Clique em **Pools de back-end** e selecione o pool de back-end atual **appGatewayBackendPool**. Isso abrirá a folha **appGatewayBackendPool**.

   ![Pools de back-end do Gateway de Aplicativo][5]

1. Clique em **Adicionar Destino** para adicionar endereços IP de valores de FQDN. Escolha **Máquina Virtual** como o **Tipo** e selecione a máquina virtual e a NIC a serem usadas. Quando terminar, clique em **Salvar**

   > [!NOTE]
   > Somente as máquinas virtuais que estão na mesma rede virtual do gateway de aplicativo ficarão disponíveis na caixa suspensa.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos, o gateway de aplicativo e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos na folha do gateway de aplicativo e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Nesse cenário, você implantou um gateway de aplicativo e adicionou um servidor ao back-end. A próxima etapa é configurar o gateway de aplicativo modificando as configurações e ajustando as regras no gateway. Essas etapas podem ser encontradas nos artigos a seguir:

Saiba como criar investigações de integridade personalizados visitando [Criar uma investigação de integridade personalizada](application-gateway-create-probe-portal.md)

Saiba como configurar o Descarregamento de SSL e levar a descriptografia SSL cara longe dos seus servidores Web visitando [Configurar Descarregamento de SSL](application-gateway-ssl-portal.md)

Saiba como proteger seus aplicativos com o [Firewall do Aplicativo Web](application-gateway-webapplicationfirewall-overview.md), um recurso do gateway de aplicativo.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png
