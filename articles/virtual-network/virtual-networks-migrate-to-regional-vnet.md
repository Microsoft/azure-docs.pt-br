---
title: Migrar uma rede virtual do Azure (clássica) de um grupo de afinidades para uma região | Microsoft Docs
description: Saiba como migrar uma rede virtual (clássica) de um grupo de afinidades para uma região.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: d3bb93d12a217e6d9066d037ff92f071b6139ab3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648628"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migrar uma rede virtual (clássica) de um grupo de afinidades para uma região

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo de implantação do Gerenciador de Recursos.

Grupos de afinidades asseguram que recursos criados dentro do mesmo grupo de afinidades sejam fisicamente hospedados por servidores próximos uns dos outros, permitindo a esses recursos comuniquem-se mais rapidamente. Antigamente, os grupos de afinidades eram um requisito para a criação de redes virtuais (clássicas). Na época, o serviço gerenciador de rede que gerenciava as redes virtuais (clássicas) podia funcionar somente dentro de um conjunto de servidores físicos ou unidade de escala. As melhorias de arquitetura aumentaram o escopo de gerenciamento de rede para uma região.

Como resultado dessas melhorias na arquitetura, os grupos de afinidade não são mais recomendados nem obrigatórios para redes virtuais (clássicas). O uso de grupos de afinidade para redes virtuais (clássicas) é substituído por regiões. Redes virtuais (clássicas) associadas a regiões são chamadas de redes virtuais regionais.

Recomendamos que você não use grupos de afinidades em geral. Além do requisito de rede virtual, os grupos de afinidades também foram importantes para garantir que recursos como computação (clássico) e armazenamento (clássico) fossem colocados próximos uns dos outros. No entanto, com a arquitetura atual de rede do Azure, esses requisitos de colocação não são mais necessários.

> [!IMPORTANT]
> Embora ainda seja tecnicamente possível criar uma rede virtual associada a um grupo de afinidades, não há nenhum motivo relevante para fazê-lo. Muitos recursos de rede virtual, como grupos de segurança de rede, estão disponíveis somente com o uso de uma rede virtual regional e não estão disponíveis para redes virtuais associadas a grupos de afinidades.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Editar o arquivo de configuração de rede

1. Exportar o arquivo de configuração de rede. Para saber como exportar um arquivo de configuração de rede usando o PowerShell ou a interface de linha de comando (CLI) 1.0 do Azure, consulte [Configurar uma rede virtual usando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md#export).
2. Edite o arquivo de configuração de rede substituindo **AffinityGroup** por **Local**. Especifique uma [região](https://azure.microsoft.com/regions) do Azure para **Local**.
   
   > [!NOTE]
   > O **Local** é a região que você especificou para o grupo de afinidades associado à sua rede virtual (clássica). Por exemplo, se sua rede virtual (clássica) estiver associada a um grupo de afinidades localizado no Oeste dos EUA, quando você migrar, seu **Local** deverá apontar para o Oeste dos EUA. 
   > 
   > 
   
    Edite as seguintes linhas no arquivo de configuração de rede substituindo os valores pelos seus: 
   
    **Valor antigo:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **Novo valor:** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. Salve suas alterações e [importe](virtual-networks-using-network-configuration-file.md#import) a configuração de rede para o Azure.

> [!NOTE]
> Esta migração NÃO gera tempo de inatividade para seus serviços.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>O que fazer se você tiver uma VM (clássica) em um grupo de afinidades
VMs (clássicas) que estão atualmente em um grupo de afinidades não precisam ser removidas do grupo de afinidades. Quando uma máquina virtual é implantada, ela é implantada em uma única unidade de escala. Os grupos de afinidades pode restringir o conjunto de tamanhos de VM disponíveis para uma nova implantação de VM, mas qualquer VM existente já implantada fica restrita ao conjunto de tamanhos de VM disponíveis na unidade de escala em que a VM estiver implantada. Porque a VM já está implantada para uma unidade de escala, remover uma VM de um grupo de afinidades não tem nenhum efeito sobre a VM.
