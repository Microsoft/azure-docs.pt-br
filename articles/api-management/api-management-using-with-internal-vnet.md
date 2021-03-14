---
title: Usar o gerenciamento de API do Azure com redes virtuais internas
titleSuffix: Azure API Management
description: Saiba como instalar e configurar o Gerenciamento de API do Azure na rede virtual interna
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.openlocfilehash: 10154f496d76ce6b9eb19d610fdff8d7a4023c2d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565947"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Usar o serviço de Gerenciamento de API do Azure com rede virtual interna
Com as VNETs (Redes Virtuais) do Azure, o Gerenciamento de API pode gerenciar as APIs que não estão acessíveis pela Internet. Várias tecnologias de VPN estão disponíveis para fazer a conexão. O Gerenciamento de API pode ser implantado em dois modos principais dentro de uma rede virtual:
* Externo
* Interna

Quando o gerenciamento de API é implantado no modo de rede virtual interna, todos os pontos de extremidade de serviço (o gateway de proxy, o portal do desenvolvedor, o gerenciamento direto e o git) só são visíveis em uma rede virtual com a qual você controla o acesso. Nenhum dos pontos de extremidade de serviço é registrado no servidor DNS público.

> [!NOTE]
> Como não há entradas DNS para os pontos de extremidade de serviço, esses pontos de extremidade não estarão acessíveis até que o [DNS seja configurado](#apim-dns-configuration) para a rede virtual.

Usando o Gerenciamento de API no modo interno você pode chegar aos seguintes cenários:

* Torne as APIs hospedadas em seu datacenter privado seguras e acessíveis por terceiros externamente usando as conexões VPN Site a Site ou ExpressRoute do Azure.
* Habilite cenários de nuvem híbrida expondo as APIs baseadas em nuvem e as APIs locais por meio de um gateway comum.
* Gerencie suas APIs hospedadas em várias localizações geográficas usando um único ponto de extremidade de gateway.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você precisa ter:

+ **Uma assinatura ativa do Azure**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Uma instância de Gerenciamento de API do Azure**. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Quando um serviço de gerenciamento de API é implantado em uma rede virtual, uma [lista de portas](./api-management-using-with-vnet.md#required-ports) é usada e precisa ser aberta. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Criar um Gerenciamento de API em uma rede virtual interna
O serviço de gerenciamento de API em uma rede virtual interna é hospedado atrás de um [balanceador de carga interno (clássico)](/previous-versions/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). Essa é a única opção disponível e não pode ser alterada.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Habilite uma conexão de rede virtual usando o portal do Azure

1. Navegue até sua instância de Gerenciamento de API do Azure no [portal do Azure](https://portal.azure.com/).
2. Selecione **Rede virtual**.
3. Configure a instância de Gerenciamento de API a ser implantada dentro da rede virtual.

    ![Menu para configurar um Gerenciamento de API do Azure em uma rede virtual interna][api-management-using-internal-vnet-menu]

4. Selecione **Salvar**.

Depois que a implantação for realizada com sucesso, você deverá ver o endereço IP virtual **privado** e o endereço IP virtual **público** do seu serviço de gerenciamento de API na folha visão geral. O endereço IP virtual **privado** é um endereço IP com balanceamento de carga de dentro da sub-rede delegada do gerenciamento de API em que os `gateway` `portal` pontos de extremidade, `management` e `scm` podem ser acessados. O endereço IP virtual **público** é usado **somente** para o tráfego do plano de controle para `management` o ponto de extremidade pela porta 3443 e pode ser bloqueado para o [ApiManagement][ServiceTags] servicetag.

![Painel de Gerenciamento de API com uma rede virtual interna configurada][api-management-internal-vnet-dashboard]

> [!NOTE]
> O console de Teste disponível no portal do Azure não funcionará para o serviço implantado do VNET **Interno**, pois a URL do Gateway não está registrada no DNS público. É necessário usar o Console de Teste fornecido no **portal do Desenvolvedor**.

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>Implantar o gerenciamento de API na rede virtual

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-internal-vnet%2Fazuredeploy.json)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Você também pode habilitar a conectividade de rede virtual usando cmdlets do PowerShell.

* Criar um serviço de gerenciamento de API dentro de uma rede virtual: Use o cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) para criar um serviço de gerenciamento de API do Azure dentro de uma rede virtual e configurá-lo para usar o tipo de rede virtual interna.

* Atualizar uma implantação existente de um serviço de gerenciamento de API dentro de uma rede virtual: Use o cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) para mover um serviço de gerenciamento de API existente dentro de uma rede virtual e configurá-lo para usar o tipo de rede virtual interna.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>Configuração de DNS
Quando o Gerenciamento de API está no modo de rede virtual externa, o DNS é gerenciado pelo Azure. Para o modo de rede virtual interna, você precisa gerenciar o seu próprio DNS. Configurar uma zona privada de DNS do Azure e vinculá-la ao serviço de gerenciamento de API de rede virtual é implantado em é a opção recomendada.  Clique [aqui](../dns/private-dns-getstarted-portal.md) para saber como configurar uma zona privada no DNS do Azure.

