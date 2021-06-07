---
title: Segurança de banco de dados - Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece proteção de banco de dados e segurança para seus dados.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: mjbrown
ms.openlocfilehash: 19b4c8466e88159839ce1f43a5ba282b1bb3ec9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94636904"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Segurança no Azure Cosmos DB – visão geral
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo aborda as melhores práticas de segurança de banco de dados e os principais recursos oferecidos pelo Azure Cosmos DB para ajudar você a prevenir, detectar e responder a violações de banco de dados.

## <a name="whats-new-in-azure-cosmos-db-security"></a>O que há de novo na segurança Azure Cosmos DB

A criptografia em repouso agora está disponível para documentos e backups armazenados no Azure Cosmos DB em todas as regiões do Azure. A criptografia em repouso é aplicada automaticamente para clientes novos e existentes nessas regiões. Não é necessário configurar nada e você obtém a mesma latência, taxa de transferência, disponibilidade e funcionalidade excelentes de antes, com o benefício de saber que seus dados estão seguros e protegidos com a criptografia em repouso.

## <a name="how-do-i-secure-my-database"></a>Como fazer proteger meu banco de dados

A segurança dos dados é uma responsabilidade compartilhada por você, o cliente e seu provedor de banco de dados. Dependendo do provedor de banco de dados que você escolher, o tamanho da responsabilidade pode variar. Se você escolher uma solução local, precisará fornecer tudo, desde a proteção do ponto de extremidade até a segurança física do seu hardware, o que não é uma tarefa fácil. Se você escolher um provedor de banco de dados em nuvem PaaS, como o Azure Cosmos DB, sua área de interesse será consideravelmente reduzida. A imagem a seguir, retirada do white paper [Responsabilidades compartilhadas para a computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) da Microsoft, mostra como sua responsabilidade diminui com um provedor de PaaS, como o Azure Cosmos DB.

:::image type="content" source="./media/database-security/nosql-database-security-responsibilities.png" alt-text="Responsabilidades do cliente e do provedor de banco de dados":::

O diagrama anterior mostra componentes de segurança de nuvem de alto nível, mas com quais itens você precisa se preocupar especificamente para sua solução de banco de dados? E como você pode comparar as soluções?

Recomendamos a seguinte lista de verificação de requisitos para comparação dos sistemas de banco de dados:

- Configurações de firewall e de segurança de rede
- Autenticação de usuário e controles de usuário refinados
- Capacidade de replicar dados globalmente para falhas regionais
- Capacidade de fazer failover de um data center para outro
- Replicação de dados locais em um data center
- Backup de dados automático
- Restauração de dados excluídos de backups
- Proteger e isolar dados confidenciais
- Monitoramento de ataques
- Resposta aos ataques
- Capacidade de limitar os dados geograficamente a fim de aderir a restrições de controle de dados
- Proteção física dos servidores em data centers protegidos
- Certificações

