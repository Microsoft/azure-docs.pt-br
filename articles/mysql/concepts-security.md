---
title: Segurança-banco de dados do Azure para MySQL
description: Uma visão geral dos recursos de segurança no banco de dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 90855059461fcd5f8ed8d2733d2b6d4addaccde3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94535037"
---
# <a name="security-in-azure-database-for-mysql"></a>Segurança do Banco de Dados do Azure para MySQL

Há várias camadas de segurança que estão disponíveis para proteger os dados em seu servidor do banco de dados do Azure para MySQL. Este artigo descreve essas opções de segurança.

## <a name="information-protection-and-encryption"></a>Proteção e criptografia de informações

### <a name="in-transit"></a>Em trânsito
O banco de dados do Azure para MySQL protege seus dados criptografando os dados em trânsito com a segurança da camada de transporte. A criptografia (SSL/TLS) é imposta por padrão.

### <a name="at-rest"></a>Em repouso
O serviço Banco de Dados do Azure para MySQL usa o módulo de criptografia validado por FIPS 140-2 para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados no disco, incluindo os arquivos temporários criados durante a execução de consultas. O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada.


## <a name="network-security"></a>Segurança de rede
As conexões com um servidor de banco de dados do Azure para MySQL são roteadas primeiro por meio de um gateway regional. O gateway tem um IP acessível publicamente, enquanto os endereços IP do servidor são protegidos. Para obter mais informações sobre o gateway, visite o [artigo arquitetura de conectividade](concepts-connectivity-architecture.md).  

Um servidor de banco de dados do Azure criado recentemente para MySQL tem um firewall que bloqueia todas as conexões externas. Embora eles atinjam o gateway, eles não têm permissão para se conectar ao servidor. 

### <a name="ip-firewall-rules"></a>Regras de firewall de IP
As regras de firewall IP concedem acesso a servidores com base no endereço IP de origem de cada solicitação. Consulte a [visão geral das regras de firewall](concepts-firewall-rules.md) para obter mais informações.

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual
Os pontos de extremidade de serviço de rede virtual estendem sua conectividade de rede virtual no backbone do Azure. Usando regras de rede virtual, você pode habilitar seu banco de dados do Azure para servidor MySQL para permitir conexões de sub-redes selecionadas em uma rede virtual. Para obter mais informações, consulte [visão geral do ponto de extremidade do serviço de rede virtual](concepts-data-access-and-security-vnet.md).

### <a name="private-ip"></a>IP Privado
O link privado permite que você se conecte ao banco de dados do Azure para MySQL no Azure por meio de um ponto de extremidade privado. O Link Privado do Azure essencialmente traz os serviços do Azure dentro de sua VNet (Rede Virtual privada). Os recursos de PaaS podem ser acessados usando o endereço IP privado, assim como qualquer outro recurso na VNet. Para obter mais informações, consulte a [visão geral do link privado](concepts-data-access-security-private-link.md)

## <a name="access-management"></a>Gerenciamento de acesso

Ao criar o banco de dados do Azure para o servidor MySQL, você fornece credenciais para um usuário administrador. Esse administrador pode ser usado para criar usuários adicionais do MySQL.


## <a name="threat-protection"></a>Proteção contra ameaças

Você pode aceitar a [proteção avançada contra ameaças](concepts-data-access-and-security-threat-protection.md) , que detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar servidores.

O [log de auditoria](concepts-audit-logs.md) está disponível para acompanhar a atividade em seus bancos de dados. 


## <a name="next-steps"></a>Próximas etapas
- Habilitar regras de firewall para [IPS](concepts-firewall-rules.md) ou [redes virtuais](concepts-data-access-and-security-vnet.md)
