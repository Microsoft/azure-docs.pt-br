---
title: 'Tutorial: Proteger seu hub virtual usando o Gerenciador de Firewall do Azure'
description: Neste tutorial, você aprenderá a proteger seu hub virtual com o Gerenciador de Firewall do Azure usando o portal do Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: 14ec6fafbbadff2ecc37b229270c269f068a666f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670453"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Tutorial: Proteger seu hub virtual usando o Gerenciador de Firewall do Azure

Com o Gerenciador de Firewall do Azure, é possível criar hubs virtuais seguros para proteger o tráfego de rede na nuvem destinado a endereços IP privados, PaaS do Azure e à Internet. O roteamento de tráfego para o firewall é automatizado, portanto, não é necessário criar UDRs (rotas definidas pelo usuário).

![proteger a rede na nuvem](media/secure-cloud-network/secure-cloud-network.png)

O Gerenciador de Firewall também dá suporte à arquitetura de rede virtual de um hub. Para obter uma comparação dos tipos de arquitetura de rede virtual de hub e hub virtual seguro, confira [quais são as opções de arquitetura do Gerenciador de Firewall do Azure?](vhubs-and-vnets.md)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar a rede virtual spoke
> * Criar um hub virtual seguro
> * Conectar as redes virtuais hub e spoke
> * Rotear o tráfego para o seu hub
> * Implantar os servidores
> * Criar uma política de firewall e proteger seu hub
> * Testar o firewall

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-hub-and-spoke-architecture"></a>Criar uma arquitetura de hub e spoke

Primeiro, crie uma redes virtuais spoke nas quais você pode colocar seus servidores.

### <a name="create-two-spoke-virtual-networks-and-subnets"></a>Criar duas redes virtuais spoke e sub-redes

As duas redes virtuais terão um servidor de carga de trabalho nelas e serão protegidas pelo firewall.

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Procure **Rede virtual** e selecione **Criar**.
2. Em **Assinatura**, selecione sua assinatura.
1. Em **Grupo de recursos**, selecione **Criar**, digite **fw-manager-rg** para o nome e escolha **OK**.
2. Em **Nome**, digite **Spoke-01**.
3. Em **Região**, selecione **EUA (Leste dos EUA)** .
4. Selecione **Avançar: Endereços IP**.
1. Em **Espaço de endereço**, digite **10.0.0.0/16**.
3. Em **Nome da sub-rede**, selecione **padrão**.
4. Em **Nome da sub-rede**, digite **Workload-01-SN**.
5. Para **Intervalo de endereços da sub-rede**, digite **10.0.1.0/24**.
6. Clique em **Salvar**.
1. Selecione **Examinar + criar**.
2. Selecione **Criar**.

Repita esse procedimento para criar outra rede virtual semelhante:

Nome: **Spoke-02**<br>
Espaço de endereço: **10.1.0.0/16**<br>
Nome da sub-rede: **Workload-02-SN**<br>
Intervalo de endereços da sub-rede: **10.1.1.0/24**

### <a name="create-the-secured-virtual-hub"></a>Criar o hub virtual seguro

Crie seu hub virtual seguro usando o Gerenciador de Firewall.

1. Na página inicial do portal do Azure, selecione **Todos os serviços**.
2. Na caixa de pesquisa, digite **Gerenciador de Firewall** e selecione **Gerenciador de Firewall**.
3. Na página **Gerenciador de Firewall**, selecione **Exibir hubs virtuais protegidos**.
4. Na página **Gerenciador de Firewall | Hubs virtuais seguros**, selecione **Criar hub virtual seguro**.
5. Em **Grupo de recursos**, selecione **fw-manager-rg**.
7. Em **Região**, selecione **Leste dos EUA**.
1. Para o **Nome do hub virtual seguro**, digite **Hub-01**.
2. Em **Espaço de endereço do hub**, digite **10.2.0.0/16**.
3. Para o novo nome de vWAN, digite **Vwan-01**.
4. Deixe a caixa de seleção **Incluir o gateway de VPN para habilitar Parceiros de Segurança Confiáveis** marcada.
5. Selecione **Avançar: Firewall do Azure**.
6. Aceite a configuração padrão **Firewall do Azure** **Habilitado** e, em seguida, selecione **Próximo: Parceiro de Segurança Confiável**.
7. Aceite a configuração padrão **Parceiro de Segurança Confiável** **Desabilitado** e selecione **Próximo: Revisar + criar**.
8. Selecione **Criar**. 

   A implantação leva cerca de 30 minutos.

Você poderá obter o endereço IP público do firewall após a conclusão da implantação.

