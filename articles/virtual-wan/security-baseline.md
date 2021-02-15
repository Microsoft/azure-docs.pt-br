---
title: Linha de base de segurança do Azure para WAN virtual
description: A linha de base de segurança de WAN virtual fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a0acbe84a98bc63e5806d25241811c0a7591b897
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98872614"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Linha de base de segurança do Azure para WAN virtual

Essa linha de base de segurança aplica diretrizes da [versão 2,0 do benchmark de segurança do Azure](../security/benchmarks/overview.md) para Microsoft Azure Wan virtual. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis à WAN virtual. Os **controles** não aplicáveis à WAN virtual foram excluídos.

Para ver como a WAN virtual é totalmente mapeada para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança de WAN virtual completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementar a segurança para tráfego interno

**Diretrizes**: Microsoft Azure Wan virtual fornece recursos de roteamento personalizados e oferece criptografia para o tráfego de ExpressRoute. Todo o gerenciamento de rota é fornecido pelo roteador do hub virtual, que também permite a conectividade de trânsito entre redes virtuais. Criptografar o tráfego do ExpressRoute com a WAN virtual fornece um trânsito criptografado entre as redes locais e as redes virtuais do Azure no ExpressRoute, sem passar pela Internet pública ou usar endereços IP públicos. 

