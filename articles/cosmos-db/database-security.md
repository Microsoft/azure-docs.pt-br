---
title: Segurança de banco de dados - Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece proteção de banco de dados e segurança para seus dados.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: rimman
ms.openlocfilehash: d37f373e98835846c2d29130781706c400086c0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60889481"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Segurança no Azure Cosmos DB – visão geral

Este artigo aborda as melhores práticas de segurança de banco de dados e os principais recursos oferecidos pelo Azure Cosmos DB para ajudar você a prevenir, detectar e responder a violações de banco de dados.
 
## <a name="whats-new-in-azure-cosmos-db-security"></a>Novidades na segurança do Azure Cosmos DB?

A criptografia em repouso agora está disponível para documentos e backups armazenados no Azure Cosmos DB em todas as regiões do Azure. A criptografia em repouso é aplicada automaticamente para clientes novos e existentes nessas regiões. Não é necessário configurar nada e você obtém a mesma latência, taxa de transferência, disponibilidade e funcionalidade excelentes de antes, com o benefício de saber que seus dados estão seguros e protegidos com a criptografia em repouso.

## <a name="how-do-i-secure-my-database"></a>Como fazer para proteger meu banco de dados? 

A segurança dos dados é uma responsabilidade compartilhada por você, o cliente e seu provedor de banco de dados. Dependendo do provedor de banco de dados que você escolher, o tamanho da responsabilidade pode variar. Se você escolher uma solução local, precisará fornecer tudo, desde a proteção do ponto de extremidade até a segurança física do seu hardware, o que não é uma tarefa fácil. Se você escolher um provedor de banco de dados em nuvem PaaS, como o Azure Cosmos DB, sua área de interesse será consideravelmente reduzida. A imagem a seguir, retirada do white paper [Responsabilidades compartilhadas para a computação em nuvem](https://aka.ms/sharedresponsibility) da Microsoft, mostra como sua responsabilidade diminui com um provedor de PaaS, como o Azure Cosmos DB.

![Responsabilidades do cliente e do provedor de banco de dados](./media/database-security/nosql-database-security-responsibilities.png)

O diagrama anterior mostra componentes de segurança de nuvem de alto nível, mas com quais itens você precisa se preocupar especificamente para sua solução de banco de dados? E como você pode comparar as soluções? 

Recomendamos a seguinte lista de verificação de requisitos para comparação dos sistemas de banco de dados:

- Configurações de firewall e de segurança de rede
- Autenticação de usuário e controles de usuário refinados
- Capacidade de replicar dados globalmente para falhas regionais
- Capacidade de executar failovers de um data center para outro
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
- Servidores com patches que permanecem atualizados
- Criptografia HTTPS por padrão/SSL
- Contas administrativas com senhas fortes

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Como o Azure Cosmos DB protege meu banco de dados?

Vamos examinar novamente a lista anterior: quantos desses requisitos de segurança o Azure Cosmos DB fornece? Todos.

Vamos examinar cada um deles em detalhes.

|Requisito de segurança|Abordagem de segurança do Azure Cosmos DB|
|---|---|
|Segurança de rede|O uso de um firewall de IP é a primeira camada de proteção de seu banco de dados. O Azure Cosmos DB dá suporte a controles de acesso baseado em IP controlados por política para suporte de firewall de entrada. Os controles de acesso baseado em IP são semelhantes às regras de firewall usadas pelos sistemas de banco de dados tradicionais, mas são expandidos, de modo que uma conta de banco de dados do Azure Cosmos DB possa ser acessada apenas em um conjunto aprovado de computadores ou serviços de nuvem. <br><br>O Azure Cosmos DB permite que você habilite um endereço IP específico (168.61.48.0), um intervalo de IPs (168.61.48.0/8) e combinações de IPs e intervalos. <br><br>Todas as solicitações provenientes de computadores fora dessa lista de permissões são bloqueadas pelo Azure Cosmos DB. As solicitações de computadores e de serviços de nuvem aprovados devem concluir o processo de autenticação para receber o controle de acesso aos recursos.<br><br>Saiba mais em [Suporte ao firewall do Azure Cosmos DB](firewall-support.md).|
|Autorização|O Azure Cosmos DB usa o HMAC (código de autenticação de mensagem baseado em hash) para autorização. <br><br>Cada solicitação recebe o hash usando a chave de conta segredo e o próximo hash codificado em base 64 é enviado com cada chamada ao Azure Cosmos DB. Para validar a solicitação, o serviço Azure Cosmos DB usa a chave segredo e as propriedades corretas para gerar um hash e, depois, compara o valor ao valor da solicitação. Se os dois valores corresponderem, a operação será autorizada e a solicitação processada; caso contrário, haverá uma falha de autorização e a solicitação será rejeitada.<br><br>Use uma [chave mestra](secure-access-to-data.md#master-keys) ou um [token de recurso](secure-access-to-data.md#resource-tokens) permitindo o acesso refinado a um recurso, como um documento.<br><br>Saiba mais em [Protegendo o acesso aos recursos do Azure Cosmos DB](secure-access-to-data.md).|
|Usuários e permissões|Ao usar a chave mestra para a conta, você pode criar recursos de usuário e de permissão por banco de dados. Um token de recurso está associado uma permissão em um banco de dados e determina se o usuário tem acesso (leitura/gravação, somente leitura ou nenhum acesso) a um recurso de aplicativo no banco de dados. Os recursos do aplicativo incluem o contêiner, documentos, anexos, procedimentos armazenados, gatilhos e UDFs. Em seguida, o token de recurso é usado durante a autenticação para fornecer ou negar acesso ao recurso.<br><br>Saiba mais em [Protegendo o acesso aos recursos do Azure Cosmos DB](secure-access-to-data.md).|
|Integração do Active Directory (RBAC)| Você também pode fornecer acesso à conta do banco de dados usando o controle de acesso (IAM) no Portal do Azure, conforme mostrado na captura de tela após esta tabela. O IAM fornece controle de acesso baseado em função e integra-se ao Active Directory. Você pode usar funções internas ou personalizadas para indivíduos e grupos, conforme mostra a imagem a seguir.|
|Replicação global|O Azure Cosmos DB oferece distribuição global turnkey, o que permite replicar os dados em qualquer um dos data centers do Azure em todo o mundo com um clique de botão. A replicação global permite que você dimensione globalmente e forneça acesso de baixa latência aos seus dados no mundo inteiro.<br><br>No contexto de segurança, a replicação global garante a proteção dos dados contra falhas regionais.<br><br>Saiba mais em [Distribuir dados globalmente](distribute-data-globally.md).|
|Failovers regionais|Se você tiver replicado seus dados em mais de um data center, o Azure Cosmos DB reverterá automaticamente aas operações, caso um data center regional fique offline. Crie uma lista de prioridade de regiões de failover usando as regiões nas quais seus dados são replicados. <br><br>Saiba mais em [Failovers regionais no Azure Cosmos DB](high-availability.md).|
|Replicação local|Mesmo em um único data center, o Azure Cosmos DB replica automaticamente os dados para proporcionar alta disponibilidade, oferecendo a você a opção de [níveis de consistência](consistency-levels.md). Isso garante [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/cosmos-db) de 99,99% para todas as contas de região única e todas as contas de várias regiões com consistência amena e 99,999% de disponibilidade de leitura em todas as contas de banco de dados de várias regiões.|
|Backups online automatizados|Os bancos de dados do Azure Cosmos DB passam por backup e são armazenados regularmente em um repositório com redundância geográfica. <br><br>Saiba mais em [Backup e restauração online automáticos com o Azure Cosmos DB](online-backup-and-restore.md).|
|Restaurar dados excluídos|Os backups online automatizados podem ser usados para recuperar dados que você pode ter excluído acidentalmente até 30 dias após o evento. <br><br>Saiba mais em [Backup e restauração online automáticos com o Azure Cosmos DB](online-backup-and-restore.md)|
|Proteger e isolar dados confidenciais|Todos os dados nas regiões listadas em Novidades agora serão criptografados em repouso.<br><br>Dados pessoais e outros dados confidenciais podem ser isolados em contêineres específicos, e o acesso de leitura/gravação ou somente leitura pode ser limitado a usuários específicos.|
|Monitorar ataques|Com o [log de auditoria e os logs de atividade](logging.md), você pode monitorar as atividades normais e anormais de sua conta. Veja as operações que foram executadas em seus recursos, quem iniciou a operação, quando a operação ocorreu, o status da operação e muito mais conforme mostrado na captura de tela após esta tabela.|
|Responder a ataques|Depois de contatar o suporte do Azure para relatar um possível ataque, começa um processo de resposta ao incidente com cinco etapas. O objetivo do processo de cinco etapas é restaurar a segurança normal do serviço e as operações o mais rápido possível após a detecção de um problema e o início de uma investigação.<br><br>Saiba mais em [Resposta da segurança do Microsoft Azure na nuvem](https://aka.ms/securityresponsepaper).|
|Isolamento geográfico|O Azure Cosmos DB garante a governança de dados em regiões soberanas (por exemplo, Alemanha, China, US Gov).|
|Instalações protegidas|Os dados do Azure Cosmos DB são armazenados em SSDs, nos data centers protegidos do Azure.<br><br>Saiba mais em [Datacenters globais da Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Criptografia HTTPS/SSL/TLS|Todas as interações do cliente com o serviço do Azure Cosmos DB são compatíveis com SSL/TLS 1.2. Além disso, toda replicação dentro do datacenter e entre datacenters é imposta por SSL/TLS 1.2.|
|Criptografia em repouso|Todos os dados armazenados no Azure Cosmos DB são criptografados em repouso. Saiba mais em [Criptografia em repouso do Azure Cosmos DB](./database-encryption-at-rest.md)|
|Servidores com patches|Por ser um banco de dados gerenciado, o Azure Cosmos DB elimina a necessidade de gerenciar e corrigir servidores, o que é feito automaticamente para você.|
|Contas administrativas com senhas fortes|É difícil de acreditar que precisamos mencionar esse requisito, mas ao contrário de alguns de nossos concorrentes, é impossível ter uma conta administrativa sem senha no Azure Cosmos DB.<br><br> A segurança via autenticação baseada em segredo de SSL e HMAC está incorporada por padrão.|
|Certificações de proteção de dados e de segurança|Para obter a lista de certificações de dados mais atualizada, consulte o [Local de Conformidade do Azure](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) global e o último [Documento de Conformidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) com todas as certificações (pesquise Cosmos). Para uma leitura mais detalhada, confira a postagem de 25 de abril de 2018 do [Azure #CosmosDB: Seguro, particular, compatível](https://azure.microsoft.com/blog/azure-cosmosdb-secure-private-compliant/) que inclui o SOCS 1/2 Tipo 2, HITRUST, PCI DSS Nível 1, ISO 27001, HIPAA, FedRAMP High, e muitos outros.

A captura de tela a seguir mostra a Integração com o Active Directory (RBAC) usando o controle de acesso (IAM) no portal do Azure: ![Controle de acesso (IAM) no portal do Azure - demonstrando a segurança de banco de dados](./media/database-security/nosql-database-security-identity-access-management-iam-rbac.png)

A captura de tela a seguir mostra como é possível usar os logs de atividade e log de auditoria para monitorar a conta: ![Logs de atividades do Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais detalhes sobre chaves mestras e tokens de recurso, consulte [Protegendo o acesso aos dados do Azure Cosmos DB](secure-access-to-data.md).

Para obter mais detalhes sobre o log de auditoria, consulte [Log de diagnósticos do Azure Cosmos DB](logging.md).

Para obter mais detalhes sobre as certificações da Microsoft, confira [Central de Confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/).
