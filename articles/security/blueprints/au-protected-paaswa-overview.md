---
title: Blueprint de Segurança e Conformidade do Azure - Aplicativo Web de PaaS PROTEGIDO para Austrália
description: Blueprint de Segurança e Conformidade do Azure - Aplicativo Web de PaaS PROTEGIDO para Austrália
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: c17f16ce796c9f296facd69c18de4effc7ff5258
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610157"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>Blueprint de Segurança e Conformidade do Azure - Aplicativo Web de PaaS PROTEGIDO para Austrália

## <a name="overview"></a>Visão geral

Este Blueprint de Segurança e Conformidade do Azure fornece diretrizes para a implantação de um ambiente de PaaS (plataforma como serviço) adequado para a coleta, armazenamento e recuperação de dados governamentais PROTEGIDOS para AU que estejam em conformidade com os objetivos do ISM (Manual de Segurança da Informação) do Governo Australiano produzido pela ASD (Australian Signals Directorate). Esse blueprint apresenta uma arquitetura de referência comum e ajuda a demonstrar o tratamento adequado de dados governamentais confidenciais em um ambiente de várias camadas compatível e seguro.

Essa arquitetura de referência, o guia de implementação e o modelo de risco fornecem uma base para os clientes realizarem seus próprios processos de credenciamento do sistema e planejamento, ajudando os clientes a implantar cargas de trabalho no Azure em conformidade com a ASD. Os clientes podem escolher implementar um Gateway de VPN do Azure ou um ExpressRoute para usar serviços federados e integrar recursos locais com recursos do Azure. Os clientes devem considerar as implicações de segurança do uso de recursos locais. Configuração adicional é necessária para atender a todos os requisitos, pois podem variar com base nos detalhes da implementação de cada cliente.

O cumprimento da conformidade com a ASD exige que um Avaliador Registrado de Segurança da Informação audite o sistema. Os clientes são responsáveis por realizar as devidas avaliações de segurança e conformidade de qualquer solução criada usando essa arquitetura, uma vez que os requisitos podem variar com base nas particularidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
Essa solução fornece uma arquitetura de referência para um aplicativo Web PaaS com um back-end do Banco de Dados SQL do Azure. O aplicativo Web é hospedado em um Ambiente do Serviço de Aplicativo do Azure, que é um ambiente privado dedicado em um datacenter do Azure. O ambiente balanceia a carga de tráfego do aplicativo Web entre as máquinas virtuais gerenciadas pelo Azure. Todas as conexões de aplicativos Web exigem TLS com uma versão mínima de 1.2. Essa arquitetura também inclui os grupos de segurança de rede, um Gateway de Aplicativo, o DNS do Azure e o balanceador de carga.

A arquitetura pode fornecer um ambiente híbrido seguro que estende uma rede local para o Azure, permitindo que cargas de trabalho baseadas na Web sejam acessadas com segurança por usuários corporativos de uma rede local privada da organização ou da Internet. Para soluções locais, o cliente é responsabilizado e responsável por todos os aspectos de segurança, operações e conformidade.

