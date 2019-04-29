---
title: Blueprint de segurança e conformidade do Azure - Aplicativo Web de IaaS para FedRAMP
description: Blueprint de segurança e conformidade do Azure - Aplicativo Web de IaaS para FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: barbkess
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: 1ba5b813843ce2f5d31f337ab4d3d94e521b0e0c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586096"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Blueprint de Segurança e Conformidade do Azure: Aplicativo Web de IaaS para FedRAMP

## <a name="overview"></a>Visão geral

O [FedRAMP (Federal Risk and Authorization Management Program)](https://www.fedramp.gov) é um programa do governo dos EUA que fornece uma abordagem padronizada para avaliação de segurança, autorização e monitoramento contínuo de produtos e serviços de nuvem. Esta Automação do Blueprint de Segurança e Conformidade do Azure fornece as diretrizes para a implantação de um ambiente de IaaS (infraestrutura como serviço) compatível com FedRAMP, adequado para um aplicativo Web simples voltado para a Internet. Essa solução automatiza a implantação e a configuração de recursos do Azure para uma arquitetura de referência comum, demonstrando maneiras pelas quais os clientes podem atender aos requisitos específicos de segurança e conformidade, e serve como base para os clientes criarem e Configurarem suas próprias soluções no Azure. A solução implementa um subconjunto de controles da linha de base FedRAMP Alta, com base em NIST SP 800-53. Para obter mais informações sobre os requisitos do FedRAMP e essa solução, confira a [documentação de conformidade](#compliance-documentation).
> [!NOTE]
> A solução é implantada no Azure Governamental.

Esta Automação de Segurança e Conformidade do Azure Blueprint implanta automaticamente uma arquitetura de referência do aplicativo Web IaaS com controles de segurança pré-configurados para que os clientes fiquem em conformidade com os requisitos de FedRAMP. A solução consiste em modelos do Azure Resource Manager e scripts do PowerShell que guiam a implantação e a configuração dos recursos.

Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção. A implantação de um aplicativo nesse ambiente sem modificações não é suficiente para atender aos requisitos da linha de base de FedRAMP Alta totalmente. Observe o seguinte:
- Essa arquitetura fornece uma linha de base para ajudar os clientes a usar o Azure de forma compatível com o FedRAMP.
- Os clientes são responsáveis por realizar as devidas avaliações de segurança e conformidade de qualquer solução criada usando essa arquitetura, já que os requisitos podem variar com base nas particularidades da implementação de cada cliente.

Para uma visão geral de como essa solução funciona, assista a este [vídeo](https://aka.ms/fedrampblueprintvideo) explicando e demonstrando sua implantação.

Clique [aqui](https://aka.ms/fedrampblueprintrepo) para obter instruções de implantação.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
A solução implanta uma arquitetura de referência para um aplicativo Web de IaaS com um back-end do SQL Server. A arquitetura inclui uma camada da Web, uma camada de dados, a infraestrutura do Active Directory, o Gateway de Aplicativo e o Load Balancer. As máquinas virtuais implantadas nas camadas da Web e de dados são configuradas em um conjunto de disponibilidade, e as instâncias do SQL Server são configuradas em um grupo de disponibilidade AlwaysOn para alta disponibilidade. As máquinas virtuais são associadas ao domínio e as políticas de grupo do Active Directory são usadas para aplicar configurações de conformidade e segurança no nível do sistema operacional. Um host bastião fornece uma conexão segura para os administradores acessarem os recursos implantados. **O Azure recomenda configurar uma conexão VPN ou do Azure ExpressRoute para gerenciamento e importação de dados para a sub-rede da arquitetura de referência.**

![Diagrama de arquitetura de referência do aplicativo Web de IaaS para FedRAMP](images/fedramp-iaaswa-architecture.png?raw=true "Diagrama de arquitetura de referência do aplicativo Web de IaaS para FedRAMP")

A solução usa os serviços do Azure a seguir. Os detalhes da arquitetura de implantação estão localizados na seção [Arquitetura de implantação](#deployment-architecture).

- Máquinas Virtuais do Azure
    - (1) host bastião (Windows Server 2016 Datacenter)
    - (2) controlador de domínio do Active Directory (Windows Server 2016 Datacenter)
    - (2) nó de cluster do SQL Server (SQL Server 2017 no Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Conjuntos de Disponibilidade
    - (1) controladores de domínio do Active Directory
    - (1) nós de cluster do SQL
    - (1) Web/IIS
- Rede Virtual do Azure
    - (1)/16 redes virtuais
    - (5)/24 sub-redes
    - As configurações de DNS são definidas em ambos os controladores de domínio
- Azure Load Balancer
- Gateway de Aplicativo do Azure
    - (1) Gateway de Aplicativo WAF habilitado
        - modo de firewall: prevenção
        - conjunto de regras: OWASP 3.0
        - ouvinte: porta 443
- Armazenamento do Azure
    - (7) contas de armazenamento com redundância geográfica
- Testemunha de nuvem do Azure
- Cofre dos Serviços de Recuperação
- Cofre da Chave do Azure
- Active Directory do Azure (Azure AD)
- Azure Resource Manager
- O Azure Monitor (logs)

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir fornece detalhes sobre os elementos de desenvolvimento e implementação.

**Host bastião**: o host bastião é o único ponto de entrada que fornece uma conexão segura para os administradores acessarem os recursos implantados. O NSG do host bastião permite conexões apenas na porta TCP 3389 para RDP. Os clientes podem configurar ainda mais o host bastião para atender aos requisitos de proteção de sistemas da organização.

### <a name="virtual-network"></a>Rede virtual
A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: Esta solução implanta recursos em uma arquitetura com uma sub-rede separada e Web, sub-rede do banco de dados, sub-rede do Active Directory e sub-rede de gerenciamento em uma rede virtual. Sub-redes são separadas logicamente pelas regras do grupo de segurança de rede aplicadas a sub-redes individuais para restringir o tráfego entre as sub-redes para somente o que é necessário para a funcionalidade de gerenciamento e de sistema.

Confira a configuração dos [grupos de segurança de rede](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implantados com essa solução. Os clientes podem configurar grupos de segurança de rede editando o arquivo acima usando [esta documentação](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) como um guia.

Cada uma das camadas de rede tem um NSG (grupo de segurança de rede dedicado):
- 1 NSG para o Gateway de Aplicativo (LBNSG)
- 1 NSG para o host bastião (MGTNSG)
- 1 NSG para controladores de domínio primário e de backup (ADNSG)
- 1 NSG para SQL Servers (SQLNSG)
- 1 NSG para a camada da Web (WEBNSG)

**sub-redes**: Cada sub-rede está associada ao seu NSG correspondente.

### <a name="data-at-rest"></a>Dados em repouso

A arquitetura protege dados em repouso usando várias medidas de criptografia.

**Armazenamento do Azure**: Para atender aos requisitos de criptografia de dados em repouso, todas as contas de armazenamento usam [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**Microsoft SQL Server**: O SQL Server está configurado para usar dados [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), que executa criptografia e descriptografia de dados e arquivos de log em tempo real para proteger informações em repouso. O TDE fornece a garantia de que os dados armazenados não tenham ficado sujeitos a acesso não autorizado.

Os clientes também podem configurar as seguintes medidas de segurança do SQL Server:
-   A [autenticação e a autorização do AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades dos usuários de banco de dados e de outros serviços da Microsoft em uma única localização central.
-   A [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) controla os eventos de banco de dados e grava-os em um log de auditoria em uma conta de armazenamento do Azure.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem o acesso aos servidores do banco de dados até que as permissões apropriadas sejam concedidas. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.
-   A [Detecção de Ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite detectar e responder a possíveis ameaças conforme elas ocorrem, fornecendo alertas de segurança sobre atividades suspeitas no banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões anormais de acesso ao banco de dados.
-   As [colunas Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que dados confidenciais nunca sejam exibidos como texto não criptografado dentro do sistema do banco de dados. Após a habilitação da criptografia de dados, somente aplicativos cliente ou servidores de aplicativo com acesso às chaves poderão acessar dados de texto não criptografado.
-   A [Máscara de Dados Dinâmicos do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) pode ser usada após a implantação da arquitetura de referência. Os clientes precisarão ajustar as configurações da Máscara de Dados Dinâmicos para seguir o esquema de banco de dados que usam.

**Azure Disk Encryption**: O Azure Disk Encryption é usado para criptografar discos de máquinas virtuais IaaS Windows. O [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para o sistema operacional e os discos de dados. A solução é integrada ao Azure Key Vault para ajudar a controlar e a gerenciar as chaves de criptografia de disco.

### <a name="identity-management"></a>Gerenciamento de identidades

As tecnologias a seguir fornecem funcionalidades de gerenciamento de identidades no ambiente do Azure:
- O [Azure AD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft.
- A autenticação para um aplicativo Web implantado pelo cliente pode ser executada usando o Azure AD. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- O [RBAC (Controle De Acesso Baseado Em Função) do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) permite o gerenciamento de acesso detalhado para o Azure. O acesso à assinatura é limitado ao administrador da assinatura, e o acesso aos recursos pode ser limitado com base na função de usuário.
- Uma instância do Active Directory IaaS implantada fornece gerenciamento de identidade no nível do sistema operacional para máquinas virtuais IaaS implantadas.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: A solução usa o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. O Azure Key Vault ajuda a gerenciar as chaves de criptografia de disco e os segredos da máquina virtual de IaaS dessa arquitetura de referência.

**Gerenciamento de patches**: Máquinas virtuais do Windows implantadas por esta Automação de Segurança e Conformidade do Azure Blueprint são configuradas por padrão para receberem atualizações automáticas do serviço Windows Update. Essa solução também implanta a solução de Automação do Azure por meio da qual as implantações de atualização podem ser criadas para implantar patches nos servidores Windows, quando necessário.

**Proteção contra malware**: O [antimalware da Microsoft](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Máquinas Virtuais oferece uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando se sabe que software mal-intencionado ou indesejado tenta se instalar ou executar em máquinas virtuais protegidas.

**Gateway de Aplicativo**: A arquitetura reduz o risco de vulnerabilidades de segurança usando o Gateway de Aplicativo com WAF (firewall do aplicativo Web) e o conjunto de regras OWASP habilitado. Dentre outros recursos estão:

- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Habilitar [Descarregamento SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Desabilitar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall do aplicativo Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo WAF)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3.0

### <a name="business-continuity"></a>Continuidade dos negócios

**Alta disponibilidade**: Pelo menos uma máquina virtual fica disponível durante um evento de manutenção planejada ou não planejada, atendendo ao SLA do Azure de 99,95%. A solução implementa todas as máquinas virtuais da camadas da Web e da camada de dados em um [Conjunto de Disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Os conjuntos de disponibilidade fazem com que as máquinas virtuais sejam distribuídas entre vários clusters de hardware isolados para melhorar a disponibilidade. Além disso, essa solução implanta as máquinas virtuais do SQL Server em um conjunto de disponibilidade como um [Grupo de Disponibilidade AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). O recurso de grupo de disponibilidade AlwaysOn fornece recursos de alta disponibilidade e recuperação de desastre.

**Cofre dos Serviços de Recuperação**: o [Cofre dos Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) armazena dados de backup e protege todas as configurações das Máquinas Virtuais do Azure nesta arquitetura. Com um Cofre dos Serviços de Recuperação, os clientes podem restaurar arquivos e pastas de uma VM de IaaS sem restaurar a VM inteira, acelerando o tempo de restauração.

**Testemunha de Nuvem**: a [Testemunha de Nuvem](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) é um tipo de testemunha de quorum do cluster de failover no Windows Server 2016 que aproveita o Azure como o ponto de arbitragem. A Testemunha de Nuvem, como qualquer outra testemunha de quorum, obtém um voto e pode participar dos cálculos de quorum, mas usa o Armazenamento de Blobs do Azure padrão, disponível ao público. Isso elimina a sobrecarga adicional de manutenção de VMs hospedadas em uma nuvem pública.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

Os logs do Azure Monitor fornece registro em log extensivo de atividade do sistema e usuário, bem como a integridade do sistema. O [registra em log do Azure Monitor](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) solução coleta e analisa os dados gerados pelos recursos no Azure e ambientes locais.

- **Logs de atividade:**  Os [logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem insights sobre as operações executadas em recursos em uma assinatura. Os logs de atividade podem ajudar a determinar o iniciador, o horário da ocorrência e o status de uma operação.
- **Logs de diagnóstico:**  Os [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são todos os logs emitidos por todos os recursos. Esses logs são logs de eventos do sistema Windows, logs de armazenamento do Azure, logs de auditoria do Key Vault e logs de acesso e firewall do Gateway de Aplicativo.
- **Arquivamento em log**:  Todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, de até 730 dias, para atender aos requisitos de retenção específicos da organização. Esses logs conectem-se aos logs do Azure Monitor para processar, armazenar e relatórios de painel.

Além disso, as seguintes soluções de monitoramento são instaladas como parte dessa arquitetura. Observe que é responsabilidade do cliente configurar essas soluções para se alinhar aos controles de segurança do FedRAMP:
-   [Avaliação do AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de Verificação de Integridade do Active Directory avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Avaliação de Antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A solução de Antimalware fornece informações sobre malware, ameaças e status de proteção.
-   [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A solução de Automação do Azure armazena, executa e gerencia runbooks.
-   [Segurança e Auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): O painel de Segurança e Auditoria fornece insights de alto nível sobre o estado de segurança dos recursos, fornecendo métricas sobre domínios de segurança, problemas importantes, detecções, inteligência contra ameaças e consultas de segurança comuns.
-   [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de Verificação de Integridade do SQL avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Gerenciamento de Atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): A solução de Gerenciamento de Atualizações permite que os clientes gerenciem as atualizações de segurança do sistema operacional, incluindo o status das atualizações disponíveis e o processo de instalação das atualizações necessárias.
-   [Integridade do Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de Integridade do Agente informa quantos agentes são implantados e a distribuição geográfica deles, além de quantos agentes estão sem resposta e o número de agentes que estão enviando dados operacionais.
-   [Logs de Atividades do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução de Análise do Log de Atividades auxilia na análise dos logs de atividades do Azure em todas as assinaturas do Azure para um cliente.
-   [Controle de Alterações](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução de Controle de Alterações permite que os clientes facilmente identifiquem alterações no ambiente.

**Azure Monitor**
[O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a rastrear o desempenho, manter a segurança e identificar tendências, permitindo que as organizações auditem, criem alertas e arquivem dados, incluindo o rastreamento de chamadas à API nos recursos do Azure dos clientes.

## <a name="threat-model"></a>Modelo de ameaça
O diagrama de fluxo de dados dessa arquitetura de referência está disponível para [download](https://aka.ms/fedrampWAdfd) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entenderem os pontos de risco em potencial na infraestrutura do sistema ao fazer modificações.

![Aplicativo Web de IaaS para o modelo contra ameaças do FedRAMP](images/fedramp-iaaswa-threat-model.png?raw=true "Aplicativo Web de IaaS para o modelo de risco do FedRAMP")

## <a name="compliance-documentation"></a>Documentação de conformidade

O [Blueprint de Segurança e Conformidade do Azure – Matriz de Responsabilidades do Cliente do FedRAMP High](https://aka.ms/blueprinthighcrm) lista todos os controles de segurança necessários para a linha de base do FedRAMP High. A matriz indica se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou é compartilhada pelos dois.

O [Blueprint de Segurança e Conformidade do Azure – Matriz de Implantação de Controle de Aplicativo Web de IaaS do FedRAMP High](https://aka.ms/blueprintwacim) lista todos os controles de segurança necessários para a linha de base do FedRAMP High. A matriz fornece informações sobre quais controles estão cobertos pela arquitetura do aplicativo Web de IaaS, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada controle coberto.

## <a name="deploy-the-solution"></a>Implantar a solução

Esta Automação de Segurança e Conformidade do Azure Blueprint é composta de arquivos de configuração JSON e scripts do PowerShell tratados pelo serviço de API do Azure Resource Manager para implantar recursos no Azure. As instruções detalhadas de implantação estão disponíveis [aqui](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> A solução é implantada no Azure Governamental.

#### <a name="quickstart"></a>Início rápido
1. Clone ou baixe [este](https://aka.ms/fedrampblueprintrepo) repositório GitHub na sua estação de trabalho local.

2. Execute o script de pré-implantação do PowerShell: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Clique no botão abaixo, entre no portal do Azure, insira os parâmetros de modelo do ARM necessários e clique em **Comprar**.

    [![Implantar no Azure](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel de VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer uma conexão segura com os recursos implantados como parte dessa arquitetura de referência de aplicativo Web de IaaS. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Implementando um túnel de VPN seguro com o Azure, é possível criar uma conexão privada virtual entre uma rede local e uma Rede Virtual do Azure. Essa conexão ocorre pela Internet e permite que os clientes enviem informações por "túnel” com segurança usando um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. O [modo de túnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego do túnel de VPN passa pela Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link de WAN dedicado entre o Azure e a instalação local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não ocorrem pela Internet, elas oferecem mais confiabilidade, mais velocidade, latências mais baixas e maior segurança que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicações do cliente, os dados não passam pela Internet e, portanto, não estão expostos a ela.

Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) práticas recomendadas para a implementação de uma rede híbrida segura que estende uma rede local para o Azure.

## <a name="disclaimer"></a>Isenção de responsabilidade

- Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.  
- Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.  
- Os clientes podem copiar e usar este documento para fins de consulta interna.  
- Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.  
- Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
- Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
