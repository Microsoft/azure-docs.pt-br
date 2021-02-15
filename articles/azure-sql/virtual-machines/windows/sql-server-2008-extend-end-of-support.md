---
title: Estender o suporte para o SQL Server 2008 e 2008 R2
description: Estenda o suporte para SQL Server 2008 e SQL Server 2008 R2 migrando sua instância do SQL Server para o Azure ou comprando o suporte estendido para manter as instâncias como locais.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 4c25adc16d14b4a5fb72ae0103ca05b193b40499
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359159"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Estender o suporte para SQL Server 2008 e SQL Server 2008 R2 com o Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

O SQL Server 2008 e SQL Server 2008 R2 chegaram, nos respectivos ciclos de vida, ao [EOS (fim do suporte)](https://www.microsoft.com/sql-server/sql-server-2008). Já que muitos clientes ainda estão usando ambas as versões, estamos fornecendo várias opções para continuar a obter suporte. Você pode migrar suas instâncias do SQL Server locais para VMs (máquinas virtuais) do Azure, migrar para o Banco de Dados SQL do Azure ou manter-se no local e comprar atualizações de segurança estendidas.

Ao contrário de uma instância gerenciada, migrar para uma VM do Azure não exige uma nova certificação de seus aplicativos. E, ao contrário de ficar no local, você receberá patches de segurança estendidos gratuitos migrando para uma VM do Azure.

O restante deste artigo fornece considerações para migrar sua instância do SQL Server para uma VM do Azure.

Para obter mais informações sobre opções de fim do suporte, confira [Fim do suporte](/sql/sql-server/end-of-support/sql-server-end-of-life-overview).

## <a name="provisioning"></a>Provisionamento

Há uma imagem do **SQL Server 2008 R2 no Windows Server 2008 R2** pago conforme o uso do disponível no Azure Marketplace.

Os clientes que estiverem no SQL Server 2008 precisarão instalar ou atualizar para o SQL Server 2008 R2 por conta própria. Da mesma forma, cada um dos clientes do Windows Server 2008 precisará implantar a respectiva VM de um VHD personalizado ou atualizar para o Windows Server 2008 R2.

As imagens implantadas por meio do Azure Marketplace têm a extensão IaaS do SQL pré-instalada. A extensão IaaS do SQL é um requisito para licenciamento flexível e aplicação de patch automatizada. Os clientes que implantarem VMs autoinstaladas precisarão instalar manualmente a extensão IaaS do SQL. A extensão IaaS do SQL não é compatível com o Windows Server 2008.

> [!NOTE]
> Embora as folhas **Criar** e **Gerenciar** do SQL Server funcionem com a imagem do SQL Server 2008 R2 no portal do Azure, os seguintes recursos são _incompatíveis_: Backups automáticos, Integração do Azure Key Vault, Serviços de R e configuração de armazenamento.

## <a name="licensing"></a>Licenciamento
As implantações pagas conforme o uso do SQL Server 2008 R2 podem ser convertidas em [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Para converter uma licença baseada no Software Assurance (SA) para pré-paga, os clientes devem se registrar com a extensão do [SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md). Após esse registro, o tipo de licença do SQL será intercambiável entre Benefício Híbrido do Azure e paga conforme o uso.

As instâncias autoinstaladas do SQL Server 2008 ou SQL Server 2008 R2 em uma VM do Azure podem ser registradas com a extensão do agente IaaS do SQL e converter seu tipo de licença para pré-pago.

## <a name="migration"></a>Migração
Você pode migrar instâncias do SQL Server em EOS para uma VM do Azure com métodos manuais de backup/restauração. Esse é o método de migração mais comum do local para uma VM do Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Para migrações em massa, recomendamos o serviço [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md). Com o Azure Site Recovery, os clientes podem replicar a VM inteira, incluindo SQL Server, do local para a VM do Azure.

O SQL Server requer instantâneos de Azure Site Recovery consistentes com o aplicativo para garantir a recuperação. O Azure Site Recovery dá suporte a instantâneos consistentes com o aplicativo com um intervalo mínimo de uma hora. O RPO (Objetivo de Ponto de Recuperação) mínimo possível para SQL Server com migrações do Azure Site Recovery é de uma hora. O RTO (Objetivo de Tempo de Recuperação) é de duas horas mais o tempo de recuperação do SQL Server.

### <a name="database-migration-service"></a>Serviço de Migração de Banco de Dados

O [serviço de migração de banco de dados do Azure](../../../dms/dms-overview.md) é uma opção para clientes se eles estiverem migrando do local para uma VM do Azure atualizando SQL Server para a versão 2012 ou posterior.

## <a name="disaster-recovery"></a>Recuperação de desastre

As soluções de recuperação de desastre para SQL Server em EOS em uma VM do Azure são as seguintes:

- **Backups do SQL Server**: use o Backup do Azure para ajudar a proteger seu SQL Server 2008 e 2008 R2 em EOS contra ransomware, exclusão acidental e corrupção com RPO de 15 minutos e recuperação pontual. Para obter mais detalhes, confira [este artigo](../../../backup/sql-support-matrix.md#scenario-support).
- **Envio de logs**: você pode criar uma réplica de envio de logs em outra zona ou região do Azure com restaurações contínuas para reduzir o RTO. Você precisará configurar manualmente o envio de logs.
- **Azure Site Recovery**: você pode replicar sua VM entre zonas e regiões por meio da replicação do Azure Site Recovery. O SQL Server requer instantâneos de consistentes com o aplicativo para garantir a recuperação em caso de um desastre. O Azure Site Recovery oferece um RPO mínimo de uma hora e um RTO de duas horas (mais o tempo de recuperação do SQL Server) para a recuperação de desastre do SQL Server em EOS.

## <a name="security-patching"></a>Aplicação de patch de segurança

As atualizações de segurança estendidas para VMs SQL Server são entregues por meio dos canais de Microsoft Update após a SQL Server VM ter sido registrada com a [extensão do SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md). Os patches podem ser baixados manual ou automaticamente.

*Automated patching* está habilitada por padrão. A aplicação de patch automatizada permite que o Azure aplique patches automaticamente no SQL Server e no sistema operacional. Você poderá especificar um dia da semana, hora e duração para uma janela de manutenção se a extensão IaaS do SQL Server estiver instalada. O Azure realiza a aplicação de patch na janela de manutenção. O agendamento do período de manutenção usa a localidade da VM para a hora. Para saber mais, confira [Aplicação de patch automatizada para SQL Server nas Máquinas Virtuais do Azure](automated-patching.md).


## <a name="next-steps"></a>Próximas etapas

Migre sua VM do SQL Server para o Azure:

* [Migrar um banco de dados do SQL Server para o SQL Server em uma VM do Azure](migrate-to-vm-from-sql-server.md)

Introdução ao SQL Server em Máquinas Virtuais do Azure:

* [Criar uma VM do SQL Server no portal do Azure](sql-vm-create-portal-quickstart.md)

Obtenha respostas para perguntas frequentes sobre máquinas virtuais do SQL Server:

* [Perguntas frequentes sobre o SQL Server em Máquinas Virtuais do Azure](frequently-asked-questions-faq.md)

Saiba mais sobre as opções de fim do suporte e as atualizações de segurança estendidas:

* [Fim do suporte](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [Atualizações de Segurança Estendidas](/sql/sql-server/end-of-support/sql-server-extended-security-updates)