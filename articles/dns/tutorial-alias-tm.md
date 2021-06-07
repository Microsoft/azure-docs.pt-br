---
title: 'Tutorial: Criar um registro de alias para dar suporte a nomes de domínio apex – Gerenciador de Tráfego'
titleSuffix: Azure DNS
description: Este tutorial mostra como configurar um registro de alias do DNS do Azure para dar suporte usando seu nome de domínio apex com o Gerenciador de Tráfego.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: 4bdfc950cc1277809811dc2c548a57cc2138a8e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "77149942"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Tutorial: Configurar um registro de alias para dar suporte a nomes de domínio apex com o Gerenciador de Tráfego 

Você pode criar um registro de alias para o apex do nome de domínio fazer referência a um perfil do Gerenciador de Tráfego. Um exemplo é contoso.com. Em vez de usar um serviço de redirecionamento, configure o DNS do Azure para fazer referência a um perfil do Gerenciador de Tráfego diretamente da sua zona. 


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma VM host e uma infraestrutura de rede.
> * Criar um perfil do Gerenciador de Tráfego.
> * Criar um registro de alias.
> * Testar o registro de alias.


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter um nome de domínio disponível para hospedar no DNS do Azure para testar. Você deve ter controle total sobre esse domínio. Controle total inclui a capacidade de definir os registros NS (nomes de servidor) para o domínio.

Para obter instruções sobre como hospedar seu domínio no DNS do Azure, confira [Tutorial: Hospedar seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

O domínio de exemplo usado neste tutorial é contoso.com, mas use seu próprio nome de domínio.

## <a name="create-the-network-infrastructure"></a>Criar a infraestrutura de rede
Primeiro, crie uma rede virtual e uma sub-rede para abrigar seus servidores Web.
1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No canto superior esquerdo do portal, escolha **Criar um recurso**. Insira *grupo de recursos* na caixa de pesquisa e crie um grupo de recursos chamado **RG-DNS-Alias-TM**.
3. Clique em **Criar um recurso** > **Rede** > **Rede virtual**.
4. Crie uma rede virtual chamada **VNet-Servers**. Coloque-a no grupo de recursos **RG-DNS-Alias-TM** e nomeie a sub-rede **SN-Web**.

## <a name="create-two-web-server-virtual-machines"></a>Criar duas máquinas virtuais do servidor web
1. Escolha **Criar um recurso** > **VM do Windows Server 2016**.
2. Insira **Web-01** como o nome e coloque a VM no grupo de recursos **RG-DNS-Alias-TM**. Insira um nome de usuário e uma senha e selecione **OK**.
3. Em **Tamanho**, selecione uma SKU com 8 GB de RAM.
4. Em **Configurações**, marque a rede virtual **VNet-Servers** e a sub-rede **SN-Web**.
5. Selecione **Endereço IP público**. Em **Atribuição**, escolha **Estático** e, em seguida, **OK**.
6. Como portas de entrada públicas, escolha **HTTP** > **HTTPS** > **RDP (3389)** e, em seguida, **OK**.
7. Sobre o **resumo** página, selecione **criar**. Esse procedimento leva alguns minutos para ser concluído.

Repita esse procedimento para criar outra máquina virtual denominada **Web-02**.

### <a name="add-a-dns-label"></a>Adicione um rótulo DNS
Os endereços IP públicos precisam de um rótulo DNS para funcionar com o Gerenciador de Tráfego.
1. No grupo de recursos **RG-DNS-Alias-TM**, escolha o endereço IP público **Web-01-ip**.
2. Em **Configurações**, escolha **Configuração**.
3. Na caixa de texto do rótulo de nome DNS, insira **web01pip**.
4. Clique em **Salvar**.

Repita esse procedimento para o endereço IP público **Web-02-ip** usando **web02pip** como o rótulo de nome DNS.

### <a name="install-iis"></a>Instalar o IIS

Instale o IIS em ambos **Web-01** e **Web 02**.

1. Conecte-se à **Web-01** e entre.
2. No painel **Gerenciador do Servidor**, escolha **Adicionar funções e recursos**.
3. Marque **Avançar** três vezes. Na página **Funções do Servidor**, selecione **Servidor Web (IIS)** .
4. Escolha **Adicionar Recursos** e, em seguida, **Avançar**.
5. Marque **Avançar** quatro vezes. Em seguida, selecione **Instalar**. Esse procedimento leva alguns minutos para ser concluído.
6. Quando a instalação for concluída, marque **Fechar**.
7. Abra um navegador da Web. Navegue até **localhost** para verificar se a página da Web do IIS padrão é exibida.

Repita esse procedimento para instalar o IIS em **Web-02**.


## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego

1. Abra o grupo de recursos **RG-DNS-Alias-TM** e escolha o endereço IP público **Web-01-ip**. Anote o endereço IP para uso posterior. Repita essa etapa para o endereço IP público **Web-02-ip**.
1. Escolha **Criar um recurso** > **Rede** > **Perfil do Gerenciador de Tráfego**.
2. Em nome, insira **TM-alias-test**. Coloque-o no grupo de recursos **RG-DNS-Alias-TM**.
3. Selecione **Criar**.
4. Depois que a implantação for concluída, marque **Ir para o recurso**.
5. Na página de perfil do Gerenciador de Tráfego, em **Configurações**, escolha **Pontos de Extremidade**.
6. Selecione **Adicionar**.
7. Em **Tipo**, escolha **Ponto de extremidade externo** e, em **Nome**, insira **EP-Web01**.
8. Na caixa de texto **FQDN (nome de domínio totalmente qualificado) ou IP**, digite o endereço IP para **Web-01-ip** que você anotou anteriormente.
9. Escolha o mesmo **Local** dos demais recursos relacionados e marque **OK**.

Repita esse procedimento para adicionar o ponto de extremidade **Web-02** usando o endereço IP que você anotou anteriormente para **Web-02-ip**.

## <a name="create-an-alias-record"></a>Criar um registro de alias

Crie um registro de alias que aponta para o perfil do Gerenciador de Tráfego.

1. Escolha sua zona DNS do Azure para abri-la.
2. Escolha **Conjunto de registros**.
3. Deixe a caixa de texto **Nome** vazia para representar o apex do nome de domínio. Um exemplo é contoso.com.
4. Deixe o **Tipo** como um registro **A**.
5. Marque a caixa de seleção **Conjunto de Registros do Alias**.
6. Escolha **Escolher serviço do Azure** e escolha o perfil do Gerenciador de Tráfego **TM-alias-test**.

## <a name="test-the-alias-record"></a>Testar o registro de alias

1. Em um navegador da Web, navegue até seu apex de nome de domínio. Um exemplo é contoso.com. Você verá a página da Web do IIS padrão. Feche o navegador da Web.
2. Desligue a máquina virtual **Web-01**. Aguarde alguns minutos até que ela seja completamente desligada.
3. Abra um novo navegador da web e navegue até seu nome de domínio apex novamente.
4. Você verá a página da Web do IIS padrão novamente já que o Gerenciador de Tráfego tratou da situação e direcionou o tráfego para **Web-02**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais dos recursos criados para este tutorial, exclua o grupo de recursos **RG-DNS-Alias-TM**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um registro de alias para que seu nome de domínio do apex fizesse referência a um perfil do Gerenciador de Tráfego. Para saber mais sobre os aplicativos Web e o DNS do Azure, continue com o tutorial para aplicativos Web.

> [!div class="nextstepaction"]
> [Hospedar aplicativos Web com carga balanceada no apex da zona](./dns-alias-appservice.md)