1. Abra **Gerenciador de Firewall**.
2. Selecione **Hubs virtuais**.
3. Selecione **hub-01**.
7. Selecione **Configuração de IP público**.
8. Anote o endereço IP público para usar depois.

### <a name="connect-the-hub-and-spoke-virtual-networks"></a>Conectar as redes virtuais hub e spoke

Agora você pode emparelhar as redes virtuais hub e spoke.

1. Escolha o grupo de recursos **fw-manager-rg** e selecione a WAN Virtual **Vwan-01**.
2. Em **Conectividade**, selecione **Conexões de rede virtual**.
3. Selecione **Adicionar conexão**.
4. Em **Nome da conexão**, digite **hub-spoke-01**.
5. Para **Hubs**, selecione **Hub-01**.
6. Em **Grupo de recursos**, selecione **fw-manager-rg**.
7. Para **Rede virtual**, selecione **Spoke-01**.
8. Selecione **Criar**.

Repita para conectar a rede virtual **Spoke-02**: nome da conexão – **hub-spoke-02**

## <a name="deploy-the-servers"></a>Implantar os servidores

1. No portal do Azure, selecione **Criar um recurso**.
2. Selecione **Windows Server 2016 Datacenter** na lista **Popular**.
3. Insira esses valores para a máquina virtual:

   |Configuração  |Valor  |
   |---------|---------|
   |Resource group     |**fw-manager-rg**|
   |Nome da máquina virtual     |**Srv-workload-01**|
   |Região     |**(EUA) Leste dos EUA**|
   |Nome de usuário administrador     |digite um nome de usuário|
   |Senha     |digite uma senha|

4. Em **Regras de porta de entrada**, **Portas de entrada públicas**, selecione **Nenhuma**.
6. Aceite os outros padrões e selecione **Próximo: Discos**.
7. Aceite os padrões de disco e selecione **Avançar: Rede**.
8. Selecione **Spoke-01** para a rede virtual e selecione **Workload-01-SN** para a sub-rede.
9. Em **IP Público**, selecione **Nenhum**.
11. Aceite os outros padrões e selecione **Próximo: Gerenciamento**.
12. Selecione **Desabilitar** para desabilitar o diagnóstico de inicialização. Aceite os outros padrões e selecione **Examinar + criar**.
13. Examine as configurações na página de resumo e, em seguida, selecione **Criar**.

Use as informações na tabela a seguir para definir outra máquina virtual chamada **Srv-Workload-02**. O restante da configuração é o mesmo da máquina virtual **Srv-workload-01**.

|Configuração  |Valor  |
|---------|---------|
|Rede virtual|**Spoke-02**|
|Sub-rede|**Workload-02-SN**|

Depois que os servidores forem implantados, selecione um recurso de servidor e, em **Rede**, anote o endereço IP privado de cada servidor.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Criar uma política de firewall e proteger seu hub

Uma política de firewall define coleções de regras para direcionar o tráfego em um ou mais hubs virtuais seguros. Você criará uma política de firewall e protegerá seu hub.

1. No Gerenciador de Firewall, selecione **Exibir políticas de Firewall do Azure**.
2. Selecione **Criar uma Política de Firewall do Azure**.
1. Em **Grupo de recursos**, selecione **fw-manager-rg**.
1. Em **Detalhes da política**, para **Nome**, digite **Policy-01** e para **Região** selecione **Leste dos EUA**.
1. Selecione **Avançar: Configurações de DNS**.
1. Escolha **Avançar: Inspeção de TLS (versão prévia)** .
1. Selecione **Avançar: Regras**.
1. Na guia **Regras**, selecione **Adicionar a coleção de regras**.
1. Na página **Adicionar a coleção de regras**, digite **App-RC-01** em **Nome**.
1. Para **Tipo de coleção de regras**, selecione **Aplicativo**.
1. Em **Prioridade**, digite **100**.
1. Verifique se a **Ação de coleção de regras** é **Permitir**.
1. Para o **Nome** da regra, digite **Allow-msft**.
1. Para o **Tipo de origem**, selecione **Endereço IP**.
1. Para **Origem**, digite **\*** .
1. Para **Protocolo**, digite **http,https**.
1. Verifique se o **Tipo de destino** é **FQDN**.
1. Para **Destino**, digite **\*.microsoft.com**.
1. Selecione **Adicionar**.

Adicione uma regra DNAT para que você possa conectar uma Área de Trabalho Remota à máquina virtual **Srv-Workload-01**.