- [Criptografia de tráfego de ExpressRoute](virtual-wan-about.md#encryption)

- [Usar o link privado na WAN virtual](howto-private-link.md)

- [Cenários de roteamento personalizado](scenario-any-to-any.md)

- [Documentação da tabela de rotas personalizadas](how-to-virtual-hub-routing.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ns-2-connect-private-networks-together"></a>NS-2: conectar redes privadas juntas 

**Diretrizes**: o Microsoft Azure ExpressRoute oferece conectividade privada à WAN virtual do Azure. Como as conexões do ExpressRoute não passam pela Internet pública, o ExpressRoute oferece mais confiabilidade, velocidades mais rápidas e latências menores do que as conexões de Internet típicas. Você também pode usar uma rede virtual privada para se conectar ao Azure por meio de VPN S2S (site a site) ou VPN de ponto a site (P2S).

- [ExpressRoute na WAN virtual](virtual-wan-expressroute-portal.md)

- [Visão geral de VPN site a site](virtual-wan-site-to-site-portal.md)

- [Visão geral de VPN ponto a site](virtual-wan-point-to-site-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteger aplicativos e serviços de ataques de rede externa

**Diretrizes**: a WAN virtual não expõe nenhum ponto de extremidade a redes externas que exigem que elas sejam protegidas com proteções de rede convencionais. Você está livre para proteger recursos em redes virtuais spoke (qualquer rede virtual conectada a um hub virtual) usando os serviços de proteção de rede virtual. 

Use o Firewall do Azure para proteger aplicativos e serviços contra tráfego potencialmente mal-intencionado da Internet e de outros locais externos. 

Escolha a proteção contra DDoS fornecida pelo Azure para proteger seus ativos contra ataques em suas redes virtuais do Azure. Use a central de segurança do Azure para detectar riscos de configurações incorretas relacionadas aos seus recursos relacionados à rede.

- [Documentação do firewall do Azure](../firewall/index.yml)

- [Gerenciar a Proteção contra DDoS do Azure Standard usando o portal do Azure](../ddos-protection/manage-ddos-protection.md) 

- [Recomendações da Central de Segurança do Azure](../security-center/recommendations-reference.md#recs-networking)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: implantar sistemas de detecção de intrusões/prevenção de intrusão (IDS/IPS)

**Diretrizes**: Wan virtual é um serviço gerenciado pela Microsoft. Ele não oferece a detecção de intrusão nativa ou recursos de prevenção de intrusão. No entanto, há recursos de segurança fornecidos à WAN virtual por meio do firewall do Azure para habilitar um ponto unificado de controle de diretiva. Você pode criar uma política de firewall do Azure e vincular a política a um hub de WAN virtual para permitir que o Hub de WAN virtual existente funcione como um hub virtual protegido, com os recursos de firewall do Azure necessários implantados.

- [Configuração do Firewall do Azure em um hub de WAN Virtual](howto-firewall.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: simplificar as regras de segurança de rede

**Diretrizes**: Simplifique as regras de segurança de rede aproveitando as marcas de serviço de rede virtual para definir controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. As marcas de serviço podem ser usadas no lugar de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço no campo de origem ou de destino de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

- [Entender e usar marcas de serviço](../virtual-network/service-tags-overview.md)

- [Entender e usar grupos de segurança de aplicativos](../virtual-network/network-security-groups-overview.md#application-security-groups)

- [Documentação do firewall do Azure](../firewall/index.yml)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: serviço de nomes de domínio seguro (DNS)

**Diretrizes**: os recursos de DNS seguros são fornecidos à WAN virtual com o Firewall do Azure. Configure o Firewall do Azure para atuar como um proxy DNS que se torna um intermediário para solicitações DNS de máquinas virtuais do cliente para um servidor DNS. Para configurações personalizadas do servidor DNS, habilite o proxy DNS para evitar uma incompatibilidade de resolução DNS e habilite a filtragem de nome de domínio totalmente qualificado nas regras de rede. 

- [Documentação do firewall do Azure](../firewall/index.yml)

- [Configurações de DNS do firewall do Azure](../firewall/dns-settings.md)

- [Usar o Firewall do Azure como um encaminhador DNS com o link privado](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="identity-management"></a>Gerenciamento de Identidades

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de identidades](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: padronizar o Azure Active Directory como o sistema central de identidade e autenticação

**Diretrizes**: Azure Active Directory (Azure AD) é o serviço de gerenciamento de identidade e acesso padrão para os serviços do Azure. incluindo WAN virtual. Padronize o Azure AD para controlar a identidade e o gerenciamento de acesso de sua organização no:

- Microsoft Cloud recursos, como o portal do Azure, o armazenamento do Azure, as máquinas virtuais do Azure (Linux e Windows), Azure Key Vault, PaaS (plataforma como serviço) e aplicativos SaaS (software como serviço).
- Os recursos da sua organização, como aplicativos no Azure ou seus recursos de rede corporativa

Proteja o Azure AD como uma prioridade alta na prática de segurança de nuvem de sua organização. Avalie a sua postura de identidade e segurança com o recurso de Pontuação de segurança da central de segurança para medir a precisão de sua configuração com as práticas recomendadas da Microsoft. Conforme necessário, implemente as recomendações de práticas recomendadas da Microsoft para aprimoramentos na sua postura de segurança.

O AD do Azure também dá suporte a identidades externas, que permitem aos usuários sem um conta Microsoft entrar em seus aplicativos e recursos com sua identidade externa. 

Examine as informações sobre como usar o Azure AD em cenários de VPN ponto a site nos links referenciados.

- [Criar um locatário Azure Active Directory (AD) para conexões de protocolo P2S OpenVPN](openvpn-azure-ad-tenant-multi-app.md)

- [Configurar a autenticação de Azure Active Directory para VPN de usuário](virtual-wan-point-to-site-azure-ad.md)

- [Locatários no Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usar controles de autenticação forte para todo o acesso baseado no Azure Active Directory

**Diretrizes**: atualmente, a autenticação do Azure Active Directory (Azure AD) é fornecida por meio da integração com VPN ponto a site da WAN virtual.

Azure Active Directory (AD do Azure) é o serviço de gerenciamento de identidade e acesso padrão para os serviços do Azure. O Azure AD dá suporte a controles de autenticação fortes com autenticação multifator e métodos fortes sem senha.

O Azure AD recomenda o seguinte para controles de autenticação fortes:

- Autenticação multifator – habilite a autenticação multifator do Azure AD e siga as recomendações de gerenciamento de identidade e acesso na central de segurança do Azure para obter as práticas recomendadas de segurança. Impor a autenticação multifator em todos, Selecionar usuários ou em nível por usuário com base em condições de entrada e fatores de risco

- Autenticação com senha – três opções de autenticação com senha estão disponíveis. Eles incluem o Windows Hello para empresas, Microsoft Authenticator aplicativo e métodos de autenticação local, como cartões inteligentes

Verifique se o nível mais alto do método de autenticação forte é usado para administradores e usuários com privilégios, seguido por uma distribuição de uma política de autenticação forte para outros usuários.

- [Como habilitar a MFA no VPN P2S para WAN virtual](openvpn-azure-ad-mfa.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: restringir o acesso aos recursos do Azure com base em condições

**Diretrizes**: habilitar a autenticação multifator do Azure Active Directory (Azure AD) para usuários VPN (ponto a site) usando a autenticação do Azure AD. Configure a autenticação multifator por usuário ou aproveite a autenticação multifator com acesso condicional. O acesso condicional permite um controle mais refinado sobre como um segundo fator deve ser promovido. Ele pode permitir a atribuição de autenticação multifator somente a VPN e excluir outros aplicativos vinculados ao locatário do Azure AD. 

Observe que a autenticação do Azure AD só está disponível para gateways usando o protocolo OpenVPN e clientes que executam o Windows.

- [O que é acesso condicional](../active-directory/conditional-access/overview.md)

- [Configurar a autenticação de Azure Active Directory para VPN de usuário](virtual-wan-point-to-site-azure-ad.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminar a exposição involuntária de credenciais

**Diretrizes**: a VPN site a site na WAN virtual usa chaves pré-compartilhadas (PSK) que são descobertas, criadas e gerenciadas pelo cliente em seu Azure Key Vault. Implemente o verificador de credenciais para identificar as credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Para o GitHub, você pode usar o recurso de verificação de segredo nativo para identificar as credenciais ou outra forma de segredos dentro do código.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Verificação de segredos do GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso privilegiado

*Para obter mais informações, confira o [Azure Security Benchmark: acesso privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: restringir o acesso administrativo a sistemas críticos para os negócios

**Diretrizes**: a WAN virtual do Azure usa controles de acesso baseado em função do Azure (RBAC do Azure) para isolar o acesso a sistemas críticos de negócios, restringindo quais contas recebem acesso privilegiado às assinaturas e aos grupos de gerenciamento nos quais elas estão.

Além disso, restrinja o acesso aos sistemas de gerenciamento, identidade e segurança que têm acesso administrativo ao seu acesso comercialmente crítico, como controladores de Domínio do Active Directory, ferramentas de segurança e ferramentas de gerenciamento de sistema com agentes instalados em sistemas de negócios críticos. Os invasores que comprometem esses sistemas de gerenciamento e segurança podem fazê-los imediatamente para comprometer os ativos críticos aos negócios.

Todos os tipos de controles de acesso devem ser alinhados à sua estratégia de segmentação corporativa para garantir o controle de acesso consistente.

- [Componentes do Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Acesso ao grupo de gerenciamento](../governance/management-groups/overview.md#management-group-access) 

- [Administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: criptografar informações confidenciais em trânsito

**Orientação**: Use VPN ponto a site, VPN site a site e rota expressa criptografada com Wan virtual para seus requisitos de conectividade. A criptografia VPN protege os dados em trânsito de ataques "fora de banda" (como, captura de tráfego) para garantir que os invasores não possam ler ou modificar os dados. 

- [VPN ponto a site](virtual-wan-point-to-site-portal.md)

- [VPN site a site](virtual-wan-site-to-site-portal.md)

- [ExpressRoute criptografado](vpn-over-expressroute.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

## <a name="asset-management"></a>Gerenciamento de Ativos

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de ativos](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: garantir que a equipe de segurança tenha visibilidade dos riscos de ativos

**Diretriz**: verifique se as equipes de segurança receberam as permissões Leitor de segurança no seu locatário e nas suas assinaturas do Azure, de modo que possam monitorar os riscos de segurança usando a Central de Segurança do Azure. 

Dependendo de como as responsabilidades da equipe de segurança são estruturadas, o monitoramento dos riscos de segurança pode ser a responsabilidade de uma equipe de segurança central ou de uma equipe local. Com isso, as informações e os riscos de segurança sempre devem ser agregados centralmente dentro de uma organização. 

As permissões de leitor de segurança podem ser aplicadas amplamente a um locatário inteiro (grupo de gerenciamento raiz) ou com escopo para grupos de gerenciamento ou assinaturas específicas. 

Observação: podem ser necessárias permissões adicionais a fim de obter visibilidade das cargas de trabalho e dos serviços. 

- [Visão geral da função Leitor de segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Visão geral dos grupos de gerenciamento do Azure](../governance/management-groups/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: verificar se a equipe de segurança tem acesso ao inventário de ativos e metadados

**Orientação**: aplique marcas aos recursos, grupos de recursos e assinaturas do Azure para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome/valor. Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção. A WAN virtual do Azure também dá suporte a implantações de recursos baseados em Azure Resource Manager com as quais você pode exportar modelos de ativos. 

- [Guia de decisão de marcação e nomenclatura de recurso](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Gerenciamento de inventário de ativos da central de segurança do Azure](../security-center/asset-inventory.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure monitor para criar regras para disparar alertas quando um serviço não aprovado for detectado. A WAN virtual reúne muitas funcionalidades de rede, segurança e roteamento para fornecer uma única interface operacional. Gateways de VPN de WAN virtual, gateways de ExpressRoute e firewall do Azure têm registro em log e métricas disponíveis por meio de Azure Monitor. 
 

- [Métricas e logs de WAN virtual](logs-metrics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Gerenciador de recursos do Azure Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Log e detecção de ameaças

*Para obter mais informações, confira o [Azure Security Benchmark: log e detecção de ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: habilitar a detecção de ameaças para recursos do Azure

**Orientação**: a VPN ponto a site com Wan virtual é integrada com o Azure Active Directory (Azure AD). O AD do Azure fornece os seguintes logs de usuário que podem ser exibidos no relatório do Azure AD ou integrados com Azure Monitor, Azure Sentinel, SIEM ou ferramentas de monitoramento para casos de uso de análise e monitoramento de ameaças mais sofisticados. Eles são:

- Entrar – o relatório de entrada fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
- Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em qualquer recurso no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.
- Entrada arriscada-uma entrada arriscada é um indicador de uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário.
- Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

Use a central de segurança do Azure para criar alertas sobre determinadas atividades suspeitas, como número excessivo de tentativas de autenticação com falha, incluindo contas preteridas na assinatura. Além do monitoramento básico de higiene de segurança, use o módulo proteção contra ameaças da central de segurança para coletar alertas de segurança mais aprofundados de recursos de computação individuais do Azure (máquinas virtuais, contêineres, serviço de aplicativo), recursos de dados (banco de dados SQL e armazenamento) e camadas de serviço do Azure. Essa funcionalidade permite que você tenha visibilidade das anomalias nas contas nos recursos individuais.

- [Relatórios de atividades de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Habilitar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Configuração do Firewall do Azure em um hub de WAN Virtual](howto-firewall.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: habilitar a detecção de ameaças para o gerenciamento de identidades e acesso do Azure

**Orientação**: a VPN ponto a site com Wan virtual é integrada com o Azure Active Directory (Azure AD). O AD do Azure fornece os seguintes logs de usuário que podem ser exibidos no relatório do Azure AD ou integrados com Azure Monitor, Azure Sentinel, SIEM ou ferramentas de monitoramento para casos de uso de análise e monitoramento de ameaças mais sofisticados. Eles são:

- Entrar – o relatório de entrada fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
- Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em qualquer recurso no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.
- Entrada arriscada-uma entrada arriscada é um indicador de uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário.
- Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

Use a central de segurança do Azure para criar alertas sobre determinadas atividades suspeitas, como número excessivo de tentativas de autenticação com falha, incluindo contas preteridas na assinatura. Além do monitoramento básico de higiene de segurança, use o módulo proteção contra ameaças da central de segurança para coletar alertas de segurança mais aprofundados de recursos de computação individuais do Azure (máquinas virtuais, contêineres, serviço de aplicativo), recursos de dados (banco de dados SQL e armazenamento) e camadas de serviço do Azure. Essa funcionalidade permite que você tenha visibilidade das anomalias nas contas nos recursos individuais.

- [Relatórios de atividades de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Habilitar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Configuração do Firewall do Azure em um hub de WAN Virtual](howto-firewall.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: habilitar o registro em log para atividades de rede do Azure

**Diretrizes**: monitorar a WAN virtual do Azure com o Azure monitor. A WAN virtual reúne muitas funcionalidades de rede, segurança e roteamento para fornecer uma única interface operacional. Gateways de VPN de WAN virtual, gateways de ExpressRoute e firewall do Azure têm registro em log e métricas disponíveis por meio de Azure Monitor. As entradas do log de atividades são coletadas por padrão e podem ser exibidas no portal do Azure. Você pode usar os logs de atividades do Azure (anteriormente conhecidos como logs operacionais e logs de auditoria) para exibir todas as operações enviadas à sua assinatura do Azure.

Uma variedade de logs de diagnóstico também está disponível para a WAN virtual e pode ser configurada para o recurso de WAN virtual com portal do Azure.  Você pode optar por enviar para Log Analytics, transmitir para um hub de eventos ou simplesmente arquivar em uma conta de armazenamento. 
 

- [Métricas e logs de WAN virtual](logs-metrics.md)

- [Logs e métricas do Firewall do Azure](../firewall/logs-and-metrics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: habilitar o registro em log para recursos do Azure

**Orientação**: logs de atividades do Azure, habilitados automaticamente, contêm todas as operações de gravação (put, post, Delete) para seus recursos de WAN virtual do Azure, exceto operações de leitura (Get). Os logs de atividades podem ser usados para encontrar um erro durante a solução de problemas ou para monitorar como um usuário em sua organização modificou um recurso.

Habilite os logs de recursos do Azure para WAN virtual. Você pode usar a central de segurança do Azure e Azure Policy para habilitar logs de recursos e coleta de dados de log. Esses logs podem ser essenciais para investigar posteriormente os incidentes de segurança e executar exercícios forenses.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Entender o registro em log e diferentes tipos de log no Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Entender a coleta de dados da central de segurança do Azure](../security-center/security-center-enable-data-collection.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizar o gerenciamento e a análise do log de segurança

**Orientação**: habilitar o log de segurança para WAN Virtual com Azure monitor. A WAN virtual reúne muitas funcionalidades de rede, segurança e roteamento para fornecer uma única interface operacional. Gateways de VPN de WAN virtual, gateways de ExpressRoute e firewall do Azure têm registro em log e métricas disponíveis por meio de Azure Monitor. As entradas do log de atividades são coletadas por padrão e podem ser exibidas no portal do Azure. Você pode usar os logs de atividades do Azure (anteriormente conhecidos como logs operacionais e logs de auditoria) para exibir todas as operações enviadas à sua assinatura do Azure. 

Uma variedade de logs de diagnóstico também está disponível para a WAN virtual e pode ser configurada para o recurso de WAN virtual com portal do Azure. Envie para Log Analytics, transmita para um hub de eventos ou para simplesmente arquivar em uma conta de armazenamento. Além disso, habilite e integre dados ao Azure Sentinel ou a uma solução de gerenciamento de eventos e informações de segurança de terceiros. 
 

- [Métricas e logs de WAN virtual](logs-metrics.md)

- [Logs e métricas do Firewall do Azure](../firewall/logs-and-metrics.md)

A segurança de WAN virtual do Azure é fornecida por meio do firewall do Azure. 

- [Documentação do firewall do Azure](../firewall/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: configurar a retenção do armazenamento de log

**Diretrizes**: configure sua retenção de log de acordo com seus requisitos de conformidade, regulamentação e negócios. No Azure Monitor, você pode definir seu período de retenção de espaço de trabalho de Log Analytics de acordo com os regulamentos de conformidade de sua organização. Use o armazenamento do Azure, Data Lake ou Log Analytics contas de espaço de trabalho para armazenamento de longo prazo e arquivamento.

- [Alterar o período de retenção de dados em Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Como configurar a política de retenção para logs de conta de armazenamento do Azure](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Exportação de recomendações e alertas da central de segurança do Azure](../security-center/continuous-export.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: preparação – atualizar o processo de resposta a incidentes do Azure

**Diretriz**: verifique se a sua organização tem processos para responder a incidentes de segurança, atualizou esses processos para o Azure e está os exercendo regularmente para garantir a preparação.

- [Implementar a segurança em todo o ambiente empresarial](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência da resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: preparação – configurar a notificação de incidentes

**Diretriz**: configure as informações de contato de incidentes de segurança na Central de Segurança do Azure. Essas informações de contato serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobrir que os seus dados foram acessados por uma pessoa não autorizada ou ilegal. Você também tem opções para personalizar o alerta de incidente e a notificação em diferentes serviços do Azure de acordo com as suas necessidades de resposta a incidentes. 

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detecção e análise – criar incidentes com base em alertas de alta qualidade

**Diretriz**: verifique se você tem um processo para criar alertas de alta qualidade e medir a qualidade deles. Isso permite que você aprenda as lições dos últimos incidentes e priorize os alertas para os analistas, de modo a não perderem tempo em falsos positivos. 

Alertas de alta qualidade podem ser criados com base na experiência dos últimos incidentes, fontes de comunidade validadas e ferramentas projetadas para gerar e limpar alertas pela combinação e pela correlação de fontes de sinal diversificadas. 

A Central de Segurança do Azure fornece alertas de alta qualidade em muitos ativos do Azure. Use o conector de dados da ASC para transmitir os alertas para o Azure Sentinel. Com o Azure Sentinel, você pode criar regras de alertas avançadas a fim de gerar incidentes automaticamente para uma investigação. 

Exporte alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação para ajudar a identificar os riscos para os recursos do Azure. Exporte os alertas e as recomendações de modo manual ou contínuo.

- [Como configurar a exportação](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detecção e análise – investigar um incidente

**Diretriz**: verifique se os analistas podem consultar e usar fontes de dados diversificadas à medida que investigam incidentes potenciais, para criar uma visão completa do que aconteceu. Logs diversificados devem ser coletados para acompanhar as atividades de um invasor potencial na cadeia de eliminação para evitar pontos cegos.  Você também deve garantir que os insights e os aprendizados sejam capturados para outros analistas e para referência histórica futura.  

As fontes de dados para investigação incluem as fontes de log centralizadas que já estão sendo coletadas dos serviços no escopo e dos sistemas em execução, mas também podem incluir:

- Dados de rede – Use os logs de fluxo dos grupos de segurança de rede, o Observador de Rede do Azure e o Azure Monitor para capturar logs de fluxo de rede e outras informações de análise. 

- Instantâneos dos sistemas em execução: 

    - Use a funcionalidade de instantâneo da máquina virtual do Azure para criar um instantâneo do disco do sistema em execução. 

    - Use a funcionalidade de despejo de memória nativa do sistema operacional para criar um instantâneo da memória do sistema em execução.

    - Use o recurso de instantâneo dos serviços do Azure ou a funcionalidade do seu software para criar instantâneos dos sistemas em execução.

O Azure Sentinel fornece ampla análise de dados em praticamente qualquer origem de log e um portal de gerenciamento de casos para gerenciar o ciclo de vida completo de incidentes. As informações de inteligência coletadas durante uma investigação podem ser associadas a um incidente para fins de rastreamento e relatório. 

- [Criar um instantâneo de disco de um computador Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Criar um instantâneo de disco de um computador Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informações de diagnóstico e coleta de despejo de memória do Suporte do Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigar incidentes com o Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detecção e análise – priorizar incidentes

**Diretriz**: forneça aos analistas o contexto dos incidentes nos quais eles devem se concentrar primeiro com base na severidade do alerta e na confidencialidade do ativo. 

A Central de Segurança do Azure atribui uma severidade a cada alerta para ajudar você a priorizar os alertas que devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na conclusão ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que gerou o alerta.

Além disso, marque os recursos usando marcas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: confinamento, erradicação e recuperação – automatizar o tratamento de incidentes

**Diretriz**: automatize tarefas repetitivas manuais para acelerar o tempo de resposta e reduzir a carga dos analistas. As tarefas manuais demoram mais para serem executadas, atrasando cada incidente e reduzindo o número de incidentes que um analista pode processar. Elas também aumentam a fadiga do analista, o que aumenta o risco de erros humanos que causam atrasos, prejudicando a capacidade dos analistas de se concentrarem efetivamente em tarefas complexas. Use os recursos de automação de fluxo de trabalho da Central de Segurança do Azure e do Azure Sentinel para disparar ações automaticamente ou executar um guia estratégico para responder aos alertas de segurança recebidos. O guia estratégico executa ações, como enviar notificações, desabilitar contas e isolar redes problemáticas. 

- [Configurar a automação de fluxo de trabalho na Central de Segurança](../security-center/workflow-automation.md)

- [Configurar respostas automatizadas a ameaças na Central de Segurança do Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas de ameaças automatizadas no Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Gerenciamento de postura e vulnerabilidades

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de postura e vulnerabilidades](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: realizar uma simulação de ataque regular

**Diretriz**: conforme necessário, realize testes de penetração ou atividades de equipe vermelhas nos seus recursos do Azure e garanta a correção de todas as conclusões de segurança críticas.
Siga as Regras de Participação no Teste de Penetração do Microsoft Cloud para garantir que os testes de penetração não violem as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft.

- [Teste de penetração no Azure](../security/fundamentals/pen-testing.md)

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="endpoint-security"></a>Segurança do ponto de extremidade

*Para obter mais informações, consulte o [benchmark de segurança do Azure: segurança do ponto de extremidade](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: usar a detecção e resposta de ponto de extremidade (EDR)

**Orientação**: os clientes não têm permissão explícita para configurar a detecção de ponto de extremidade e as configurações de resposta. No entanto, as máquinas virtuais usadas na oferta de WAN virtual do Azure usam esses recursos. Saiba mais sobre esses recursos gerais nos links referenciados. 

- [Visão geral da proteção avançada contra ameaças do Microsoft defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Serviço Microsoft defender ATP para Windows Servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Serviço Microsoft defender ATP para servidores não Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

## <a name="governance-and-strategy"></a>Governança e estratégia

*Para obter mais informações, confira o [Azure Security Benchmark: governança e estratégia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definir a estratégia de proteção de dados e gerenciamento de ativos 

**Diretriz**: documente e comunique uma estratégia clara para o monitoramento contínuo e a proteção de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o monitoramento de sistemas e dados comercialmente críticos. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Padrão de classificação de dados de acordo com os riscos de negócios

-   Visibilidade da organização de segurança dos riscos e do inventário de ativos 

-   Aprovação da organização de segurança dos serviços do Azure para uso 

-   Segurança de ativos por meio do ciclo de vida

-   Estratégia de controle de acesso obrigatório de acordo com a classificação de dados organizacionais

-   Uso de funcionalidades nativas e de terceiros de proteção de dados do Azure

-   Requisitos de criptografia de dados para casos de uso em trânsito e em repouso

-   Padrões de criptografia apropriados

Para saber mais, consulte as referências a seguir:
- [Recomendação da arquitetura de segurança do Azure – Armazenamento, dados e criptografia](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Conceitos básicos de segurança do Azure – Segurança, criptografia e armazenamento de dados do Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – Melhores práticas de segurança e criptografia de dados do Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – Gerenciamento de ativos](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark – Proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definir uma estratégia de segmentação corporativa 

**Diretriz**: estabeleça uma estratégia de toda a empresa para segmentar o acesso aos ativos usando uma combinação de identidade, rede, aplicativo, assinatura, grupo de gerenciamento e outros controles.

Equilibre cuidadosamente a necessidade de separação de segurança com a necessidade de habilitação da operação diária dos sistemas que precisam se comunicar entre si e acessar dados.

Garanta que a estratégia de segmentação seja implementada de maneira consistente nos tipos de controle, incluindo segurança de rede, modelos de identidade e acesso, além de modelos de acesso/permissão de aplicativo e controles de processos humanos.

- [Diretrizes sobre a estratégia de segmentação no Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Diretrizes sobre a estratégia de segmentação no Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação de rede com a estratégia de segmentação corporativa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definir uma estratégia de gerenciamento de postura de segurança

**Diretriz**: Meça e atenue continuamente os riscos para seus ativos individuais e o ambiente no qual eles estão hospedados. Priorize os ativos de alto valor e as superfícies de ataque altamente expostas, como aplicativos publicados, pontos de entrada e saída de rede, pontos de extremidade de usuário e administrador etc.

- [Azure Security Benchmark – Gerenciamento de postura e vulnerabilidade](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: alinhar funções e responsabilidades da organização

**Diretriz**: documente e comunique uma estratégia clara para funções e responsabilidades na sua organização de segurança. Priorize o fornecimento de responsabilidade clara por decisões de segurança, educando todos no modelo de responsabilidade compartilhada e instrua equipes técnicas sobre a tecnologia usada para proteger a nuvem.

- [Melhor prática de segurança do Azure 1 – Pessoas: educar as equipes na jornada de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Melhor prática de segurança do Azure 2 – Pessoas: educar as equipes na tecnologia de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Melhor prática de segurança do Azure 3 – Processo: atribuir responsabilidade por decisões de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definir uma estratégia de segurança de rede

**Diretriz**: estabeleça uma abordagem de segurança de rede do Azure como parte da estratégia geral de controle de acesso de segurança da sua organização.  

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Gerenciamento de rede centralizado e responsabilidade pela segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação corporativa

-   Estratégia de correção em diferentes cenários de ameaças e ataques

-   Estratégia de entrada e saída e borda da Internet

-   Estratégia de interconectividade local e de nuvem híbrida

-   Artefatos de segurança de rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para saber mais, consulte as referências a seguir:
- [Melhor prática de segurança do Azure 11 – Arquitetura: uma estratégia de segurança unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – Segurança de rede](../security/benchmarks/security-controls-v2-network-security.md)

- [Visão geral da segurança de rede do Azure](../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura de rede corporativa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definir uma estratégia de identidade e acesso privilegiado

**Diretriz**: estabeleça abordagens de identidade e acesso privilegiado do Azure como parte da estratégia geral de controle de acesso de segurança da sua organização.  

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Um sistema de identidade e autenticação centralizado e a interconectividade dele com outros sistemas de identidade internos e externos

-   Métodos de autenticação forte em diferentes casos de uso e condições

-   Proteção de usuários altamente privilegiados

-   Monitoramento e tratamento de atividades de usuário de anomalias  

-   Processo de revisão de acesso e reconciliação e identidade do usuário

Para saber mais, consulte as referências a seguir:

- [Azure Security Benchmark – Gerenciamento de identidades](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark – Acesso privilegiado](../security/benchmarks//security-controls-v2-privileged-access.md)

- [Melhor prática de segurança do Azure 11 – Arquitetura: uma estratégia de segurança unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Visão geral da segurança de gerenciamento de identidade do Azure](../security/fundamentals/identity-management-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definir uma estratégia de log e resposta a ameaças

**Diretriz**: estabeleça uma estratégia de log e resposta a ameaças para detectar e corrigir as ameaças rapidamente, atendendo aos requisitos de conformidade. Priorize o fornecimento de alertas de alta qualidade e experiências integradas aos analistas, de modo que possam se concentrar nas ameaças em vez da integração e de etapas manuais. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   A função e as responsabilidades da organização de SecOps (operações de segurança) 

-   Um processo de resposta a incidentes bem definido, alinhando-se ao NIST ou a outra estrutura do setor 

-   Captura e retenção de log para dar suporte às necessidades de conformidade, detecção de ameaças e resposta a incidentes

-   Visibilidade centralizada das ameaças e informações de correlação sobre elas por meio do SIEM, de funcionalidades nativas do Azure e de outras fontes 

-   Plano de comunicação e notificação com seus clientes, fornecedores e partes públicas interessadas

-   Uso de plataformas nativas e de terceiros do Azure para tratamento de incidentes, como log e detecção de ameaças, análise forense, além de correção e erradicação de ataques

-   Processos para tratamento de incidentes e atividades pós-incidente, como lições aprendidas e retenção de evidências

Para saber mais, consulte as referências a seguir:

- [Azure Security Benchmark – Log e detecção de ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – Resposta a incidentes](../security/benchmarks/security-controls-v2-incident-response.md)

- [Melhor prática de segurança do Azure 4 – Processo: atualizar processos de resposta a incidentes para a nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de log, de decisão de relatórios e do Adoption Framework do Azure](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala empresarial, gerenciamento e monitoramento do Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
