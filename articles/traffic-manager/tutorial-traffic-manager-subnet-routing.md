---
title: Tutorial – configurar o roteamento de tráfego da sub-rede com o Gerenciador de Tráfego do Azure
description: Este tutorial explica como configurar o Gerenciador de Tráfego para rotear o tráfego de sub-redes de usuário para pontos de extremidade específicos.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 9b916f9942b0459b41d98b952fad072ae48318b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505419"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Tutorial: Tráfego direto para pontos de extremidade específicos com base na sub-rede do usuário usando o Gerenciador de Tráfego

Este artigo descreve como configurar o método de roteamento de tráfego de sub-rede. O método de roteamento de tráfego da **Sub-rede** permite mapear um conjunto de intervalos de endereços IP para pontos de extremidade específicos. Quando o Gerenciador de Tráfego receber uma solicitação, ele inspecionará o IP de origem dela e retornará um ponto de extremidade associado a ela.

Neste tutorial, o tráfego será roteado para um site interno ou um site de produção usando um roteamento da sub-rede, dependendo do endereço IP da consulta do usuário.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie duas VMs em execução de um site da Web básica no IIS
> * Crie duas VMs para exibir o Gerenciador de Tráfego em ação de teste
> * Configurar nome DNS para as VMs que executam IIS
> * Criar um perfil do Gerenciador de Tráfego para rotear o tráfego com base na sub-rede do usuário
> * Criar pontos de extremidade no perfil do Gerenciador de Tráfego
> * Ver o Gerenciador de Tráfego em ação

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para conferir o Gerenciador de Tráfego em ação, este tutorial exige implantar o seguinte:

- dois sites básicos em execução em diferentes regiões do Azure - **Leste dos EUA** (serve como site interno) e **Europa Ocidental** (serve como site de produção).
- duas VMs de teste para testar o Gerenciador de Tráfego do Microsoft Azure - uma VM no **Leste dos EUA** e a segunda VM na **Europa Ocidental**.

As VMs de teste são usadas para ilustrar como o Traffic Manager roteia o tráfego de usuários para o site interno ou para o site de produção com base na sub-rede de onde a consulta do usuário se origina.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

### <a name="create-websites"></a>Criar sites

Nesta seção, você criará duas instâncias de site que fornecem os dois pontos de extremidade para o perfil do Gerenciador de Tráfego em duas regiões do Azure. Criar dois sites inclui as seguintes etapas:

1. Criar duas VMs para executar um site básico - uma no **Leste dos EUA** e o outro na **Europa Ocidental**.
2. Instale o servidor IIS em cada VM e atualize a página de site padrão que descreve o nome da VM que um usuário está conectado ao visitar o site.

#### <a name="create-vms-for-running-websites"></a>Criar VMs para a execução de sites

Nesta seção, você criará duas VMs *myIISVMEastUS* e *myIISVMWestEurope* nas regiões do Azure **Leste dos EUA** e **Oeste da Europa**.

1. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Computação** > **Datacenter do Windows Server 2019**.
2. Em **Criar uma máquina virtual**, insira ou selecione os seguintes valores na guia **Configurações básicas**:

   - **Assinatura** > **Grupo de Recursos**: Selecione **Criar novo** e, em seguida, digite **myResourceGroupTM1**.
   - **Detalhes da Instância** > **Nome da máquina virtual**: Digite *myIISVMEastUS*.
   - **Detalhes da instância** > **Região**:  Selecione **Leste dos EUA**.
   - **Conta Administrador** > **Nome de Usuário**:  Insira um nome de usuário de sua escolha.
   - **Conta Administrador** > **Senha**:  Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regras de Porta de Entrada** > **Portas de entrada públicas**: Selecione **Permitir portas selecionadas**.
   - **Regras de Portas de Entrada** > **Selecionar portas de entrada**: selecione **RDP** e **HTTP** na caixa suspensa.

