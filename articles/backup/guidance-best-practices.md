---
title: Diretrizes e melhores práticas
description: Descubra as práticas recomendadas e as diretrizes para fazer backup da carga de trabalho local e na nuvem para a nuvem
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 7b65556d8dd9b5b12e8da25055f6e39732c83afd
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258754"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Fazer backup de cargas de trabalho locais e na nuvem para a nuvem

## <a name="introduction"></a>Introdução

O backup do Azure protege de maneira abrangente seus ativos de dados no Azure por meio de uma solução simples, segura e econômica, que exige uma infraestrutura zero. É uma solução de proteção de dados interna Azure's para uma ampla gama de cargas de trabalho. Ele ajuda a proteger suas cargas de trabalho de missão crítica em execução na nuvem e garante que os backups estejam sempre disponíveis e gerenciados em escala em todo o seu espaço de backup.

### <a name="intended-audience"></a>Público-alvo

O público-alvo principal deste artigo é a ti e os administradores de aplicativos e implementadores de organizações de grande e médio porte, que desejam aprender sobre os recursos da tecnologia de proteção de dados interna do Azure, o backup do Azure e como implementar soluções com eficiência que melhoram a proteção de suas implantações. O artigo pressupõe que você esteja familiarizado com as principais tecnologias do Azure, conceitos de proteção de dados e tenha experiência de trabalho com uma solução de backup. As diretrizes abordadas neste artigo podem facilitar o design de sua solução de backup no Azure usando padrões estabelecidos e evitar armadilhas conhecidas.

### <a name="how-this-article-is-organized"></a>Como este artigo é organizado

Embora seja fácil começar a proteger a infraestrutura e os aplicativos no Azure, quando você garante que os recursos subjacentes do Azure sejam configurados corretamente e que sejam usados de forma ideal, você pode acelerar seu tempo de valor. Este artigo aborda uma breve visão geral das considerações de design e diretrizes para configurar de maneira ideal sua implantação de backup do Azure. Ele examina os componentes principais (por exemplo, cofre de serviços de recuperação, política de backup) e conceitos (por exemplo, governança) e como considerá-los e seus recursos com links para documentação detalhada do produto.

## <a name="architecture"></a>Arquitetura

