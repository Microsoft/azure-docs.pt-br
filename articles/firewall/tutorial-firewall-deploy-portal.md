---
title: 'Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure'
description: Neste tutorial, você aprenderá a implantar e configurar o Firewall do Azure usando o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 02/19/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 54900b7b9089d4a4c6cbc742ecf09aa19ff2a550
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741949"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure

O controle do acesso à saída de rede é uma parte importante de um plano geral de segurança de rede. Por exemplo, você talvez queira limitar o acesso a sites. Ou você talvez queira limitar os endereços IP e portas de saída que podem ser acessados.

Uma maneira de controlar o acesso à saída de rede em uma sub-rede do Azure é com o Firewall do Azure. Com o Firewall do Azure, você pode configurar:

* Regras de aplicativo que definem FQDNs (nomes de domínio totalmente qualificados) que podem ser acessados em uma sub-rede.
* Regras de rede que definem endereço de origem, protocolo, porta de destino e endereço de destino.

O tráfego de rede está sujeito às regras de firewall configuradas quando o tráfego de rede para o firewall foi roteado como a sub-rede de gateway padrão.

Para este tutorial, você criará uma só VNet simplificada com duas sub-redes para facilitar a implantação.

Para implantações de produção, é recomendado um [modelo de hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), em que o firewall é, por si só, a VNet. Os servidores de carga de trabalho estão em VNets emparelhadas na mesma região que uma ou mais sub-redes.

* **AzureFirewallSubnet**: o firewall está nesta sub-rede.
* **Workload-SN**: o servidor de carga de trabalho está nessa sub-rede. O tráfego de rede dessa sub-rede passa pelo firewall.