3. Selecione a guia **Gerenciamento** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede** e, em seguida, **Avançar: Gerenciamento**. Em **Monitoramento**, defina **Diagnóstico de inicialização** como **Desativado**.
4. Selecione **Examinar + criar**.
5. Examine as configurações e selecione **Criar**.  
6. Siga as etapas para criar uma segunda VM chamada *myIISVMWestEurope*, com o nome de **Grupo de recursos** *myResourceGroupTM2*, a **localização** *Oeste da Europa* e todas as outras configurações iguais a *myIISVMEastUS*.
7. As VMs podem levar alguns minutos para serem criadas. Não prossiga com as etapas restantes até que as duas VMs sejam criadas.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalar o IIS e personalizar a página da Web padrão

Nesta seção, você deve instalar o servidor IIS em duas VMs - *myIISVMEastUS* & *myIISVMWestEurope* e, em seguida, atualizar a página padrão do site. A página do site personalizado mostrará o nome da VM à qual você está se conectando ao visitar o site de um navegador da Web.

1. Selecione **Todos os Recursos** no menu esquerdo e *myIISVMEastUS* na lista de recursos. Essa opção está localizada no grupo de recursos *myResourceGroupTM1*.
2. Na página **Visão Geral**, clique em **Conectar**. Depois em **Conectar-se à máquina virtual**, clique em **Baixar arquivo RDP**.
3. Abra o arquivo rdp baixado. Se solicitado, selecione **Conectar**. Insira o nome de usuário e senha que você especificou ao criar a VM. Talvez seja necessário selecionar **Mais escolhas** e, em seguida, **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.
4. Selecione **OK**.
5. Você pode receber um aviso do certificado durante o processo de logon. Caso receba o aviso, clique em **Sim** ou **Continuar** para prosseguir com a conexão.
6. Na área de trabalho do servidor, navegue até **Ferramentas Administrativas do Windows**>**Gerenciador do Servidor**.
7. Inicie o Windows PowerShell na VM *myIISVMEastUS* e use os seguintes comandos para instalar o servidor IIS e atualizar o arquivo htm padrão.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Feche a conexão RDP com a VM *myIISVMEastUS*.
9. Repita as etapas 1 a 6 criando uma conexão RDP com a VM *myIISVMWestEurope* no grupo de recursos *myResourceGroupTM2* para instalar o IIS e personalizar sua página da Web padrão.
10. Inicie o Windows PowerShell na VM *myIISVMWestEurope* e use os comandos a seguir para instalar o servidor IIS e atualizar o arquivo padrão htm.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Configurar nome DNS para as VMs que executam IIS

O Gerenciador de Tráfego roteia o tráfego de usuário com base no nome DNS dos pontos de extremidade de serviço. Nesta seção, você configurará os nomes DNS dos servidores IIS: *myIISVMEastUS* e *myIISVMWestEurope*.

1. Selecione **Todos os Recursos** no menu esquerdo e *myIISVMEastUS* na lista de recursos. Essa opção está localizada no grupo de recursos *myResourceGroupTM1*.
2. Na página **Visão Geral**, em **Nome DNS**, selecione **Configurar**.
3. Na página **Configuração**, em rótulo de nome DNS, adicione um nome exclusivo e, em seguida, selecione **Salvar**.
4. Repita as etapas 1 a 3 para a VM denominada *myIISVMWestEurope* que está localizada no grupo de recursos *myResourceGroupTM2*.

### <a name="create-test-vms"></a>Criar VMs de teste

Nesta seção, você criará uma VM (*myVMEastUS* e *myVMWestEurope*) em cada região do Azure (**Leste dos EUA** e **Oeste da Europa**). Você usará essas VMs para testar como o Gerenciador de Tráfego roteia o tráfego de usuário com base na sub-rede da consulta do usuário.

1. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Computação** > **Datacenter do Windows Server 2019**.
2. Em **Criar uma máquina virtual**, insira ou selecione os seguintes valores na guia **Configurações básicas**:

   - **Assinatura** > **Grupo de Recursos**: Selecione **myResourceGroupTM1**.
   - **Detalhes da Instância** > **Nome da máquina virtual**: Digite *myVMEastUS*.
   - **Detalhes da instância** > **Região**:  Selecione **Leste dos EUA**.
   - **Conta Administrador** > **Nome de Usuário**:  Insira um nome de usuário de sua escolha.
   - **Conta Administrador** > **Senha**:  Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regras de Porta de Entrada** > **Portas de entrada públicas**: Selecione **Permitir portas selecionadas**.
   - **Regras de Portas de Entrada** > **Selecionar portas de entrada**: selecione **RDP** na caixa suspensa.

