---
title: Estender o suporte para SQL Server 2008 e SQL Server 2008 R2 com o Azure
description: Saiba como estender o suporte para SQL Server 2008 e SQL Server 2008 R2 por migrar sua instância do SQL Server para o Azure ou comprar o suporte estendido para manter instâncias locais.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b16fecd6db1f4bed319c832795a2252f8de96c6c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607131"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Estender o suporte para SQL Server 2008 e SQL Server 2008 R2 com o Azure

SQL Server 2008 e SQL Server 2008 R2 estão chegando a [final de seu ciclo de vida de suporte (EOS)](https://www.microsoft.com/sql-server/sql-server-2008). Já que muitos de nossos clientes ainda estiver usando as duas versões, estamos fornecendo várias opções para continuar recebendo suporte. Você pode migrar suas instâncias do SQL Server local para máquinas virtuais (VMs), migrar para o banco de dados SQL Azure, ou permanecem no local e as atualizações de segurança estendida de compra.

Ao contrário de com uma instância gerenciada, migrando para uma VM do Azure não requer recertifying seus aplicativos. E diferentemente com permanecer no local, você receberá os patches de segurança estendida livre migrando para uma VM do Azure.

O restante deste artigo fornece considerações para migrar sua instância do SQL Server para uma VM do Azure.

## <a name="provisioning"></a>Provisionamento

Há um pré-pago `SQL Server 2008 R2 on Windows Server 2008 R2` imagem disponível no Azure marketplace.

Os clientes que estão no SQL Server 2008 serão preciso instalar sozinho ou atualizar para o SQL Server 2008 R2. Da mesma forma, os clientes no Windows Server 2008 precisará implantar sua VM por meio de um VHD personalizado, ou atualizar para o Windows Server 2008 R2.

Imagens implantadas por meio do Marketplace são fornecidos com a extensão SQL IaaS pré-instalado. A extensão IaaS do SQL é um requisito para o licenciamento flexível e aplicação de patch automatizada. Os clientes que implantam VMs automaticamente instaladas precisará instalar manualmente a extensão SQL IaaS. Não há suporte para a extensão SQL IaaS no Windows 2008.

  > [!NOTE]
  > Embora o SQL Server `Create` e `Manage` folhas funcionará com a imagem do SQL Server 2008 R2 no portal do Azure, os seguintes recursos estão _não tem suporte_: Backups automáticos, integração do Azure Key Vault, os serviços de R e configuração de armazenamento.

## <a name="licensing"></a>Licenciamento
Implantações do pago conforme o uso do SQL Server 2008 R2 podem converter [benefício de híbrido do Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/).

Para converter uma licença do Software Assurance (SA) com base em pré-pago, os clientes devem se registrar com a VM do SQL [provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md). Depois de registrado com o provedor de recursos de VM do SQL, o tipo de licença do SQL será intercambiável entre AHB e pago pelo uso.

Instâncias de SQL Server 2008 ou SQL Server 2008 R2 automaticamente instaladas na VM do Azure podem registrar com o provedor de recursos do SQL e converter o tipo de licença para pré-pago.

## <a name="migration"></a>Migração
Você pode migrar instâncias do EOS SQL Server para uma VM do Azure com métodos de backup/restauração manual; Isso é o método mais comum de migração do local para uma VM do Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Para migrações em massa, é recomendável [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) service. Com o Azure Site Recovery, os clientes podem replicar a VM inteira, incluindo o SQL Server no local para uma VM do Azure.

O SQL Server exige instantâneos consistentes de aplicativos do Azure Site Recovery para garantir a recuperação; e o Azure Site Recovery dá suporte a instantâneos consistentes de aplicativos com o intervalo mínimo de 1 hora. O RPO mínimo possível para o SQL Server com as migrações do Azure Site Recovery é 1 hora e o RTO é de 2 horas mais o tempo de recuperação do SQL Server.

### <a name="database-migration-service"></a>Serviço de Migração de Banco de Dados

O [serviço de migração de banco de dados](/azure/dms/dms-overview) é uma opção para os clientes se migrar do local para a VM do Azure com a atualização do SQL Server para SQL Server 2012 e superior.

## <a name="disaster-recovery"></a>Recuperação de desastre

Soluções de recuperação de desastres para o EOS SQL Server em VM do Azure são da seguinte maneira:

- **Backups do SQL Server**: Use o Backup do Azure para proteger o SQL Server do EOS contra ransomware, exclusão acidental e corrupção. A solução está atualmente em visualização para EOS SQL Server e oferece suporte ao SQL Server 2008 e 2008 R2 em execução no Windows 2008 R2 SP1. Para obter mais detalhes, consulte [artigo](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#support-for-sql-server-2008-and-sql-server-2008-r2)
- **Envio de logs**: Você pode criar uma réplica de envio de log em outra zona ou região do Azure com restaurações contínuas para reduzir o RTO. Os clientes precisarão configurar manualmente o envio de logs.
- **Azure Site Recovery**: Você pode replicar a VM entre regiões e regiões por meio da replicação do Azure Site Recovery. SQL Server requer a instantâneos consistentes com o aplicativo garantir a recuperação em caso de desastre. O Azure Site Recovery oferece 2 horas + tempo de recuperação do SQL Server RTO e RPO de 1 hora mínimo para DR do SQL Server EOS.

## <a name="security-patching"></a>Aplicação de patch de segurança
Atualizações de segurança estendida para VMs do SQL Server serão entregue por meio dos canais Microsoft Update depois que a VM do SQL Server foi registrado com o SQL [provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md). Patches podem ser baixados manualmente ou automaticamente.

**Automated patching** está habilitada por padrão. A aplicação de patch automatizada permite que o Azure aplique patches automaticamente no SQL Server e no sistema operacional. Se a extensão IaaS do SQL é instalado, você pode especificar um dia da semana, hora e duração de uma janela de manutenção. O Azure realiza a aplicação de patch na janela de manutenção. O agendamento do período de manutenção usa a localidade da VM para a hora.  Para saber mais, consulte [Aplicação de Patch Automatizada para SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Próximas etapas

Migrar sua VM do SQL Server para o Azure

* [Migrar um banco de dados do SQL Server para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md)

Introdução ao SQL Server em Máquinas Virtuais do Azure:

* [Criar uma VM do SQL Server no portal do Azure](quickstart-sql-vm-create-portal.md)

Obtenha respostas para perguntas frequentes sobre máquinas virtuais do SQL:

* [Perguntas frequentes sobre o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-faq.md)