> [!NOTE]
> O serviço de Gerenciamento de API não escuta as solicitações que vêm de endereços IP. Ele só responde às solicitações para o nome de host configurado em seus pontos de extremidade de serviço. Esses pontos de extremidade incluem o gateway, o portal do Azure, o portal do Desenvolvedor, o ponto de extremidade de gerenciamento direto e o Git.

### <a name="access-on-default-host-names"></a>Acesso em nomes de host padrão
Quando você cria um serviço de gerenciamento de API, chamado "contosointernalvnet", por exemplo, os seguintes pontos de extremidade de serviço são configurados por padrão:

   * Gateway ou proxy: contosointernalvnet.azure-api.net

   * O portal do desenvolvedor: contosointernalvnet.portal.azure-api.net

   * O novo portal do desenvolvedor: contosointernalvnet.developer.azure-api.net

   * Ponto de extremidade de gerenciamento direto: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Para acessar esses pontos de extremidade do serviço de Gerenciamento de API, você pode criar uma máquina virtual em uma sub-rede conectada à rede virtual na qual o Gerenciamento de API está implantado. Supondo que o endereço IP virtual interno do seu serviço seja 10.1.0.5, você pode mapear o arquivo de hosts,%SystemDrive%\drivers\etc\hosts, da seguinte maneira:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Então você pode acessar todos os pontos de extremidade do serviço da máquina virtual criada.
Se você usar um servidor DNS personalizado em uma rede virtual, também poderá criar registros DNS A e acessar esses pontos de extremidade de qualquer lugar em sua rede virtual.

### <a name="access-on-custom-domain-names"></a>Acessar em nomes de domínio personalizados

1. Se você não quiser acessar o serviço de Gerenciamento de API com os nomes de host padrão, poderá configurar nomes de domínio personalizados para todos os seus pontos de extremidade de serviço, conforme mostra a imagem abaixo:

   ![Configurar o domínio personalizado para Gerenciamento de API][api-management-custom-domain-name]

2. Em seguida, você pode criar registros no seu servidor DNS para acessar os pontos de extremidade que só estão acessíveis pela rede virtual.

## <a name="routing"></a><a name="routing"> </a> Roteamento

* Um endereço IP virtual *privado* com balanceamento de carga do intervalo de sub-rede será reservado e usado para acessar os pontos de extremidade do serviço de gerenciamento de API de dentro da rede virtual. Esse endereço IP *privado* pode ser encontrado na folha de visão geral do serviço no portal do Azure. Esse endereço deve ser registrado com os servidores DNS usados pela rede virtual.
* Um endereço IP *público* com balanceamento de carga (VIP) também será reservado para fornecer acesso ao ponto de extremidade do serviço de gerenciamento pela porta 3443. Esse endereço IP *público* pode ser encontrado na folha de visão geral do serviço no portal do Azure. O endereço IP *público* é usado somente para o tráfego do plano de controle para o `management` ponto de extremidade pela porta 3443 e pode ser bloqueado para o [ApiManagement][ServiceTags] servicetag.
* Os endereços IP do intervalo de IPS de sub-rede (DIP) serão atribuídos a cada VM no serviço e serão usados para acessar recursos na rede virtual. Um endereço IP público (VIP) será usado para acessar recursos fora da rede virtual. Se as listas de restrição de IP forem usadas para proteger recursos na rede virtual, todo o intervalo para a sub-rede em que o serviço de gerenciamento de API é implantado deverá ser especificado para conceder ou restringir o acesso do serviço.
* Os endereços IP públicos e privados com balanceamento de carga podem ser encontrados na folha visão geral do portal do Azure.
* Os endereços IP atribuídos para acesso público e privado poderão ser alterados se o serviço for removido do e, em seguida, adicionado novamente à rede virtual. Se isso acontecer, pode ser necessário atualizar os registros de DNS, as regras de roteamento e as listas de restrição de IP dentro da rede virtual.

## <a name="related-content"></a><a name="related-content"> </a>Conteúdo relacionado
Confira os seguintes artigos para saber mais:
* [Problemas comuns de configuração de rede ao configurar o Gerenciamento de API do Azure em uma rede virtual][Common network configuration problems]
* [Perguntas frequentes sobre rede virtual](../virtual-network/virtual-networks-faq.md)
* [Criando um registro no DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
