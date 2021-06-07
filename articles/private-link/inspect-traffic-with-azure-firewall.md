---
title: Usar o Firewall do Azure para inspecionar o tráfego destinado a um ponto de extremidade privado
titleSuffix: Azure Private Link
description: Saiba como você pode inspecionar o tráfego destinado a um ponto de extremidade privado usando o Firewall do Azure.
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu
ms.openlocfilehash: c3218d8781377e76f05d10a8da2c954ac0b685a7
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641985"
---
# <a name="use-azure-firewall-to-inspect-traffic-destined-to-a-private-endpoint"></a>Usar o Firewall do Azure para inspecionar o tráfego destinado a um ponto de extremidade privado

O ponto de extremidade privado do Azure é o bloco de construção fundamental para o link privado do Azure. Os pontos de extremidade privados permitem que os recursos do Azure implantados em uma rede virtual se comuniquem de forma privada com recursos de link privado.

Os pontos de extremidade privados permitem que os recursos acessem o serviço de vínculo privado implantado em uma rede virtual. Acesso ao ponto de extremidade privado por meio do emparelhamento de rede virtual e conexões de rede local estendem a conectividade.

Talvez seja necessário inspecionar ou bloquear o tráfego de clientes para os serviços expostos por meio de pontos de extremidade privados. Conclua essa inspeção usando o [Firewall do Azure](../firewall/overview.md) ou uma solução de virtualização de rede de terceiros.

As seguintes limitações se aplicam:

* Os NSG (grupos de segurança de rede) são ignorados pelo tráfego proveniente de pontos de extremidade privados
* As UDR (rotas definidas pelo usuário) são ignoradas pelo tráfego proveniente de pontos de extremidade privados
* Uma única tabela de rotas pode ser anexada a uma sub-rede
* Uma tabela de rotas dá suporte a até 400 rotas

O Firewall do Azure filtra o tráfego usando:

* [FQDN em regras de rede](../firewall/fqdn-filtering-network-rules.md) para protocolos TCP e UDP
* [FQDN nas regras de aplicativo](../firewall/features.md#application-fqdn-filtering-rules) para http, HTTPS e MSSQL. 

> [!IMPORTANT] 
> O uso de regras de aplicativo em regras de rede é recomendado ao inspecionar o tráfego destinado a pontos de extremidade privados para manter a simetria do fluxo. Se as regras de rede forem usadas ou se um NVA for usado em vez do firewall do Azure, o SNAT deverá ser configurado para o tráfego destinado a pontos de extremidade privados.

> [!NOTE]
> A filtragem de FQDN do SQL tem suporte apenas no [modo de proxy](../azure-sql/database/connectivity-architecture.md#connection-policy) (porta 1433). O modo **proxy** pode resultar em mais latência em comparação com o *redirecionamento*. Se você quiser continuar usando o modo de redirecionamento, que é o padrão para clientes que se conectam no Azure, você pode filtrar o acesso usando o FQDN em regras de rede de firewall.

## <a name="scenario-1-hub-and-spoke-architecture---dedicated-virtual-network-for-private-endpoints"></a>Cenário 1: arquitetura de Hub e spoke-rede virtual dedicada para pontos de extremidade privados

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hub-and-spoke.png" alt-text="Rede virtual dedicada para pontos de extremidade privados" border="true":::

Esse cenário é a arquitetura mais expansível para se conectar de forma privada a vários serviços do Azure usando pontos de extremidade privados. Uma rota apontando para o espaço de endereço de rede onde os pontos de extremidade privados são implantados é criado. Essa configuração reduz a sobrecarga administrativa e impede a execução do limite de 400 rotas.

As conexões de uma rede virtual de cliente para o Firewall do Azure em uma rede virtual de Hub incorrerão em encargos se as redes virtuais estiverem emparelhadas. As conexões do firewall do Azure em uma rede virtual de Hub para pontos de extremidade privados em uma rede virtual emparelhada não são cobradas.

Para obter mais informações sobre encargos relacionados a conexões com redes virtuais emparelhadas, consulte a seção perguntas frequentes da página de [preços](https://azure.microsoft.com/pricing/details/private-link/) .

## <a name="scenario-2-hub-and-spoke-architecture---shared-virtual-network-for-private-endpoints-and-virtual-machines"></a>Cenário 2: arquitetura de Hub e spoke-rede virtual compartilhada para pontos de extremidade privados e máquinas virtuais

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/shared-spoke.png" alt-text="Pontos de extremidade privados e máquinas virtuais na mesma rede virtual" border="true":::

Esse cenário é implementado quando:

* Não é possível ter uma rede virtual dedicada para os pontos de extremidade privados

* Quando apenas alguns serviços são expostos na rede virtual usando pontos de extremidade privados

As máquinas virtuais terão/32 rotas do sistema apontando para cada ponto de extremidade privado. Uma rota por ponto de extremidade privado é configurada para rotear o tráfego pelo firewall do Azure. 

A sobrecarga administrativa de manutenção da tabela de rotas aumenta à medida que os serviços são expostos na rede virtual. A possibilidade de atingir o limite de rota também aumenta.

Dependendo da arquitetura geral, é possível encontrar o limite de 400 rotas. É recomendável usar o cenário 1 sempre que possível.

As conexões de uma rede virtual de cliente para o Firewall do Azure em uma rede virtual de Hub incorrerão em encargos se as redes virtuais estiverem emparelhadas. As conexões do firewall do Azure em uma rede virtual de Hub para pontos de extremidade privados em uma rede virtual emparelhada não são cobradas.

Para obter mais informações sobre encargos relacionados a conexões com redes virtuais emparelhadas, consulte a seção perguntas frequentes da página de [preços](https://azure.microsoft.com/pricing/details/private-link/) .

## <a name="scenario-3-single-virtual-network"></a>Cenário 3: rede virtual única

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/single-vnet.png" alt-text="Rede virtual única" border="true":::

Use esse padrão quando uma migração para uma arquitetura de Hub e spoke não for possível. As mesmas considerações, conforme o cenário 2, se aplicam. Nesse cenário, os encargos de emparelhamento de rede virtual não se aplicam.

## <a name="scenario-4-on-premises-traffic-to-private-endpoints"></a>Cenário 4: tráfego local para pontos de extremidade privados

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/on-premises.png" alt-text="Tráfego local para pontos de extremidade privados" border="true":::

Essa arquitetura pode ser implementada se você tiver configurado a conectividade com sua rede local usando: 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [VPN site a site](../vpn-gateway/tutorial-site-to-site-portal.md) 

Se seus requisitos de segurança exigirem tráfego de cliente para serviços expostos por meio de pontos de extremidade privados a serem roteados por meio de um dispositivo de segurança, implante esse cenário.

As mesmas considerações sobre o cenário 2 acima se aplicam. Nesse cenário, não há encargos de emparelhamento de rede virtual. Para obter mais informações sobre como configurar seus servidores DNS para permitir que as cargas de trabalho locais acessem pontos de extremidade privados, consulte [cargas de trabalho locais usando um encaminhador DNS](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure.
* Um workspace do Log Analytics.  

Consulte [criar um log Analytics espaço de trabalho no portal do Azure](../azure-monitor/logs/quick-create-workspace.md) para criar um espaço de trabalho se você não tiver um em sua assinatura.


## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-vm"></a>Criar uma máquina virtual

Nesta seção, você criará uma rede virtual e uma sub-rede para hospedar a VM usada para acessar o recurso de link privado. Um banco de dados SQL do Azure é usado como o serviço de exemplo.

## <a name="virtual-networks-and-parameters"></a>Redes virtuais e parâmetros

Crie três redes virtuais e suas sub-redes correspondentes para:

* Conter o Firewall do Azure usado para restringir a comunicação entre a VM e o ponto de extremidade privado.
* Hospede a VM que é usada para acessar o recurso de link privado.
* Hospede o ponto de extremidade privado.

Substitua os seguintes parâmetros nas etapas com as informações abaixo:

### <a name="azure-firewall-network"></a>Rede de firewall do Azure

| Parâmetro                   | Valor                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | Centro-Sul dos Estados Unidos      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### <a name="virtual-machine-network"></a>Rede de máquinas virtuais

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | Centro-Sul dos Estados Unidos      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | VMSubnet      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### <a name="private-endpoint-network"></a>Rede de ponto de extremidade privado

| Parâmetro                   | Valor                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | Centro-Sul dos Estados Unidos      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

10. Repita as etapas de 1 a 9 para criar as redes virtuais para hospedar os recursos de máquina virtual e ponto de extremidade privado.

### <a name="create-virtual-machine"></a>Criar máquina virtual

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso**  >  **computação**  >  **máquina virtual**.

2. Em **Criar uma máquina virtual – Noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você criou esse grupo de recursos na seção anterior.  |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira a opção **myVM**. |
    | Região | Marque **(US) EUA Central do Sul**. |
    | Opções de disponibilidade | Deixe o padrão **Nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Selecione **Ubuntu Server 18, 4 LTS-Gen1**. |
    | Tamanho | Selecione **Standard_B2s**. |
    | **Conta de administrador** |  |
    | Tipo de autenticação | Selecione **Senha**. |
    | Nome de Usuário | Insira um nome de usuário de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/linux/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Senha | Reinsira a senha. |
    | **Regras de porta de entrada** |  |
    | Porta de entrada públicas | Selecione **Nenhum**. |
    |||

3. Selecione **Avançar: Discos**.

4. Em **Criar uma máquina virtual – Discos**, mantenha os padrões e selecione **Avançar: Rede**.

5. Em **Criar uma máquina virtual – Rede**, selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Rede virtual | Selecione **myVMVNet**.  |
    | Sub-rede | Selecione **VMSubnet (10.1.0.0/24)**.|
    | IP público | Deixe o padrão **(novo) myVm-ip**. |
    | Porta de entrada públicas | Selecione **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | Selecione **SSH**.|
    ||

6. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

7. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

## <a name="deploy-the-firewall"></a>Implantar o firewall

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.

2. Digite **firewall** na caixa de pesquisa e pressione **Enter**.

3. Selecione **Firewall** e, em seguida, selecione **Criar**.

4. Na página **Criar um Firewall**, use a tabela abaixo para configurar o firewall:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**.  |
    | **Detalhes da instância** |  |
    | Nome | Insira **myAzureFirewall**. |
    | Region | Selecione **EUA Central do Sul**. |
    | Zona de disponibilidade | Deixar o padrão **Nenhum**. |
    | Escolher uma rede virtual    |    Selecione **usar existente**.    |
    | Rede virtual    |    Selecione **myAzFwVNet**.    |
    | Endereço IP público    |    Selecione **Adicionar novo** e, em nome, digite **myfirewall-IP**.    |
    | Túnel forçado    | Deixe o padrão **desabilitado**.    |
    |||
5. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

6. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

## <a name="enable-firewall-logs"></a>Habilitar logs de firewall

Nesta seção, você habilita os logs no firewall.

1. Na portal do Azure, selecione **todos os recursos** no menu à esquerda.

2. Selecione o firewall **myAzureFirewall** na lista de recursos.

3. Em **monitoramento** nas configurações do firewall, selecione **configurações de diagnóstico**

4. Selecione **+ Adicionar configuração de diagnóstico** nas configurações de diagnóstico.

5. Em **configuração de diagnóstico**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome da configuração de diagnóstico | Insira **myDiagSetting**. |
    | Detalhes da categoria | |
    | log | Selecione **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule**. |
    | Detalhes do destino | Selecione **Enviar para o Log Analytics**. |
    | Subscription | Selecione sua assinatura. |
    | Espaço de trabalho do Log Analytics | Selecione seu espaço de trabalho do Log Analytics. |

6. Selecione **Salvar**.

## <a name="create-azure-sql-database"></a>Criar um Banco de Dados SQL do Azure

Nesta seção, você criará um banco de dados SQL privado.

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso**  >  **bancos** de  >  **dados SQL Database**.

2. Em **criar banco de dados SQL-noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você criou esse grupo de recursos na seção anterior.|
    | **Detalhes do banco de dados** |  |
    | Nome do banco de dados  | Insira **mydatabase**.  |
    | Servidor | Selecione **criar novo** e insira as informações abaixo.    |
    | Nome do servidor | Insira **MyDBServer**. Se esse nome for obtido, insira um nome exclusivo.   |
    | Logon de administrador do servidor | Insira um nome de sua escolha. |
    | Senha    |    Insira uma senha de sua escolha.    |
    | Confirmar Senha | Insira novamente a senha    |
    | Location    | Marque **(US) EUA Central do Sul**.    |
    | Deseja usar o pool elástico do SQL    | Deixe o padrão **Não**. |
    | Computação + armazenamento | Deixe o padrão **uso geral Gen5, 2 vCores, 32 GB de armazenamento**. |
    |||

3. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

4. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

## <a name="create-private-endpoint"></a>Criar um ponto de extremidade privado

Nesta seção, você cria um ponto de extremidade privado para o banco de dados SQL do Azure na seção anterior.

1. Na portal do Azure, selecione **todos os recursos** no menu à esquerda.

2. Selecione o **MyDBServer** do SQL Server do Azure na lista de serviços.  Se você usou um nome de servidor diferente, escolha esse nome.

3. Nas configurações do servidor, selecione **conexões de ponto de extremidade privado** em **segurança**.

4. Selecione **+ Ponto de extremidade privado**.

5. Em **criar um ponto de extremidade privado**, insira ou selecione essas informações na guia **noções básicas** :

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. |
    | **Detalhes da instância** | |
    | Nome | Insira **SQLPrivateEndpoint**. |
    | Region | Marque **(US) EUA Central do Sul.** |

6. Selecione a guia **recurso** ou selecione **próximo: recurso** na parte inferior da página.

7. Na guia **recurso** , insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Método de conexão | Selecione **Conectar-se a um recurso do Azure em meu diretório**. |
    | Subscription | Selecione sua assinatura. |
    | Tipo de recurso | Selecione **Microsoft.Sql/servers**. |
    | Recurso | Selecione **MyDBServer** ou o nome do servidor que você criou na etapa anterior.
    | Sub-recurso de destino | Selecione **sqlServer**. |

8. Selecione a guia **configuração** ou selecione **Avançar: configuração** na parte inferior da página.

9. Na guia **configuração** , insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Rede** | |
    | Rede virtual | Selecione **myPEVnet**. |
    | Sub-rede | Selecione **PrivateEndpointSubnet**. |
    | **Integração de DNS privado** | |
    | Integrar com a zona DNS privado | Selecione **Sim** na barra superior. |
    | Subscription | Selecione sua assinatura. |
    | Zonas DNS privadas | Deixe o **privatelink.Database.Windows.net** padrão. |

10. Selecione a guia **revisar + criar** ou selecione **revisar + criar** na parte inferior da página.

11. Selecione **Criar**.

12. Depois que o ponto de extremidade for criado, selecione **firewalls e redes virtuais** em **segurança**.

13. Em **firewalls e redes virtuais**, selecione **Sim** ao lado de **permitir que os serviços e recursos do Azure acessem este servidor**.

14. Selecione **Salvar**.

## <a name="connect-the-virtual-networks-using-virtual-network-peering"></a>Conectar as redes virtuais usando o emparelhamento de rede virtual

Nesta seção, conectaremos as redes virtuais **myVMVNet** e **myPEVNet** ao **myAzFwVNet** usando o emparelhamento. Não haverá conectividade direta entre **myVMVNet** e **myPEVNet**.

1. Na barra de pesquisa do portal, insira **myAzFwVNet**.

2. Selecione **emparelhamentos** no menu **configurações** e selecione **+ Adicionar**.

3. Em **Adicionar emparelhamento** , insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome do emparelhamento de myAzFwVNet para rede virtual remota | Insira **myAzFwVNet-to-myVMVNet**. |
    | **Detalhes do par** |  |
    | Modelo de implantação de rede virtual  | Deixe o **Gerenciador de recursos** padrão.  |
    | Sei minha ID de recurso | Deixe essa opção desmarcada.    |
    | Subscription | Selecione sua assinatura.    |
    | Rede virtual | Selecione **myVMVNet**. |
    | Nome do emparelhamento da rede virtual remota para myAzFwVNet    |    Insira **myVMVNet-to-myAzFwVNet**.    |
    | **Configuration** | |
    | **Definir configurações de acesso à rede virtual** | |
    | Permitir acesso à rede virtual de myAzFwVNet para rede virtual remota | Deixe o padrão **Habilitado**.    |
    | Permitir o acesso à rede virtual da rede virtual remota para myAzFwVNet    | Deixe o padrão **Habilitado**.    |
    | **Definir configurações de tráfego encaminhado** | |
    | Permitir tráfego encaminhado da rede virtual remota para myAzFwVNet    | Selecione **Habilitado**. |
    | Permitir tráfego encaminhado de myAzFwVNet para rede virtual remota | Selecione **Habilitado**. |
    | **Definir configurações de trânsito de gateway** | |
    | Permitir trânsito de gateway | Deixe essa opção desmarcada |
    |||

4. Selecione **OK**.

5. Selecione **+ Adicionar**.

6. Em **Adicionar emparelhamento** , insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome do emparelhamento de myAzFwVNet para rede virtual remota | Insira **myAzFwVNet-to-myPEVNet**. |
    | **Detalhes do par** |  |
    | Modelo de implantação de rede virtual  | Deixe o **Gerenciador de recursos** padrão.  |
    | Sei minha ID de recurso | Deixe essa opção desmarcada.    |
    | Subscription | Selecione sua assinatura.    |
    | Rede virtual | Selecione **myPEVNet**. |
    | Nome do emparelhamento da rede virtual remota para myAzFwVNet    |    Insira **myPEVNet-to-myAzFwVNet**.    |
    | **Configuration** | |
    | **Definir configurações de acesso à rede virtual** | |
    | Permitir acesso à rede virtual de myAzFwVNet para rede virtual remota | Deixe o padrão **Habilitado**.    |
    | Permitir o acesso à rede virtual da rede virtual remota para myAzFwVNet    | Deixe o padrão **Habilitado**.    |
    | **Definir configurações de tráfego encaminhado** | |
    | Permitir tráfego encaminhado da rede virtual remota para myAzFwVNet    | Selecione **Habilitado**. |
    | Permitir tráfego encaminhado de myAzFwVNet para rede virtual remota | Selecione **Habilitado**. |
    | **Definir configurações de trânsito de gateway** | |
    | Permitir trânsito de gateway | Deixe essa opção desmarcada |

7. Selecione **OK**.

## <a name="link-the-virtual-networks-to-the-private-dns-zone"></a>Vincular as redes virtuais à zona DNS privada

Nesta seção, Vincularemos as redes virtuais **myVMVNet** e **MYAZFWVNET** à zona DNS privada **privatelink.Database.Windows.net** . Essa zona foi criada quando criamos o ponto de extremidade privado. 

O link é necessário para que a VM e o firewall resolvam o FQDN do banco de dados para seu endereço de ponto de extremidade privado. A rede virtual **myPEVNet** foi vinculada automaticamente quando o ponto de extremidade privado foi criado.

>[!NOTE]
>Se você não vincular as redes virtuais da VM e do firewall à zona DNS privada, a VM e o firewall ainda poderão resolver o SQL Server FQDN. Eles serão resolvidos para seu endereço IP público.

1. Na barra de pesquisa do portal, insira **privatelink. Database**.

2. Selecione **privatelink.Database.Windows.net** nos resultados da pesquisa.

3. Selecione **links de rede virtual** em **configurações**.

4. Selecione **+ Adicionar**

5. Em **Adicionar link de rede virtual** , insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome do link | Insira **link-to-myVMVNet**. |
    | **Detalhes da rede virtual** |  |
    | Eu sei a ID de recurso da rede virtual  | Deixe essa opção desmarcada.  |
    | Subscription | Selecione sua assinatura.    |
    | Rede virtual | Selecione **myVMVNet**. |
    | **CONFIGURATION** | |
    | Habilitar o registro automático | Deixe essa opção desmarcada.    |


6. Selecione **OK**.

## <a name="configure-an-application-rule-with-sql-fqdn-in-azure-firewall"></a>Configurar uma regra de aplicativo com o FQDN do SQL no firewall do Azure

Nesta seção, configure uma regra de aplicativo para permitir a comunicação entre **myVM** e o ponto de extremidade privado para SQL Server **MyDBServer.Database.Windows.net**. 

Essa regra permite a comunicação por meio do firewall que criamos nas etapas anteriores.

1. Na barra de pesquisa do portal, insira **myAzureFirewall**.

2. Selecione **myAzureFirewall** nos resultados da pesquisa.

3. Selecione **regras** em **configurações** na visão geral do **myAzureFirewall** .

4. Selecione a guia **Coleção de regras de aplicativo**.

5. Selecione **+ Adicionar coleção de regras de aplicativo**.

6. Em **Adicionar coleção de regras de aplicativo** , insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira **SQLPrivateEndpoint**. |
    | Prioridade | Insira **100**. |
    | Ação | Digite **permitir**. |
    | **Regras** |  |
    | **Marcas de FQDN** | |
    | Nome  | Deixe em branco.  |
    | Tipo de origem | Deixe o **endereço IP** padrão.    |
    | Fonte | Deixe em branco. |
    | Marcas de FQDN | Deixe o padrão **0 selecionado**. |
    | **FQDNs de destino** | |
    | Nome | Insira **SQLPrivateEndpoint**.    |
    | Tipo de origem | Deixe o **endereço IP** padrão. |
    | Fonte | Insira **10.1.0.0/16**. |
    | Protocolo: porta | Digite **MSSQL: 1433**. |
    | FQDNs de destino | Insira **MyDBServer.Database.Windows.net**. |
    |||

7. Selecione **Adicionar**.

## <a name="route-traffic-between-the-virtual-machine-and-private-endpoint-through-azure-firewall"></a>Rotear o tráfego entre a máquina virtual e o ponto de extremidade privado por meio do firewall do Azure

Não criamos um emparelhamento de rede virtual diretamente entre as redes virtuais **myVMVNet** e **myPEVNet**. A máquina virtual **myVM** não tem uma rota para o ponto de extremidade privado que criamos. 

Nesta seção, criaremos uma tabela de rotas com uma rota personalizada. 

A rota envia o tráfego da sub-rede **myVM** para o espaço de endereço da rede virtual **myPEVNet**, por meio do firewall do Azure.

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.

2. Digite **tabela de rotas** na caixa de pesquisa e pressione **Enter**.

3. Selecione **tabela de rotas** e, em seguida, selecione **criar**.

4. Na página **criar tabela de rotas** , use a tabela a seguir para configurar a tabela de rotas:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**.  |
    | **Detalhes da instância** |  |
    | Região | Selecione **EUA Central do Sul**. |
    | Nome | Insira **VMsubnet-to-AzureFirewall**. |
    | Propagar rotas de gateway | Selecione **Não**. |

5. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

6. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

7. Quando a implantação for concluída, selecione **ir para o recurso**.

8. Selecione **rotas** em **configurações**.

9. Selecione **+ Adicionar**.

10. Na página **Adicionar rota** , insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome da rota | Insira **myVMsubnet-to-privateendpoint**. |
    | Prefixo de endereço | Insira **10.2.0.0/16**.  |
    | Tipo do próximo salto | Selecione **Solução de virtualização**. |
    | Endereço do próximo salto | Insira **10.0.0.4**. |

11. Selecione **OK**.

12. Selecione **sub-redes** em **configurações**.

13. Selecione **+ Associar**.

14. Na página **associar sub-rede** , insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Rede virtual | Selecione **myVMVNet**. |
    | Sub-rede | Selecione **VMSubnet**.  |

15. Selecione **OK**.

## <a name="connect-to-the-virtual-machine-from-your-client-computer"></a>Conectar-se à máquina virtual do computador cliente

Conecte-se à VM **myVm** da Internet da seguinte forma:

1. Na barra de pesquisa do portal, digite **myVm-IP**.

2. Selecione **myVM-IP** nos resultados da pesquisa.

3. Copie ou anote o valor em **endereço IP**.

4. Se você estiver usando o Windows 10, execute o comando a seguir usando o PowerShell. Para outras versões de cliente do Windows, use um cliente [](https://www.putty.org/)SSH como reversão:

* Substitua **username** pelo nome de usuário do administrador que você inseriu durante a criação da VM.

* Substitua **IPAddress** pelo endereço IP da etapa anterior.

    ```bash
    ssh username@IPaddress
    ```

5. Insira a senha que você definiu ao criar **myVm**

## <a name="access-sql-server-privately-from-the-virtual-machine"></a>Acesse SQL Server privadamente da máquina virtual

Nesta seção, você se conectará de modo privado ao banco de dados SQL usando o ponto de extremidade privado.

1. Inserir `nslookup mydbserver.database.windows.net`
    
    Você receberá uma mensagem semelhante à seguinte:

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mydbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mydbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

2. Instale [SQL Server ferramentas de linha de comando](/sql/linux/quickstart-install-connect-ubuntu#tools).

3. Execute o comando a seguir para se conectar ao SQL Server. Use o administrador do servidor e a senha que você definiu quando criou o SQL Server nas etapas anteriores.

* Substitua **\<ServerAdmin>** pelo nome de usuário do administrador que você inseriu durante a criação do SQL Server.

* Substitua **\<YourPassword>** pela senha de administrador que você inseriu durante a criação do SQL Server.

    ```bash
    sqlcmd -S mydbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```
4. Um prompt de comando do SQL será exibido no logon bem-sucedido. Insira **sair** para sair da ferramenta **sqlcmd** .

5. Feche a conexão com o **myVM** digitando **sair**.

## <a name="validate-the-traffic-in-azure-firewall-logs"></a>Validar o tráfego nos logs de firewall do Azure

1. No portal do Azure, selecione **todos os recursos** e selecione seu espaço de trabalho do log Analytics.

2. Selecione **logs** em **geral** na página log Analytics espaço de trabalho.

3. Selecione o botão de **introdução** azul.

4. Na janela **consultas de exemplo** , selecione **firewalls** em **todas as consultas**.

5. Selecione o botão **executar** em **dados de log de regra de aplicativo**.

6. Na saída da consulta de log, verifique se **MyDBServer.Database.Windows.net** está listado em **FQDN** e **SQLPrivateEndpoint** está listado em **RuleCollection**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você terminar de usar os recursos, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira **myResourceGroup** na caixa **Pesquisar** na parte superior do portal e selecione **myResourceGroup** nos resultados da pesquisa.

1. Selecione **Excluir grupo de recursos**.

1. Insira **myResourceGroup** para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você explorou cenários diferentes que podem ser usados para restringir o tráfego entre uma máquina virtual e um ponto de extremidade privado usando o Firewall do Azure. 

Você se conectou à VM e se comunica com segurança ao banco de dados por meio do firewall do Azure usando o link privado.

Para saber mais sobre o ponto de extremidade privado, consulte [o que é o ponto de extremidade privado do Azure?](private-endpoint-overview.md).