![Arquitetura de Backup do Azure](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>Cargas de trabalho

O backup do Azure permite a proteção de dados para várias cargas de trabalho (local e nuvem). É um mecanismo de proteção de dados interno seguro e confiável no Azure. Ele pode dimensionar de maneira simples sua proteção em várias cargas de trabalho sem nenhuma sobrecarga de gerenciamento para você. Há vários canais de automação também para habilitar isso (por meio do PowerShell, da CLI, dos modelos de Azure Resource Manager e das APIs REST).

* **Armazenamento escalonável, durável e seguro-** O backup do Azure usa um armazenamento de blob confiável com recursos de segurança e alta disponibilidade criados. Você pode escolher armazenamentos LRS, GRS ou RA-GRS para os dados de backup.  

* **Integração de carga de trabalho nativa-** O backup do Azure fornece integração nativa com as cargas de trabalho do Azure (VMs, SAP HANA, SQL em VMs do Azure e até mesmo arquivos do Azure) sem exigir que você gerencie a automação ou a infraestrutura para implantar agentes, grave novos scripts ou provisione o armazenamento.

### <a name="data-plane"></a>Plano de dados

* **Gerenciamento de armazenamento automatizado** – o backup do Azure automatiza o provisionamento e o gerenciamento de contas de armazenamento para os dados de backup para garantir que ele seja dimensionado conforme o crescimento dos dados de backup.

* **Proteção contra exclusão mal-intencionada –** Proteja-se contra qualquer tentativa acidental e mal-intencionada de excluir seus backups por meio da exclusão reversível de backups. Os dados de backup excluídos são armazenados por 14 dias gratuitamente e permitem que ele seja recuperado a partir desse Estado.

* **Backups criptografados seguros-** O backup do Azure garante que os dados de backup sejam armazenados de maneira segura, aproveitando os recursos de segurança internos da plataforma Azure, como o RBAC do Azure e a criptografia.

* **Gerenciamento do ciclo de vida de dados de backup-** O backup do Azure limpa automaticamente os dados de backup mais antigos para atender às políticas de retenção. Você também pode hierarquizar seus dados do armazenamento operacional para o armazenamento de cofre.

### <a name="management-plane"></a>Plano de gerenciamento

* **Controle de acesso** – cofres (serviços de recuperação e cofres de backup) fornecem os recursos de gerenciamento e podem ser acessados por meio de portal do Azure, centro de backup, painéis de cofre, SDK, CLI e até mesmo APIs REST. Ele também é um limite do RBAC do Azure, fornecendo a opção de restringir o acesso a backups somente para administradores de backup autorizados.

* **Gerenciamento de políticas** – as políticas de backup do Azure em cada cofre definem quando os backups devem ser disparados e por quanto tempo eles precisam ser retidos. Você também pode gerenciar essas políticas e aplicá-las em vários itens.

* **Monitoramento e relatórios** – o backup do Azure integra-se ao log Analytics e fornece a capacidade de ver relatórios via pastas de trabalho também.

* **Gerenciamento de instantâneo** – o backup do Azure usa instantâneos para algumas cargas de trabalho nativas do Azure (VMS e arquivos do Azure), gerencia esses instantâneos e permite restaurações rápidas a partir deles. Essa opção reduz drasticamente o tempo de recuperação dos dados para o armazenamento original.

## <a name="vault-considerations"></a>Considerações sobre o cofre

O backup do Azure usa cofres (serviços de recuperação e cofres de backup) para orquestrar e gerenciar backups. Ele também usa cofres para armazenar dados de backup. O design de cofre efetivo ajuda as organizações a estabelecer uma estrutura para organizar e gerenciar ativos de backup no Azure para dar suporte às suas prioridades de negócios. Considere as seguintes diretrizes ao criar um cofre:  

### <a name="align-to-subscription-design-strategy"></a>Alinhar à estratégia de design da assinatura

Como o cofre tem como escopo uma assinatura, ajuste seu design de cofre para atender à estratégia de design da assinatura, como *estratégia de categoria de aplicativo* , em que as assinaturas são separadas com base em aplicativos ou serviços específicos ou nas linhas de arquétipos do aplicativo. Para obter mais informações, consulte este [artigo](/azure/cloud-adoption-framework/decision-guides/subscriptions/).

### <a name="single-or-multiple-vault"></a>Um ou vários cofres

Você pode usar um único cofre ou vários cofres para organizar e gerenciar o backup. Considere as seguintes diretrizes:

* Se suas cargas de trabalho forem todas gerenciadas por uma única assinatura e um único recurso, você poderá usar um único cofre para monitorar e gerenciar seu espaço de backup.

* Se suas cargas de trabalho forem distribuídas entre assinaturas, você poderá criar vários cofres, um ou mais por assinatura.
  * O centro de backup permite que você tenha um único painel de vidro para gerenciar todas as tarefas relacionadas ao backup. [Saiba mais aqui]().
  * Você pode personalizar suas exibições com modelos de pasta de trabalho. O Gerenciador de backup é um desses modelos para VMs do Azure. [Saiba mais aqui](monitor-azure-backup-with-backup-explorer.md).
  * Se você precisar de uma política consistente entre os cofres, poderá usar a política do Azure para propagar a política de backup em vários cofres. Você pode escrever uma [definição de Azure Policy](../governance/policy/concepts/definition-structure.md) personalizada que usa o efeito [' deployifnotexists '](../governance/policy/concepts/effects.md#deployifnotexists) para propagar uma política de backup em vários cofres. Você também pode [atribuir](../governance/policy/assign-policy-portal.md) essa definição de Azure Policy a um escopo específico (assinatura ou RG), para que ele implante um recurso de ' política de backup ' em todos os cofres dos serviços de recuperação no escopo da atribuição de Azure Policy. As configurações da política de backup (como frequência de backup, retenção e assim por diante) devem ser especificadas pelo usuário como parâmetros na atribuição de Azure Policy.

* À medida que sua superfície organizacional cresce, convém mover as cargas de trabalho entre as assinaturas pelos seguintes motivos: alinhar por política de backup, consolidar cofres, compensação de redundância menor para economizar no custo (mover de GRS para LRS).  O backup do Azure dá suporte à movimentação de um cofre de serviços de recuperação entre assinaturas do Azure ou a outro grupo de recursos na mesma assinatura. [Saiba mais aqui](backup-azure-move-recovery-services-vault.md).

### <a name="review-default-settings"></a>Examinar as configurações padrão

Examine as configurações padrão de tipo de replicação de armazenamento e configurações de segurança para atender às suas necessidades antes de configurar backups no cofre.

* O *tipo de replicação de armazenamento* por padrão é definido como com redundância geográfica (GRS). Depois de configurar o backup, a opção para modificar é desabilitada. Siga [estas](backup-create-rs-vault.md#set-storage-redundancy) etapas para examinar e modificar as configurações.

* A *exclusão reversível* por padrão é habilitada em cofres recém-criados para proteger dados de backup de exclusões acidentais ou mal-intencionadas. Siga [estas](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) etapas para examinar e modificar as configurações.

* A *restauração entre regiões* permite que você restaure as VMs do Azure em uma região secundária, que é uma região emparelhada do Azure. Essa opção permite que você realize análises para atender aos requisitos de auditoria ou de conformidade e para restaurar a VM ou seu disco se houver um desastre na região primária. A CRR é um recurso opcional para qualquer cofre GRS. [Saiba mais aqui](backup-create-rs-vault.md#set-cross-region-restore).

* Antes de finalizar o design do cofre, examine as [matrizes de suporte do cofre](backup-support-matrix.md#vault-support) para entender os fatores que podem influenciar ou limitar suas opções de design.

## <a name="backup-policy-considerations"></a>Considerações sobre política de backup

A política de backup do Azure tem dois componentes: *agendamento* (quando fazer backup) e *retenção* (por quanto tempo manter o backup). Você pode definir a política com base no tipo de dados de backup, requisitos de RTO/RPO, necessidades de conformidade operacional ou regulatória e tipo de carga de trabalho (por exemplo, VM, banco de dados, arquivos). [Saiba mais aqui](backup-architecture.md#backup-policy-essentials).

Considere as seguintes diretrizes ao criar a política de backup:

### <a name="schedule-considerations"></a>Considerações de agendamento

* Considere o agrupamento de VMs que exigem a mesma hora de início da agenda, a frequência e as configurações de retenção em uma única política.

* Verifique se a hora de início agendada de backup está durante o tempo de aplicativo de produção que não é de pico.

* Para distribuir o tráfego de backup, considere fazer backup de diferentes VMs em diferentes momentos do dia e garantir que os horários não se sobreponham.

### <a name="retention-considerations"></a>Considerações de retenção

* A retenção de curto prazo pode ser "minutos" ou "diariamente". A retenção de pontos de backup "semanais", "mensais" ou "anuais" é conhecida como retenção de longo prazo.

* Retenção de longo prazo:
  * Planejado (requisitos de conformidade)-se você souber antecipadamente que os dados são necessários anos a partir da hora atual, use a retenção de longo prazo.
  * Não planejado (requisito sob demanda)-se você não souber com antecedência, use você pode usar sob demanda com configurações de retenção personalizadas específicas (essas configurações de retenção personalizadas não são afetadas pelas configurações de política).

* Backup sob demanda com retenção personalizada – se você precisar fazer um backup não agendado por meio da política de backup, poderá usar um backup sob demanda. Isso pode ser útil para fazer backups que não se ajustam ao seu backup agendado ou para fazer backup granular (por exemplo, vários backups de VM IaaS por dia, pois o backup agendado permite apenas um backup por dia). É importante observar que a política de retenção definida na política agendada não se aplica a backups sob demanda.

### <a name="optimize-backup-policy"></a>Otimizar política de backup

* Conforme os requisitos de negócios mudam, talvez seja necessário estender ou reduzir a duração da retenção. Ao fazer isso, você pode esperar o seguinte:  
  * Se a retenção for estendida, os pontos de recuperação existentes serão marcados e mantidos de acordo com a nova política.
  * Se a retenção for reduzida, os pontos de recuperação serão marcados para remoção no próximo trabalho de limpeza e, subsequentemente, excluídos.
  * As regras de retenção mais recentes se aplicam a todos os pontos de retenção (exceto pontos de retenção sob demanda). Portanto, se o período de retenção for estendido (por exemplo, 100 dias), quando o backup for feito, seguido pela redução da retenção (por exemplo, de 100 dias a sete dias), todos os dados de backup serão retidos de acordo com o último período de retenção especificado (ou seja, 7 dias).

* O backup do Azure fornece a flexibilidade para *parar de proteger e gerenciar seus backups*:
  * *Interromper a proteção e manter os dados de backup*. Se você estiver desativando ou descomissionando sua fonte de dados (VM, aplicativo), mas precisar reter dados para fins de auditoria ou de conformidade, poderá usar essa opção para impedir que todos os trabalhos de backup futuros protejam sua fonte de dados e mantenham os pontos de recuperação que foram armazenados em backup. Em seguida, você pode restaurar ou retomar a proteção da VM.
  * *Interrompa a proteção e exclua os dados de backup*. Esta opção impedirá que todos os trabalhos de backup futuros protejam sua VM e exclua todos os pontos de recuperação. Você não poderá restaurar a VM nem usar a opção retomar backup.

  * Se você retomar a proteção (de uma fonte de dados que foi interrompida com reter dados), as regras de retenção serão aplicadas. Todos os pontos de recuperação expirados serão removidos (no horário agendado).

* Antes de concluir o design da política, é importante estar ciente dos seguintes fatores que podem influenciar suas opções de design.
  * Uma política de backup tem como escopo um cofre.
  * Há um limite no número de itens por política (por exemplo, 100 VMs). Para dimensionar, você pode criar políticas duplicadas com as mesmas ou agendas diferentes.
  * Não é possível excluir seletivamente pontos de recuperação específicos.
  * Não é possível desabilitar completamente o backup agendado e manter a fonte de dados em um estado protegido. O backup menos frequente que você pode configurar com a política é ter um backup semanal agendado. Uma alternativa seria interromper a proteção com a retenção de dados e habilitar a proteção sempre que você quiser fazer um backup, fazer um backup sob demanda e desativar a proteção, mas manter os dados de backup. [Saiba mais aqui](backup-azure-manage-vms.md#stop-protecting-a-vm).

## <a name="security-considerations"></a>Considerações sobre segurança

Para ajudá-lo a proteger seus dados de backup e atender às necessidades de segurança de seus negócios, o backup do Azure fornece garantia de confidencialidade, integridade e disponibilidade contra ataques deliberados e abuso de seus dados e sistemas valiosos. Considere as seguintes diretrizes de segurança para sua solução de backup do Azure:

### <a name="authentication-and-authorization"></a>Autenticação e autorização

* O Azure RBAC (controle de acesso baseado em função) permite o gerenciamento de acesso refinado, a diferenciação de tarefas dentro de sua equipe e a concessão apenas da quantidade de acesso para os usuários necessários para executar seus trabalhos. [Saiba mais aqui](backup-rbac-rs-vault.md).

* O backup do Azure fornece três funções internas para controlar as operações de gerenciamento de backup: colaboradores de backup, operadores e leitores. [Saiba mais aqui](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions).

* O backup do Azure tem vários controles de segurança incorporados ao serviço para impedir, detectar e responder a vulnerabilidades de segurança (Saiba mais)

* As contas de armazenamento usadas pelos cofres dos serviços de recuperação são isoladas e não podem ser acessadas por usuários para fins mal-intencionados. O acesso é permitido somente por meio de operações de gerenciamento do Backup do Azure, como a restauração.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Criptografia de dados em trânsito e em repouso

A criptografia protege seus dados e ajuda a atender aos compromissos de conformidade e segurança de sua organização.

* Dentro do Azure, os dados em trânsito entre o armazenamento do Azure e o cofre são protegidos por HTTPS. Esses dados permanecem na rede do Azure.

* Os dados de backup são criptografados automaticamente usando chaves gerenciadas pela Microsoft. Como alternativa, você pode usar suas próprias chaves, também conhecidas como [chaves gerenciadas pelo cliente](encryption-at-rest-with-cmk.md).

* O Backup do Azure dá suporte ao backup e restauração de VMs do Azure que têm os respectivos discos de sistema operacional/dados criptografados com o ADE (Azure Disk Encryption). [Saiba mais aqui](backup-azure-vms-encryption.md).

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>Proteção de dados de backup de exclusões não intencionais

O Backup do Azure fornece recursos de segurança para ajudar a proteger os dados de backup mesmo após a exclusão. Com a exclusão reversível, se um usuário excluir o backup (de uma VM, um banco de dados SQL Server, um compartilhamento de arquivos do Azure, um SAP HANA Database), eles serão retidos por 14 dias adicionais, permitindo a recuperação do item de backup sem perda de dados. A retenção adicional de 14 dias dos dados de backup no estado de "exclusão reversível" não incorre em nenhum custo para você. [Saiba mais aqui](backup-azure-security-feature-cloud.md).

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitoramento e alertas de atividade suspeita

O Backup do Azure fornece funcionalidades internas de monitoramento e alerta para exibir e configurar ações para eventos relacionados ao Backup do Azure. [Saiba mais aqui](security-overview.md#monitoring-and-alerts-of-suspicious-activity).

### <a name="security-features-to-help-protect-hybrid-backups"></a>Recursos de segurança para ajudar a proteger backups híbridos

O serviço de Backup do Azure usa o agente de MARS (Serviços de Recuperação do Microsoft Azure) para fazer backup e restaurar arquivos, pastas e o estado do volume ou do sistema de um computador local para o Azure. O MARS agora fornece recursos de segurança: uma frase secreta para criptografar antes de carregar e descriptografar após o download do backup do Azure, os dados de backup excluídos são mantidos por mais 14 dias a partir da data de exclusão e operação crítica (por exemplo, a alteração de uma frase secreta) pode ser executada somente por usuários que têm credenciais válidas do Azure. [Saiba mais aqui](backup-azure-security-feature.md).

## <a name="network-considerations"></a>Considerações de rede

O backup do Azure requer a movimentação de dados de sua carga de trabalho para o cofre dos serviços de recuperação. O backup do Azure fornece vários recursos para proteger os dados de backup de serem expostos inadvertidamente (como um ataque man-in-the-Middle na rede). Considere as seguintes diretrizes:

### <a name="internet-connectivity"></a>Conectividade com a Internet

* *Backup de VM do Azure* -toda a comunicação necessária e a transferência de dados entre o armazenamento e o serviço de backup do Azure ocorrem na rede do Azure sem a necessidade de acessar sua rede virtual. Portanto, o backup das VMs do Azure colocadas dentro de redes seguras não exige que você permita o acesso a IPs ou FQDNs.

* *SAP Hana bancos de dados na VM do Azure, SQL Server bancos de dados na VM do Azure* -requer conectividade com o serviço de backup do Azure, o armazenamento do Azure e o Azure Active Directory. Isso pode ser feito usando pontos de extremidade privados ou concedendo acesso aos endereços IP públicos ou FQDNs necessários. Não permitir a conectividade adequada com os serviços do Azure necessários pode levar a uma falha em operações como descoberta de banco de dados, configuração de backup, realização de backups e restauração de dados. Para obter orientação de rede completa ao usar marcas NSG, o Firewall do Azure e o proxy HTTP, consulte estes artigos sobre [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) e [SAP Hana](./backup-azure-sap-hana-database.md#establish-network-connectivity) .

* *Híbrido* -o agente MARS (serviços de recuperação do Microsoft Azure) requer acesso à rede para todas as operações críticas – instalação, configuração, backup e restauração. O agente MARS pode se conectar ao serviço de backup do Azure por meio [do Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) usando o emparelhamento público (disponível para circuitos antigos) e o emparelhamento da Microsoft, usando [pontos de extremidade privados](install-mars-agent.md#private-endpoints) ou por [Proxy/Firewall com controles de acesso apropriados](install-mars-agent.md#verify-internet-access).

### <a name="private-endpoints-for-azure-backup"></a>Pontos de extremidade privados para o backup do Azure

O [ponto de extremidade privado](../private-link/private-endpoint-overview.md) do Azure é uma interface de rede que conecta você de forma privada e segura a um serviço da plataforma Azure link privado. O backup do Azure permite que você faça backup e restaure seus dados de seus cofres de serviços de recuperação usando pontos de extremidade privados.

* Quando você habilita pontos de extremidade privados para o cofre, eles são usados somente para backup e restauração de cargas de trabalho do SQL e do SAP HANA em uma VM do Azure e backups do agente MARS.  Você também pode usar o cofre para o backup de outras cargas de trabalho (mas elas não precisarão de pontos de extremidade privados). Além do backup de cargas de trabalho do SQL e do SAP HANA e do backup usando o agente MARS, os pontos de extremidade privados também são usados para executar a recuperação de arquivos no caso do backup de VM do Azure. [Saiba mais aqui](private-endpoints.md#recommended-and-supported-scenarios).

* No momento, o Azure Active Directory não dá suporte a pontos de extremidade privados. Portanto, os IPs e FQDNs necessários para Azure Active Directory precisarão ter acesso de saída da rede protegida ao executar o backup de bancos de dados em VMs do Azure e fazer backup usando o agente MARS. Você também pode usar marcas NSG e marcas de firewall do Azure para permitir o acesso ao Azure AD, conforme aplicável. Saiba mais sobre os [pré-requisitos aqui](./private-endpoints.md#before-you-start).

## <a name="governance-considerations"></a>Considerações de governança

A governança no Azure é implementada principalmente com [Azure Policy](../governance/policy/overview.md) e o [Gerenciamento de custos do Azure](../cost-management-billing/cost-management-billing-overview.md). O [Azure Policy](../governance/policy/overview.md) permite criar, atribuir e gerenciar definições de política para impor regras aos recursos. Esse recurso mantém os recursos em conformidade com os padrões corporativos. O [Gerenciamento de custos do Azure](../cost-management-billing/cost-management-billing-overview.md) permite acompanhar o uso da nuvem e as despesas dos recursos do Azure e de outros provedores de nuvem. Além disso, as ferramentas a seguir, como a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) e o [Azure Advisor](../advisor/advisor-overview.md)  , desempenham um papel importante no processo de gerenciamento de custos.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>O backup do Azure dá suporte a dois cenários principais por meio de Azure Policy interno

* Certifique-se de que os computadores críticos para os negócios criados recentemente sejam submetidos a backup automaticamente com as configurações de retenção corretas. O backup do Azure fornece uma política interna (usando Azure Policy) que pode ser atribuída a todas as VMs do Azure em um local especificado dentro de uma assinatura ou grupo de recursos. Quando essa política é atribuída a um determinado escopo, todas as novas VMs criadas nesse escopo são automaticamente configuradas para backup em um cofre existente no mesmo local e assinatura. O usuário pode especificar o cofre e a política de retenção à qual as VMs de backup devem ser associadas. [Saiba mais aqui](backup-azure-auto-enable-backup.md).

* Verifique se os cofres recém-criados têm o diagnóstico habilitado para dar suporte a relatórios. Muitas vezes, adicionar uma configuração de diagnóstico manualmente por cofre pode ser uma tarefa complicada. Além disso, qualquer novo cofre criado precisa ter as configurações de diagnóstico habilitadas para que você possa exibir relatórios para este cofre. Para simplificar a criação de configurações de diagnóstico em escala (com Log Analytics como o destino), o backup do Azure fornece um Azure Policy interno. Essa política adiciona uma configuração de diagnóstico LA a todos os cofres em cada assinatura ou grupo de recursos. As seções a seguir fornecem instruções sobre como usar essa política. [Saiba mais aqui](azure-policy-configure-diagnostics.md).

### <a name="azure-backup-cost-considerations"></a>Considerações de custo de backup do Azure

Os recursos do serviço de backup do Azure oferecem a flexibilidade para gerenciar seus custos com eficiência e ainda atendem aos requisitos de negócios do BCDR (continuidade dos negócios e recuperação de desastre). Considere as seguintes diretrizes:

* Use a calculadora de preços para avaliar e otimizar os custos ajustando várias alavancas. [Saiba mais aqui](azure-backup-pricing.md)

* Gerenciador de backup: Use o Gerenciador de backup ou relatórios de backup para entender e otimizar o crescimento do armazenamento de backup, parando os backups de cargas de trabalho não críticas ou de VMs excluídas. Você pode obter uma exibição agregada de toda a sua imobiliária a partir de uma perspectiva de backup. Isso inclui não apenas informações sobre seus itens de backup, mas também recursos que não estão configurados para backup. Isso garante que você nunca perca a proteção de dados críticos em seu espaço crescente e que seus backups são otimizados para cargas de trabalho não críticas ou para cargas de trabalho excluídas.

* Otimizar política de backup
  * Otimizar as configurações de agendamento e retenção com base em arquétipos de carga de trabalho (por exemplo, missão crítica, não crítica)
  * Otimizar as configurações de retenção para restauração instantânea
  * Escolha o tipo de backup certo para atender aos requisitos, ao mesmo tempo em que leva os tipos de backup com suporte (completo, incremental, log, diferencial) pela carga de trabalho no backup do Azure em consideração.

* Discos de backup seletivo: excluir disco (recurso de visualização) fornece uma opção eficiente e econômica para fazer backup seletivo de dados críticos. Por exemplo, faça backup de apenas um disco quando não quiser fazer backup do restante dos discos anexados a uma VM. Isso também é útil quando você tem várias soluções de backup. Por exemplo, quando você faz o backup de seus bancos de dados ou de data com uma solução de backup de carga de trabalho (SQL Server banco de dados no backup de VM do Azure) e deseja usar o backup em nível de VM do Azure para os discos selecionados.

* O backup do Azure tira instantâneos das VMs do Azure e as armazena junto com os discos para impulsionar a criação do ponto de recuperação e acelerar as operações de restauração. Isso é conhecido como restauração instantânea. Por padrão, os instantâneos de restauração instantânea são mantidos por dois dias. Esse recurso permite uma operação de restauração desses instantâneos, reduzindo os tempos de restauração. Ele reduz o tempo necessário para transformar e copiar dados de volta do cofre. Como resultado, você vê os custos de armazenamento correspondentes aos instantâneos obtidos durante esse período. [Saiba mais aqui](backup-instant-restore-capability.md#configure-snapshot-retention).

* O tipo de replicação de armazenamento do cofre de backup do Azure por padrão é definido como GRS (com redundância geográfica). Esta opção não pode ser alterada após a proteção de itens. O GRS (armazenamento com redundância geográfica) fornece um nível mais alto de durabilidade de dados do que o LRS (armazenamento com redundância local), permite que uma aceitação use a restauração entre regiões e os custos mais. Analise as compensações entre custos menores e maior durabilidade de dados e decida o que é melhor para seu cenário. [Saiba mais aqui](backup-create-rs-vault.md#set-storage-redundancy)

* Se você estiver protegendo a carga de trabalho em execução dentro de uma VM e a própria VM, verifique se essa proteção dupla é necessária.

## <a name="monitoring-and-alerting-considerations"></a>Considerações sobre monitoramento e alertas

Como um usuário ou administrador de backup, você deve ser capaz de monitorar todas as soluções de backup e ser notificado em cenários importantes. Esta seção detalha os recursos de monitoramento e notificação fornecidos pelo serviço de backup do Azure.

### <a name="monitoring"></a>Monitoramento

* O backup do Azure fornece **monitoramento de trabalho interno** para operações como configurar backup, backup, restauração, excluir backup e assim por diante. Isso tem como escopo o cofre e é ideal para monitorar um único cofre. [Saiba mais aqui](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

* Se você precisar monitorar atividades operacionais em escala, o **Gerenciador de backup** fornecerá uma exibição agregada de todo o seu espaço de backup, permitindo análise detalhada e solução de problemas. É uma pasta de trabalho Azure Monitor interna que fornece um único local central para ajudá-lo a monitorar atividades operacionais em todo o espaço de backup no Azure, abrangendo locatários, locais, assinaturas, grupos de recursos e cofres. [Saiba mais aqui](monitor-azure-backup-with-backup-explorer.md).
  * Use-o para identificar recursos que não estão configurados para backup e garanta que você não perca a proteção de dados críticos em seu espaço cada vez maior.
  * O painel fornece atividades operacionais para os últimos sete dias (máximo). Se precisar manter esses dados, você poderá exportar como um arquivo do Excel e remantê-los.
  * Se você for um usuário Lighthouse do Azure, poderá exibir informações em vários locatários, habilitando o monitoramento sem limites.

* Se você precisar manter e exibir as atividades operacionais de longo prazo, use **relatórios**. Um requisito comum para administradores de backup é obter informações sobre backups com base em dados que abrangem um longo período de tempo. Os casos de uso para essa solução incluem:
  * Alocação e previsão do armazenamento em nuvem consumido.
  * Auditoria de backups e restaurações.
  * Identificação das principais tendências em diferentes níveis de granularidade.

* Além disso,
  * Você pode enviar dados (por exemplo, trabalhos, políticas e assim por diante) para o espaço de trabalho **log Analytics** . Isso permitirá que os recursos dos logs de Azure Monitor habilitem a correlação de dados com outros dados de monitoramento coletados pelo Azure Monitor, consolide entradas de log de várias assinaturas e locatários do Azure em um local para análise, use consultas de log para executar análises complexas e obter insights aprofundados sobre entradas de log. [Saiba mais aqui](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace).
  * Você pode enviar dados para o Hub de eventos para enviar entradas fora do Azure, por exemplo, para um SIEM de terceiros (gerenciamento de eventos e informações de segurança) ou outra solução do log Analytics. [Saiba mais aqui](../azure-monitor/platform/activity-log.md#send-to-azure-event-hubs).
  * Você pode enviar dados para uma conta de armazenamento do Azure se desejar manter seus dados de log por mais de 90 dias para auditoria, análise estática ou backup. Se você só precisa reter seus eventos por 90 dias ou menos, não precisa configurar arquivos mortos para uma conta de armazenamento, pois os eventos do log de atividades são mantidos na plataforma do Azure por 90 dias. [Saiba mais](../azure-monitor/platform/activity-log.md#send-to--azure-storage).

### <a name="alerting"></a>Alertas

* Os alertas são, principalmente, uma maneira de ser notificado para executar uma ação relevante. A seção alertas de backup mostra alertas gerados pelo serviço de backup do Azure.

* O backup do Azure fornece um mecanismo **de notificação de alerta interno** por email para falhas, avisos e operações críticas. Você pode especificar endereços de email individuais ou listas de distribuição a serem notificadas quando um alerta for gerado. Você também pode escolher se deseja ser notificado para cada alerta individual ou agrupá-lo em um resumo por hora e, em seguida, ser notificado.
  * Esses alertas são definidos pelo serviço e fornecem suporte para cenários limitados-falhas de backup/restauração, interromper a proteção com reter dados/parar proteção com excluir dados e assim por diante. [Saiba mais aqui](backup-azure-monitoring-built-in-monitor.md#alert-scenarios).
  * Se uma operação destrutiva, como parar proteção com dados de exclusão, for executada, um alerta será gerado e um email será enviado para proprietários, administradores e coadministradores de assinatura, mesmo que as notificações **não** estejam configuradas para o cofre dos serviços de recuperação.
  * Determinadas cargas de trabalho podem gerar alta frequência de falhas (por exemplo, SQL Server a cada 15 minutos). Para evitar ficar sobrecarregado com alertas gerados para cada ocorrência de falha, os alertas são consolidados. [Saiba mais aqui](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts).
  * Os alertas internos não podem ser personalizados e são restritos a emails definidos no portal do Azure.

* Se você precisar **criar alertas personalizados** (por exemplo, alertas de trabalhos com êxito), use log Analytics. No Azure Monitor, você pode criar seus alertas em um workspace do Log Analytics. As cargas de trabalho híbridas (DPM/MABS) também podem enviar dados para LA e usar LA para fornecer alertas comuns entre cargas de trabalho com suporte do backup do Azure.

* Você também pode obter notificações por meio de **logs de atividade** do cofre de serviços de recuperação internos. No entanto, ele dá suporte a cenários limitados e não é adequado para operações como backup agendado, que se alinha melhor com os logs de recursos do que com os logs de atividade. Para saber mais sobre essas limitações e como você pode usar Log Analytics espaço de trabalho para monitoramento e alertas em escala para todas as suas cargas que são protegidas pelo backup do Azure, consulte este [artigo](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale).

## <a name="next-steps"></a>Próximas etapas

Recomendamos que você leia os seguintes artigos como pontos de partida para usar o backup do Azure:

* [Visão geral do backup do Azure](backup-overview.md)
* [Perguntas frequentes](backup-azure-backup-faq.md)
