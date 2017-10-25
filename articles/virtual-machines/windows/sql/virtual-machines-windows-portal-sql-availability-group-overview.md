---
title: "Grupos de Disponibilidade do SQL Server - máquinas virtuais do Azure - visão geral | Microsoft Docs"
description: "Este artigo apresenta os Grupos de Disponibilidade do SQL Server em máquinas virtuais do Azure."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: 2cbb9ff3b2d13996b1b8dc64aa833807c264c877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Introdução aos grupos de disponibilidade do AlwaysOn do SQL Server em máquinas virtuais do Azure #

Este artigo apresenta os grupos de disponibilidade do SQL Server em máquinas virtuais do Azure. 

Os grupos de disponibilidade Always On em máquinas virtuais do Azure são semelhantes aos grupos de disponibilidade Always On locais. Para obter mais informações, confira [Grupos de Disponibilidade AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx). 

O diagrama ilustra as partes de um Grupo de Disponibilidade do SQL Server completo em Máquinas Virtuais do Azure.

![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

A principal diferença para um Grupo de Disponibilidade em máquinas virtuais do Azure é que as máquinas virtuais do Azure exigem um [balanceador de carga](../../../load-balancer/load-balancer-overview.md). O balanceador de carga contém o endereço IP do ouvinte do grupo de disponibilidade. Se você tiver mais de um grupo de disponibilidade cada grupo exige um ouvinte. Um balanceador de carga pode dar suporte a vários ouvintes.

Quando estiver pronto para criar um grupo de disponibilidade do SQL Server em máquinas virtuais do Azure, consulte estes tutoriais.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Criar automaticamente um grupo de disponibilidade de um modelo

[Configurar automaticamente o grupo de disponibilidade Always On na VM do Azure - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Criar manualmente um grupo de disponibilidade no portal do Azure

Você também pode criar as máquinas virtuais sem o modelo. Primeiro, complete os pré-requisitos e criar o grupo de disponibilidade. Confira os seguintes tópicos: 

- [Configurar os pré-requisitos para grupos de disponibilidade do SQL Server Always On nas máquinas virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Criar Grupo de Disponibilidade Always On para melhorar a disponibilidade e a recuperação de desastre](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Próximas etapas

[Configurar um SQL Server sempre no grupo de disponibilidade em máquinas virtuais do Azure em diferentes regiões](virtual-machines-windows-portal-sql-availability-group-dr.md).
