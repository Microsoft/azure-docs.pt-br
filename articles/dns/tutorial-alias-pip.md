---
title: 'Tutorial: Criar um registro de alias do DNS do Azure para se referir a um endereço IP público do Azure'
description: Este tutorial mostra como configurar um registro de alias do DNS do Azure para referenciar um endereço IP público do Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: d3017d09e94040d16950598dad360fe32930c16b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80985432"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Tutorial: Configurar um registro de alias para se referir a um endereço IP público do Azure 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma infraestrutura de rede.
> * Criar uma máquina virtual de servidor Web com um IP público.
> * Criar um registro de alias que aponta para o IP público.
> * Testar o registro de alias.


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter um nome de domínio disponível para hospedar no DNS do Azure para testar. Você deve ter controle total sobre esse domínio. Controle total inclui a capacidade de definir os registros NS (nomes de servidor) para o domínio.

Para obter instruções para hospedar seu domínio no DNS do Azure, confira o [Tutorial: Hospede seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

O domínio de exemplo usado neste tutorial é contoso.com, mas use seu próprio nome de domínio.

## <a name="create-the-network-infrastructure"></a>Criar a infraestrutura de rede
Primeiro, crie uma rede virtual e uma sub-rede para abrigar seus servidores Web.
1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No canto superior esquerdo do portal, escolha **Criar um recurso**. Insira *grupo de recursos* na caixa de pesquisa e crie um grupo de recursos chamado **RG-DNS-Alias-pip**.
3. Clique em **Criar um recurso** > **Rede** > **Rede virtual**.
4. Crie uma rede virtual denominada **VNet-Server**. Coloque-a no grupo de recursos **RG-DNS-Alias-pip** e nomeie a sub-rede **SN-Web**.

## <a name="create-a-web-server-virtual-machine"></a>Crie uma máquina virtual do servidor web
1. Escolha **Criar um recurso** > **VM do Windows Server 2016**.
2. Insira **Web-01** como o nome e coloque a VM no grupo de recursos **RG-DNS-Alias-TM**. Insira um nome de usuário e uma senha e selecione **OK**.
3. Em **Tamanho**, selecione uma SKU com 8 GB de RAM.
4. Em **Configurações**, marque a rede virtual **VNet-Servers** e a sub-rede **SN-Web**. Como portas de entrada públicas, escolha **HTTP** > **HTTPS** > **RDP (3389)** e, em seguida, **OK**.
5. Sobre o **resumo** página, selecione **criar**.

Esse procedimento leva alguns minutos para ser concluído. A máquina virtual terá uma NIC anexada, que terá um IP público dinâmico básico chamado Web-01-ip. O IP público será alterado sempre que a máquina virtual for reiniciada.

### <a name="install-iis"></a>Instalar o IIS

Instale o IIS em **Web-01**.

1. Conecte-se à **Web-01** e entre.
2. No painel **Gerenciador do Servidor**, escolha **Adicionar funções e recursos**.
3. Marque **Avançar** três vezes. Na página **Funções do Servidor**, selecione **Servidor Web (IIS)** .
4. Selecione **Adicionar Recursos** e selecione **Avançar**.
5. Selecione **Avançar** quatro vezes e selecione **Instalar**. Esse procedimento leva alguns minutos para ser concluído.
6. Depois que a instalação for concluída, selecione **Fechar**.
7. Abra um navegador da Web. Navegue até **localhost** para verificar se a página da Web do IIS padrão é exibida.

## <a name="create-an-alias-record"></a>Criar um registro de alias

Crie um registro de alias que aponta para o endereço IP público.

1. Escolha sua zona DNS do Azure para abri-la.
2. Escolha **Conjunto de registros**.
3. Na caixa de texto **Nome**, selecione **web01**.
4. Deixe o **Tipo** como um registro **A**.
5. Marque a caixa de seleção **Conjunto de Registros do Alias**.
6. Selecione **Escolher serviço do Azure** e selecione o endereço IP público **Web-01-ip**.

## <a name="test-the-alias-record"></a>Testar o registro de alias

1. No grupo de recursos **RG-DNS-Alias-pip**, selecione a máquina virtual **Web-01**. Observe o endereço IP público.
1. Em um navegador da Web, navegue até o nome de domínio totalmente qualificado para a máquina virtual Web01-01. Um exemplo é **web01.contoso.com**. Você agora verá a página da Web do IIS padrão.
2. Feche o navegador da web.
3. Pare a máquina virtual **Web-01** e, em seguida, reinicie-a.
4. Quando a máquina virtual for reiniciada, observe o novo endereço IP público da máquina virtual.
5. Abra um novo navegador. Navegue novamente até o nome de domínio totalmente qualificado para a máquina virtual Web01-01. Um exemplo é **web01.contoso.com**.

Esse procedimento funciona porque você usou um registro de alias para apontar para o recurso de endereço IP público e não um registro A padrão.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisar mais dos recursos criados para este tutorial, exclua o grupo de recursos **RG-DNS-Alias-pip**.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um registro de alias para fazer referência a um endereço IP público do Azure. Para saber mais sobre os aplicativos Web e o DNS do Azure, continue com o tutorial para aplicativos Web.

> [!div class="nextstepaction"]
> [Criar registros DNS para um aplicativo Web em um domínio personalizado](./dns-web-sites-custom-domain.md)