Os recursos do Azure incluídos nessa solução podem conectar uma rede local ou instalações de colocação de datacenter (p. ex., CDA em Canberra) por meio de VPN IPSec usando Gateway de VPN ou por meio do ExpressRoute. A decisão de utilizar uma VPN deve ser feita com a classificação dos dados transmitidos e o caminho da rede em mente. Os clientes executando cargas de trabalho críticas e em larga escala com requisitos de big data devem considerar uma arquitetura de rede híbrida usando o ExpressRoute para conectividade de rede privada para serviços do Azure. Consulte a seção de [diretrizes e recomendações](#guidance-and-recommendations) para obter detalhes adicionais sobre os mecanismos de conexão do Azure.

A federação com Azure Active Directory deve ser usada para permitir que os usuários autentiquem-se usando credenciais locais e acessem todos os recursos na nuvem usando uma infraestrutura de Serviços de Federação do Active Directory (AD FS) local. Os Serviços de Federação do Active Directory (AD FS) podem fornecer recursos seguros e simplificados de federação de identidade e de logon único na Web para esse ambiente híbrido. Consulte a seção de [diretrizes e recomendações](#guidance-and-recommendations) para obter mais detalhes sobre a configuração do Azure Active Directory.

A solução usa contas de Armazenamento do Azure, que os clientes podem configurar para usar a Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O Azure armazena três cópias de dados na região selecionada do cliente para resiliência. As regiões do Azure são implantadas em pares de regiões resilientes e o armazenamento com redundância geográfica garante que os dados sejam replicados para a segunda região também com três cópias. Isso evita um evento adverso no local dos dados primários do cliente, o que resulta em uma perda de dados.

Para maior segurança, todos os recursos do Azure nessa solução são gerenciados como um grupo de recursos por meio do Azure Resource Manager. O controle de acesso baseado em função do Azure Active Directory é usado para controlar o acesso aos recursos implantados às chaves no Azure Key Vault. A integridade do sistema é monitorada por meio da Central de Segurança do Azure e do Azure Monitor. Os clientes configuram os dois serviços de monitoramento para capturar logs e exibir a integridade do sistema em um único painel facilmente navegável. O Gateway de Aplicativo Azure é configurado como um firewall no modo de prevenção e não permite tráfego que não seja TLS v1.2 ou superior. A solução usa o Ambiente do Serviço de Aplicativo Azure v2 para isolar a camada da Web em um ambiente de vários locatários.

![Arquitetura de referência do Aplicativo Web de PaaS PROTEGIDO para AU](images/au-protected-paaswa-architecture.png?raw=true "Diagrama da arquitetura de referência do Aplicativo Web de PaaS PROTEGIDO para AU")

A solução usa os serviços do Azure a seguir. Mais detalhes estão na seção [arquitetura de implantação](#deployment-architecture).

- Gateway de Aplicativo
    - Firewall do aplicativo Web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP
        - Porta do ouvinte: 443
- Application Insights
- Azure Active Directory
- Ambiente do Serviço de Aplicativo Azure v2
- Automação do Azure
- DNS do Azure
- Cofre da Chave do Azure
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Central de Segurança do Azure
- Banco de Dados SQL do Azure
- Armazenamento do Azure
- Logs do Azure Monitor
- Rede Virtual do Azure
    - (1) /16 rede
    - (4) /24 redes
    - Grupos de segurança de rede
- Grupos de segurança de rede
- Cofre dos Serviços de Recuperação
- Aplicativo Web do Azure

Este Blueprint contém Serviços do Azure que não foram certificados para uso na classificação Protegido pelo ACSC (Centro de Segurança Cibernética Australiano). A Microsoft recomenda que os clientes revisem a segurança publicada e os relatórios de auditoria relacionados a esses Serviços do Azure e usem a estrutura de gerenciamento de riscos para determinar se o Serviço do Azure é adequado para o credenciamento interno e o uso na classificação Protegido.

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir fornece detalhes sobre os elementos de implantação e implementação.

**Azure Resource Manager**: O [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permite que os clientes trabalhem com os recursos da solução como um grupo. Os clientes podem implantar, atualizar ou excluir todos os recursos da solução em uma única operação coordenada. Os clientes usam um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. O Resource Manager fornece recursos de segurança, auditoria e marcação para ajudar os clientes a gerenciar seus recursos após a implantação.

**Host bastião**: O host bastião é o único ponto de entrada que permite que os usuários acessem os recursos implantados nesse ambiente. O host bastião fornece uma conexão segura com os recursos implantados, permitindo apenas tráfego remoto de endereços IP públicos em uma lista de segurança. Para permitir o tráfego RDP (área de trabalho remota), a origem do tráfego precisa ser definida no grupo de segurança de rede.

Essa solução cria uma máquina virtual como um host bastião ingressado em domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Extensão de Diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usando o Azure Key Vault
-   Uma [política de desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos de máquina virtual quando eles não estiverem em uso

**Ambiente do Serviço de Aplicativo v2**: O [Ambiente do Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/environment/intro) é um recurso do Serviço de Aplicativo que fornece um ambiente totalmente isolado e dedicado para a execução segura de aplicativos de Serviço de Aplicativo em grande escala.

Os ambientes do Serviço de Aplicativo são isolados para executar somente aplicativos de um único cliente e sempre são implantados em uma rede virtual. Os clientes têm um controle refinado sobre o tráfego de entrada e saída da rede de aplicativos, e os aplicativos podem estabelecer conexões seguras de alta velocidade por meio de redes virtuais com recursos corporativos locais.

O uso de Ambientes do Serviço de Aplicativo para essa arquitetura permite os seguintes controles/configurações:

- Ambientes do Serviço de Aplicativo devem ser configurados para usar o plano de serviço isolado
    - Configurar diferentes Ambientes do Serviço de Aplicativo para aplicativos em diferentes classificações
- Host dentro de uma Rede Virtual do Azure protegida e regras de segurança de rede
- Ambientes do Serviço de Aplicativo configurados com um certificado de balanceador de carga interno autoassinado para comunicação HTTPS. Como melhor prática, a Microsoft recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.
- [Modo de balanceamento de carga interno](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (modo 3)
- Desabilitar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Alterar a [criptografia TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Controlar [portas N/W de tráfego de entrada](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Firewall do aplicativo Web – restringir dados](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Permitir o [Tráfego de Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Aplicativo Web do Azure**: O [Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/) permite que os clientes criem e hospedem aplicativos Web na linguagem de programação de sua escolha sem gerenciar a infraestrutura. Ele oferece dimensionamento automático e alta disponibilidade, compatível com Windows e Linux, e permite implantações automatizadas do GitHub, Azure DevOps Services ou qualquer repositório Git.

### <a name="virtual-network"></a>Rede Virtual
A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: Os [Grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm listas de controle de acesso que permitem ou negam tráfego dentro de uma rede virtual. Os grupos de segurança de rede podem ser usados para proteger o tráfego no nível da sub-rede ou da máquina virtual individual. Existem os seguintes grupos de segurança de rede:
- 1 Grupo de segurança de rede do Gateway de Aplicativo
- 1 Grupo de segurança de rede do Ambiente do Serviço de Aplicativo
- 1 Grupo de segurança de rede do Banco de Dados SQL do Azure
- 1 grupo de segurança de rede para host bastião

Cada um dos grupos de segurança de rede têm portas e protocolos específicos abertos para que a solução possa funcionar corretamente e com segurança. Além disso, as configurações a seguir são habilitadas para cada grupo de segurança de rede:

  - [Eventos e logs de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
  - Os logs do Azure Monitor está conectado ao [o diagnóstico do grupo de segurança de rede](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**sub-redes**: Cada sub-rede está associada ao seu grupo de segurança de rede correspondente.

**DNS do Azure**: O sistema de nomes de domínio, ou DNS, é responsável por converter (ou seja, resolver) um nome do site ou serviço para seu endereço IP. O [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de hospedagem para domínios DNS que fornece a resolução de nomes usando a infraestrutura do Azure. Ao hospedar domínios no Azure, os usuários podem gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. O DNS do Azure também dá suporte a domínios DNS privados.

**Azure Load Balancer**: O [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) permite que os clientes dimensionem os aplicativos e criem alta disponibilidade para serviços. O Azure Load Balancer é compatível tanto com cenários de entrada como de saída e fornece latência baixa, taxa de transferência alta e escala verticalmente a milhões de fluxos para todos os aplicativos TCP e UDP.

### <a name="data-in-transit"></a>Dados em trânsito
O Azure criptografa todas as comunicações entre datacenters do Azure por padrão. 

Para dados Protegidos em trânsito de redes de propriedade do cliente, a Arquitetura usa o Azure, a Internet ou o ExpressRoute com um Gateway de VPN configurado com IPSEC.

Além disso, todas as transações para o Azure por meio do portal de gerenciamento do Azure ocorrerem via HTTPS utilizando v TLS 1.2.

### <a name="data-at-rest"></a>Dados em repouso
A arquitetura protege dados em repouso usando criptografia, auditoria de banco de dados e outras medidas.

**Armazenamento do Azure**: Para atender aos requisitos de criptografia de dados em repouso, o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption). Isso ajuda a proteger e preservar os dados em suporte aos compromissos de segurança organizacional e aos requisitos de conformidade definidos pelo ISM do Governo Australiano.

**Azure Disk Encryption**: O [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utiliza o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução é integrada ao Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de Dados SQL do Azure**: A instância do Banco de Dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:
-   A [autenticação e a autorização do Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) permitem o gerenciamento de identidade dos usuários de banco de dados e de outros serviços da Microsoft em uma única localização central.
-   A [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) controla os eventos de banco de dados e grava-os em um log de auditoria em uma conta de armazenamento do Azure.
-   O Banco de Dados SQL do Azure está configurado para usar a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real do banco de dados, dos backups associados e dos arquivos de log de transações para proteger as informações em repouso. A Transparent Data Encryption oferece a garantia de que os dados armazenados não fiquem sujeitos a acesso não autorizado.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem o acesso aos servidores do banco de dados até que as permissões apropriadas sejam concedidas. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.
-   A [Detecção de Ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite detectar e responder a possíveis ameaças conforme elas ocorrem, fornecendo alertas de segurança sobre atividades suspeitas no banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões anormais de acesso ao banco de dados. A Detecção de Ameaças de SQL integra alertas à [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/), que fornece detalhes de atividades suspeitas e ação recomendada sobre como investigar e atenuar a ameaça.
-   As [Colunas Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que dados confidenciais nunca sejam exibidos como texto não criptografado dentro do sistema do banco de dados. Após a habilitação da criptografia de dados, somente aplicativos cliente ou servidores de aplicativo com acesso às chaves poderão acessar dados de texto não criptografado.
- A [Máscara de Dados Dinâmicos do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos não privilegiados. A Máscara de Dados Dinâmicos pode descobrir automaticamente dados potencialmente confidenciais e sugerir as máscaras apropriadas a serem aplicadas. Isso ajuda a reduzir o acesso de forma que os dados confidenciais não saiam do banco de dados por meio de acesso não autorizado. Os clientes precisarão ajustar as configurações da Máscara de Dados Dinâmicos para seguir o esquema de banco de dados que usam.

### <a name="identity-management"></a>Gerenciamento de identidades
Os clientes podem utilizar os Serviços Federados do Active Directory local para federar com [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), que é o serviço de gerenciamento de identidades e diretório baseado em nuvem multilocatário da Microsoft. O [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) integra diretórios locais com Azure Active Directory. Todos os usuários dessa solução requerem contas do Azure Active Directory, incluindo usuários que acessam o Banco de Dados SQL do Azure. Com logon federado, os usuários podem entrar no Azure Active Directory e se autenticarem nos recursos do Azure usando credenciais locais.

Além disso, os seguintes recursos do Azure Active Directory ajudam a gerenciar o acesso aos dados no ambiente do Azure:
- A autenticação no aplicativo é executada usando o Azure Active Directory. Para obter mais informações, confira [Integrando aplicativos com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a criptografia da coluna do banco de dados utiliza o Azure Active Directory para autenticar o aplicativo no Banco de Dados SQL do Azure. Para obter mais informações, veja como [proteger dados confidenciais no Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- O [controle de acesso baseado em função do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso de que os usuários precisam para realizar seus trabalhos. Em vez de conceder permissão irrestrita aos recursos do Azure a todos os usuários, os administradores podem permitir apenas determinadas ações para acessar os dados. O acesso à assinatura é limitado ao administrador da assinatura.
- O [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permite que os clientes minimizem o número de usuários que tenham acesso a determinadas informações. Os administradores podem usar o Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar identidades privilegiadas e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor o acesso administrativo sob demanda Just-In-Time quando necessário.
- O [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades da organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas a essas identidades e investiga incidentes suspeitos e toma a medida apropriada para resolvê-los.

**Autenticação Multifator do Azure**: Para proteger identidades, a autenticação multifator deve ser implementada. A [Autenticação Multifator do Microsoft Azure](https://azure.microsoft.com/services/multi-factor-authentication/) é uma solução fácil de usar, escalonável e confiável que fornece um segundo método de autenticação para proteger os usuários. A Autenticação Multifator do Microsoft Azure usa o poder da nuvem e integra-se ao Active Directory local e aos aplicativos personalizados. Essa proteção é estendida a cenários críticos e de alto volume.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: A solução usa o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Os recursos do Azure Key Vault a seguir ajudam os clientes a proteger os dados:
- Políticas de acesso avançadas são configuradas com base na necessidade.
- As políticas de acesso do Key Vault são definidas com o mínimo de permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault têm datas de validade.
- Todas as chaves no Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma Chave RSA de 2048 bits protegida para módulo de segurança de hardware.
- Todos os usuários e identidades recebem permissões mínimas necessárias usando o controle de acesso baseado em função.
- Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
- As operações criptográficas permitidas para chaves são restritas às necessárias.

**Central de Segurança do Azure**: Com a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques de forma centralizada. Além disso, a Central de Segurança do Azure acessa as configurações existentes de serviços do Azure para fornecer recomendações de serviço e configuração, a fim de ajudar a melhorar a postura de segurança e proteger os dados.

A Central de Segurança do Azure usa uma variedade de funcionalidades de detecção para alertar os clientes de ataques potenciais direcionados a seus ambientes. Esses alertas contêm informações valiosas sobre o que disparou o alerta, os recursos de destino e a origem do ataque. A Central de Segurança do Azure tem um conjunto de [alertas de segurança predefinidos](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que são disparados em caso de ameaça ou atividade suspeita. As [regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) na Central de Segurança do Azure permitem que os clientes definam novos alertas de segurança com base nos dados já coletados do ambiente.

A Central de Segurança do Azure fornece alertas de segurança e incidentes priorizados, simplificando a descoberta e a resolução por parte dos clientes de possíveis problemas de segurança. Um [relatório de inteligência contra ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada, a fim de ajudar as equipes de resposta a incidentes a investigar e corrigir as ameaças.

**Gateway de Aplicativo**: A arquitetura reduz o risco de vulnerabilidades de segurança usando o Gateway de Aplicativo com WAF (firewall do aplicativo Web) e o conjunto de regras OWASP habilitado. Dentre outros recursos estão:

- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Habilitar [Descarregamento SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Desabilitar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall do aplicativo Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo de prevenção)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3.0
- [Habilitar o registro em log de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Investigações de integridade personalizadas](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center) e o [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecer proteção e notificações adicionais. A Central de Segurança do Azure também fornece um sistema de reputação.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

Os serviços do Azure registram em log de forma extensiva as atividades do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividades**: Os [logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem insights sobre as operações executadas em recursos em uma assinatura. Os logs de atividade podem ajudar a determinar o iniciador, o horário da ocorrência e o status de uma operação.
- **Logs de diagnóstico**: Os [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registros emitidos por todos os recursos. Esses logs são logs do sistema de eventos do Windows, logs de Armazenamento do Azure, logs de auditoria do Key Vault e logs de acesso e firewall do Gateway de Aplicativo. Todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, de até 730 dias, para atender aos requisitos de retenção específicos da organização.

**Logs do Azure Monitor**: Esses logs são consolidados [registra em log do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e emissão de relatórios do painel. Depois de coletados, os dados são organizados em tabelas separadas para cada tipo de dados, o que permite que todos os dados sejam analisados em conjunto, independentemente de sua fonte. Além disso, a Central de segurança do Azure integra-se com os logs do Azure Monitor permitindo que os clientes usem Kusto consultas para acessar seus dados de evento de segurança e combiná-lo com dados de outros serviços.

O Azure a seguir [soluções de monitoramento](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) são incluídos como parte dessa arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de Verificação de Integridade do Active Directory avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de Verificação de Integridade do SQL avalia o risco e a integridade dos ambientes do servidor em intervalos regulares e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de Integridade do Agente informa quantos agentes são implantados e sua distribuição geográfica, além de quantos agentes sem resposta e o número de agentes que estão enviando dados operacionais.
-   [Análise do Log de Atividades](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução de Análise do Log de Atividades auxilia na análise dos logs de atividades do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: A [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nessa solução, os runbooks ajudam a coletar logs do Banco de Dados SQL do Azure. A solução [Controle de Alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) da Automação permite que os clientes identifiquem com facilidade as alterações no ambiente.

**Azure Monitor**: O [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações façam auditoria, criem alertas e arquivem dados, incluindo o rastreamento de chamadas à API nos recursos do Azure.

Observador de Rede do Azure: O [Observador de Rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) fornece ferramentas para monitorar, diagnosticar, exibir métricas e habilitar ou desabilitar os registros de recursos em uma rede virtual do Azure.  As entidades de comunidade devem implementar logs de fluxo do Observador de Rede para NSGs e Máquinas Virtuais. Esses logs devem ser armazenados em uma conta de armazenamento dedicada, na qual apenas os logs de segurança são armazenados e o acesso à conta de armazenamento deve ser protegida com Controles de Acesso Baseado em Função.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados dessa arquitetura de referência está disponível para [download](https://aka.ms/au-protected-paaswa-tm) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entenderem os pontos de risco em potencial na infraestrutura do sistema ao fazer modificações.

![Modelo de risco do Aplicativo Web de PaaS PROTEGIDO para AU](images/au-protected-paaswa-threat-model.png?raw=true "Diagrama do modelo de risco do Aplicativo Web de PaaS PROTEGIDO para AU")

## <a name="compliance-documentation"></a>Documentação de conformidade
Esta documentação de conformidade é produzida pela Microsoft com base em plataformas e serviços disponibilizados pela Microsoft. Devido à grande variedade de implantações de clientes, esta documentação fornece uma abordagem generalizada para uma solução hospedada somente no ambiente do Azure. Os clientes podem identificar e usar produtos e serviços alternativos com base em seus próprios ambientes operacionais e resultados dos negócios. Clientes que escolherem utilizar recursos locais deverão atender à segurança e às operações desses recursos locais. A solução documentada pode ser personalizada pelos clientes para atender aos requisitos locais e de segurança específicos.

O [Blueprint de Segurança e Conformidade do Azure - Matriz de responsabilidade do cliente para aplicativo PROTEGIDO para AU](https://aka.ms/au-protected-crm) lista todos os controles de segurança exigidos pelo aplicativo PROTEGIDO para AU. Essa matriz detalha se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou compartilhada entre os dois.

O [Blueprint de Segurança e Conformidade do Azure - Matriz de implementação do aplicativo Web de PaaS PROTEGIDO para AU](https://aka.ms/au-protected-paaswa-cim) fornece informações sobre quais controles de aplicativo PROTEGIDO para AU são abordados pela arquitetura do aplicativo Web de PaaS, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada controle coberto.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute

Para informações classificadas, um túnel VPN IPSec seguro precisa ser configurado para estabelecer uma conexão segura com os recursos implantados como parte dessa arquitetura de referência do aplicativo Web de IaaS. Configurando apropriadamente uma VPN IPSec, os clientes podem adicionar uma camada de proteção para dados em trânsito.

Ao implementar um túnel VPN IPSec seguro com Azure, uma conexão privada virtual entre uma rede local e uma rede virtual do Azure poderá ser criada. Essa conexão pode ocorrer pela Internet e permite que os clientes "encapsulem" informações com segurança dentro de um vínculo criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. 

Como o tráfego dentro do túnel VPN atravessa a Internet com uma VPN site a site, a Microsoft oferece uma opção de conexão privada. O Azure ExpressRoute é um vínculo dedicado entre o Azure e uma localização local ou um provedor de hospedagem do Exchange, e é considerado uma rede privada. Como as conexões do ExpressRoute não passam pela Internet, essas conexões oferecem mais confiabilidade, velocidades mais rápidas e latências mais baixas do que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicações do cliente, os dados não passam pela Internet e, portanto, não estão expostos a ela.

Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) práticas recomendadas para a implementação de uma rede híbrida segura que estende uma rede local para o Azure. 

Para ajudar a proteger dados classificados, seja usando a Internet ou o Azure ExpressRoute, os clientes devem configurar a VPN IPSec aplicando as seguintes configurações:

•   O iniciador VPN do cliente deve ser configurado para um tempo de vida de SA não superior a 14400 segundos.
•   O iniciador VPN do cliente deve desabilitar o modo agressivo IKEv1.
•   O iniciador VPN do cliente deve configurar PFS.
•   O iniciador VPN do cliente deve configurar o uso de HMAC-SHA256 ou superior.

As opções de configuração para dispositivos VPN e parâmetros IPSec/IKE estão [disponíveis](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) para revisão.

### <a name="azure-active-directory-setup"></a>Configuração do Azure Active Directory
O [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para gerenciar a implantação e provisionar acesso para pessoas que interagem com o ambiente. Um Windows Server Active Directory existente pode ser integrado ao Azure Active Directory com [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express).

Além disso, o [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) permite que os clientes configurem a federação com [Serviços de Federação do Active Directory (AD FS)]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) e Azure Active Directory local. Com logon federado, os clientes podem permitir que os usuários entrem nos serviços baseados no Azure Active Directory com suas senhas locais e sem a necessidade de inserir as senhas novamente enquanto estiverem na rede corporativa. Usando a opção de federação com Serviços de Federação do Active Directory (AD FS), é possível implantar uma nova instalação dos Serviços de Federação do Active Directory (AD FS) ou especificar uma instalação existente em um farm do Windows Server 2012 R2.

Para impedir que dados classificados sejam sincronizados com Active Directory do Azure, os clientes podem restringir os atributos que são replicados para o Azure Active Directory, aplicando as seguintes configurações no Azure Active Directory Connect:

- [Habilitar filtragem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [Desabilitar sincronização de hash de senha](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
-   [Desabilitar write-back de senha](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [Desabilitar write-back de dispositivo](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
-   Deixe as configurações padrão para [evitar exclusões acidentais](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) e [atualização automática](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)


## <a name="disclaimer"></a>Isenção de responsabilidade

 - Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de consulta interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.
 - Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
 - Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