E embora pareça óbvio, as [falhas recentes em grande escala de banco de dados](https://thehackernews.com/2017/01/mongodb-database-security.html) nos lembram da importância simples, mas fundamental, dos requisitos a seguir:

- Servidores com patches que são mantidos atualizados
- HTTPS por padrão/criptografia TLS
- Contas administrativas com senhas fortes

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Como o Azure Cosmos DB proteger meu banco de dados

Vamos examinar novamente a lista anterior: quantos desses requisitos de segurança o Azure Cosmos DB fornece? Todos.

Vamos examinar cada um deles em detalhes.

|Requisito de segurança|Abordagem de segurança do Azure Cosmos DB|
|---|---|
|Segurança de rede|O uso de um firewall de IP é a primeira camada de proteção de seu banco de dados. O Azure Cosmos DB dá suporte a controles de acesso baseado em IP controlados por política para suporte de firewall de entrada. Os controles de acesso baseados em IP são semelhantes às regras de firewall usadas pelos sistemas de banco de dados tradicionais, mas são expandidos para que uma conta de banco de dados Cosmos do Azure seja acessível somente de um conjunto aprovado de computadores ou serviços de nuvem. Saiba mais no artigo de [suporte do Azure Cosmos DB firewall](how-to-configure-firewall.md) .<br><br>O Azure Cosmos DB permite que você habilite um endereço IP específico (168.61.48.0), um intervalo de IPs (168.61.48.0/8) e combinações de IPs e intervalos. <br><br>Todas as solicitações provenientes de computadores fora dessa lista de permissões são bloqueadas pelo Azure Cosmos DB. As solicitações de computadores e de serviços de nuvem aprovados devem concluir o processo de autenticação para receber o controle de acesso aos recursos.<br><br> Você pode usar [marcas de serviço de rede virtual](../virtual-network/service-tags-overview.md) para obter isolamento de rede e proteger seus recursos de Azure Cosmos DB da Internet geral. Use marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, AzureCosmosDB) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente.|
|Autorização|O Azure Cosmos DB usa o HMAC (código de autenticação de mensagem baseado em hash) para autorização. <br><br>Cada solicitação recebe o hash usando a chave de conta segredo e o próximo hash codificado em base 64 é enviado com cada chamada ao Azure Cosmos DB. Para validar a solicitação, o serviço Azure Cosmos DB usa a chave segredo e as propriedades corretas para gerar um hash e, depois, compara o valor ao valor da solicitação. Se os dois valores corresponderem, a operação será autorizada e a solicitação processada; caso contrário, haverá uma falha de autorização e a solicitação será rejeitada.<br><br>Você pode usar uma [chave primária](#primary-keys)ou um token de [recurso](secure-access-to-data.md#resource-tokens) que permite o acesso refinado a um recurso, como um documento.<br><br>Saiba mais em [Protegendo o acesso aos recursos do Azure Cosmos DB](secure-access-to-data.md).|
|Usuários e permissões|Usando a chave primária da conta, você pode criar recursos de usuário e recursos de permissão por banco de dados. Um token de recurso está associado uma permissão em um banco de dados e determina se o usuário tem acesso (leitura/gravação, somente leitura ou nenhum acesso) a um recurso de aplicativo no banco de dados. Os recursos do aplicativo incluem o contêiner, documentos, anexos, procedimentos armazenados, gatilhos e UDFs. Em seguida, o token de recurso é usado durante a autenticação para fornecer ou negar acesso ao recurso.<br><br>Saiba mais em [Protegendo o acesso aos recursos do Azure Cosmos DB](secure-access-to-data.md).|
|Integração do Active Directory (RBAC do Azure)| Você também pode fornecer ou restringir o acesso à conta do cosmos, ao banco de dados, ao contêiner e às ofertas (taxa de transferência) usando o controle de acesso (IAM) no portal do Azure. O IAM fornece controle de acesso baseado em função e integra-se ao Active Directory. Você pode usar funções internas ou funções personalizadas para indivíduos e grupos. Consulte o artigo [integração Active Directory](role-based-access-control.md) para obter mais informações.|
|Replicação global|O Azure Cosmos DB oferece distribuição global turnkey, o que permite replicar os dados em qualquer um dos data centers do Azure em todo o mundo com um clique de botão. A replicação global permite que você dimensione globalmente e forneça acesso de baixa latência aos seus dados no mundo inteiro.<br><br>No contexto de segurança, a replicação global garante a proteção dos dados contra falhas regionais.<br><br>Saiba mais em [Distribuir dados globalmente](distribute-data-globally.md).|
|Failovers regionais|Se você tiver replicado seus dados em mais de um data center, o Azure Cosmos DB reverterá automaticamente aas operações, caso um data center regional fique offline. Crie uma lista de prioridade de regiões de failover usando as regiões nas quais seus dados são replicados. <br><br>Saiba mais em [Failovers regionais no Azure Cosmos DB](high-availability.md).|
|Replicação local|Mesmo em um único data center, o Azure Cosmos DB replica automaticamente os dados para proporcionar alta disponibilidade, oferecendo a você a opção de [níveis de consistência](consistency-levels.md). Essa replicação garante um SLA de [disponibilidade](https://azure.microsoft.com/support/legal/sla/cosmos-db) de 99,99% para todas as contas de região única e todas as contas de várias regiões com consistência reduzida e 99,999% de disponibilidade de leitura em todas as contas de banco de dados de várias regiões.|
|Backups online automatizados|Os bancos de dados Cosmos do Azure são submetidos a backup regularmente e armazenados em um armazenamento com redundância geográfica. <br><br>Saiba mais em [Backup e restauração online automáticos com o Azure Cosmos DB](online-backup-and-restore.md).|
|Restaurar dados excluídos|Os backups online automatizados podem ser usados para recuperar dados que você pode ter excluído acidentalmente até 30 dias após o evento. <br><br>Saiba mais em [backup e restauração online automáticos com o Azure Cosmos DB](online-backup-and-restore.md)|
|Proteger e isolar dados confidenciais|Todos os dados nas regiões listadas em Novidades agora serão criptografados em repouso.<br><br>Dados pessoais e outros dados confidenciais podem ser isolados em contêineres específicos, e o acesso de leitura/gravação ou somente leitura pode ser limitado a usuários específicos.|
|Monitorar ataques|Ao usar o [log de auditoria e os logs de atividade](./monitor-cosmos-db.md), você pode monitorar sua conta para atividades normais e anormais. Veja as operações que foram executadas em seus recursos, quem iniciou a operação, quando a operação ocorreu, o status da operação e muito mais conforme mostrado na captura de tela após esta tabela.|
|Responder a ataques|Depois de contatar o suporte do Azure para relatar um possível ataque, começa um processo de resposta ao incidente com cinco etapas. O objetivo do processo de cinco etapas é restaurar a segurança normal do serviço e as operações o mais rápido possível após a detecção de um problema e o início de uma investigação.<br><br>Saiba mais em [Resposta da segurança do Microsoft Azure na nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).|
|Isolamento geográfico|O Azure Cosmos DB garante a governança de dados em regiões soberanas (por exemplo, Alemanha, China, US Gov).|
|Instalações protegidas|Os dados do Azure Cosmos DB são armazenados em SSDs, nos data centers protegidos do Azure.<br><br>Saiba mais em [Datacenters globais da Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Criptografia HTTPS/SSL/TLS|Todas as conexões com Azure Cosmos DB dão suporte a HTTPS. Azure Cosmos DB também dá suporte a TLS 1,2.<br>É possível impor uma versão mínima do TLS do lado do servidor. Para fazer isso, entre em contato com [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) .|
|Criptografia em repouso|Todos os dados armazenados no Azure Cosmos DB são criptografados em repouso. Saiba mais em [Criptografia em repouso do Azure Cosmos DB](./database-encryption-at-rest.md)|
|Servidores com patches|Por ser um banco de dados gerenciado, o Azure Cosmos DB elimina a necessidade de gerenciar e corrigir servidores, o que é feito automaticamente para você.|
|Contas administrativas com senhas fortes|É difícil de acreditar que precisamos mencionar esse requisito, mas ao contrário de alguns de nossos concorrentes, é impossível ter uma conta administrativa sem senha no Azure Cosmos DB.<br><br> A segurança por meio de autenticação baseada em TLS e HMAC é inclusas por padrão.|
|Certificações de proteção de dados e de segurança| Para obter a lista mais atualizada de certificações, consulte o [site de conformidade](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) geral do Azure, bem como o documento de conformidade mais recente do [Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) com todas as certificações (pesquise por Cosmos). Para obter uma leitura mais focalizada, confira 25 de abril de 2018 post [Azure #CosmosDB: seguro, privado, em conformidade que inclui SOCS 1/2 tipo 2, HITRUST, PCI DSS nível 1, ISO 27001, HIPAA, FedRAMP alto e muitos outros.

A captura de tela a seguir mostra como você pode usar os logs de atividade e log de auditoria para monitorar sua conta: :::image type="content" source="./media/database-security/nosql-database-security-application-logging.png" alt-text="Logs de atividades do Azure Cosmos DB":::

<a id="primary-keys"></a>

## <a name="primary-keys"></a>Chaves primárias

As chaves primárias fornecem acesso a todos os recursos administrativos da conta do banco de dados. Chaves primárias:

- Fornecem acesso a contas, a bancos de dados, a usuários e a permissões. 
- Não pode ser usado para fornecer acesso granular a contêineres e documentos.
- São criadas durante a criação de uma conta.
- Podem ser geradas novamente a qualquer momento.

Cada conta consiste em duas chaves primárias: uma chave primária e uma chave secundária. A finalidade das chaves duplas é para que você possa gerar novamente, ou reverter as chaves, fornecendo acesso contínuo à sua conta e dados.

Além das duas chaves primárias para a conta de Cosmos DB, há duas chaves somente leitura. Essas chaves somente leitura só permitem operações de leitura na conta. As chaves somente leitura não fornecem acesso a recursos com permissões de leitura.

Chaves primárias primárias, secundárias, somente leitura e de leitura/gravação podem ser recuperadas e geradas novamente usando o portal do Azure. Para obter instruções, veja [Exibir, copiar e gerar novamente as chaves de acesso](manage-with-cli.md#regenerate-account-key).

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Controle de acesso (IAM) no Portal do Azure - demonstrando a segurança do banco de dados NoSQL":::

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre chaves primárias e tokens de recurso, consulte [Securing Access to Azure Cosmos DB data](secure-access-to-data.md).

Para obter mais informações sobre o log de auditoria, consulte [Azure Cosmos DB log de diagnóstico](./monitor-cosmos-db.md).

Para obter mais informações sobre certificações da Microsoft, consulte [central de confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/).