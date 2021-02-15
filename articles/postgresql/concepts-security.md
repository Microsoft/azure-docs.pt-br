---
title: Segurança no banco de dados do Azure para PostgreSQL-servidor único
description: Uma visão geral dos recursos de segurança no banco de dados do Azure para PostgreSQL-servidor único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: be042a0ec076538cf0f0d155667acea6f1ae19cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710474"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Segurança no banco de dados do Azure para PostgreSQL-servidor único

Há várias camadas de segurança que estão disponíveis para proteger os dados no servidor do banco de dado do Azure para PostgreSQL. Este artigo descreve essas opções de segurança.

## <a name="information-protection-and-encryption"></a>Proteção e criptografia de informações

### <a name="in-transit"></a>Em trânsito
O banco de dados do Azure para PostgreSQL protege seus dados criptografando os dados em trânsito com a segurança da camada de transporte. A criptografia (SSL/TLS) é imposta por padrão.

### <a name="at-rest"></a>Em repouso
O serviço Banco de Dados do Azure para PostgreSQL usa o módulo de criptografia validado por FIPS 140-2 para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados no disco, incluindo os arquivos temporários criados durante a execução de consultas. O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada.


## <a name="network-security"></a>Segurança de rede
As conexões com um servidor de banco de dados do Azure para PostgreSQL são roteadas primeiro por meio de um gateway regional. O gateway tem um IP acessível publicamente, enquanto os endereços IP do servidor são protegidos. Para obter mais informações sobre o gateway, visite o [artigo arquitetura de conectividade](concepts-connectivity-architecture.md).  

Um banco de dados do Azure criado recentemente para o servidor PostgreSQL tem um firewall que bloqueia todas as conexões externas. Embora eles atinjam o gateway, eles não têm permissão para se conectar ao servidor. 

### <a name="ip-firewall-rules"></a>Regras de firewall de IP
As regras de firewall IP concedem acesso a servidores com base no endereço IP de origem de cada solicitação. Consulte a [visão geral das regras de firewall](concepts-firewall-rules.md) para obter mais informações.

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual
Os pontos de extremidade de serviço de rede virtual estendem sua conectividade de rede virtual no backbone do Azure. Usando regras de rede virtual, você pode habilitar seu banco de dados do Azure para o servidor PostgreSQL para permitir conexões de sub-redes selecionadas em uma rede virtual. Para obter mais informações, consulte [visão geral do ponto de extremidade do serviço de rede virtual](concepts-data-access-and-security-vnet.md).

### <a name="private-ip"></a>IP Privado
O link privado permite que você se conecte ao banco de dados do Azure para PostgreSQL servidor único no Azure por meio de um ponto de extremidade privado. O Link Privado do Azure essencialmente traz os serviços do Azure dentro de sua VNet (Rede Virtual privada). Os recursos de PaaS podem ser acessados usando o endereço IP privado, assim como qualquer outro recurso na VNet. Para obter mais informações, consulte a [visão geral do link privado](concepts-data-access-and-security-private-link.md)


## <a name="access-management"></a>Gerenciamento de acesso

Ao criar o banco de dados do Azure para o servidor PostgreSQL, você fornece credenciais para uma função de administrador. Essa função de administrador pode ser usada para criar [funções](https://www.postgresql.org/docs/current/user-manag.html)adicionais do PostgreSQL.

Você também pode se conectar ao servidor usando a [autenticação do Azure Active Directory (AAD)](concepts-aad-authentication.md).


## <a name="threat-protection"></a>Proteção contra ameaças

Você pode aceitar a [proteção avançada contra ameaças](concepts-data-access-and-security-threat-protection.md) , que detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar servidores.

O [log de auditoria](concepts-audit.md) está disponível para acompanhar a atividade em seus bancos de dados. 

## <a name="migrating-from-oracle"></a>Migrando do Oracle

O Oracle dá suporte a Transparent Data Encryption (TDE) para criptografar dados de tabela e de espaço de tabelas. No Azure para PostgreSQL, os dados são criptografados automaticamente em várias camadas. Consulte a seção "em repouso" nesta página e também consulte vários tópicos de segurança, incluindo [chaves gerenciadas pelo cliente](./concepts-data-encryption-postgresql.md) e [criptografia dupla de infraestrutura](./concepts-infrastructure-double-encryption.md). Você também pode considerar o uso da extensão [pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html) que tem suporte no [Azure para PostgreSQL](./concepts-extensions.md).

## <a name="next-steps"></a>Próximas etapas
- Habilitar regras de firewall para [IPS](concepts-firewall-rules.md) ou [redes virtuais](concepts-data-access-and-security-vnet.md)
- Saiba mais sobre a [autenticação Azure Active Directory](concepts-aad-authentication.md) no banco de dados do Azure para PostgreSQL
