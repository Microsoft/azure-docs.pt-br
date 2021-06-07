---
title: Usando a área de trabalho virtual do Windows com Azure NetApp Files | Microsoft Docs
description: Fornece orientações de práticas recomendadas e plantas de exemplo sobre como implantar a área de trabalho virtual do Windows com o Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: 0cd1f6210fbdb74e3fd511150157dccca3e92dda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91932457"
---
# <a name="benefits-of-using-azure-netapp-files-with-windows-virtual-desktop"></a>Benefícios de usar o Azure NetApp Files com a Área de Trabalho Virtual do Windows 

Este artigo fornece orientações de práticas recomendadas sobre a implantação da área de trabalho virtual do Windows (WVD) com o Azure NetApp Files.

Azure NetApp Files é um serviço de armazenamento de arquivos altamente funcional do Azure. Ele pode fornecer até 450.000 IOPS e latência de submilissegundo, capaz de dar suporte a uma escala extremamente grande de implantações de área de trabalho virtual do Windows. Você pode ajustar a largura de banda e alterar o nível de serviço de seus volumes Azure NetApp Files sob demanda quase instantaneamente sem pausar a e/s enquanto mantém o acesso ao plano de dados. Esse recurso permite que você otimize facilmente sua escala de implantação do WVD para custos. Você também pode criar instantâneos de volume point-in-time econômicos sem afetar o desempenho do volume. Esse recurso possibilita que você reverta [contêineres de perfil de usuário FSLogix](../virtual-desktop/store-fslogix-profile.md) individuais por meio de uma cópia do `~snapshot` diretório ou reverta instantaneamente todo o volume de uma vez por meio do recurso de reversão de volume.  Com os instantâneos de até 255 (rotativo) in-loco para proteger um volume contra perda ou corrupção de dados, os administradores têm muitas chances de desfazer o que foi feito.

## <a name="sample-blueprints"></a>Plantas de exemplo

Os seguintes planos gráficos de exemplo mostram a integração da área de trabalho virtual do Windows com o Azure NetApp Files. Em um cenário de área de trabalho em pool, os usuários são direcionados para a melhor sessão disponível (o [modo de amplitude inicial](../virtual-desktop/host-pool-load-balancing.md#breadth-first-load-balancing-method)) no pool, usando [máquinas virtuais de várias sessões](../virtual-desktop/windows-10-multisession-faq.md#what-is-windows-10-enterprise-multi-session). Por outro lado, as áreas de trabalho pessoais são reservadas para cenários nos quais cada usuário tem sua própria máquina virtual.

### <a name="pooled-desktop-scenario"></a>Cenário de área de trabalho em pool

Para o cenário em pool, a equipe de área de trabalho virtual do Windows [recomenda](/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations) as seguintes diretrizes por conta de usuário para vCPU. Observe que nenhum tamanho de máquina virtual é especificado nesta recomendação.

|     Tipo de carga de trabalho     |     Claro    |     Médio    |     Intenso    |
|-----------------------|--------------|---------------|--------------|
|     Usuários por vCPU    |     6        |     4         |     2        |


Essa recomendação é confirmada por um teste de LoginVSI de 500 usuários, registrando aproximadamente 62 "usuários de conhecimento/médio" em cada máquina virtual D16as_V4. 

Por exemplo, em 62 usuários por máquina virtual D16as_V4, Azure NetApp Files pode facilmente dar suporte a 60.000 usuários por ambiente. O teste para avaliar o limite superior do D32as_v4 máquina virtual está em andamento. Se a recomendação do usuário do WVD por vCPU for verdadeira para o D32as_v4, mais de 120.000 usuários se ajustarão em 1.000 máquinas virtuais antes [de broaching o limite de VNet de IP 1.000](./azure-netapp-files-network-topologies.md), conforme mostrado na figura a seguir.  

![Cenário de área de trabalho em pool de área de trabalho virtual do Windows](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>Cenário de área de trabalho pessoal 

Em um cenário de área de trabalho pessoal, a figura a seguir mostra a recomendação de arquitetura de finalidade geral. Os usuários são mapeados para pods de área de trabalho específicas e cada pod tem apenas menos de 1.000 máquinas virtuais, deixando espaço para endereços IP propagando da VNet de gerenciamento. Azure NetApp Files pode facilmente lidar com 900 + áreas de trabalho pessoais por VNet do pool de hosts de uma única sessão, com o número real de máquinas virtuais iguais a 1.000 menos o número de hosts de gerenciamento encontrados na VNet do Hub. Se mais áreas de trabalho pessoais forem necessárias, será fácil adicionar mais pods (pools de hosts e redes virtuais), conforme mostrado na figura a seguir. 

![Cenário de desktop pessoal do Windows Virtual Desktop](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

Ao criar uma arquitetura baseada em POD como essa, atribuir usuários ao Pod correto no logon é de importância para garantir que os usuários sempre encontrem seus perfis de usuário. 

## <a name="next-steps"></a>Próximas etapas

- [Arquiteturas da solução usando o Azure NetApp Files](azure-netapp-files-solution-architectures.md)