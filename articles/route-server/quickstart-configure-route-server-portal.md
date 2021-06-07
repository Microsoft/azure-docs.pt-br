---
title: 'Guia de início rápido: criar e configurar um Servidor de Rota usando o portal do Azure'
description: Neste guia de início rápido, você aprenderá a criar e configurar um Servidor de Rota usando o portal do Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: f76c48af4f5ebc8013daad457f9973cf7792c7c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547978"
---
# <a name="quickstart-create-and-configure-route-server-using-the-azure-portal"></a>Guia de início rápido: criar e configurar um Servidor de Rota usando o portal do Azure

Este artigo ajudará você a configurar um Servidor de Rota do Azure para executar um emparelhamento com uma NVA (Solução de Virtualização de Rede) em sua rede virtual usando o portal do Azure. O Servidor de Rota do Azure aprenderá e programará as rotas da NVA nas máquinas virtuais da rede virtual. O Servidor de Rota do Azure também vai anunciar as rotas de rede virtual para a NVA. Para obter mais informações, leia [Servidor de Rota do Azure](overview.md).

> [!IMPORTANT]
> No momento, o Servidor de Rota do Azure (versão prévia) está na versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Examine os [limites de serviço do Servidor de Rota do Azure](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Criar um Servidor de Rota

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Entre na sua conta do Azure e selecione sua assinatura.

Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

### <a name="create-a-route-server"></a>Criar um Servidor de Rota

1. Ir para https://aka.ms/routeserver.

1. Clique em **+Criar Servidor de Rota**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Uma captura de tela da página de aterrissagem do Servidor de Rota."::: 

1. Na página **Criar Servidor de Rota**, insira ou selecione as informações necessárias.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/create-route-server-page.png" alt-text="Uma captura de tela da página Criar Servidor de Rota.":::     

    | Configurações | Valor |
    |----------|-------|
    | Assinatura | Selecione a assinatura do Azure que você deseja usar para implantar o Servidor de Rota. |
    | Resource group | Selecione um grupo de recursos no qual o Servidor de Rota será criado. Se você não tiver um grupo de recursos atualmente, crie um. |
    | Nome | Insira um nome para o Servidor de Rota. |
    | Região | Selecione a região na qual o Servidor de Rota será criado. Selecione a mesma região da rede virtual criada anteriormente para conferir a rede virtual na lista suspensa. |
    | Rede Virtual | Selecione a rede virtual na qual o Servidor de Rota será criado. É possível criar uma rede virtual ou usar uma existente. Caso esteja usando uma rede virtual existente, verifique se ela tem, no mínimo, espaço suficiente para uma sub-rede /27 com o objetivo de acomodar o requisito de sub-rede do Servidor de Rota. Caso não veja sua rede virtual na lista suspensa, verifique se você selecionou o Grupo de Recursos ou a região de modo adequado. |
    | Sub-rede | Após criar ou selecionar uma rede virtual, o campo da sub-rede será exibido. Essa sub-rede será dedicada somente ao Servidor de Rota. Clique em **Gerenciar configuração de sub-rede** e crie uma sub-rede do Servidor de Rota do Azure. Clique em **+Sub-rede** e crie uma sub-rede usando as seguintes diretrizes:</br><br>– A sub-rede deverá ser nomeada como *RouteServerSubnet*.</br><br>– A sub-rede deverá ser no mínimo /27 ou maior.</br> |

1. Clique em **Examinar + criar** e examine o resumo, depois clique em **Criar**. 

    > [!NOTE]
    > A implantação do Servidor de Rota levará cerca de 20 minutos.

## <a name="set-up-peering-with-nva"></a>Configurar um emparelhamento com a NVA

Esta seção ajudará você a configurar um emparelhamento via protocolo BGP com a NVA.

1. Acesse a opção [Servidor de Rota](https://aka.ms/routeserver) no portal do Azure e selecione o Servidor de Rota que você deseja configurar.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/select-route-server.png" alt-text="Uma captura de tela da lista de Servidores de Rota."::: 

1. Selecione a opção **Pares** em *Configurações* no painel de navegação esquerdo. Depois clique em **+Adicionar** para adicionar um par.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/peers-landing-page.png" alt-text="Uma captura de tela da página de aterrissagem dos pares."::: 

1. Insira as informações a seguir sobre o par da NVA.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/add-peer-page.png" alt-text="Uma captura de tela da página Adicionar par.":::

    | Configurações | Valor |
    |----------|-------|
    | Nome | Nomeie o emparelhamento entre o Servidor de Rota e a NVA. |
    | ASN |  Insira o ASN (Número de Sistemas Autônomos) da NVA. |
    | Endereço IPv4 | Insira o endereço IP da NVA com o qual o Servidor de Rota se comunicará a fim de estabelecer o roteamento BGP. |

1. Clique em **Adicionar** para adicionar esse par.

## <a name="complete-the-configuration-on-the-nva"></a>Conclua a configuração na NVA

Será preciso obter os IPs e o ASN do par do Servidor de Rota do Azure para concluir a configuração na NVA a fim de estabelecer uma sessão de roteamento de BGP. É possível obter essas informações na página de visão geral do Servidor de Rota.

:::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-overview.png" alt-text="Uma captura de tela da página de visão geral do Servidor de Rota.":::

## <a name="configure-route-exchange"></a>Configurar troca de rota

Caso tenha um gateway do ExpressRoute e/ou um gateway de VPN e deseje que eles troquem rotas com o Servidor de Rota, será possível habilitar a troca de rotas.

1. Acesse a opção [Servidor de Rota](https://aka.ms/routeserver) no portal do Azure e selecione o Servidor de Rota que você deseja configurar.

1. Selecione a opção **Configuração** em *Configurações* no painel de navegação esquerdo.

1. Clique em **Habilitar** para definir a configuração **Ramificação por Ramificação**, depois clique em **Salvar**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/enable-route-exchange.png" alt-text="Uma captura de tela de como habilitar a troca de rotas.":::

## <a name="clean-up-resources"></a>Limpar os recursos

Caso não precise mais do Servidor de Rota do Azure, clique em **Excluir** na página de visão geral para desprovisionar o Servidor de Rota.

:::image type="content" source="./media/quickstart-configure-route-server-portal/delete-route-server.png" alt-text="Uma captura de tela de como excluir o Servidor de Rota.":::

## <a name="next-steps"></a>Próximas etapas

Depois de criar o Servidor de Rota do Azure, continue a aprender como o Servidor de Rota do Azure interage com os gateways de VPN e ExpressRoute: 

> [!div class="nextstepaction"]
> [Suporte à VPN do Azure e ao Azure ExpressRoute](expressroute-vpn-support.md)