3. Selecione a guia **Gerenciamento** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede** e, em seguida, **Avançar: Gerenciamento**. Em **Monitoramento**, defina **Diagnóstico de inicialização** como **Desativado**.
4. Selecione **Examinar + criar**.
5. Examine as configurações e selecione **Criar**.  
6. Execute as etapas para criar uma segunda VM chamada *myVMWestEurope*, com o nome de **Grupo de recursos** *myResourceGroupTM2*, a **localização** *Oeste da Europa* e todas as outras configurações iguais a *myVMEastUS*.
7. As VMs podem levar alguns minutos para serem criadas. Não continue com as etapas restantes até que ambas as VMs sejam criadas.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego

Crie um perfil de Gerenciador de Tráfego que permite que você retorne pontos de extremidade específicos com base no IP de origem da solicitação.

1. No canto superior esquerdo da tela, clique em **Criar um recurso**. Pesquise o *perfil do Gerenciador de Tráfego* e selecione **Criar**.
2. Na opção **Criar perfil do Gerenciador de Tráfego**, insira ou selecione as informações abaixo. Aceite os padrões para as configurações restantes, depois clique em **Criar**.

    ![Criar um perfil do Gerenciador de Tráfego](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Nome                   | Esse nome deve ser exclusivo na zona trafficmanager.net e resulta no nome DNS, trafficmanager.net, que é usado para acessar o seu perfil do Gerenciador de Tráfego.                                   |
    | Método de roteamento          | Selecione o método de roteamento **Sub-rede**.                                       |
    | Subscription            | Selecione sua assinatura.                          |
    | Resource group          | Selecione **Existing** e insira *myResourceGroupTM1*. |

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos de extremidade do Gerenciador de Tráfego

Adicione as duas VMs executando os servidores IIS: *myIISVMEastUS* & *myIISVMWestEurope* para rotear o tráfego de usuário com base na sub-rede da consulta do usuário.

1. Na barra de pesquisa do portal, pesquise o nome do Perfil do Gerenciador de Tráfego que você criou na seção anterior e selecione o perfil do gerenciador de tráfego nos resultados que são exibidos.
2. Em **Perfil do Gerenciador de Tráfego**, na seção **Configurações**, selecione **Pontos de Extremidade** e **Adicionar**.
3. Insira ou selecione as informações abaixo. Aceite os padrões para as configurações restantes, depois clique em **OK**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Type                    | Ponto de extremidade do Azure                                   |
    | Nome           | myInternalWebSiteEndpoint                                        |
    | Tipo de recurso de destino           | Endereço IP público                          |
    | Recurso de destino          | **Escolha um endereço IP Público** para mostrar a lista de recursos com endereços IP públicos na mesma assinatura. Em **Recursos**, selecione o endereço IP público denominado *myIISVMEastUS-ip*. Isso é o endereço IP público do servidor IIS VM no Leste dos EUA.|
    |  Configurações de roteamento da sub-rede    |   Adicione o endereço IP do *myVMEastUS* VM de teste. Toda consulta de usuário originada dessa VM será direcionada para o *myInternalWebSiteEndpoint*.    |

4. Repita as etapas 2 e 3 para adicionar outro ponto de extremidade chamado *myProdWebsiteEndpoint* ao endereço IP público *myIISVMWestEurope-ip* que está associado à VM do servidor IIS denominado *myIISVMWestEurope*. Para **configurações de roteamento da sub-rede**, adicione o endereço IP da VM - teste *myVMWestEurope*. Toda consulta de usuário dessa VM de teste será encaminhada para o ponto de extremidade *myProdWebsiteEndpoint*.
5. Quando a adição de ambos os pontos de extremidade estiver concluída, eles serão exibidos no **Perfil do Gerenciador de Tráfego**, juntamente com seu status de monitoramento como **Online**.

## <a name="test-traffic-manager-profile"></a>Testar perfil de Gerenciador de Tráfego

Nesta seção, você testa como o Gerenciador de Tráfego encaminha o tráfego de usuário de uma determinada sub-rede para um ponto de extremidade específico. Para exibir o Gerenciador de Tráfego em ação, conclua as seguintes etapas:

1. Determine o nome DNS do seu perfil do Gerenciador de Tráfego.
2. Visualizar o Gerenciador de Tráfego em ação conforme a seguir:
    - Na VM de teste (*myVMEastUS*) que está localizada na região do **Leste dos EUA**, em um navegador da web, navegue até o nome DNS do seu perfil do Gerenciador de Tráfego.
    - Na VM de teste (*myVMWestEurope*) que está localizada na região do **Europa Ocidental**, em um navegador da Web, navegue até o nome DNS do seu perfil do Gerenciador de Tráfego.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Determinar o nome DNS do Perfil do Gerenciador de Tráfego

Neste tutorial, para manter a simplicidade, você usará o nome DNS do perfil do Gerenciador de Tráfego do Microsoft Azure para visitar os sites da Web.

Você pode determinar o nome DNS do seu perfil do Gerenciador de Tráfego conforme a seguir:

1. Na barra de pesquisa do portal, procure o nome do **Perfil do Gerenciador de Tráfego** criado na seção anterior. Nos resultados que serão exibidos, selecione o perfil do Gerenciador de Tráfego.
2. Selecione **Visão geral**.
3. O **Perfil do Gerenciador de Tráfego** exibe o nome DNS do perfil do Gerenciador de Tráfego criado recentemente. Nas implantações de produção, você configura um nome de domínio intuitivo para apontar para o nome de domínio do Gerenciador de Tráfego, usando um registro DNS CNAME.

### <a name="view-traffic-manager-in-action"></a>Ver o Gerenciador de Tráfego em ação

Nesta seção, você pode ver que o Gerenciador de Tráfego é a ação.

1. Selecione **Todos os Recursos** no menu esquerdo e *myVMEastUS* na lista de recursos. Essa opção está localizada no grupo de recursos *myResourceGroupTM1*.
2. Na página **Visão Geral**, clique em **Conectar**. Depois em **Conectar-se à máquina virtual**, clique em **Baixar arquivo RDP**.
3. Abra o arquivo rdp baixado. Se solicitado, selecione **Conectar**. Insira o nome de usuário e senha que você especificou ao criar a VM. Talvez seja necessário selecionar **Mais escolhas** e, em seguida, **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.
4. Selecione **OK**.
5. Você pode receber um aviso do certificado durante o processo de logon. Caso receba o aviso, clique em **Sim** ou **Continuar** para prosseguir com a conexão.
6. Em um navegador da Web na VM *myVMEastUS*, digite o nome do DNS do seu perfil do Gerenciador de Tráfego para visualizar seu site. Como o endereço IP da VM *myVMEastUS* está associado ao ponto de extremidade *myInternalWebsiteEndpoint*, o navegador da Web inicia o servidor do site de teste *myIISVMEastUS*.

7. Em seguida, conecte-se à VM *myVMWestEurope* localizada na **Europa Ocidental** usando as etapas 1 a 5 e navegue para o nome de domínio de perfil do Gerenciador de Tráfego do Microsoft Azure dessa VM. Como o endereço IP da VM *myVMWestEurope* está associado ao ponto de extremidade *myProductionWebsiteEndpoint*, o navegador da Web inicia o servidor do site de teste *myIISVMWestEurope*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua os grupos de recursos (**ResourceGroupTM1** e **ResourceGroupTM2**). Para fazer isso, selecione o grupo de recursos (**ResourceGroupTM1** ou **ResourceGroupTM2**) e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o método de roteamento de sub-rede, confira:

> [!div class="nextstepaction"]
> [Método de roteamento de tráfego de sub-rede](traffic-manager-routing-methods.md#subnet)
