---
title: Opções de rede do serviço do Azure Image Builder
description: Entender as opções de rede ao implantar o serviço do construtor de imagem de VM do Azure
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: cd5027ca6e0ce3dc02da14b7dd6afd6e00e3f92d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669431"
---
# <a name="azure-image-builder-service-networking-options"></a>Opções de rede do serviço do Azure Image Builder

Você precisa optar por implantar o construtor de imagens do Azure com ou sem uma VNET existente.

## <a name="deploy-without-specifying-an-existing-vnet"></a>Implantar sem especificar uma VNET existente

Se você não especificar uma VNET existente, o construtor de imagem do Azure criará uma VNET e uma sub-rede no grupo de recursos de preparo. Um recurso de IP público é usado com um grupo de segurança de rede para restringir o tráfego de entrada para o serviço do construtor de imagens do Azure. O IP público facilita o canal para comandos do Azure Image Builder durante a compilação da imagem. Depois que a compilação for concluída, a VM, o IP público, os discos e a VNET serão excluídos. Para usar essa opção, não especifique nenhuma propriedade de VNET.

## <a name="deploy-using-an-existing-vnet"></a>Implantar usando uma VNET existente

Se você especificar uma VNET e uma sub-rede, o construtor de imagens do Azure implantará a VM de compilação em sua VNET escolhida. Você pode acessar recursos que podem ser acessados em sua VNET. Você também pode criar uma VNET em silo que não está conectada a nenhuma outra VNET. Se você especificar uma VNET, o construtor de imagens do Azure não usará um endereço IP público. A comunicação do serviço do construtor de imagens do Azure para a VM de compilação usa a tecnologia de link privado do Azure.

Para obter mais informações, consulte um dos exemplos a seguir:

* [Usar o construtor de imagens do Azure para VMs do Windows permitindo o acesso a uma VNET do Azure existente](../windows/image-builder-vnet.md)
* [Usar o construtor de imagens do Azure para VMs Linux permitindo o acesso a uma VNET do Azure existente](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>O que é o Link Privado do Azure?

O link privado do Azure fornece conectividade privada de uma rede virtual para a PaaS (plataforma como um serviço) do Azure, de Propriedade do cliente ou serviços de parceiros da Microsoft. Ele simplifica a arquitetura de rede e protege a conexão entre os pontos de extremidade no Azure, eliminando a exposição de dados para a Internet pública. Para obter mais informações, consulte a [documentação do link privado](../../private-link/index.yml).

### <a name="required-permissions-for-an-existing-vnet"></a>Permissões necessárias para uma VNET existente

O construtor de imagens do Azure requer permissões específicas para usar uma VNET existente. Para obter mais informações, consulte [configurar permissões de serviço do construtor de imagem do Azure usando CLI do Azure](image-builder-permissions-cli.md) ou [configurar permissões de serviço do construtor de imagem do Azure usando o PowerShell](image-builder-permissions-powershell.md).

### <a name="what-is-deployed-during-an-image-build"></a>O que é implantado durante uma compilação de imagem?

Usar uma VNET existente significa que o construtor de imagens do Azure implanta uma VM adicional (VM de proxy) e um Azure Load Balancer (ALB) que está conectado ao link privado. O tráfego do serviço AIB passa pelo link privado para o ALB. O ALB se comunica com a VM de proxy usando a porta 60001 para o SO Linux ou 60000 para o sistema operacional Windows. O proxy encaminha os comandos para a VM de compilação usando a porta 22 para o SO Linux ou 5986 para o sistema operacional Windows.

> [!NOTE]
> A VNET deve estar na mesma região que a região de serviço do construtor de imagens do Azure.
> 

### <a name="why-deploy-a-proxy-vm"></a>Por que implantar uma VM de proxy?

Quando uma VM sem IP público está atrás de um Load Balancer interno, ela não tem acesso à Internet. O Azure Load Balancer usado para VNET é interno. A VM de proxy permite o acesso à Internet para a VM de compilação durante as compilações. Os grupos de segurança de rede associados podem ser usados para restringir o acesso à VM de compilação.

O tamanho da VM do proxy implantado é o padrão A1_v2 além da VM de compilação. A VM de proxy é usada para enviar comandos entre o serviço do construtor de imagens do Azure e a VM de compilação. As propriedades da VM do proxy não podem ser alteradas, incluindo o tamanho ou o sistema operacional. Você não pode alterar as propriedades da VM do proxy para compilações de imagem do Windows e do Linux.

### <a name="image-template-parameters-to-support-vnet"></a>Parâmetros de modelo de imagem para dar suporte à VNET
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| Configuração | Descrição |
|---------|---------|
| name | Adicional Nome de uma rede virtual pré-existente. |
| subnetName | Nome da sub-rede na rede virtual especificada. Deve ser especificado se e somente se o *nome* for especificado. |
| resourceGroupName | Nome do grupo de recursos que contém a rede virtual especificada. Deve ser especificado se e somente se o *nome* for especificado. |

O serviço de vínculo privado requer um IP da VNET e da sub-rede fornecidas. Atualmente, o Azure não dá suporte a políticas de rede nesses IPs. Portanto, as políticas de rede precisam ser desabilitadas na sub-rede. Para obter mais informações, consulte a [documentação do link privado](../../private-link/index.yml).

### <a name="checklist-for-using-your-vnet"></a>Lista de verificação para usar sua VNET

1. Permitir que Azure Load Balancer (ALB) se comuniquem com a VM de proxy em um NSG
    * [Exemplo de CLI do AZ](image-builder-vnet.md#add-network-security-group-rule)
    * [Exemplo de PowerShell](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. Desabilitar política de serviço particular na sub-rede
    * [Exemplo de CLI do AZ](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [Exemplo de PowerShell](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. Permitir que o construtor de imagens do Azure crie um ALB e adicione VMs à VNET
    * [Exemplo de CLI do AZ](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [Exemplo de PowerShell](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. Permitir que o construtor de imagens do Azure Leia/grave imagens de origem e crie imagens
    * [Exemplo de CLI do AZ](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [Exemplo de PowerShell](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. Verifique se você está usando VNET na mesma região que a região de serviço do construtor de imagem do Azure.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [visão geral do construtor de imagens do Azure](../image-builder-overview.md).