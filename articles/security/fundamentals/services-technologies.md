---
title: Tecnologias e serviços de segurança do Azure | Microsoft Docs
description: O artigo fornece uma lista estruturada das tecnologias e serviços de segurança do Azure.
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: terrylan
ms.openlocfilehash: 5914d5a54606f49f6fbd809a17e2b47375b58dcd
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641979"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Tecnologias e serviços de segurança disponíveis no Azure

Em nossas discussões com clientes atuais e futuros do Azure, muitas vezes é perguntado: "você tem uma lista de todos os serviços e tecnologias relacionados à segurança que o Azure tem a oferecer?"

Ao avaliar as opções do provedor de serviços de nuvem, é útil ter essas informações. Portanto, nós fornecemos essa lista para você começar.

Ao longo do tempo, essa lista será alterada e aumentará, exatamente como o Azure. Lembre-se de visitar essa página regularmente para se manter atualizado sobre nossas tecnologias e serviços relacionados à segurança.

## <a name="general-azure-security"></a>Segurança Geral do Azure
|Serviço|Descrição|
|--------|--------|
|[Central de &nbsp;Segurança&nbsp; do Azure](../../security-center/security-center-introduction.md)| Uma solução de proteção de carga de trabalho de nuvem que fornece gerenciamento de segurança e proteção avançada contra ameaças nas cargas de trabalho de nuvem híbrida.|
|[Azure Key Vault](../../key-vault/general/overview.md)| Um repositório de segredos seguro para as senhas, cadeias de caracteres de conexão e outras informações necessárias para manter seus aplicativos funcionando. |
|[Logs do Azure Monitor](../../azure-monitor/logs/log-query-overview.md)|Um serviço de monitoramento que coleta a telemetria e outros dados e fornece um mecanismo de linguagem e análise de consulta para fornecer insights operacionais para seus aplicativos e recursos. Pode ser usado sozinho ou com outros serviços, como a Central de Segurança. |
|[Laboratórios de desenvolvimento/teste do Azure](../../devtest-labs/devtest-lab-overview.md)|Um serviço que ajuda os desenvolvedores e testadores a rapidamente criar ambientes no Azure, minimizando o desperdício e controlando custos.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Segurança de armazenamento
|Serviço|Descrição|
|------|--------|
| [Criptografia do &nbsp;Serviço de &nbsp;Armazenamento do &nbsp;Azure](../../storage/common/storage-service-encryption.md)|Um recurso de segurança que criptografa automaticamente os dados no armazenamento do Azure.   |
|[Armazenamento híbrido criptografado StorSimple](../../storsimple/storsimple-ova-overview.md)| Uma solução de armazenamento integrado que gerencia as tarefas de armazenamento entre os dispositivos local e o armazenamento em nuvem do Azure.|
|[Criptografia do Lado do Cliente do Azure](../../storage/common/storage-client-side-encryption.md)| Uma solução de criptografia do lado do cliente que criptografa os dados em aplicativos de cliente antes de carregar no Armazenamento do Azure; também descriptografa os dados durante o download. |
| [Assinaturas de Acesso Compartilhado do Armazenamento do Azure](../../storage/common/storage-sas-overview.md)|Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento.  |
|[Chaves de conta de armazenamento do Azure](../../storage/common/storage-account-create.md)| Um método de controle de acesso para o armazenamento do Azure que é usado para autenticação quando a conta de armazenamento é acessada. |
|[Compartilhamentos de arquivos do Azure com criptografia SMB 3,0](../../storage/files/storage-files-introduction.md)|Uma tecnologia de segurança de rede que permite a criptografia automática de rede para o protocolo de compartilhamento de arquivos de bloco de mensagens de servidor (SMB). |
|[Análise de Armazenamento do Azure](/rest/api/storageservices/Storage-Analytics)| Uma tecnologia de log e métricas de geração de dados em sua conta de armazenamento. |

<!------>

## <a name="database-security"></a>Segurança de banco de dados
|Serviço|Descrição|
|------|--------|
| [Azure&nbsp;SQL&nbsp;Firewall](../../azure-sql/database/firewall-configure.md)|Um recurso de controle de acesso de rede que protege contra ataques baseado em rede ao banco de dados. |
|[Nível de Criptografia de &nbsp;Célula do &nbsp;SQL do &nbsp;Azure](/archive/blogs/sqlsecurity/recommendations-for-using-cell-level-encryption-in-azure-sql-database)| Uma tecnologia de segurança de banco de dados que fornece criptografia em um nível granular.  |
| [Criptografia de Conexão do &nbsp;SQL do &nbsp;Azure](../../azure-sql/database/logins-create-manage.md)|Para fornecer segurança, o Banco de Dados SQL controla o acesso com regras de firewall que limitam a conectividade por endereço IP,mecanismos de autenticação que exigem que usuários comprovem suas identidade e mecanismos de autorização que limitam os usuários a ações e dados específicos. |
| [Criptografia sempre ativa do SQL do Azure](/sql/relational-databases/security/encryption/always-encrypted-database-engine)|Protege dados confidenciais, como números de cartão de crédito ou números de identificação nacional (por exemplo, números do seguro social dos EUA), armazenados no Banco de Dados SQL do Azure ou nos bancos de dados SQL Server.  |
| [Transparent Data Encryption do &nbsp;SQL do &nbsp;Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)| Um recurso de segurança de banco de dados que criptografa o armazenamento de um banco de dados inteiro. |
| [Auditoria do banco de dados SQL do Azure](../../azure-sql/database/auditing-overview.md)|Um recurso de auditoria do banco de dados SQL que rastreia eventos do banco de dados e os grava em um log de auditoria em sua conta de armazenamento do Azure.  |


## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso
|Serviço|Descrição|
|------|--------|
| [&nbsp;Controle de acesso baseado em função do Azure &nbsp;](../../role-based-access-control/role-assignments-portal.md)|Um recurso de controle de acesso projetado para permitir que os usuários acessem apenas os recursos necessários para acesso com base em suas funções dentro da empresa.  |
| [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)|Um repositório de autenticação baseado em nuvem que oferece suporte a um diretório de multilocatário, baseados em nuvem e vários serviços de gerenciamento de identidade no Azure.  |
| [Azure Active Directory B2C](../../active-directory-b2c/overview.md)|Um serviço de gerenciamento de identidade que permite controlar como os clientes se inscrevem, entram e gerenciam seus perfis ao usar aplicativos baseados no Azure.   |
| [Serviços de Domínio do Active Directory do Azure](../../active-directory-domain-services/overview.md)| Uma versão com base em nuvem e gerenciada do Active Directory Domain Services. |
| [Autenticação multifator do Azure AD](../../active-directory/authentication/concept-mfa-howitworks.md)| Uma provisão de segurança que emprega várias formas diferentes de autenticação e verificação antes de permitir acesso a informações protegidas. |

## <a name="backup-and-disaster-recovery"></a>Backup e recuperação de desastres
|Serviço|Descrição|
|------|--------|
| [Backup do Azure &nbsp;](../../backup/backup-overview.md)| Um serviço baseado no Azure usado para fazer backup e restaurar dados na nuvem do Azure. |
| [&nbsp;Recuperação de site do Azure &nbsp;](../../site-recovery/site-recovery-overview.md)|Um serviço online que replica as cargas de trabalho em execução em máquinas físicas e virtuais (VMs) de um site primário para um local secundário para permitir a recuperação de serviços após uma falha. |

## <a name="networking"></a>Rede
|Serviço|Descrição|
|------|--------|
| [&nbsp;Grupos de segurança de rede &nbsp;](../../virtual-network/virtual-network-vnet-plan-design-arm.md)| Um recurso de controle de acesso baseado em rede usando 5 tuplas para fazer permitir ou negar as decisões.  |
| [Gateway de VPN do Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md)| Um dispositivo de rede usado como um ponto de extremidade VPN para permitir acesso entre locais às redes virtuais do Azure.  |
| [Gateway de Aplicativo do Azure](../../application-gateway/overview.md)|Um balanceador de carga de aplicativo web avançado que pode rotear com base na URL e executar o descarregamento de SSL. |
|[Firewall do aplicativo Web](../../web-application-firewall/afds/afds-overview.md) (WAF)|Um recurso do Gateway de Aplicativo que fornece proteção centralizada dos aplicativos Web contra explorações e vulnerabilidades comuns|
| [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)|Um balanceador de carga de rede do aplicativo TCP/UDP. |
| [Azure ExpressRoute](../../expressroute/expressroute-introduction.md)| Uma conexão WAN dedicada entre redes locais e Redes Virtuais do Azure. |
| [Gerenciador de Tráfego do Azure](../../traffic-manager/traffic-manager-overview.md)| Um balanceador de carga do DNS global.|
| [Proxy de aplicativo do Azure](../../active-directory/manage-apps/application-proxy.md)| Um front-end de autenticação usado para proteger o acesso remoto para aplicativos da web hospedados no local. |
|[Firewall do Azure](../../firewall/overview.md)|Um serviço de segurança de rede gerenciado baseado em nuvem que protege os recursos da Rede Virtual do Azure.|
|[Proteção contra DDoS do Azure](../../ddos-protection/ddos-protection-overview.md)|Combinado com as melhores práticas de design de aplicativo, fornece defesa contra ataques DDoS.|
|[Pontos de extremidade do serviço de Rede Virtual](../../virtual-network/virtual-network-service-endpoints-overview.md)|Estende o espaço de endereço privado de rede virtual e a identidade da VNet aos serviços do Azure através de uma conexão direta.|
