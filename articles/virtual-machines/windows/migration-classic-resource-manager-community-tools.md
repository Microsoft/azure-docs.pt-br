---
title: Ferramentas da comunidade - Mover recursos clássicos para o Azure Resource Manager | Microsoft Docs
description: Este artigo cataloga as ferramentas que foram fornecidas pela comunidade para ajudar a migrar os recursos de IaaS do modelo de implantação clássico para o modelo de implantação do Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 63e1ad044204bf7695d274fa46f06523fd9d460f
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720270"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Ferramentas da comunidade para migrar recursos de IaaS do clássico para o Azure Resource Manager
Este artigo cataloga as ferramentas que foram fornecidas pela comunidade para auxiliar com a migração dos recursos de IaaS do modelo de implantação clássico para o modelo de implantação do Azure Resource Manager.

> [!NOTE]
> Não há suporte oficial para essas ferramentas no Suporte da Microsoft. Portanto, são software livre no GitHub e aceitamos PRs para correções ou cenários adicionais. Para relatar um problema, use o recurso de problemas do GitHub.
> 
> A migração com essas ferramentas causará tempo de inatividade de sua Máquina Virtual clássica. Se você estiver buscando uma migração da plataforma com suporte, visite 
> 
>   * [Platform supported migration of IaaS resources from Classic to Azure Resource Manager stack (Migração de recursos de IaaS com suporte da plataforma da pilha Clássica para o Azure Resource Manager)](migration-classic-resource-manager-overview.md)
>   * [Análise técnica aprofundada sobre a migração com suporte da plataforma do Clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando o Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Trata-se de uma coleção de ferramentas auxiliares criadas como parte de migrações empresariais do Gerenciamento de Serviços do Azure para o Azure Resource Manager. Essa ferramenta permite replicar sua infraestrutura para outra assinatura, o que pode ser usado para testar a migração e solucionar problemas antes de executar a migração em sua assinatura de produção.

[Link para a documentação da ferramenta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz é uma opção adicional para migrar um conjunto completo de recursos de IaaS clássicos para recursos de IaaS do Azure Resource Manager. A migração pode ocorrer dentro da mesma assinatura ou entre assinaturas diferentes e tipos de assinatura (ex: Assinaturas de CSP).

[Link para a documentação da ferramenta](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planejamento para a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Examinar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Confira as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

