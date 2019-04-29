---
title: Blueprint de segurança e conformidade do Azure - Aplicativo Web de IaaS para NIST SP 800-171
description: Blueprint de segurança e conformidade do Azure - Aplicativo Web de IaaS para NIST SP 800-171
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: b30094e264086f018acbf84144300df46c60ac4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610258"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Blueprint de segurança e conformidade do Azure - Aplicativo Web de IaaS para NIST SP 800-171

## <a name="overview"></a>Visão geral
A [Publicação Especial do NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornece diretrizes para proteção de CUI (Controlled Unclassified Information) que reside em organizações e sistemas de informações não federais. O NIST SP 800-171 estabelece 14 famílias de requisitos de segurança para proteger a confidencialidade de CUI.

Este blueprint de segurança e conformidade do Azure fornece diretrizes para ajudar os clientes a implantar uma arquitetura de aplicativo Web no Azure que implementa um subconjunto de controles do NIST SP 800-171. Esta solução demonstra maneiras em que os clientes podem atender a requisitos de conformidade e segurança específicos. Ela também funciona como base para os clientes criarem e configurarem seus próprios aplicativos Web no Azure.

Essa arquitetura de referência, o guia de implementação associado e o modelo de risco devem funcionar como uma base que clientes adaptarão às suas necessidades específicas. Eles não devem ser usados no estado em que se encontram em um ambiente de produção. A implantação dessa arquitetura sem modificação é insuficiente para atender completamente aos requisitos do NIST SP 800-171. Os clientes são responsáveis por realizar avaliações de conformidade e segurança apropriadas de qualquer solução criada usando essa arquitetura. Os requisitos podem variar com base nas especificações da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
Este Blueprint de Segurança e Conformidade do Azure implanta uma arquitetura de referência para um aplicativo Web de IaaS com um back-end do SQL Server. A arquitetura inclui uma camada da Web, uma camada de dados, a infraestrutura do Active Directory, o Gateway de Aplicativo do Azure e o Azure Load Balancer. VMs (máquinas virtuais) implantadas na Web e camadas de dados são configuradas em um conjunto de disponibilidade. Instâncias do SQL Server são configuradas no grupo de disponibilidade Always On para alta disponibilidade. VMs são ingressadas no domínio. As políticas de grupo do Active Directory impõem configurações de conformidade e segurança no nível do sistema operacional.

A solução integral é criada com base no Armazenamento do Microsoft Azure que os clientes configuram no portal do Azure. O armazenamento criptografa todos os dados com a Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O armazenamento com redundância geográfica garante que um evento adverso no data center primário do cliente não resulta em perda de dados. Uma segunda cópia é armazenada em um local separado a centenas de milhas de distância.

Para maior segurança, todos os recursos nessa solução são gerenciados como um grupo de recursos por meio do Azure Resource Manager. O RBAC (controle de acesso baseado em função) do Azure AD (Azure Active Directory) é usado para controlar o acesso aos recursos e chaves implantados no Azure Key Vault. A integridade do sistema é monitorada por meio do Azure Monitor. Os clientes configuram ambos os serviços de monitoramento para capturar os logs. A integridade do sistema é exibida em um único painel fácil de usar.

Um bastião de gerenciamento fornece conexão segura para os administradores acessarem recursos implantados. *A Microsoft recomenda que você configure uma conexão VPN ou do Azure ExpressRoute para importação de dados e gerenciamento na sub-rede da arquitetura de referência.*


![Aplicativo Web de IaaS para diagrama de arquitetura de referência do NIST SP 800-171](images/nist171-iaaswa-architecture.png "Aplicativo Web de IaaS para diagrama de arquitetura de referência do NIST SP 800-171")

A solução usa os serviços do Azure a seguir. Para obter mais informações, confira a seção [Arquitetura de implantação](#deployment-architecture).

- Máquinas Virtuais do Azure
    - (1) gerenciamento/bastião (Windows Server 2016 Datacenter)
    - (2) controlador de domínio do Active Directory (Windows Server 2016 Datacenter)
    - (2) nó de cluster do SQL Server (SQL Server 2017 no Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Rede Virtual do Azure
    - (1) /16 rede
    - (5) /24 redes
    - (5) grupos de segurança de rede
- Conjuntos de disponibilidade
    - (1) controladores de domínio do Active Directory
    - (1) nós de cluster do SQL
    - (1) Web/IIS
- Gateway de Aplicativo do Azure
    - (1) Firewall do aplicativo Web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP 3.0
        - Porta ouvinte: 443
- Azure Active Directory
- Cofre da Chave do Azure
- Azure Load Balancer
- O Azure Monitor (logs)
- Azure Resource Manager
- Central de Segurança do Azure
- Armazenamento do Azure
- Automação do Azure
- Testemunha de nuvem
- Cofre dos Serviços de Recuperação

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir fornece detalhes sobre os elementos de implantação e implementação.

**Host bastião**: O host de bastiões é o único ponto de entrada que os usuários podem usar para acessar os recursos implantados nesse ambiente. O host bastião fornece uma conexão segura aos recursos implantados, permitindo apenas o tráfego remoto de endereços IP públicos em uma lista segura. Para permitir tráfego de área de trabalho remota, a origem do tráfego precisa ser definida no NSG (grupo de segurança de rede).

Essa solução cria uma VM como um host bastião ingressado no domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Extensão de Diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usando o Key Vault.
-   Uma [política de desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos da VM quando eles não estão em uso.
-   O [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) é habilitado para que as credenciais e outros segredos sejam executados em um ambiente protegido e isolado do sistema operacional em execução.

### <a name="virtual-network"></a>Rede virtual
A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: Essa solução implanta recursos em uma arquitetura com sub-redes separadas para web, banco de dados, do Active Directory e gerenciamento em uma rede virtual. Sub-redes são separadas logicamente pelas regras de NSG aplicadas a sub-redes individuais. As regras restringem o tráfego entre as sub-redes para somente o que é necessário para a funcionalidade de gerenciamento e de sistema.

Confira a configuração dos [NSGs](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implantada com esta solução. As organizações podem configurar NSGs editando o arquivo anterior usando [esta documentação](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) como guia.

Cada uma das sub-redes tem um NSG dedicado:
- Um NSG para o Gateway de Aplicativo (LBNSG)
- Um NSG para o host bastião (MGTNSG)
- Um NSG para controladores de domínio primário e de backup (ADNSG)
- Um NSG para SQL Servers e testemunha de nuvem (SQLNSG)
- Um NSG para a camada da Web (WEBNSG)

### <a name="data-in-transit"></a>Dados em trânsito
O Azure criptografa todas as comunicações entre os data centers do Azure por padrão. Além disso, todas as transações para o Armazenamento por meio do portal do Azure ocorrem por HTTPS.

### <a name="data-at-rest"></a>Dados em repouso
A arquitetura protege dados em repouso por meio de várias medidas. Essas medidas incluem criptografia e auditoria do banco de dados.

**Armazenamento do Azure**: Para atender aos requisitos de dados em repouso criptografados, todo o [Armazenamento](https://azure.microsoft.com/services/storage/) usa [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption). Esse recurso ajuda a proteger e preservar os dados em suporte aos compromissos da segurança organizacional e aos requisitos de conformidade definidos pelo NIST SP 800-171.

**Azure Disk Encryption**: Criptografia de disco é usado para discos de VM de IaaS do Windows criptografados. O [Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usa o recurso BitLocker do Windows para fornecer criptografia de volume para o sistema operacional e os discos de dados. A solução é integrada ao Key Vault para ajudar a controlar e a gerenciar as chaves de criptografia de disco.

**Microsoft SQL Server**: a instância do SQL Server usa as seguintes medidas de segurança de banco de dados:
-   [Auditoria do SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) rastreia eventos do banco de dados e grava-os em logs de auditoria.
-   [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) executa criptografia e descriptografia em tempo real do banco de dados, dos backups associados e dos arquivos de log de transações para proteger as informações em repouso. A Transparent Data Encryption oferece a garantia de que os dados armazenados não estejam sujeitos a acesso não autorizado.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem o acesso aos servidores do banco de dados até que as permissões apropriadas sejam concedidas. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.
-   As [colunas criptografadas](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) garantem que os dados confidenciais nunca sejam exibidos como texto sem formatação dentro do sistema de banco de dados. Depois que a criptografia de dados estiver habilitada, somente os aplicativos clientes ou servidores de aplicativos com acesso às chaves poderão acessar dados de texto sem formatação.
- A [Máscara de Dados Dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos sem privilégios. Ela pode descobrir automaticamente dados possivelmente confidenciais e sugerir as máscaras apropriadas a serem aplicadas. A Máscara de Dados Dinâmicos ajuda a reduzir o acesso para que os dados confidenciais não saiam do banco de dados por meio de acesso não autorizado. *Os clientes são responsáveis por ajustar as configurações para seguir seu próprio esquema de banco de dados.*

### <a name="identity-management"></a>Gerenciamento de identidades
As seguintes tecnologias oferecem funcionalidades para gerenciar o acesso a dados no ambiente do Azure:
-   O [Azure AD](https://azure.microsoft.com/services/active-directory/) é o serviço multilocatário de gerenciamento de identidade e de diretório baseado em nuvem da Microsoft. Todos os usuários dessa solução são criados no Azure AD e incluem os usuários que acessam a Instância do SQL Server.
-   A autenticação para o aplicativo é executada por meio do Azure AD. Para obter mais informações, confira como [integrar aplicativos ao Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   O [RBAC do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) pode ser usado pelos administradores para definir permissões de acesso refinadas e conceder apenas a quantidade de acesso de que os usuários precisam para realizar seus trabalhos. Em vez de conceder permissões irrestritas aos recursos do Azure a todos os usuários, os administradores podem permitir apenas determinadas ações para acesso a dados. O acesso à assinatura é limitado ao administrador da assinatura.
- O [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) pode ser usado pelos clientes para minimizar o número de usuários com acesso a determinados recursos. Os administradores podem usar o Azure AD Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor o acesso administrativo Just-In-Time sob demanda, quando necessário.
- O [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades da organização. Ele configura respostas automatizadas para detectar ações suspeitas relacionadas às identidades da organização. Ele também investiga incidentes suspeitos para tomar as devidas providências para resolvê-los.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: A solução usa [Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Key Vault ajuda a proteger chaves de criptografia e segredos usados por aplicativos e serviços de nuvem. As seguintes funcionalidades do Key Vault ajudam os clientes a proteger os dados:
- Políticas de acesso avançadas são configuradas com base na necessidade.
- As políticas de acesso do Key Vault são definidas com o mínimo de permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault têm datas de validade.
- Todas as chaves no Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma chave RSA de 2048 bits protegida pelo módulo de segurança de hardware.
- Todos os usuários e identidades recebem as permissões mínimas necessárias quando o RBAC é usado.
- Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
- As operações criptográficas permitidas para chaves são restritas às necessárias.
- A solução é integrada ao Key Vault para gerenciar chaves e segredos de criptografia de disco de VMs IaaS.

**Gerenciamento de patches**: VMs do Windows implantadas como parte dessa arquitetura de referência são configuradas por padrão para receberem atualizações automáticas do serviço do Windows Update. Essa solução também inclui o serviço [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) por meio do qual as implantações atualizadas podem ser criadas para aplicar patch em VMs quando necessário.

**Proteção contra malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para VMs fornece funcionalidade de proteção em tempo real que ajuda a identifica e remove vírus, spyware e outros softwares mal-intencionados. Os clientes podem configurar alertas que são gerados quando software mal-intencionado ou indesejado tenta se instalar ou executar em VMs protegidas.

**Central de Segurança do Azure**: Com a [Central de Segurança](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques de forma centralizada. A Central de Segurança também acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e de serviço para ajudar a melhorar a situação de segurança e proteger os dados.

A Central de Segurança usa uma variedade de funcionalidades de detecção para alertar os clientes sobre possíveis ataques direcionados a seus ambientes. Esses alertas contêm informações valiosas sobre o que disparou o alerta, os recursos de destino e a origem do ataque. A Central de Segurança tem um conjunto de [alertas de segurança predefinidos](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que é disparado em caso de ameaça ou de atividade suspeita. Os clientes podem usar as [regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) para definir novos alertas de segurança com base nos dados que já foram coletados do ambiente.

A Central de Segurança fornece alertas e incidentes de segurança priorizados. A Central de Segurança facilita a descoberta e a resolução de possíveis problemas de segurança para os clientes. Um [relatório de inteligência de ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada. As equipes de resposta a incidentes podem usar os relatórios ao investigarem e corrigirem ameaças.

Essa arquitetura de referência usa a funcionalidade de [avaliação de vulnerabilidade](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) na Central de Segurança. Depois de configurado, um agente parceiro (por exemplo, Qualys) reporta dados de vulnerabilidade para a plataforma de gerenciamento do parceiro. por sua vez, fornecerá dados de monitoramento de vulnerabilidade e integridade de volta para a Central de Segurança. Os clientes podem usar essas informações para identificar rapidamente as VMs vulneráveis.

**Gateway de Aplicativo do Azure**: A arquitetura reduz o risco de vulnerabilidades de segurança por meio de um gateway de aplicativo com o firewall do aplicativo web configurado e o conjunto de regras OWASP habilitado. Dentre outros recursos estão:

- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Habilitar o [descarregamento SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Desabilitar o [TLS v1.0 e v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Firewall do aplicativo Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo de prevenção).
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com o conjunto de regras OWASP 3.0.
- Habilitar o [log de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Investigações de integridade personalizadas](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- A [Central de Segurança](https://azure.microsoft.com/services/security-center) e o [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecem proteção e notificações adicionais. A Central de segurança também fornece um sistema de reputação.

### <a name="business-continuity"></a>Continuidade dos negócios

**Alta disponibilidade**: A solução implanta todas as VMs em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Os conjuntos de disponibilidade fazem com que as VMs sejam distribuídas entre vários clusters de hardware isolados para melhorar a disponibilidade. Pelo menos uma VM fica disponível durante um evento de manutenção planejada ou não planejada, atendendo ao SLA do Azure de 99,95%.

**Cofre dos serviços de recuperação**: o [Cofre dos Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) armazena dados de backup e protege todas as configurações das Máquinas Virtuais do Azure nesta arquitetura. Com um Cofre dos Serviços de Recuperação, os clientes podem restaurar arquivos e pastas de uma VM de IaaS sem restaurar a VM inteira. Esse processo acelera o tempo de restauração.

**Testemunha de Nuvem**: [Testemunha de nuvem](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) é um tipo de testemunha de quorum de cluster de failover no Windows Server 2016 que usa o Azure como o ponto de arbitragem. A Testemunha de Nuvem, como qualquer outra testemunha de quorum, obtém um voto e pode participar dos cálculos de quorum. Ela usa o Armazenamento de Blobs do Azure padrão, disponível ao público. Isso elimina a sobrecarga adicional de manutenção de VMs hospedadas em uma nuvem pública.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

Os serviços do Azure registram em log de forma extensiva as atividades do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividades**: Os [logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem insights sobre as operações executadas em recursos em uma assinatura. Os logs de atividade podem ajudar a determinar o iniciador, o horário da ocorrência e o status de uma operação.
- **Logs de diagnóstico**: Os [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registros emitidos por todos os recursos. Esses logs incluem os logs do sistema de eventos do Windows, os logs de Armazenamento, os logs de auditoria do Key Vault e os logs de acesso e do firewall do Gateway de Aplicativo. Todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. Os usuários podem configurar um período de retenção de até 730 dias para atender às suas necessidades específicas.

**Logs do Azure Monitor**: Esses logs são consolidados [registra em log do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e emissão de relatórios do painel. Depois que os dados são coletados, eles são organizados em tabelas separadas para cada tipo de dados nos espaços de trabalho do Log Analytics. Dessa forma, todos os dados podem ser analisados juntos, independentemente de sua fonte original. A Central de segurança se integra com os logs do Azure Monitor. Os clientes podem usar consultas Kusto para acessar seus dados de evento de segurança e combiná-lo com dados de outros serviços.

O Azure a seguir [soluções de monitoramento](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) são incluídos como parte dessa arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de Verificação de Integridade do Active Directory avalia o risco e a integridade dos ambientes de servidor em um intervalo regular. Ela fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de Verificação de Integridade do SQL avalia o risco e a integridade dos ambientes do servidor em um intervalo regular. Ela fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de Integridade do Agente informa quantos agentes estão implantados e sua distribuição geográfica. Ela também relata quantos agentes não estão respondendo e o número de agentes que enviam dados operacionais.
-   [Análise do Log de Atividades](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução de Análise do Log de Atividades auxilia na análise dos logs de atividades do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: A [Automação](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nessa solução, os runbooks ajudam a coletar logs do SQL Server. Os clientes podem usar a solução de [Controle de Alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) da Automação para identificar facilmente as alterações no ambiente.

**Azure Monitor**: O [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências. As organizações podem usá-lo para auditar, criar alertas e arquivar dados. Elas também podem acompanhar as chamadas à API em seus recursos do Azure.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados dessa arquitetura de referência está disponível para [download](https://aka.ms/nist171-iaaswa-tm) ou pode ser encontrado aqui. Esse modelo pode ajudar os clientes a entenderem os possíveis pontos de risco na infraestrutura do sistema ao fazer modificações.

![Aplicativo Web de IaaS para modelo de risco do NIST SP 800-171](images/nist171-iaaswa-threat-model.png "Aplicativo Web de IaaS para modelo de risco do NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentação de conformidade

O [Blueprint de segurança e conformidade do Azure - Matriz de responsabilidade do cliente para NIST SP 800-171](https://aka.ms/nist171-crm) lista todos os controles de segurança exigidos pelo NIST SP 800-171. Essa matriz detalha se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou compartilhada entre os dois.

O [Blueprint de Segurança e Conformidade do Azure – Matriz de Implementação de Controle de Aplicativo Web de IaaS do NIST SP 800-171](https://aka.ms/nist171-iaaswa-cim) fornece informações sobre quais controles do NIST SP 800-171 são cobertos pela arquitetura de aplicativo Web de IaaS. Ele inclui descrições detalhadas de como a implementação atende aos requisitos de cada controle coberto.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel VPN seguro ou um [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer uma conexão com os recursos implantados como parte dessa arquitetura de referência de aplicativo Web de IaaS de forma segura. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Implementando um túnel de VPN seguro com o Azure, é possível criar uma conexão privada virtual entre uma rede local e uma Rede Virtual do Azure. Essa conexão ocorre pela Internet. Os clientes podem usar essa conexão para enviar informações por “túnel” com segurança usando um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. O [modo de túnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego do túnel de VPN passa pela Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O ExpressRoute é um link de WAN dedicado entre o Azure e uma localização local ou um provedor de hospedagem do Exchange. As conexões do ExpressRoute conectam-se diretamente ao provedor de telecomunicações do cliente. Como resultado, os dados não passam pela Internet e não são expostos a ela. Essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências mais baixas e segurança maior do que as conexões típicas. 

Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) práticas recomendadas para a implementação de uma rede híbrida segura que estende uma rede local para o Azure.

## <a name="disclaimer"></a>Isenção de responsabilidade

- Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo. 
- Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft. 
- Os clientes podem copiar e usar este documento para fins de consulta interna. 
- Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente. 
- Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
- Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
