---
title: Experiências de gerenciamento entre locatários
description: O gerenciamento de recursos delegados do Azure permite uma experiência de gerenciamento entre locatários.
ms.date: 03/23/2021
ms.topic: conceptual
ms.openlocfilehash: a774eb08989e8fc45a117d83d81a5b510e8202b4
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559903"
---
# <a name="cross-tenant-management-experiences"></a>Experiências de gerenciamento entre locatários

Como um provedor de serviços, você pode usar o [Azure Lighthouse](../overview.md) para gerenciar recursos para vários clientes de dentro de seu próprio locatário Azure Active Directory (Azure AD). Muitas tarefas e serviços podem ser executados em locatários gerenciados usando o [Gerenciamento de recursos delegado do Azure](../concepts/azure-delegated-resource-management.md).

> [!TIP]
> O gerenciamento de recursos delegados do Azure também pode ser usado [dentro de uma empresa que tem vários locatários do Azure AD próprios](enterprise.md) para simplificar a administração entre locatários.

## <a name="understanding-tenants-and-delegation"></a>Entendendo locatários e delegação

Um locatário do Azure AD é uma representação de uma organização. É uma instância dedicada do Azure AD que uma organização recebe quando cria uma relação com a Microsoft inscrevendo-se no Azure, no Microsoft 365 ou em outros serviços. Cada locatário do Azure AD é diferente e separado de outros locatários do Azure AD e tem sua própria ID de locatário (um GUID). Para saber mais, confira [O que é Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

Normalmente, para gerenciar recursos do Azure para um cliente, os provedores de serviço teriam que entrar no portal do Azure usando uma conta associada ao locatário desse cliente, que requer um administrador no locatário do cliente para criar e gerenciar contas de usuário para o provedor de serviços.

Com o Azure Lighthouse, o processo de integração especifica os usuários no locatário do provedor de serviços que poderão trabalhar em assinaturas delegadas e grupos de recursos no locatário do cliente. Esses usuários podem entrar no portal do Azure usando suas próprias credenciais. Dentro do portal do Azure, eles podem gerenciar recursos que pertencem a todos os clientes aos quais eles têm acesso. Isso pode ser feito acessando a página [Meus clientes](../how-to/view-manage-customers.md) no portal do Azure ou trabalhando diretamente no contexto da assinatura do cliente, seja no portal do Azure ou por meio de APIs.

O Azure Lighthouse permite maior flexibilidade para gerenciar recursos para vários clientes sem precisar entrar em contas diferentes em locatários diferentes. Por exemplo, um provedor de serviços pode ter dois clientes com responsabilidades e níveis de acesso diferentes. Usando o Azure Lighthouse, os usuários autorizados podem entrar no locatário do provedor de serviços para acessar esses recursos.

![Diagrama mostrando os recursos do cliente gerenciados por meio de um locatário do provedor de serviços.](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Suporte às ferramentas de gerenciamento e APIs

Você pode executar tarefas de gerenciamento em recursos delegados diretamente no portal ou usando APIs e ferramentas de gerenciamento (como a CLI do Azure e o Azure PowerShell). Todas as APIs existentes podem ser usadas ao trabalhar com recursos delegados, desde que a funcionalidade seja compatível com o gerenciamento entre diferentes locatários e que o usuário tenha as permissões apropriadas.

O [cmdlet Azure PowerShell Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) mostrará o `TenantId` para o locatário de gerenciamento por padrão. Você pode usar os `HomeTenantId` `ManagedByTenantIds` atributos e para cada assinatura, permitindo que você identifique se uma assinatura retornada pertence a um locatário gerenciado ou ao seu locatário de gerenciamento.

Da mesma forma, CLI do Azure comandos como a [lista de contas AZ](/cli/azure/account#az-account-list) mostram os `homeTenantId` `managedByTenants` atributos e. Se você não vir esses valores ao usar a CLI do Azure, tente limpar o cache executando `az account clear` seguido por `az login --identity`.

Na API REST do Azure, os comandos [Subscription-Get](/rest/api/resources/subscriptions/get) e [subscriptions-List](/rest/api/resources/subscriptions/list) incluem `ManagedByTenant` .

> [!NOTE]
> Além das informações de locatário relacionadas ao Lighthouse do Azure, os locatários mostrados por essas APIs também podem refletir locatários de parceiros para Azure Databricks ou aplicativos gerenciados do Azure.

Também fornecemos APIs que são específicas para executar tarefas do Azure Lighthouse. Para saber mais, confira a seção **Referência**.

## <a name="enhanced-services-and-scenarios"></a>Serviços e cenários com aprimorados

A maioria das tarefas e serviços pode ser realizada em recursos delegados em locatários gerenciados. Abaixo estão alguns dos principais cenários em que o gerenciamento entre locatários pode ser especialmente eficaz.

[Arco do Azure](../../azure-arc/index.yml):

- Gerenciar servidores híbridos em escala – [servidores habilitados para Arc do Azure](../../azure-arc/servers/overview.md):
  - [Gerenciar computadores Windows Server ou Linux fora do Azure que estão conectados](../../azure-arc/servers/onboard-portal.md) a assinaturas delegadas e/ou grupos de recursos no Azure
  - Gerenciar computadores conectados usando constructos do Azure, como o Azure Policy e a marcação
  - Garantir que o mesmo conjunto de políticas seja aplicado nos ambientes híbridos dos clientes
  - Usar a central de segurança do Azure para monitorar a conformidade entre os ambientes híbridos dos clientes
- Gerenciar clusters kubernetes híbridos em escala – [kubernetes habilitado para Arc do Azure (versão prévia)](../../azure-arc/kubernetes/overview.md):
  - [Gerenciar clusters kubernetes que estão conectados](../../azure-arc/kubernetes/quickstart-connect-cluster.md) a assinaturas delegadas e/ou grupos de recursos no Azure
  - [Usar o GitOps](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md) para clusters conectados
  - Impor políticas entre clusters conectados

[Automação do Azure](../../automation/index.yml):

- Usar contas de automação para acessar e trabalhar com recursos delegados

[Backup do Azure](../../backup/index.yml):

- Fazer backup e restaurar dados [do cliente de cargas de trabalho locais, VMS do Azure, compartilhamentos de arquivos do Azure e muito mais](../..//backup/backup-overview.md#what-can-i-back-up)
- Exibir dados de todos os recursos de cliente delegados no [centro de backup](../../backup/backup-center-overview.md)
- Use o [Backup Explorer](../../backup/monitor-azure-backup-with-backup-explorer.md) para ajudar a ver informações operacionais de itens de backup (incluindo recursos do Azure ainda não configurados para backup) e informações de monitoramento (trabalhos e alertas) para assinaturas delegadas. O Backup Explorer está disponível no momento apenas para dados de VM do Azure.
- Use [Relatórios de Backup](../../backup/configure-reports.md) entre assinaturas delegadas para acompanhar tendências históricas, analisar o consumo de armazenamento de backup e auditar backups e restaurações.

[Plantas do Azure](../../governance/blueprints/index.yml):

- Use plantas do Azure para orquestrar a implantação de modelos de recursos e outros artefatos (requer [acesso adicional](https://www.wesleyhaakman.org/preparing-azure-lighthouse-customer-subscriptions-for-azure-blueprints/) para preparar a assinatura do cliente)

[Gerenciamento de custos do Azure + cobrança](../../cost-management-billing/index.yml):

- Do locatário de gerenciamento, os parceiros CSP podem exibir, gerenciar e analisar os custos de consumo de imposto prévio (não inclusivo de compras) para clientes que estão sob o plano do Azure. O custo será baseado em taxas de varejo e no acesso do Azure RBAC (controle de acesso baseado em função) que o parceiro tem para a assinatura do cliente.

[Azure Key Vault](../../key-vault/general/index.yml):

- Criar cofres de chaves em locatários do cliente
- Usar uma identidade gerenciada para criar cofres de chaves em locatários do cliente

[AKS (Serviço de Kubernetes do Azure)](../../aks/index.yml):

- Gerencie ambientes Kubernetes hospedados e implante e gerencie aplicativos em contêineres dentro de locatários do cliente
- Implantar e gerenciar clusters em locatários do cliente
-   Usar Azure Monitor para contêineres para monitorar o desempenho entre locatários do cliente

[Migrações para Azure](../../migrate/index.yml):

- Criar projetos de migração no locatário do cliente e migrar VMs

[Azure Monitor](../../azure-monitor/index.yml):

- Exibir alertas para assinaturas delegadas, com a capacidade de exibir e atualizar alertas em todas as assinaturas
- Exibir detalhes do log de atividades para assinaturas delegadas
- [Log Analytics](../../azure-monitor/logs/service-providers.md): consultar dados de espaços de trabalho remotos em vários locatários (Observe que as contas de automação usadas para acessar dados de espaços de trabalho em locatários do cliente devem ser criadas no mesmo locatário)
- [Criar, exibir e gerenciar alertas do log de atividades](../../azure-monitor/alerts/alerts-activity-log.md) em locatários do cliente
- Criar alertas em locatários do cliente que disparam a automação, como runbooks de automação do Azure ou Azure Functions, no gerenciamento de locatário por meio de WebHooks
- Criar [configurações de diagnóstico](../..//azure-monitor/essentials/diagnostic-settings.md) em locatários do cliente para enviar logs de recursos para espaços de trabalho no locatário de gerenciamento
- Para cargas de trabalho do SAP, [monitore as métricas de soluções SAP com uma exibição agregada entre locatários do cliente](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293)

[Rede do Azure](../../networking/networking-overview.md):

- Implantar e gerenciar a [rede virtual do Azure](../../virtual-network/index.yml) e vNICs (placas de interface de rede virtual) em locatários gerenciados
- Implantar e configurar [Firewall do Azure](../../firewall/overview.md) para proteger os recursos de Rede Virtual dos clientes
- Gerenciar serviços de conectividade, como [Wan virtual do Azure](../../virtual-wan/virtual-wan-about.md), [ExpressRoute](../../expressroute/expressroute-introduction.md)e [gateways de VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
- Use o Azure Lighthouse para dar suporte a principais cenários para o [Programa MSP de Rede do Azure](../../networking/networking-partners-msp.md)

[Azure Policy](../../governance/policy/index.yml):

- Criar e Editar definições de política em assinaturas delegadas
- Implantar definições de política e atribuições de política em vários locatários
- Atribuir definições de política definidas pelo cliente em assinaturas delegadas
- Os clientes veem políticas criadas pelo provedor de serviços junto com políticas que eles mesmos criaram
- Pode [corrigir deployIfNotExists ou modificar atribuições dentro do locatário gerenciado](../how-to/deploy-policy-remediation.md)
- Observe que não há suporte atualmente para a exibição de detalhes de conformidade para recursos sem conformidade em locatários do cliente

[Azure Resource Graph](../../governance/resource-graph/index.yml):

- Agora inclui a ID do locatário nos resultados da consulta retornados, permitindo que você identifique se uma assinatura pertence a um locatário gerenciado

[Central de Segurança do Azure](../../security-center/index.yml):

- Visibilidade entre locatários
  - Monitore a conformidade para políticas de segurança e verifique a cobertura de segurança entre os recursos de todos os locatários
  - Monitoramento contínuo da conformidade regulatória entre vários locatários em uma única exibição
  - Monitore, faça triagem e priorize recomendações de segurança acionáveis com o cálculo de classificação de segurança
- Gerenciamento de postura de segurança entre locatários
  - Gerenciar políticas de segurança
  - Execute ações em recursos que estão fora de conformidade com recomendações de segurança acionáveis
  - Colete e armazene dados relacionados à segurança
- Detecção e proteção contra ameaças entre locatários
  - Detectar ameaças entre recursos de locatários
  - Aplique controles de proteção avançada contra ameaças como acesso à VM JIT (Just-in-time)
  - Proteja configuração do grupo de segurança de rede com o Fortalecimento de Rede Adaptável
  - Verifique se os servidores estão executando apenas os aplicativos e processos que eles devem estar com controles de aplicativo adaptáveis
  - Monitore alterações em arquivos importantes e entradas do Registro com o FIM (Monitoramento de Integridade do Arquivo)
- Observe que toda a assinatura deve ser delegada ao locatário de gerenciamento; Não há suporte para cenários da central de segurança do Azure com grupos de recursos delegados

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Gerenciar recursos do Azure Sentinel [em locatários do cliente](../../sentinel/multiple-tenants-service-providers.md)
- [Acompanhar ataques e exibir alertas de segurança em vários locatários](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Exibir incidentes](../../sentinel/multiple-workspace-view.md) em vários espaços de trabalho do Azure Sentinel distribuídos entre locatários

[Integridade do Serviço do Azure](../../service-health/index.yml):

- Monitore a integridade dos recursos do cliente com o Azure Resource Health
- Acompanhe a integridade dos serviços do Azure usados por seus clientes

[Azure Site Recovery](../../site-recovery/index.yml):

- Gerenciar opções de recuperação de desastre para máquinas virtuais do Azure em locatários do cliente (Observe que você não pode usar `RunAs` contas para copiar extensões de VM)

[Máquinas Virtuais do Azure](../../virtual-machines/index.yml):

- Usar extensões de máquina virtual para fornecer tarefas de configuração e automação de pós-implantação em VMs do Azure
- Usar o diagnóstico de inicialização para solucionar problemas de VMs do Azure
- Acessar VMs com o console serial
- Integre VMs com Azure Key Vault para senhas, segredos ou chaves de criptografia para criptografia de disco usando [identidade gerenciada por meio de política](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret), garantindo que os segredos sejam armazenados em um Key Vault nos locatários gerenciados
- Observe que você não pode usar Azure Active Directory para logon remoto em VMs

Solicitações de suporte:

- [Abrir solicitações de suporte de **ajuda + suporte**](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) no portal do Azure para recursos delegados (selecionando o plano de suporte disponível para o escopo delegado)
- Usar a [API de cota do Azure](/rest/api/reserved-vm-instances/quotaapi) para exibir e gerenciar cotas de serviço do Azure para recursos de cliente delegados

## <a name="current-limitations"></a>Limitações atuais

Com todos os cenários, esteja ciente das seguintes limitações atuais:

- As solicitações manipuladas pelo Azure Resource Manager podem ser executadas usando o Azure Lighthouse. Os URIs de operação para essas solicitações começam com `https://management.azure.com`. No entanto, as solicitações que são manipuladas por uma instância de um tipo de recurso (como Key Vault acesso aos segredos ou acesso a dados de armazenamento) não têm suporte com o Azure Lighthouse. Os URIs de operação para essas solicitações normalmente começam com um endereço exclusivo de sua instância, como `https://myaccount.blob.core.windows.net` ou `https://mykeyvault.vault.azure.net/`. A última opção também são normalmente operações de dados em vez de operações de gerenciamento.
- As atribuições de função devem usar [funções internas do Azure](../../role-based-access-control/built-in-roles.md). Atualmente, todas as funções internas têm suporte com o gerenciamento de recursos delegado do Azure, exceto o proprietário ou qualquer função interna com [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) permissão. A função de Administrador de Acesso do Usuário tem suporte apenas para uso limitado na [atribuição de funções a identidades gerenciadas](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  As funções personalizadas e as [funções de administrador de assinatura clássica](../../role-based-access-control/classic-administrators.md) não têm suporte.
- Embora você possa integrar assinaturas que usam o Azure Databricks, os usuários no locatário de gerenciamento não podem iniciar os workspaces do Azure Databricks em uma assinatura delegada no momento.
- Embora você possa integrar assinaturas e grupos de recursos que têm bloqueios de recursos, esses bloqueios não impedirão que as ações sejam executadas por usuários no locatário de gerenciamento. As [atribuições de negação](../../role-based-access-control/deny-assignments.md) que protegem recursos gerenciados pelo sistema, como aqueles criados pelos Aplicativos Gerenciados do Azure ou pelo Azure Blueprints (atribuições de negação atribuídas ao sistema), impedem que os usuários do locatário de gerenciamento executem ações nesses recursos; no entanto, atualmente, os usuários do locatário do cliente não podem criar atribuições de negação próprias (atribuições de negação atribuídas ao usuário).
- Não há suporte para a delegação de assinaturas em uma [nuvem nacional](../../active-directory/develop/authentication-national-cloud.md) e na nuvem pública do Azure ou em duas nuvens nacionais separadas.

## <a name="next-steps"></a>Próximas etapas

- Integre seus clientes ao Azure Lighthouse, [usando modelos de Azure Resource Manager](../how-to/onboard-customer.md) ou [publicando uma oferta privada ou pública de serviços gerenciados para o Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Exiba e gerencie clientes](../how-to/view-manage-customers.md) acessando **Meus clientes** no portal do Azure.