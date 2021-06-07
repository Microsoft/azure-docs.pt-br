---
title: Novidades da Automação do Azure
description: Atualizações mensais significativas na Automação do Azure.
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: 899249c98c3ce0fdf061b1e689182f71c120aa13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729379"
---
# <a name="whats-new-in-azure-automation"></a>Novidades da Automação do Azure

A Automação do Azure recebe aprimoramentos continuamente. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug

Esta página é atualizada mensalmente; portanto, visite-a regularmente.

## <a name="february-2021"></a>Fevereiro de 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Suporte para GA da Automação e do State Configuration no Oeste do Japão

**Tipo:** novo recurso

Disponibilidade do State Configuration e da conta de Automação na região Oeste do Japão. Para obter mais informações, leia o [comunicado](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/).

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Introdução da conformidade personalizada do Azure Policy para aplicar a execução do runbook no Hybrid Worker

**Tipo:** novo recurso

Você pode usar a nova regra de conformidade do Azure Policy para permitir a criação de trabalhos, WebHooks e agendas de trabalho para serem executados somente nos grupos do Hybrid Worker.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Disponibilidade de Gerenciamento de Atualizações nas regiões Leste dos EUA, França Central e Norte da Europa

**Tipo:** novo recurso

O recurso de Gerenciamento de Atualizações da Automação está disponível nas regiões Leste dos EUA, França Central e Norte da Europa. Confira [Mapeamento de regiões compatível](how-to/region-mappings.md) para obter atualizações à documentação que reflitam essa alteração.

## <a name="january-2021"></a>Janeiro de 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>Suporte para GA da Automação e do State Configuration no Oeste da Suíça

**Tipo:** novo recurso

Disponibilidade do State Configuration e da conta de Automação na região Oeste da Suíça. Para obter mais informações, leia o [comunicado](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/).

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Adição do script Python 3 para importar o módulo com várias dependências

**Tipo:** novo recurso

O script está disponível para download em nosso [repositório GitHub](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py). 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Suporte à função Hybrid Runbook Worker para CentOS 8.x/RHEL 8.x/SLES 15

**Tipo.** New recurso

