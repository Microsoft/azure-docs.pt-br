---
title: Gerenciar o acesso seguro aos recursos no spoke VNets para clientes P2S
titleSuffix: Azure Virtual WAN
description: Este artigo ajuda você a usar as regras de WAN virtual do Azure e firewall do Azure para gerenciar o acesso seguro a redes virtuais para clientes VPN de usuário (ponto a site).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cherylmc
ms.openlocfilehash: 751d11fcd4b5d4c33145ee7f2b7b49971b8927ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048244"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>Gerenciar o acesso seguro aos recursos no spoke VNets para clientes VPN de usuário

Este artigo mostra como usar as regras e os filtros de WAN virtual e firewall do Azure para gerenciar o acesso seguro para conexões com seus recursos no Azure por meio de conexões IKEv2 ou de VPN abertas de ponto a site. Essa configuração será útil se você tiver usuários remotos para os quais deseja restringir o acesso aos recursos do Azure ou para proteger seus recursos no Azure.

As etapas neste artigo ajudam você a criar a arquitetura no diagrama a seguir para permitir que clientes VPN de usuários acessem um recurso específico (VM1) em uma VNet spoke conectada ao Hub virtual, mas não a outros recursos (VM2). Use este exemplo de arquitetura como uma diretriz básica.

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="Diagrama: Hub virtual protegido" :::

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* Você tem os valores disponíveis para a configuração de autenticação que deseja usar. Por exemplo, um servidor RADIUS, Azure Active Directory autenticação ou [gerar e exportar certificados](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

## <a name="create-a-virtual-wan"></a>Criar uma WAN virtual

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>Definir parâmetros de configuração de P2S

A configuração ponto a site (P2S) define os parâmetros para conectar clientes remotos. Esta seção ajuda você a definir parâmetros de configuração do P2S e, em seguida, criar a configuração que será usada para o perfil do cliente VPN. As instruções que você seguir dependem do método de autenticação que você deseja usar.

### <a name="authentication-methods"></a>Métodos de autenticação

Ao selecionar o método de autenticação, você tem três opções. Cada método tem requisitos específicos. Selecione um dos métodos a seguir e conclua as etapas.

* **Autenticação Azure Active Directory:** Obtenha o seguinte:

   * A **ID de aplicativo** do aplicativo empresarial da VPN do Azure registrada em seu locatário do Azure AD.
   * O **emissor**. Exemplo: `https://sts.windows.net/your-Directory-ID`.
   * O **locatário do Azure ad**. Exemplo: `https://login.microsoftonline.com/your-Directory-ID`.

* **Autenticação baseada em RADIUS:** Obtenha o IP do servidor RADIUS, o segredo do servidor RADIUS e as informações de certificado.

* **Certificados do Azure:** Para essa configuração, os certificados são necessários. Você precisa gerar ou obter certificados. Um certificado de cliente é necessário para cada cliente. Além disso, as informações de certificado raiz (chave pública) precisam ser carregadas. Para obter mais informações sobre os certificados necessários, consulte [gerar e exportar certificados](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>Criar o Hub e o gateway

Nesta seção, você cria o Hub virtual com um gateway de ponto a site. Ao configurar, você pode usar os seguintes valores de exemplo:

* **Espaço de endereço IP privado do Hub:** 10.0.0.0/24
* **Pool de endereços do cliente:** 10.5.0.0/16
* **Servidores DNS personalizados:** Você pode listar até 5 servidores DNS

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Gerar os arquivos de configuração de cliente VPN

Nesta seção, você gera e baixa os arquivos de perfil de configuração. Esses arquivos são usados para configurar o cliente VPN nativo no computador cliente. Para obter informações sobre o conteúdo dos arquivos de perfil do cliente, consulte [configuração ponto a site-certificados](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md).

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>Configurar clientes de VPN

Use o perfil baixado para configurar os clientes de acesso remoto. O procedimento para cada sistema operacional é diferente, siga as instruções que se aplicam ao seu sistema.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>Conectar a VNet do spoke

Nesta seção, você anexará a rede virtual do spoke ao Hub WAN virtual.

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>Criar máquinas virtuais

Nesta seção, você criará duas VMs em sua VNet, VM1 e VM2. No diagrama de rede, usamos 10.18.0.4 e 10.18.0.5. Ao configurar suas VMs, certifique-se de selecionar a rede virtual que você criou (encontrada na guia rede). Para obter as etapas para criar uma VM, consulte [início rápido: criar uma VM](../virtual-machines/windows/quick-create-portal.md).

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>Proteger o Hub virtual

Um hub virtual padrão não tem políticas de segurança internas para proteger os recursos em redes virtuais spoke. Um hub virtual protegido usa o Firewall do Azure ou um provedor de terceiros para gerenciar o tráfego de entrada e saída para proteger seus recursos no Azure.

Converta o Hub em um hub protegido usando o seguinte artigo: [Configurar o Firewall do Azure em um hub de WAN virtual](howto-firewall.md).

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a> Criar regras para gerenciar e filtrar o tráfego

Crie regras que regem o comportamento do firewall do Azure. Ao proteger o Hub, garantimos que todos os pacotes que entram no Hub virtual estejam sujeitos ao processamento do firewall antes de acessar os recursos do Azure.

Depois de concluir essas etapas, você terá criado uma arquitetura que permite que os usuários VPN acessem a VM com o endereço IP privado 10.18.0.4, mas **não** acesse a VM com o endereço IP privado 10.18.0.5

1. No portal do Azure, navegue até **Gerenciador de firewall**.
1. Em segurança, selecione **políticas de firewall do Azure**.
1. Selecione **Criar uma Política de Firewall do Azure**.
1. Em **detalhes da política**, digite um nome e selecione a região na qual o Hub virtual está implantado.
1. Selecione **Avançar: Configurações de DNS (versão prévia)** .
1. Selecione **Avançar: Regras**.
1. Na guia **Regras**, selecione **Adicionar a coleção de regras**.
1. Forneça um nome para a coleção. Defina o tipo como **rede**. Adicione um valor de prioridade **100**.
1. Preencha o nome da regra, o tipo de origem, a origem, o protocolo, as portas de destino e o tipo de destino, conforme mostrado no exemplo abaixo. Em seguida, selecione **Adicionar**. Essa regra permite que qualquer endereço IP do pool de clientes VPN acesse a VM com o endereço IP privado 10.18.04, mas não com nenhum outro recurso conectado ao Hub virtual. Crie todas as regras que você deseja que se adaptem às regras de permissões e de arquitetura desejadas.

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="Regras de firewall" :::

1. Selecione **Avançar: Inteligência contra ameaças**.
1. Selecione **Avançar: Hubs**.
1. Na guia **Hubs**, selecione **Associar hubs virtuais**.
1. Selecione o Hub virtual que você criou anteriormente e, em seguida, selecione **Adicionar**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

Pode levar 5 minutos ou mais para que esse processo seja concluído.

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>Rotear tráfego por meio do firewall do Azure

Nesta seção, você precisa garantir que o tráfego seja roteado pelo firewall do Azure.

1. No portal, no **Gerenciador de firewall**, selecione **hubs virtuais protegidos**.
1. Selecione o Hub virtual que você criou.
1. Em **Configurações**, escolha **Configuração de segurança**.
1. Em **Tráfego privado**, selecione **Enviar por meio do Firewall do Azure**.
1. Verifique se a conexão VNet e o tráfego privado da conexão de ramificação estão protegidos pelo firewall do Azure.
1. Clique em **Salvar**.

## <a name="validate"></a><a name="validate"></a>Verifica

Verifique a configuração do seu hub protegido.

1. Conecte-se ao **Hub virtual protegido** por meio de VPN do dispositivo cliente.
1. Execute ping no endereço IP **10.18.0.4** do seu cliente. Você deverá ver uma resposta.
1. Execute ping no endereço IP **10.18.0.5** do seu cliente. Você não deve ser capaz de ver uma resposta.

### <a name="considerations"></a>Considerações

* Certifique-se de que a **tabela rotas efetivas** no Hub virtual protegido tenha o próximo salto para tráfego privado pelo firewall. Para acessar a tabela rotas efetivas, navegue até o recurso de **Hub virtual** . Em **conectividade**, selecione **Roteamento** e, em seguida, selecione **rotas efetivas**. A partir daí, selecione a tabela de rotas **padrão** .
* Verifique se você criou regras na seção [criar regras](#create-rules) . Se essas etapas forem perdidas, as regras que você criou não serão realmente associadas ao Hub, e a tabela de rotas e o fluxo de pacotes não usarão o Firewall do Azure.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, consulte as [Perguntas frequentes sobre a WAN Virtual](virtual-wan-faq.md).
* Para obter mais informações sobre o Firewall do Azure, consulte as [perguntas frequentes](../firewall/firewall-faq.yml)sobre o Firewall do Azure.
