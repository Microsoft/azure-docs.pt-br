---
title: Configurar o domínio padrão do NFSv 4.1 para Azure NetApp Files | Microsoft Docs
description: Descreve como configurar o cliente NFS para usar o NFSv 4.1 com o Azure NetApp Files.
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/14/2020
ms.author: b-juche
ms.openlocfilehash: c3c853190d5f63bbe9012727d8b7b7ac91da135f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072145"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Configurar o domínio padrão do NFSv4.1 para o Azure NetApp Files

NFSv4 introduz o conceito de domínio de autenticação. O Azure NetApp Files atualmente dá suporte ao mapeamento de usuário somente raiz do serviço para o cliente NFS. Para usar a funcionalidade NFSv 4.1 com Azure NetApp Files, você precisa atualizar o cliente NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Comportamento padrão do mapeamento de usuário/grupo

O mapeamento de raiz usa como padrão o `nobody` usuário, pois o domínio NFSv4 é definido como `localdomain` por padrão. Ao montar um volume Azure NetApp Files NFSv 4.1 como raiz, você verá permissões de arquivo da seguinte maneira:  

![Comportamento padrão do mapeamento de usuário/grupo para NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Como mostra o exemplo acima, o usuário `file1` deve ser `root` , mas é mapeado para `nobody` por padrão.  Este artigo mostra como definir o `file1` usuário como `root` alterando a `idmap Domain` configuração para `defaultv4iddomain.com` .  

## <a name="steps"></a>Etapas 

1. Edite o `/etc/idmapd.conf` arquivo no cliente NFS.   
    Remova a marca de comentário da linha `#Domain` (ou seja, remove a `#` da linha) e altere o valor `localdomain` para `defaultv4iddomain.com` . 

    Configuração inicial: 
    
    ![Configuração inicial do NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Configuração atualizada:
    
    ![Configuração atualizada para o NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Desmonte todos os volumes NFS montados no momento.
3. Atualize o `/etc/idmapd.conf` arquivo.
4. Reinicie o `rpcbind` serviço no host ( `service rpcbind restart` ) ou simplesmente reinicialize o host.
5. Monte os volumes de NFS conforme necessário.   

    Confira [montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

O exemplo a seguir mostra a alteração de usuário/grupo resultante: 

![Captura de tela que mostra um exemplo da alteração de usuário/grupo resultante.](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Como mostra o exemplo, o usuário/grupo agora mudou de `nobody` para `root` .

## <a name="behavior-of-other-non-root-users-and-groups"></a>Comportamento de outros usuários e grupos (não raiz)

O Azure NetApp Files dá suporte a usuários locais (usuários criados localmente em um host) que têm permissões associadas a arquivos ou pastas nos volumes NFSv 4.1. No entanto, no momento, o serviço não dá suporte ao mapeamento de usuários/grupos em vários nós. Portanto, os usuários criados em um host não são mapeados por padrão para os usuários criados em outro host. 

No exemplo a seguir, `Host1` tem três contas de usuário de teste existentes ( `testuser01` , `testuser02` , `testuser03` ): 

![Captura de tela que mostra que o Host1 tem três contas de usuário de teste existentes.](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

No `Host2` , observe que as contas de usuário de teste não foram criadas, mas o mesmo volume é montado em ambos os hosts:

![Configuração resultante para NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Próxima etapa 

[Montar ou desmontar um volume para máquinas virtuais do Windows ou do Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

