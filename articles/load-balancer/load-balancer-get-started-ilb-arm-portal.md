---
title: Criar um balanceador de carga interno - Portal do Azure | Microsoft Docs
description: Saiba como criar um balanceador de carga interno no Resource Manager usando o portal do Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ac14fb9-8d14-4892-bfe6-8bc74c48ae2c
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3be595b03f667cf9700d2f17eb2080aa74f41dd9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Criar um Balanceador de carga interno no portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modelo](../load-balancer/load-balancer-get-started-ilb-arm-template.md)


[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo aborda usando o modelo de implantação do Gerenciador de Recursos, que a Microsoft recomenda para a maioria das novas implantações em vez de do [modelo de implantação clássico](load-balancer-get-started-ilb-classic-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Introdução à criação de um balanceador de carga interno usando o portal do Azure

Use as etapas a seguir para criar um balanceador de carga interno no portal do Azure.

1. Abra um navegador, navegue até o [portal do Azure](http://portal.azure.com) e entre com sua conta do Azure.
2. No canto superior esquerdo da tela, clique em **Novo** > **Rede** > **Balanceador de carga**.
3. Na folha **Criar balanceador de carga**, insira um **Nome** para o balanceador de carga.
4. Em **Esquema**, clique em **Interno**.
5. Clique em **Rede virtual**e selecione a rede virtual na qual você deseja criar o balanceador de carga.

   > [!NOTE]
   > Caso você não veja a rede virtual que deseja usar, verifique o **Local** que você está usando para o balanceador de carga e altere-o adequadamente.

6. Clique em **Sub-rede**e selecione a sub-rede onde você quer criar o balanceador de carga.
7. Em **Atribuição do endereço IP**, clique em **Dinâmico** ou **Estático**, dependendo de você querer que o endereço IP para o balanceador de carga seja fixo (estático) ou não.

   > [!NOTE]
   > Se você optar por usar um endereço IP estático, será necessário fornecer um endereço para o balanceador de carga.

8. Em **Grupo de recursos**, especifique o nome de um novo grupo de recursos para o balanceador de carga ou clique em **selecionar existente** e selecione um grupo de recursos existente.
9. Clique em **Criar**.

## <a name="configure-load-balancing-rules"></a>Configuração de regras de balanceamento de carga

Após a criação do balanceador de carga, navegue até o recurso do balanceador de carga para configurá-lo.
Configure um pool de endereços back-end e uma investigação antes de configurar uma regra de balanceamento de carga.

### <a name="step-1-configure-a-backend-pool"></a>Etapa 1: Configurar um pool de back-end

1. No portal do Azure, clique em **Procurar** > **Balanceadores de carga** e então clique no balanceador de carga que você criou anteriormente.
2. Na folha **Configurações**, clique nos **Pools de back-end**.
3. Na folha **Pools de endereços de back-end**, clique em **Adicionar**.
4. Na folha **Adicionar pool de back-end**, insira um **Nome** para o pool de back-end e clique em **OK**.

### <a name="step-2-configure-a-probe"></a>Etapa 2: Configurar uma investigação

1. No portal do Azure, clique em **Procurar** > **Balanceadores de carga** e então clique no balanceador de carga que você criou anteriormente.
2. Na folha **Configurações**, clique em **Investigações**.
3. Na folha **Investigações**, clique em **Adicionar**.
4. Na folha **Adicionar investigação**, insira um **Nome** para a investigação.
5. Em **Protocolo**, selecione **HTTP** (para sites) ou **TCP** (para outros aplicativos baseados em TCP).
6. Em **Porta**, especifique a porta a ser usada ao acessar a investigação.
7. Em **Caminho** (apenas para investigações HTTP), especifique o caminho a ser usado como uma investigação.
8. Em **Intervalo** , especifique a frequência de investigação do aplicativo.
9. Em **Limite não íntegro**, especifique quantas tentativas devem falhar antes que a máquina virtual de back-end seja marcada como não íntegra.
10. Clique em **OK** para criar a investigação.

### <a name="step-3-configure-load-balancing-rules"></a>Etapa 3: Configurar regras de balanceamento de carga

1. No portal do Azure, clique em **Procurar** > **Balanceadores de carga** e então clique no balanceador de carga que você criou anteriormente.
2. Na folha **Configurações**, clique em **Regras de balanceamento da carga**.
3. Na folha **Regras de balanceamento da carga**, clique em **Adicionar**.
4. Na folha **Adicionar regra balanceamento da carga**, insira um **Nome** para a regra.
5. Em **Protocolo**, selecione **HTTP** (para sites) ou **TCP** (para outros aplicativos baseados em TCP).
6. Em **Porta**, especifique a porta que os clientes conectam no balanceador de carga.
7. Em **Porta back-end**, especifique a porta a ser usada no pool de back-end (normalmente, a porta do balanceador de carga e a porta de back-end são iguais).
8. Em **Pool de back-end**, selecione o pool de back-end criado anteriormente.
9. Em **Persistência da sessão**, selecione como você deseja que as sessões persistam.
10. Em **Tempo limite de ociosidade (minutos)**, especifique o tempo limite de ociosidade.
11. Em **IP flutuante (retorno de servidor direto)**, clique em **Desabilitado** ou **Habilitado**.
12. Clique em **OK**.

## <a name="next-steps"></a>Próximas etapas

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

