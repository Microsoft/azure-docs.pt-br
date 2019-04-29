---
title: Gerenciar listas de controle de acesso de ponto de extremidade do Azure | PowerShell | Clássico | Microsoft Docs
description: Saiba como gerenciar ACLs com o PowerShell
services: virtual-network
documentationcenter: na
author: genlin
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 1f6c14c15d4930902ced642bd02d1d1833e0b361
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032530"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Gerenciar as listas de controle de acesso de ponto de extremidade usando o PowerShell no modelo de implantação clássica
Você pode criar e gerenciar listas de controle de acesso (ACLs) para pontos de extremidade usando o PowerShell do Azure ou no Portal de Gerenciamento. Neste tópico, você encontrará procedimentos para tarefas comuns de ACL que podem ser concluídas usando o PowerShell. Para obter a lista de cmdlets do Azure PowerShell, confira [Cmdlets de gerenciamento do Azure](https://go.microsoft.com/fwlink/?LinkId=317721). Para saber mais sobre ACLs, confira [O que é uma lista de controle de acesso (ACL) de rede?](virtual-networks-acl.md). Se você quiser gerenciar suas ACLs usando o Portal de Gerenciamento, confira [Como configurar pontos de extremidade para uma máquina virtual](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Gerenciar ACLs de rede usando o PowerShell do Azure
Você pode usar os cmdlets do PowerShell do Azure para criar, remover e configurar (definir) listas de controle de acesso (ACLs) de rede. Incluímos alguns exemplos de maneiras pelas quais você pode configurar uma ACL usando o PowerShell.

Para recuperar uma lista completa dos cmdlets do PowerShell para ACL, você pode usar uma das seguintes opções:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Criar uma ACL de rede com regras que permitem o acesso de uma sub-rede remota
O exemplo a seguir ilustra uma maneira de criar uma nova ACL que contém regras. Essa ACL é então aplicada a um ponto de extremidade de máquina virtual. As regras de ACL no exemplo a seguir permitem o acesso de uma sub-rede remota. Para criar uma nova ACL de rede com regras de permissão para uma sub-rede remota, abra um ISE do Azure PowerShell. Copie e cole o script abaixo, configurando-o com seus próprios valores, e execute-o.

1. Crie um novo objeto ACL de rede.
   
        $acl1 = New-AzureAclConfig
2. Defina uma regra que permite o acesso de uma sub-rede remota. No exemplo a seguir, você pode definir uma regra *100* (que tem prioridade sobre regras 200 e superiores) para permitir que a sub-rede remota *10.0.0.0/8* acesse o ponto de extremidade de máquina virtual. Substitua os valores pelos seus próprios requisitos de configuração. O nome "Configuração de ACL de SharePoint" deve ser substituído com o nome amigável que você deseja usar para esta regra.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Para regras adicionais, repita o cmdlet, substituindo os valores pelos seus próprios requisitos de configuração. Não deixe de alterar a ordem de numeração das regras para refletir a ordem na qual você deseja que as regras sejam aplicadas. O número de regra mais baixo tem precedência sobre o número mais alto.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Em seguida, você pode criar um novo ponto de extremidade (Adicionar) ou definir a ACL para um ponto de extremidade existente (Definir). Neste exemplo, adicionaremos um novo ponto de extremidade de máquina virtual chamado "Web" e atualizaremos o ponto de extremidade de máquina virtual com as configurações de ACL.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Em seguida, combinamos os cmdlets e executamos o script. Neste exemplo, os cmdlets combinados teriam esta aparência:
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Remover uma regra de ACL de rede que permite o acesso de uma sub-rede remota
O exemplo a seguir ilustra uma maneira de remover uma regra de ACL de rede.  Para remover uma regra de ACL de rede com regras de permissão para uma sub-rede remota, abra um ISE do Azure PowerShell. Copie e cole o script abaixo, configurando-o com seus próprios valores, e execute-o.

1. Primeira etapa é obter o objeto de ACL de rede para o ponto de extremidade de máquina virtual. Em seguida, você deve remover a regra de ACL. Nesse caso, estamos removendo-a através da ID de regra. Isso removerá apenas a ID de regra 0 da ACL. O objeto de ACL do ponto de extremidade de máquina virtual não é removido.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Em seguida, você deve aplicar o objeto de ACL de rede ao ponto de extremidade de máquina virtual e atualizar a máquina virtual.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Remover uma ACL de rede de um ponto de extremidade de máquina virtual
Em determinados cenários, você talvez queira remover um objeto de ACL de rede de um ponto de extremidade de máquina virtual. Para isso, abra um ISE do Azure PowerShell. Copie e cole o script abaixo, configurando-o com seus próprios valores, e execute-o.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Próximas etapas
[O que é uma lista de controle de acesso (ACL) de rede?](virtual-networks-acl.md)

