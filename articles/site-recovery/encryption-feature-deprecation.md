---
title: Substituição de Azure Site Recovery recurso de criptografia de dados | Microsoft Docs
description: Detalhes regarig Azure Site Recovery recurso de criptografia de dados
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: sharrai
ms.openlocfilehash: 5860928d71c0e7431190908d5df5d7496e8ffb17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426309"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Substituição do recurso de criptografia de dados Site Recovery

Este documento descreve os detalhes de substituição e a ação de correção que você precisa tomar se estiver usando o Site Recovery recurso de criptografia de dados ao configurar a recuperação de desastre de máquinas virtuais do Hyper-V para o Azure. 

## <a name="deprecation-information"></a>Informações de substituição


O recurso de criptografia de dados Site Recovery estava disponível para clientes que protegem VMs do Hyper-V para garantir que os dados replicados foram protegidos contra ameaças à segurança. Esse recurso será preterido em **30 de abril de 2022**. Ele está sendo substituído pelo recurso de criptografia mais avançada [em repouso](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) , que usa [criptografia do serviço de armazenamento](../storage/common/storage-service-encryption.md) (SSE). Com a SSE, os dados são criptografados antes de persistirem no armazenamento e descriptografados na recuperação e, após o failover para o Azure, suas VMs serão executadas a partir das contas de armazenamento criptografadas, permitindo um RTO (objetivo de tempo de recuperação) aprimorado.

Observe que, se você for um cliente existente usando esse recurso, terá recebido comunicações com os detalhes de substituição e as etapas de correção. 


## <a name="what-are-the-implications"></a>Quais são as implicações?

Após **30 de abril de 2022**, todas as VMs que ainda usam o recurso de criptografia desativado não terão permissão para executar o failover. 

## <a name="required-action"></a>Ação necessária
Para continuar as operações de failover bem-sucedidas e as replicações, siga as etapas mencionadas abaixo:

Siga estas etapas para cada VM: 
1.  [Desabilite a replicação](./site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Crie uma nova política de replicação](./hyper-v-azure-tutorial.md#set-up-a-replication-policy).
3.  [Habilite a replicação](./hyper-v-vmm-azure-tutorial.md#enable-replication) e selecione uma conta de armazenamento com a SSE habilitada.

Depois de concluir a replicação inicial para contas de armazenamento com a SSE habilitada, suas VMs usarão a criptografia em repouso com Azure Site Recovery.


## <a name="next-steps"></a>Próximas etapas
Planeje a execução das etapas de correção e execute-as mais cedo. Caso você tenha alguma consulta relativa a essa reprovação, entre em contato com Suporte da Microsoft. Para ler mais sobre o cenário do Hyper-V para o Azure, consulte [aqui](hyper-v-vmm-architecture.md).
