---
title: Configurar a política de exportação para o volume do NFS-Azure NetApp Files
description: Descreve como configurar a política de exportação para controlar o acesso a um volume NFS usando o Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: d3f268a4ce2660350055367770e987a06828e2d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740110"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurar a política de exportação para um volume do NFS

Você pode configurar a política de exportação para controlar o acesso a um volume de Azure NetApp Files. Azure NetApp Files política de exportação dá suporte a volumes que usam o protocolo NFS (NFSv3 e NFSv 4.1) e o protocolo dual (NFSv3 e SMB). 

Você pode criar até cinco regras de política de exportação.

## <a name="configure-the-policy"></a>Configurar a política 

1.  Na página **volumes** , selecione o volume para o qual você deseja configurar a política de exportação e, em seguida, selecione **Exportar política**. Você também pode configurar a política de exportação durante a criação do volume.

2.  Para criar uma regra de política de exportação, especifique as seguintes informações:   
    * **Índice**: especifique o número de índice para a regra.  
      
      Uma política de exportação pode consistir em até cinco regras. As regras são avaliadas de acordo com sua ordem na lista de números de índice. As regras com números de índice mais baixos são avaliadas primeiro. Por exemplo, a regra com o número de índice 1 é avaliada antes da regra com o número de índice 2. 

    * **Clientes permitidos**: especifique o valor em um dos seguintes formatos:  
      * Endereço IPv4. Exemplo: `10.1.12.24`
      * Endereço IPv4 com uma máscara de sub-rede expressa como um número de bits. Exemplo: `10.1.12.10/4`
      * Endereços IP separados por vírgulas. Você pode inserir vários IPs de host em uma única regra separando-os com vírgulas. Exemplo: `10.1.12.25,10.1.12.28,10.1.12.29`

    * **Acesso**: selecione um dos seguintes tipos de acesso:  
      * Sem Acesso 
      * Leitura e Gravação
      * Somente leitura

    * **Somente leitura** e **leitura/gravação**: se você usar a criptografia Kerberos com o nfsv 4.1, siga as instruções em [Configurar a criptografia Kerberos do nfsv 4.1](configure-kerberos-encryption.md).  Para o impacto no desempenho do Kerberos, consulte [impacto no desempenho do Kerberos em volumes do nfsv 4.1](performance-impact-kerberos.md). 

      ![Opções de segurança do Kerberos](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Acesso à raiz**: especifique se a `root` conta pode acessar o volume.  Por padrão, o acesso à raiz é definido como **ativado** e a `root` conta tem acesso ao volume.

      ![Exportar política](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

## <a name="next-steps"></a>Próximas etapas 
* [Montar ou desmontar um volume para máquinas virtuais](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gerenciar instantâneos](azure-netapp-files-manage-snapshots.md)