![Infraestrutura de rede do tutorial](media/tutorial-firewall-deploy-portal/tutorial-network.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implantar um firewall
> * Criar uma rota padrão
> * Configure uma regra de aplicativo para permitir acesso a www.google.com
> * Configurar uma regra de rede para permitir o acesso a servidores DNS externos
> * Configurar uma regra NAT para permitir uma Área de Trabalho Remota para o servidor de teste
> * Testar o firewall

Se preferir, você pode concluir este tutorial usando o [Azure PowerShell](deploy-ps.md).

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="set-up-the-network"></a>Configurar a rede

Primeiro, crie um grupo de recursos para conter os recursos necessários à implantação do firewall. Em seguida, crie uma VNet, as sub-redes e um servidor de teste.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O grupo de recursos contém todos os recursos para o tutorial.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No menu do portal do Azure, selecione **Grupos de recursos** ou pesquise e selecione *Grupos de recursos* em qualquer página. Em seguida, selecione **Adicionar**.
4. Em **Assinatura**, selecione sua assinatura.
1. Em **Nome do grupo de recursos**, insira *Test-FW-RG*.
1. Em **Local do grupo de recursos**, selecione um local. Todos os demais recursos criados devem estar na mesma localização.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

### <a name="create-a-vnet"></a>Criar uma VNET

Essa VNet terá três sub-redes.

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é /26. Para obter mais informações sobre o tamanho da sub-rede, confira [Perguntas frequentes sobre o Firewall do Azure](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.
1. Selecione **Rede** > **Rede virtual**.
1. Selecione **Criar**.
1. Em **Assinatura**, selecione sua assinatura.
1. Para **Grupo de recursos**, selecione **Test-FW-RG**.
1. Em **Nome**, digite **Test-FW-VN**.
1. Em **Região**, selecione a mesma localização usada anteriormente.
1. Selecione **Avançar: Endereços IP**.
1. Em **Espaço de endereços IPv4**, digite **10.0.0.0/16**.
1. Em **Sub-rede**, selecione **Padrão**.
1. Em **Nome da sub-rede**, digite **AzureFirewallSubnet**. O firewall estará nessa sub-rede e o nome da sub-rede **precisa** ser AzureFirewallSubnet.
1. Em **Intervalo de endereços**, digite **10.0.1.0/26**.
1. Clique em **Salvar**.

   Em seguida, crie uma sub-rede para o servidor de carga de trabalho.

1. Selecione **Adicionar sub-rede**.
4. Em **Nome da sub-rede**, digite **Workload-SN**.
5. Para **Intervalo de endereços da sub-rede**, digite **10.0.2.0/24**.
6. Selecione **Adicionar**.
7. Selecione **Examinar + criar**.
8. Selecione **Criar**.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Agora crie a máquina virtual da carga de trabalho e coloque-a na sub-rede **Workload-SN**.

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.
2. Selecione **Windows Server 2016 Datacenter**.
4. Insira esses valores para a máquina virtual:

   |Configuração  |Valor  |
   |---------|---------|
   |Resource group     |**Test-FW-RG**|
   |Nome da máquina virtual     |**Srv-Work**|
   |Região     |Igual ao anterior|
   |Imagem|Windows Server 2019 Datacenter|
   |Nome de usuário administrador     |Digite um nome de usuário|
   |Senha     |Digite uma senha|

4. Em **Regras de porta de entrada**, **Portas de entrada públicas**, selecione **Nenhuma**.
6. Aceite os outros padrões e selecione **Próximo: Discos**.
7. Aceite os padrões de disco e selecione **Avançar: Rede**.
8. Verifique se **Test-FW-VN** está selecionado para a rede virtual e se a sub-rede é **Workload-SN**.
9. Em **IP Público**, selecione **Nenhum**.
11. Aceite os outros padrões e selecione **Próximo: Gerenciamento**.
12. Selecione **Desabilitar** para desabilitar o diagnóstico de inicialização. Aceite os outros padrões e selecione **Examinar + criar**.
13. Examine as configurações na página de resumo e, em seguida, selecione **Criar**.

## <a name="deploy-the-firewall"></a>Implantar o firewall

Implante o firewall na VNET.

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.
2. Digite **firewall** na caixa de pesquisa e pressione **Enter**.
3. Selecione **Firewall** e, em seguida, selecione **Criar**.
4. Na página **Criar um Firewall**, use a tabela abaixo para configurar o firewall:

   |Configuração  |Valor  |
   |---------|---------|
   |Subscription     |\<your subscription\>|
   |Resource group     |**Test-FW-RG** |
   |Nome     |**Test-FW01**|
   |Região     |Selecionar o mesmo local usado anteriormente|
   |Gerenciamento do firewall|**Use regras de Firewall (clássicas) para gerenciar este firewall**|
   |Escolher uma rede virtual     |**Usar existente**: **Test-FW-VN**|
   |Endereço IP público     |**Adicionar nova**<br>**Nome**: **fw-pip**|

5. Aceite os outros valores padrão e selecione **Examinar + criar**.
6. Examine o resumo e selecione **Criar** para criar o firewall.

   Isso levará alguns minutos para ser implantado.
7. Após a implantação ser concluída, vá para o grupo de recursos **Test-FW-RG** e selecione o firewall **Test-FW01**.
8. Anote os endereços IP públicos e privados do firewall. Você usará esses endereços mais tarde.

## <a name="create-a-default-route"></a>Criar uma rota padrão

Para a sub-rede **Workload-SN**, configure a rota de saída padrão para atravessar o firewall.

1. No menu do portal do Azure, selecione **Todos os recursos** ou, em qualquer página, pesquise por *Todos os serviços* e selecione essa opção.
2. Em **Rede**, selecione **Tabelas de rotas**.
3. Selecione **Adicionar**.
5. Em **Assinatura**, selecione sua assinatura.
6. Para **Grupo de recursos**, selecione **Test-FW-RG**.
7. Em **Região**, selecione a mesma localização usada anteriormente.
4. Em **Nome**, digite **Firewall-route**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

Após a implantação ser concluída, selecione **Ir para o recurso**.

1. Na página de rota do Firewall, selecione **Sub-redes** e escolha **Associar**.
1. Clique em **Rede virtual** > **Test-FW-VN**.
1. Para **Sub-rede**, selecione **Workload-SN**. Não deixe de selecionar apenas a sub-rede **Workload-SN** para essa rota, caso contrário, o firewall não funcionará corretamente.

13. Selecione **OK**.
14. Selecione **Rotas** e, em seguida, selecione **Adicionar**.
15. Para **Nome da rota**, digite **fw-dg**.
16. Em **Prefixo de endereço**, digite **0.0.0.0/0**.
17. Em **Tipo do próximo salto**, selecione **Solução de virtualização** .

    O Firewall do Azure é, na verdade, um serviço gerenciado, mas a solução de virtualização funciona nessa situação.
18. Em **endereço do próximo salto**, digite o endereço IP privado do firewall anotado anteriormente.
19. Selecione **OK**.

## <a name="configure-an-application-rule"></a>Configurar uma regra de aplicativo

Essa é a regra de aplicativo que permite o acesso de saída para `www.google.com`.

1. Abra **Test-FW-RG** e selecione o firewall **Test-FW01**.
2. Na página **Test-FW01**, em **Configurações**, selecione **Regras (clássicas)** .
3. Selecione a guia **Coleção de regras de aplicativo**.
4. Selecione **Adicionar coleção de regras de aplicativo**.
5. Em **Nome**, digite **App-Coll01**.
6. Digite **200** em **Prioridade**.
7. Em **Ação**, selecione **Permitir**.
8. Em **Regras**, **FQDNs de Destino**, para **Nome**, digite **Allow-Google**.
9. Em **Tipo de origem**, selecione **Endereço IP**.
10. Em **Origem**, digite **10.0.2.0/24**.
11. Em **Protocol:port**, digite **http, https**.
12. Em **FQDNS de Destino**, digite **`www.google.com`**
13. Selecione **Adicionar**.

O Firewall do Azure inclui uma coleção de regras internas para FQDNs de infraestrutura que têm permissão por padrão. Esses FQDNs são específicos da plataforma e não podem ser usados para outras finalidades. Para saber mais, veja [FQDNs de infraestrutura](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Configurar uma regra de rede

Essa é a regra de rede que permite o acesso de saída para dois endereços IP na porta 53 (DNS).

1. Selecione a guia **Coleção de regras de rede**.
2. Selecione **Adicionar coleção de regras de rede**.
3. Em **Nome**, digite **Net-Coll01**.
4. Digite **200** em **Prioridade**.
5. Em **Ação**, selecione **Permitir**.
6. Em **Regras**, **Endereços IP**, para de **Nome**, digite **Allow-DNS**.
7. Em **Protocolo**, selecione **UDP**.
9. Em **Tipo de origem**, selecione **Endereço IP**.
1. Em **Origem**, digite **10.0.2.0/24**.
2. Em **Tipo de destino**, selecione **Endereço IP**.
3. Em **Endereço de destino**, digite **209.244.0.3,209.244.0.4**

   Esses são servidores DNS públicos operados pelo CenturyLink.
1. Em **Portas de Destino**, digite **53**.
2. Selecione **Adicionar**.

## <a name="configure-a-dnat-rule"></a>Configurar uma regra de DNAT

Essa regra permite que você conecte uma Área de Trabalho Remota à máquina virtual Srv-Work por meio do firewall.

1. Selecione a guia **Coleção de regras NAT**.
2. Selecione **Adicionar coleção de regras do NAT**.
3. Em **Nome**, digite **rdp**.
4. Digite **200** em **Prioridade**.
5. Em **Regras**, para **Nome**, digite **rdp-nat**.
6. Em **Protocolo**, selecione **TCP**.
7. Em **Tipo de origem**, selecione **Endereço IP**.
8. Para **Origem**, digite **\*** .
9. Em **Endereço de destino**, digite o endereço IP público do firewall.
10. Em **Portas de Destino**, digite **3389**.
11. Em **Endereço convertido**, digite o endereço IP privado do **Srv-work**.
12. Para **Porta traduzida**, digite **3389**.
13. Selecione **Adicionar**.


### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Altere os endereços DNS primário e secundário para o adaptador de rede **Srv-Wprk**

Para fins de teste neste tutorial, você vai configurar os endereços DNS primários e secundários do servidor. Isso não é um requisito geral do Firewall do Azure.

1. No menu do portal do Azure, selecione **Grupos de recursos** ou pesquise e selecione *Grupos de recursos* em qualquer página. Selecione o grupo de recursos **Test-FW-RG**.
2. Selecione o adaptador de rede da máquina virtual **Srv-Work**.
3. Em **Configurações**, selecione **Servidores DNS**.
4. Em **Servidores DNS**, selecione **Personalizado**.
5. Digite **209.244.0.3** na caixa de texto **Adicionar servidor DNS** e **209.244.0.4** na caixa de texto seguinte.
6. Clique em **Salvar**.
7. Reinicie a máquina virtual **Srv-Work**.

## <a name="test-the-firewall"></a>Testar o firewall

Agora teste o firewall para confirmar se ele funciona conforme o esperado.

1. Conecte uma Área de Trabalho Remota ao endereço IP público do firewall e entre na máquina virtual **Srv-Work**. 
3. Abra o Internet Explorer e navegue até `https://www.google.com`.
4. Selecione **OK** > **Fechar** nos alertas de segurança do Internet Explorer.

   Você deve ver a página inicial do Google.

5. Navegue até `https://www.microsoft.com`.

   Você deve ser bloqueado pelo firewall.

Agora que você verificou se as regras de firewall estão funcionando:

* Você pode navegar para o FQDN permitido, mas não para os outros.
* É possível resolver nomes DNS usando o servidor DNS externo configurado.

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode manter seus recursos de firewall para o próximo tutorial ou, se não forem mais necessários, exclua o grupo de recursos **Test-FW-RG** para excluir todos os recursos relacionados ao firewall.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Monitorar os logs do Firewall do Azure](./firewall-diagnostics.md)