O recurso Hybrid Runbook Worker dá suporte a distribuições do CentOS 8.x, REHL 8.x e SLES 15 somente para a automação de processos nos Hybrid Runbook Workers.  Confira [Sistemas operacionais compatíveis](automation-linux-hrw-install.md#supported-linux-operating-systems) para obter atualizações à documentação a fim de refletir essas alterações.

### <a name="update-management--change-tracking-availability-in-australia-east-east-asia-west-us--central-us-regions"></a>Disponibilidade do Gerenciamento de Atualizações e do Controle de Alterações nas regiões Leste da Austrália, Leste da Ásia, Oeste dos EUA e EUA Central

**Tipo:** novo recurso

A conta de automação, o Controle de Alterações e Inventário e o Gerenciamento de Atualizações estão disponíveis nas regiões Leste da Austrália, Leste da Ásia, Oeste dos EUA e EUA Central. 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>Introdução da versão prévia pública dos runbooks do Python 3 na nuvem do Governo dos EUA

**Tipo:** o novo recurso Automação do Azure apresenta suporte à versão prévia pública da nuvem do Python 3 e da execução de Hybrid Runbook nas regiões de nuvem do Governo dos EUA.  Para obter mais informações, confira o [comunicado](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/).

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Migração dos runbooks de Automação do Azure do TechNet Script Center para o GitHub

**Tipo:** plano de alteração

O TechNet Script Center está sendo desativado, e todos os runbooks hospedados na Galeria de runbooks foram migrados para a nossa [organização de Automação no GitHub](https://github.com/azureautomation). Para obter mais informações, leia [Migração dos runbooks de Automação do Azure para o GitHub](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="december-2020"></a>Dezembro de 2020

### <a name="azure-automation-and-update-management-private-link-ga"></a>GA do Link Privado na Automação do Azure e no Gerenciamento de Atualizações

**Tipo:** novo recurso

Comunicado do suporte da Automação do Azure e do Gerenciamento de Atualizações como GA para nuvens globais e governamentais do Azure. A Automação do Azure habilitou o suporte do Link Privado para proteger a execução de um runbook em uma função de Hybrid Worker, usar o Gerenciamento de Atualizações para aplicar patches a computadores, invocar um runbook por meio de um webhook e usar o serviço State Configuration para manter a conformidade dos computadores. Para obter mais informações, leia [Suporte do Link Privado na Automação do Azure](https://azure.microsoft.com/updates/azure-automation-private-link)

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Automação do Azure classificada com Nível C em Acessibilidade

**Tipo:** novo recurso

Os recursos de acessibilidade dos produtos da Microsoft ajudam as agências a atender aos requisitos de acessibilidade globais. Na página do [comunicado no blog](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/), pesquise **Automação do Azure** para ler o relatório de conformidade de Acessibilidade para o serviço de Automação.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Suporte para GA da Automação e do State Configuration no Norte dos EAU

**Tipo:** novo recurso

Disponibilidade do State Configuration e da conta de Automação na região Norte dos EAU. Para obter mais informações, leia o [comunicado](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Suporte para GA da Automação e do State Configuration no Centro-Oeste da Alemanha

**Tipo:** novo recurso

Disponibilidade do State Configuration e da conta de Automação na região Oeste da Alemanha. Para obter mais informações, leia o [comunicado](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/).

### <a name="dsc-support-for-oracle-6-and-7"></a>Suporte do DSC para Oracle 6 e 7

**Tipo:** novo recurso

Gerencie computadores Oracle Linux 6 e 7 com o State Configuration da Automação. Confira [Distribuições do Linux compatíveis](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) para obter atualizações à documentação a fim de refletir essas alterações.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Versão prévia pública dos runbooks do Python 3 na Automação

**Tipo:** novo recurso

A Automação do Azure agora dá suporte à execução de runbook híbrido e de nuvem do Python 3 na versão prévia pública em todas as regiões na nuvem global do Azure. Confira o [comunicado]((https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) para obter mais detalhes.

## <a name="november-2020"></a>Novembro de 2020

### <a name="dsc-support-for-ubuntu-1804"></a>Suporte do DSC para Ubuntu 18.04

**Tipo:** novo recurso

Confira [Distribuições do Linux compatíveis](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) para obter atualizações à documentação que refletem essas alterações.

## <a name="october-2020"></a>Outubro de 2020

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Suporte para GA da Automação e do State Configuration no Norte da Suíça

**Tipo:** novo recurso

Disponibilidade do State Configuration e da conta de Automação no Norte da Suíça. Para obter mais informações, leia o [comunicado](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Suporte para GA da Automação e do State Configuration no Sudeste do Brasil

**Tipo:** novo recurso

Disponibilidade do State Configuration e da conta de Automação na região Sudeste do Brasil. Para obter mais informações, leia o [comunicado](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/).

### <a name="update-management-availability-in-south-central-us"></a>Disponibilidade do Gerenciamento de Atualizações no Centro-Sul dos EUA

**Tipo:** novo recurso

Atualização do mapeamento de regiões da Automação do Azure para dar suporte ao recurso Gerenciamento de Atualizações na região Centro-Sul dos EUA. Confira [Mapeamento de regiões compatível](how-to/region-mappings.md#supported-mappings) para obter atualizações à documentação a fim de refletir essa alteração.

## <a name="september-2020"></a>Setembro de 2020

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>Atualização dos runbooks da solução Iniciar/Parar VMs fora do horário comercial para usar os módulos Az do Azure

**Tipo:** novo recurso

Atualização dos runbooks da solução Iniciar/Parar VMs para usar os módulos Az no lugar dos módulos do Azure Resource Manager. Confira a visão geral de [Iniciar/Parar VMs fora do horário comercial](automation-solution-vm-management.md) para obter atualizações à documentação a fim de refletir essas alterações.

## <a name="august-2020"></a>Agosto de 2020

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Publicação da extensão do DSC para dar suporte ao Azure Arc

**Tipo:** novo recurso

Use o State Configuration da Automação do Azure para armazenar as configurações centralmente e manter o estado desejado de computadores conectados híbridos habilitados por meio da extensão de VM do DSC dos servidores habilitados para Azure Arc. Para obter mais informações, leia a [visão geral das extensões de VM dos servidores habilitados para Arc](../azure-arc/servers/manage-vm-extensions.md).

### <a name="july-2020"></a>Julho de 2020

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Introdução da versão prévia pública do suporte do Link Privado na Automação

**Tipo:** novo recurso

Use o Link Privado do Azure para conectar redes virtuais com segurança à Automação do Azure usando pontos de extremidade privados. Para obter mais informações, leia o [comunicado](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/).

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Suporte do Hybrid Runbook Worker para o Windows Server 2008 R2

**Tipo:** novo recurso

O Hybrid Runbook Worker de Automação dá suporte ao sistema operacional Windows Server 2008 R2. Confira [Sistemas operacionais compatíveis](automation-windows-hrw-install.md#supported-windows-operating-system) para obter atualizações à documentação a fim de refletir essas alterações.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Suporte do Gerenciamento de Atualizações para o Windows Server 2008 R2

**Tipo:** novo recurso

O Gerenciamento de Atualizações dá suporte à avaliação e à aplicação de patch no sistema operacional Windows Server 2008 R2. Confira [Sistemas operacionais compatíveis](update-management/overview.md#clients) para obter atualizações à documentação a fim de refletir essas alterações.

### <a name="automation-diagnostic-logs-schema-update"></a>Atualização do esquema dos logs de diagnóstico da Automação

**Tipo:** novo recurso

Alteração do esquema dos dados de log da Automação do Azure no serviço Log Analytics. Para saber mais, confira [Encaminhar os dados de trabalho da Automação do Azure para os logs do Azure Monitor](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object).

### <a name="azure-lighthouse-supports-automation-update-management"></a>O Azure Lighthouse dá suporte ao Gerenciamento de Atualizações da Automação

**Tipo:** novo recurso

O Azure Lighthouse permite o gerenciamento de recursos delegado com o Gerenciamento de Atualizações para clientes e provedores de serviços. Leia mais [aqui](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/).

## <a name="june-2020"></a>Junho de 2020

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Disponibilidade da Automação e do Gerenciamento de Atualizações na região US Gov – Arizona

**Tipo:** novo recurso

A conta de Automação e o Gerenciamento de Atualizações estão disponíveis no US Gov – Arizona. Para obter mais informações, confira o [comunicado](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/).

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Atualização do script de integração do Hybrid Runbook Worker para usar os módulos Az

**Tipo:** novo recurso

O runbook New-OnPremiseHybridWorker foi atualizado para dar suporte aos módulos Az. Para obter mais informações, confira o pacote na [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7).

### <a name="update-management-availability-in-china-east-2"></a>Disponibilidade do Gerenciamento de Atualizações no Leste da China 2

**Tipo:** novo recurso

Atualização do mapeamento de regiões da Automação do Azure para dar suporte ao recurso Gerenciamento de Atualizações na região Leste da China 2. Confira [Mapeamento de regiões compatível](how-to/region-mappings.md#supported-mappings) para obter atualizações à documentação a fim de refletir essa alteração.

## <a name="may-2020"></a>Maio de 2020

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>Atualização dos registros DNS do serviço de Automação de URLs específicas da região para URLs específicas da conta de Automação

**Tipo:** novo recurso

Os registros DNS da Automação do Azure foram atualizados para dar suporte a Links Privados. Para obter mais informações, leia o [comunicado](https://azure.microsoft.com/updates/azure-automation-updateddns-records/).

### <a name="added-capability-to-keep-automation-runbooks--dsc-scripts-encrypted-by-default"></a>Adição da capacidade para manter os scripts do DSC e os runbooks de Automação criptografados por padrão

**Tipo:** novo recurso

Além de aprimorar a segurança dos ativos, os runbooks e os scripts do DSC também são criptografados para aprimorar a segurança da Automação do Azure.

## <a name="april-2020"></a>Abril de 2020

### <a name="retirement-of-the-automation-watcher-task"></a>Desativação da tarefa do Observador de Automação

**Tipo:** plano de alteração

Os Aplicativos Lógicos do Azure já são a maneira recomendada e compatível para monitorar eventos, agendar tarefas recorrentes e disparar ações. Não haverá mais investimentos na funcionalidade da tarefa do Observador. Para saber mais, confira [Agendar e executar tarefas automatizadas recorrentes com Aplicativos Lógicos](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

## <a name="march-2020"></a>Março de 2020

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Suporte para isolamento de computação IL5 (Impacto Nível 5) na nuvem comercial e governamental do Azure

**Tipo:**

O Hybrid Runbook Worker de Automação do Azure pode ser usado no Azure Government para dar suporte a cargas de trabalho de Impacto Nível 5. Para saber mais, confira nossa [documentação](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5).

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Introdução ao suporte para marcas de serviço de rede virtual do Azure

**Tipo:** novo recurso

O suporte da Automação de marcas de serviço permite ou nega o tráfego para o serviço de Automação em um subconjunto de cenários. Para saber mais, confira a [documentação](automation-hybrid-runbook-worker.md#service-tags).

### <a name="enable-tls-12-support-for-azure-automation-service"></a>Habilitar o suporte ao TLS 1.2 para o serviço de Automação do Azure

**Tipo:** plano de alteração

A Automação do Azure dá suporte completo ao TLS 1.2 e a todas as chamadas de cliente (por meio de webhooks, nós DSC e Hybrid Worker). Ainda há suporte para o TLS 1.1 e 1.0 na compatibilidade com versões anteriores em clientes mais antigos até que os clientes padronizem e migrem totalmente para o TLS 1.2.

## <a name="january-2020"></a>Janeiro de 2020

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Introdução da versão prévia pública de chaves gerenciadas pelo cliente para a Automação do Azure

**Tipo:** novo recurso

Os clientes podem gerenciar e proteger a criptografia de ativos da Automação do Azure usando chaves gerenciadas próprias. Para obter mais informações, confira [Uso de chaves gerenciadas pelo cliente](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account).

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Desativação das APIs REST do ASM (Gerenciamento de Serviços do Azure) para a Automação do Azure

**Tipo:** Desativação

As APIs REST do ASM (Gerenciamento de Serviços do Azure) para a Automação do Azure serão desativadas e não terão mais suporte após 30 de janeiro de 2020. Para saber mais, confira o [comunicado](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/).

## <a name="next-steps"></a>Próximas etapas

Caso deseje enviar sua contribuição para a documentação da Automação do Azure, confira o [Guia do Colaborador do Docs](/contribute/).