1. Escolha **Adicionar/Coleção de regras**.
1. Em **Nome**, digite **dnat-rdp**.
1. Em **Tipo de coleção de regras**, selecione **DNAT**.
1. Em **Prioridade**, digite **100**.
1. No **Nome** da regra, digite **Allow-rdp**.
1. Para o **Tipo de origem**, selecione **Endereço IP**.
1. Para **Origem**, digite **\*** .
1. Em **Protocolo**, selecione **TCP**.
1. Em **Portas de Destino**, digite **3389**.
1. Para **Tipo de Destino**, selecione **Endereço IP**.
1. Em **Destino**, digite o endereço IP público do firewall que você anotou anteriormente.
1. Para **Endereço convertido**, digite o endereço IP privado de **Srv-Workload-01** que você anotou anteriormente.
1. Para **Porta traduzida**, digite **3389**.
1. Selecione **Adicionar**.

Adicione uma regra de rede para que você possa conectar uma Área de Trabalho Remota de **Srv-Workload-01** para **Srv-Workload-02**.

1. Selecione **Adicionar uma coleção de regras**.
2. Em **Nome**, digite **vnet-rdp**.
3. Para **Tipo de coleção de regras**, selecione **Rede**.
4. Em **Prioridade**, digite **100**.
1. Em **Ação de coleção de regras**, selecione **Permitir**.
1. Em **Nome** da regra, digite **Allow-vnet**.
1. Para o **Tipo de origem**, selecione **Endereço IP**.
1. Para **Origem**, digite **\*** .
1. Em **Protocolo**, selecione **TCP**.
1. Em **Portas de Destino**, digite **3389**.
1. Para **Tipo de Destino**, selecione **Endereço IP**.
1. Em **Destino**, digite o endereço IP privado **Srv-Workload-02** que você anotou anteriormente.
1. Selecione **Adicionar**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

## <a name="associate-policy"></a>Associar a política

Associe a política de firewall ao hub.

1. No Gerenciador de Firewall, selecione **Políticas de Firewall do Azure**.
1. Marque a caixa de seleção de **Policy-01**.
1. Selecione **Gerenciar associações/Associar hubs**.
1. Selecione **hub-01**.
1. Selecione **Adicionar**.

## <a name="route-traffic-to-your-hub"></a>Rotear o tráfego para o seu hub

Agora, você deve verificar se o tráfego de rede é roteado por meio do firewall.

1. No Gerenciador de Firewall, escolha **Hubs virtuais**.
2. Selecione **Hub-01**.
3. Em **Configurações**, escolha **Configuração de segurança**.
4. Em **Tráfego da Internet**, selecione **Firewall do Azure**.
5. Em **Tráfego privado**, selecione **Enviar por meio do Firewall do Azure**.
1. Clique em **Salvar**.

   São necessários alguns minutos para que as tabelas de rotas sejam atualizadas.
1. Verifique se as duas conexões mostram que o Firewall do Azure protege o tráfego da Internet e privado.

## <a name="test-the-firewall"></a>Testar o firewall

Para testar as regras de firewall, você conectará uma Área de Trabalho Remota usando o endereço IP público do firewall, cuja NAT é realizada para **Srv-Workload-01**. Lá você usará um navegador para testar a regra de aplicativo e conectar uma Área de Trabalho Remota a **Srv-Workload-02** para testar a regra de rede.

### <a name="test-the-application-rule"></a>Testar a regra de aplicativo

Agora, teste as regras de firewall para confirmar se elas funcionam conforme o esperado.

1. Conecte uma Área de Trabalho Remota ao endereço IP público do firewall e entre.

3. Abra o Internet Explorer e navegue até `https://www.microsoft.com`.
4. Selecione **OK** > **Fechar** nos alertas de segurança do Internet Explorer.

   Você deve ver a página inicial da Microsoft.

5. Navegue até `https://www.google.com`.

   Você deve ser bloqueado pelo firewall.

Agora que você verificou se a regra de aplicativo de firewall está funcionando:

* Você pode navegar para o FQDN permitido, mas não para os outros.

### <a name="test-the-network-rule"></a>Testar a regra de rede

Agora teste a regra de rede.

- Em Srv-Workload-01, abra uma Área de Trabalho Remota para o endereço IP privado de Srv-Workload-02.

   Uma Área de Trabalho Remota deve se conectar a Srv-Workload-02.

Agora que você verificou se a regra de rede de firewall está funcionando:
* Você pode conectar uma Área de Trabalho Remota a um servidor localizado em outra rede virtual.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você terminar de testar seus recursos de firewall, exclua o grupo de recursos **fw-manager-rg** para excluir todos os recursos relacionados ao firewall.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre parceiros de segurança confiáveis](trusted-security-partners.md)
