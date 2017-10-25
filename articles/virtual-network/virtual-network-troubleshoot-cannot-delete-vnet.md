---
title: "Não é possível excluir uma rede virtual no Azure | Microsoft Docs"
description: "Saiba como solucionar um problema em que você não consegue excluir uma rede virtual no Azure."
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2017
ms.author: genli
ms.openlocfilehash: 3fd0beed7fe76d1cd0861cdc278ac66ead2d0bd3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Solução de problemas: falha ao excluir uma rede virtual no Azure

Você poderá receber erros ao tentar excluir uma rede virtual no Microsoft Azure. Este artigo apresenta etapas para a solução de problemas para ajudá-lo a resolver esse problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Diretriz de solução de problemas 

1. [Verifique se um gateway de rede virtual está em execução na rede virtual](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Verifique se um gateway de aplicativo está em execução na rede virtual](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Verifique se o Serviço de Domínio do Azure Active Directory está habilitado na rede virtual](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Verifique se a rede virtual está conectada a outro recurso](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Verifique se uma máquina virtual ainda está em execução na rede virtual](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Verifique se a rede virtual está presa na migração](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Verifique se um gateway de rede virtual está em execução na rede virtual

Para remover a rede virtual, você deve remover primeiro o gateway de rede virtual.

Para redes virtuais clássicas, vá para a página **Visão geral** da rede virtual clássica no portal do Azure. Na seção **conexões VPN**, se o gateway estiver em execução na rede virtual, você verá o endereço IP do gateway. 

![Verifique se o gateway está em execução](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Para redes virtuais, vá para a página **Visão geral** da rede virtual. Confira **Dispositivos conectados** para o gateway de rede virtual.

![Verifique o dispositivo conectado](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Antes de remover o gateway, primeiro remova qualquer objeto de **Conexão** no gateway. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Verifique se um gateway de aplicativo está em execução na rede virtual

Vá para a página **Visão geral** da rede virtual. Verifique os **Dispositivos conectados** para o gateway de aplicativo.

![Verifique o dispositivo conectado](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Se houver um gateway de aplicativo, você deverá removê-lo antes de você pode excluir a rede virtual.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Verifique se o Serviço de Domínio do Azure Active Directory está habilitado na rede virtual

Se o Serviço de Domínio do Active Directory estiver habilitado e conectado à rede virtual, não será possível excluir essa rede virtual. 

![Verifique o dispositivo conectado](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Para desabilitar o serviço, siga estas etapas:

1. Vá para o [portal clássico do Azure](https://manage.windowsazure.com).
2. No painel esquerdo, selecione **Active Directory**.
3. Selecione o diretório do Azure AD (Azure Active Directory) que tem o Serviço de Domínio do Active Directory habilitado.
4. Selecione a guia **Configurar** .
5. Em **serviços de domínio**, altere a opção **Habilitar serviços de domínio para este diretório** para **Não**.  

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Verifique se a rede virtual está conectada a outro recurso

Verifique se há Links de Circuito, conexões e emparelhamentos de rede virtual. Qualquer uma dessas opções pode fazer a exclusão de uma rede virtual falhar. 

A ordem de exclusão recomendada é a seguinte:

1. Conexões de gateway
2. Gateways
3. IPs
4. Emparelhamentos de rede virtual
5. ASE (Ambiente do Serviço de Aplicativo)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Verifique se uma máquina virtual ainda está em execução na rede virtual

Verifique se nenhuma máquina virtual está na rede virtual.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Verifique se a rede virtual está presa na migração

Se a rede virtual estiver presa em um estado de migração, ela não poderá ser excluída. Execute o comando a seguir para anular a migração e, em seguida, excluir a rede virtual.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Próximas etapas

- [Rede Virtual do Azure](virtual-networks-overview.md)
- [Perguntas frequentes sobre a rede virtual do Azure (Perguntas frequentes)](virtual-networks-faq.md)