---
title: Tutorial – Diagnosticar problema de comunicação entre redes usando o portal do Azure
titleSuffix: Azure Network Watcher
description: Neste tutorial, saiba como diagnosticar um problema de comunicação entre uma rede virtual do Azure conectada a um local ou a outra rede virtual, por meio de um gateway de rede virtual do Azure, usando a funcionalidade de diagnóstico de VPN do Observador de Rede.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: e9122a323556bebd4ed08ab99f2183313d04eaca
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066017"
---
# <a name="tutorial-diagnose-a-communication-problem-between-networks-using-the-azure-portal"></a>Tutorial: Diagnosticar um problema de comunicação entre redes usando o portal do Azure

Um gateway de rede virtual conecta uma rede virtual do Azure a uma rede virtual local ou a outra rede virtual. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Diagnosticar um problema com um gateway de rede virtual com a funcionalidade de diagnóstico de VPN do Observador de Rede
> * Diagnosticar um problema com uma conexão de gateway
> * Resolver um problema com um gateway

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para usar o diagnóstico de VPN, é necessário ter um gateway de VPN existente e em execução. Se você não tiver um gateway de VPN existente para diagnosticar, implante um usando um [script do PowerShell](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Execute o script do PowerShell por meio de:
- **Uma instalação local do PowerShell**: Este script exige o módulo `Az` do Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Caso precise atualizar, consulte [Instalar o Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.
- **O Azure Cloud Shell**: O [Azure Cloud Shell](https://shell.azure.com/powershell) tem a última versão do PowerShell instalada e configurada e conecta você ao Azure.

O script leva aproximadamente uma hora para criar um gateway de VPN. As etapas restantes pressupõem que o gateway que você está diagnosticando seja aquele implantado por esse script. Se, em vez disso, você diagnosticar seu próprio gateway existente, os resultados variarão.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="enable-network-watcher"></a>Habilitar o Observador de Rede

Se você já tiver um observador de rede habilitado na região Leste dos EUA, vá para [Diagnosticar um gateway](#diagnose-a-gateway).

1. No portal, selecione **Todos os serviços**. Na **caixa Filtro**, insira *Observador de Rede*. Quando o **Observador de Rede** for exibido nos resultados, selecione-o.
2. Selecione **Regiões** para expandi-la e, em seguida, selecione **...** à direita de **Leste dos EUA**, conforme mostrado na seguinte imagem:

    ![Habilitar o Observador de Rede](./media/diagnose-communication-problem-between-networks/enable-network-watcher.png)

3. Selecione **Habilitar o Observador de Rede**.

## <a name="diagnose-a-gateway"></a>Diagnosticar um gateway

1. No lado esquerdo do portal, selecione **Todos os serviços**.
2. Comece digitando *observador de rede* na caixa **Filtrar**. Quando os resultados da pesquisa exibirem **Observador de Rede**, selecione essa opção.
3. Em **FERRAMENTAS DE DIAGNÓSTICO DE REDE**, selecione **Diagnóstico de VPN**.
4. Selecione **Conta de armazenamento** e, em seguida, selecione a conta de armazenamento na qual deseja gravar as informações de diagnóstico.
5. Na lista de **Contas de armazenamento**, selecione a conta de armazenamento que deseja usar. Se você não tiver uma conta de armazenamento existente, selecione **+ Conta de armazenamento**, insira ou selecione as informações necessárias e, em seguida, selecione **Criar**, para criar uma. Se você criou um gateway de VPN usando o script nos [pré-requisitos](#prerequisites), talvez você deseje criar a conta de armazenamento no mesmo grupo de recursos, *TestRG1*, do gateway.
6. Na lista de **Contêineres**, selecione o contêiner que deseja usar e, em seguida, selecione **Selecionar**. Se você não tiver nenhum contêiner, selecione **+ Contêiner**, insira um nome para o contêiner e, em seguida, selecione **OK**.
7. Selecione um gateway e, em seguida, selecione **Iniciar a solução de problemas**. Conforme mostrado na seguinte imagem, o teste é executado em um gateway chamado **Vnet1GW**:

    ![Diagnóstico de VPN](./media/diagnose-communication-problem-between-networks/vpn-diagnostics.png)

8. Durante a execução do teste, o status **Em execução** é exibido na coluna **STATUS DA SOLUÇÃO DE PROBLEMAS**, em que **Não iniciado** é mostrado, na imagem anterior. O teste pode levar alguns minutos para ser executado.
9. Veja o status de um teste concluído. A seguinte imagem mostra os resultados do status de um teste de diagnóstico concluído:

    ![Captura de tela mostra os resultados de status de um teste de diagnóstico, não íntegro nesse exemplo, incluindo um resumo e detalhes.](./media/diagnose-communication-problem-between-networks/status.png)

    Você pode ver que o **STATUS DA SOLUÇÃO DE PROBLEMAS** é **Não íntegro**, bem como um **Resumo** e **Detalhes** do problema na guia **Status**.
10. Quando você seleciona a guia **Ação**, o diagnóstico de VPN fornece mais informações. No exemplo mostrado na seguinte imagem, o diagnóstico de VPN permite que você saiba que deve verificar a integridade de cada conexão:

    ![Captura de tela mostra a guia Ação, que fornece informações adicionais.](./media/diagnose-communication-problem-between-networks/action.png)

## <a name="diagnose-a-gateway-connection"></a>Diagnosticar uma conexão de gateway

Um gateway está conectado a outras redes por meio de uma conexão de gateway. O gateway e as conexões de gateway devem estar íntegros para a comunicação bem-sucedida entre uma rede virtual e uma rede conectada.

1. Conclua a etapa 7 de [Diagnosticar um gateway](#diagnose-a-gateway) novamente, desta vez, selecionando uma conexão. No seguinte exemplo, uma conexão chamada **VNet1toSite1** é testada:

    ![Captura de tela mostra como iniciar a solução de problemas para uma conexão selecionada.](./media/diagnose-communication-problem-between-networks/connection.png)

    O teste é executado por vários minutos.
2. Depois que o teste da conexão é concluído, você recebe resultados semelhantes aos resultados mostrados nas seguintes imagens nas guias **Status** e **Ação**:

    ![Status da conexão](./media/diagnose-communication-problem-between-networks/connection-status.png)

    ![Ação de conexão](./media/diagnose-communication-problem-between-networks/connection-action.png)

    O diagnóstico de VPN informa o que há de errado na guia **Status** e oferece diversas sugestões para descobrir o que está causando o problema na guia **Ação**.

    Se o gateway que você testou foi aquele implantado pelo [script](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) em [Pré-requisitos](#prerequisites), o problema na guia **Status** e os dois primeiros itens na guia **Ações** são exatamente a descrição do problema. O script configura um endereço IP de espaço reservado, 23.99.221.164, para o dispositivo de gateway de VPN local.

    Para resolver o problema, você precisa garantir que o gateway de VPN local está [configurado corretamente](../vpn-gateway/vpn-gateway-about-vpn-devices.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e alterar o endereço IP configurado pelo script para o gateway de rede local, para o endereço público real do gateway de VPN local.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você criou um gateway de VPN usando o script nos [pré-requisitos](#prerequisites) exclusivamente para concluir este tutorial e não precisar mais dele, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira *TestRG1* na caixa **Pesquisa** na parte superior do portal. Quando você vir **TestRG1** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira *TestRG1* para **DIGITE O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a diagnosticar um problema com um gateway de rede virtual. Talvez você deseje registrar em log a comunicação de rede bidirecionalmente em uma VM, de modo que possa examinar o log em busca de anomalias. Para saber como, vá para o próximo tutorial.

> [!div class="nextstepaction"]
> [Registrar o tráfego de rede de e para uma VM](network-watcher-nsg-flow-logging-portal.md)
