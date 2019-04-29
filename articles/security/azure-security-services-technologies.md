---
title: Tecnologias e serviços de segurança do Azure | Microsoft Docs
description: O artigo fornece uma lista estruturada das tecnologias e serviços de segurança do Azure.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: barclayn
ms.openlocfilehash: 13183282e5e607f0052194a474203f97e0160adb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610905"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Tecnologias e serviços de segurança disponíveis no Azure

Em nossas discussões com clientes do Azure atuais e futuros, frequentemente nos fazem a seguinte pergunta “você tem uma lista de todas as tecnologias e serviços relacionados à segurança que o Azure tem a oferecer?”

Quando você avaliar as opções de provedor de serviços de nuvem, é útil ter essas informações. Portanto, nós fornecemos essa lista para você começar.

Ao longo do tempo, essa lista será alterada e aumentará, exatamente como o Azure. Lembre-se de visitar essa página regularmente para se manter atualizado sobre nossas tecnologias e serviços relacionados à segurança.

## <a name="general-azure-security"></a>Segurança Geral do Azure
|Serviço|DESCRIÇÃO|
|--------|--------|
|[Central de &nbsp;Segurança&nbsp; do Azure](../security-center/security-center-intro.md)| Uma solução de proteção de carga de trabalho de nuvem que fornece gerenciamento de segurança e proteção avançada contra ameaças nas cargas de trabalho de nuvem híbrida.|
|[Cofre da Chave do Azure](../key-vault/key-vault-overview.md)| Um repositório de segredos seguro para as senhas, cadeias de caracteres de conexão e outras informações necessárias para manter seus aplicativos funcionando. |
|[Logs do Azure Monitor](../log-analytics/log-analytics-overview.md)|Um serviço de monitoramento que coleta a telemetria e outros dados e fornece um mecanismo de linguagem e análise de consulta para fornecer insights operacionais para seus aplicativos e recursos. Pode ser usado sozinho ou com outros serviços, como a Central de Segurança. |
|[Azure Dev/Test Labs](../devtest-lab/devtest-lab-overview.md)|Um serviço que ajuda os desenvolvedores e testadores a rapidamente criar ambientes no Azure, minimizando o desperdício e controlando custos.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Segurança de armazenamento
|Serviço|DESCRIÇÃO|
|------|--------|
| [Criptografia do &nbsp;Serviço de &nbsp;Armazenamento do &nbsp;Azure](../storage/common/storage-service-encryption.md)|Um recurso de segurança que criptografa automaticamente os dados no armazenamento do Azure.   |
|[Armazenamento híbrido criptografado StorSimple](../storsimple/storsimple-ova-overview.md)| Uma solução de armazenamento integrado que gerencia as tarefas de armazenamento entre os dispositivos local e o armazenamento em nuvem do Azure.|
|[Criptografia do lado do cliente do Azure](../storage/common/storage-client-side-encryption.md)| Uma solução de criptografia do lado do cliente que criptografa os dados em aplicativos de cliente antes de carregar no Armazenamento do Azure; também descriptografa os dados durante o download. |
| [Assinaturas de Acesso Compartilhado do Armazenamento do Azure](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento.  |
|[Chaves de conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md)| Um método de controle de acesso para o armazenamento do Azure que é usado para autenticação quando a conta de armazenamento é acessada. |
|[Compartilhamentos de arquivos do Azure com criptografia SMB 3.0](../storage/files/storage-files-introduction.md)|Uma tecnologia de segurança de rede que permite a criptografia automática de rede para o protocolo de compartilhamento de arquivos de bloco de mensagens de servidor (SMB). |
|[Análise do Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/Storage-Analytics)| Uma tecnologia de log e métricas de geração de dados em sua conta de armazenamento. |

<!------>

## <a name="database-security"></a>Segurança de banco de dados
|Serviço|DESCRIÇÃO|
|------|--------|
| [Azure&nbsp;SQL&nbsp;Firewall](../sql-database/sql-database-firewall-configure.md)|Um recurso de controle de acesso de rede que protege contra ataques baseado em rede ao banco de dados. |
|[Nível de Criptografia de &nbsp;Célula do &nbsp;SQL do &nbsp;Azure](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Uma tecnologia de segurança de banco de dados que fornece criptografia em um nível granular.  |
| [Criptografia de Conexão do &nbsp;SQL do &nbsp;Azure](../sql-database/sql-database-control-access.md)|Para fornecer segurança, o Banco de Dados SQL controla o acesso com regras de firewall que limitam a conectividade por endereço IP,mecanismos de autenticação que exigem que usuários comprovem suas identidade e mecanismos de autorização que limitam os usuários a ações e dados específicos. |
| [Criptografia sempre ativa do SQL do Azure](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Protege dados confidenciais, como números de cartão de crédito ou números de identificação nacional (por exemplo, números do seguro social dos EUA), armazenados no Banco de Dados SQL do Azure ou nos bancos de dados SQL Server.  |
| [Transparent Data Encryption do &nbsp;SQL do &nbsp;Azure](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Um recurso de segurança de banco de dados que criptografa o armazenamento de um banco de dados inteiro. |
| [Auditoria do Banco de Dados SQL do Azure](../sql-database/sql-database-auditing.md)|Um recurso de auditoria do banco de dados SQL que rastreia eventos do banco de dados e os grava em um log de auditoria em sua conta de armazenamento do Azure.  |


## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso
|Serviço|DESCRIÇÃO|
|------|--------|
| [Controle de Acesso &nbsp;com base em &nbsp;função do &nbsp;Azure](../active-directory/role-based-access-control-configure.md)|Um recurso de controle de acesso projetado para permitir que os usuários acessem apenas os recursos necessários para acesso com base em suas funções dentro da empresa.  |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)|Um repositório de autenticação baseado em nuvem que oferece suporte a um diretório de multilocatário, baseados em nuvem e vários serviços de gerenciamento de identidade no Azure.  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|Um serviço de gerenciamento de identidade que permite controlar como os clientes se inscrevem, entram e gerenciam seus perfis ao usar aplicativos baseados no Azure.   |
| [Serviços de Domínio do Active Directory do Azure](../active-directory-domain-services/active-directory-ds-overview.md)| Uma versão com base em nuvem e gerenciada do Active Directory Domain Services. |
| [Autenticação Multifator do Azure](../active-directory/authentication/multi-factor-authentication.md)| Uma provisão de segurança que emprega várias formas diferentes de autenticação e verificação antes de permitir acesso a informações protegidas. |

## <a name="backup-and-disaster-recovery"></a>Backup e recuperação de desastres
|Serviço|DESCRIÇÃO|
|------|--------|
| [Backup do &nbsp;Azure](../backup/backup-introduction-to-azure-backup.md)| Um serviço baseado no Azure usado para fazer backup e restaurar dados na nuvem do Azure. |
| [Recuperação de &nbsp;Site do &nbsp;Azure](../site-recovery/site-recovery-overview.md)|Um serviço online que replica as cargas de trabalho em execução em máquinas físicas e virtuais (VMs) de um site primário para um local secundário para permitir a recuperação de serviços após uma falha. |

## <a name="networking"></a>Rede
|Serviço|DESCRIÇÃO|
|------|--------|
| [Grupos de &nbsp;Segurança de &nbsp;Rede](../virtual-network/virtual-networks-nsg.md)| Um recurso de controle de acesso baseado em rede usando 5 tuplas para fazer permitir ou negar as decisões.  |
| [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)| Um dispositivo de rede usado como um ponto de extremidade VPN para permitir acesso entre locais às redes virtuais do Azure.  |
| [Application Gateway do Azure](../application-gateway/application-gateway-introduction.md)|Um balanceador de carga de aplicativo web avançado que pode rotear com base na URL e executar o descarregamento de SSL. |
|[WAF](../application-gateway/waf-overview.md) (Firewall do aplicativo Web)|Um recurso do Gateway de Aplicativo que fornece proteção centralizada dos aplicativos Web contra explorações e vulnerabilidades comuns|
| [Balanceador de carga do Azure](../load-balancer/load-balancer-overview.md)|Um balanceador de carga de rede do aplicativo TCP/UDP. |
| [Azure ExpressRoute](../expressroute/expressroute-introduction.md)| Uma conexão WAN dedicada entre redes locais e Redes Virtuais do Azure. |
| [Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md)| Um balanceador de carga do DNS global.|
| [Proxy de aplicativo do Azure](../active-directory/active-directory-application-proxy-get-started.md)| Um front-end de autenticação usado para proteger o acesso remoto para aplicativos da web hospedados no local. |
|[Firewall do Azure](../firewall/overview.md)|Um serviço de segurança de rede gerenciado baseado em nuvem que protege os recursos da Rede Virtual do Azure.|
|[Proteção contra DDoS do Azure](../virtual-network/ddos-protection-overview.md)|Combinado com as melhores práticas de design de aplicativo, fornece defesa contra ataques DDoS.|
|[Pontos de extremidade do serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md)|Estende o espaço de endereço privado de rede virtual e a identidade da VNet aos serviços do Azure através de uma conexão direta.|