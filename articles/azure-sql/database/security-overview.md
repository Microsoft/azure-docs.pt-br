---
title: Visão geral de segurança
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Saiba mais sobre a segurança no banco de dados SQL do Azure e o SQL Instância Gerenciada do Azure, incluindo como ele difere do SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 10/26/2020
ms.openlocfilehash: 39119f62fa938f5f4f6529539d4ca9a84bdf8fd7
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989183"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>Uma visão geral do banco de dados SQL do Azure e recursos de segurança do SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Este artigo descreve as noções básicas de como proteger a camada de dados de um aplicativo usando o [Azure SQL Database](sql-database-paas-overview.md), o [Azure SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md)e o [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). A estratégia de segurança descrita segue a abordagem de defesa em profundidade em camadas conforme mostrado na imagem abaixo e se movimenta de fora para dentro:

![Diagrama de defesa em camadas. Os dados do cliente são encaixados em camadas de segurança de rede, gerenciamento de acesso e proteção de informações e ameaças.](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>Segurança de rede

Banco de Dados SQL do Microsoft Azure, o SQL Instância Gerenciada e o Azure Synapse Analytics fornecem um serviço de banco de dados relacional para aplicativos de nuvem e empresariais. Para ajudar a proteger os dados do cliente, os firewalls impedem o acesso à rede ao servidor até que o acesso seja explicitamente concedido com base no endereço IP ou na origem do tráfego de rede virtual do Azure.

### <a name="ip-firewall-rules"></a>Regras de firewall de IP

As regras de firewall de IP permitem acesso a bancos de dados com base no endereço IP de origem de cada solicitação. Para obter mais informações, consulte [visão geral das regras de firewall do banco de dados SQL do Azure e do Azure Synapse Analytics](firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual

Os [pontos de extremidade de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md) estendem sua conectividade de rede virtual no backbone do Azure e habilitam o banco de dados SQL do Azure para identificar a sub-rede da rede virtual da qual o tráfego é originado. Para permitir que o tráfego alcance o Banco de Dados SQL do Azure, use as [marcas de serviço](../../virtual-network/network-security-groups-overview.md) do SQL para permitir o tráfego de saída por meio de Grupos de Segurança de Rede.

[As regras de rede virtual](vnet-service-endpoint-rule-overview.md) permitem que o banco de dados SQL do Azure aceite somente comunicações que são enviadas de sub-redes selecionadas dentro de uma rede virtual.

> [!NOTE]
> O controle de acesso com regras de firewall *não* se aplica ao **SQL instância gerenciada**. Para obter mais informações sobre a configuração de rede necessária, consulte [conectando-se a uma instância gerenciada](../managed-instance/connect-application-instance.md)

## <a name="access-management"></a>Gerenciamento de acesso

> [!IMPORTANT]
> O gerenciamento de bancos de dados e servidores no Azure é controlado pelas atribuições de função da sua conta de usuário do portal. Para obter mais informações sobre este artigo, consulte [controle de acesso baseado em função do Azure no portal do Azure](../../role-based-access-control/overview.md).

### <a name="authentication"></a>Autenticação

A autenticação é o processo de provar que o usuário é quem diz ser. O banco de dados SQL do Azure e o SQL Instância Gerenciada dão suporte a dois tipos de autenticação:

- **Autenticação do SQL**:

    A autenticação do SQL refere-se à autenticação de um usuário ao se conectar ao banco de dados SQL do Azure ou ao Azure SQL Instância Gerenciada usando o nome de usuário e a senha. Um logon de **administrador de servidor** com um nome de usuário e senha devem ser especificados quando o servidor está sendo criado. Usando essas credenciais, um **administrador de servidor** pode se autenticar em qualquer banco de dados nesse servidor ou instância como o proprietário do banco de dados. Depois disso, os usuários e logons do SQL adicionais podem ser criados pelo administrador do servidor, que permite que os usuários se conectem usando nome de usuário e senha.

- **Autenticação Azure Active Directory**:

    Azure Active Directory autenticação é um mecanismo de conexão com o [banco de dados SQL do](sql-database-paas-overview.md)Azure, o [SQL instância gerenciada do Azure](../managed-instance/sql-managed-instance-paas-overview.md) e o [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando identidades no Azure Active Directory (Azure AD). A autenticação do Azure AD permite que os administradores gerenciem centralmente as identidades e permissões de usuários de banco de dados junto com outros serviços do Azure em um local central. Isso inclui a minimização do armazenamento de senha e habilita políticas centralizadas de rotação de senha.

     Um administrador do servidor chamado o **administrador do Active Directory** deve ser criado para usar autenticação do Azure AD com o Banco de Dados SQL. Para saber mais, confira [Connecting to SQL Database By Using Azure Active Directory Authentication](authentication-aad-overview.md) (Conectando-se ao Banco de Dados SQL usando a Autenticação do Azure Active Directory). A autenticação do Azure AD dá suporte a contas gerenciadas e federadas. As contas federadas dão suporte a grupos e usuários do Windows para um domínio de cliente federado com o Azure AD.

    Mais opções de autenticação do Azure AD disponíveis são conexões de [Autenticação Universal do Active Directory para SQL Server Management Studio](authentication-mfa-ssms-overview.md), incluindo [Autenticação Multifator](../../active-directory/authentication/concept-mfa-howitworks.md) e [Acesso Condicional](conditional-access-configure.md).

> [!IMPORTANT]
> O gerenciamento de bancos de dados e servidores no Azure é controlado pelas atribuições de função da sua conta de usuário do portal. Para obter mais informações sobre este artigo, consulte [controle de acesso baseado em função do Azure em portal do Azure](../../role-based-access-control/overview.md). O controle de acesso com regras de firewall *não* se aplica ao **SQL instância gerenciada**. Consulte o seguinte artigo sobre como [se conectar a uma instância gerenciada](../managed-instance/connect-application-instance.md) para obter mais informações sobre a configuração de rede necessária.

## <a name="authorization"></a>Autorização

A autorização refere-se às permissões atribuídas a um usuário em um banco de dados no banco de dados SQL do Azure ou no Azure SQL Instância Gerenciada e determina o que o usuário tem permissão para fazer. As permissões são controladas adicionando contas de usuário a [funções de banco de dados](/sql/relational-databases/security/authentication-access/database-level-roles) e atribuindo permissões de nível de banco de dados a essas funções ou concedendo ao usuário determinadas [permissões de nível de objeto](/sql/relational-databases/security/permissions-database-engine). Para saber mais, confira [Logins and users](logins-create-manage.md) (Logons e usuários)

Como prática recomendada, crie funções personalizadas quando necessário. Adicione usuários à função com os privilégios mínimos necessários para realizar sua função de trabalho. Não atribua permissões diretamente aos usuários. A conta do administrador do servidor é um membro da função de db_owner interna, que tem permissões extensivas e só deve ser concedida a poucos usuários com tarefas administrativas. Para aplicativos, use [Executar como](/sql/t-sql/statements/execute-as-clause-transact-sql) para especificar o contexto de execução do módulo chamado ou usar [funções de aplicativo](/sql/relational-databases/security/authentication-access/application-roles) com permissões limitadas. Essa prática garante que o aplicativo que se conecta ao banco de dados tenha os privilégios mínimos necessários para o aplicativo. Seguir essas práticas recomendadas também promove a separação de tarefas.

### <a name="row-level-security"></a>Segurança em nível de linha

A segurança em nível de linha permite aos clientes controlar o acesso às linhas em uma tabela de banco de dados com base nas características do usuário executando uma consulta (por exemplo, associação a grupo ou contexto de execução). Row-Level segurança também pode ser usada para implementar conceitos de segurança personalizados baseados em rótulo. Para obter mais informações, consulte [segurança em nível de linha](/sql/relational-databases/security/row-level-security).

![Diagrama mostrando que Row-Level segurança protege as linhas individuais de um banco de dados SQL do Access por usuários por meio de um aplicativo cliente.](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Proteção contra ameaças

O banco de dados SQL e o SQL Instância Gerenciada protegem os dados do cliente fornecendo recursos de auditoria e detecção de ameaças.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Auditoria do SQL em logs de Azure Monitor e hubs de eventos

O banco de dados SQL e o SQL Instância Gerenciada a auditoria rastreia atividades de banco de dados e ajuda a manter a conformidade com os padrões de segurança ao gravar eventos de banco de dados em um log de auditoria em uma conta de armazenamento do Azure de propriedade A auditoria permite que os usuários monitorem as atividades do banco de dados em andamento, além de analisar e investigar a atividade de histórico para identificar possíveis ameaças ou suspeitas de violações de segurança e de abuso. Para obter mais informações, consulte Introdução à [auditoria do banco de dados SQL](../../azure-sql/database/auditing-overview.md).  

### <a name="advanced-threat-protection"></a>Proteção Avançada contra Ameaças

A proteção avançada contra ameaças está analisando seus logs para detectar comportamento incomum e tentativas potencialmente prejudiciais de acessar ou explorar bancos de dados. Os alertas são criados para atividades suspeitas, como injeção de SQL, potencial pós-infiltração de dados e ataques de força bruta ou para anomalias em padrões de acesso para capturar as progressão de privilégio e o uso de credenciais violadas. Os alertas são exibidos na  [central de segurança do Azure](https://azure.microsoft.com/services/security-center/), onde os detalhes das atividades suspeitas são fornecidos e recomendações para uma investigação mais detalhada fornecida junto com as ações para atenuar a ameaça. A proteção avançada contra ameaças pode ser habilitada por servidor por uma taxa adicional. Para obter mais informações, consulte Introdução [à proteção avançada contra ameaças do banco de dados SQL](threat-detection-configure.md).

![Diagrama que mostra a detecção de ameaças do SQL monitorando o acesso ao banco de dados SQL para um aplicativo Web de um invasor externo e um insider mal-intencionado.](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Proteção e criptografia de informações

### <a name="transport-layer-security-encryption-in-transit"></a>Segurança da camada de transporte (criptografia em trânsito)

O banco de dados SQL, o SQL Instância Gerenciada e a análise de Synapse do Azure protegem o cliente ao criptografar dados em movimento com o protocolo [TLS](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

O banco de dados SQL, o SQL Instância Gerenciada e o Azure Synapse Analytics impõem a criptografia (SSL/TLS) em todos os momentos para todas as conexões. Isso garante que todos os dados sejam criptografados "em trânsito" entre o cliente e o servidor, independentemente da configuração de **Encrypt** ou **TrustServerCertificate** na cadeia de conexão.

Como prática recomendada, recomendamos que na cadeia de conexão usada pelo aplicativo, você especifique uma conexão criptografada e _**não**_ confie no certificado do servidor. Isso força seu aplicativo a verificar o certificado do servidor e, portanto, impede que o aplicativo fique vulnerável a ataques de tipo intermediário.

Por exemplo, ao usar o driver ADO.NET, isso é realizado por meio de  **encrypt = true** e **TrustServerCertificate = false**. Se você obtiver sua cadeia de conexão do portal do Azure, ela terá as configurações corretas.

> [!IMPORTANT]
> Observe que alguns drivers que não são da Microsoft não podem usar o TLS por padrão ou contar com uma versão mais antiga do TLS (<1,2) para funcionar. Nesse caso, o servidor ainda permite que você se conecte ao seu banco de dados. No entanto, é recomendável que você avalie os riscos de segurança de permitir que esses drivers e aplicativos se conectem ao banco de dados SQL, especialmente se você armazená-los.
>
> Para obter mais informações sobre TLS e conectividade, consulte [Considerações sobre TLS](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (Criptografia em repouso)

A [TDE (Transparent Data Encryption) para o banco de dados SQL, o SQL instância gerenciada e o Azure Synapse Analytics](transparent-data-encryption-tde-overview.md) adiciona uma camada de segurança para ajudar a proteger dados em repouso contra acesso não autorizado ou offline a arquivos brutos ou backups. Os cenários comuns incluem roubo de data center ou descarte não seguro de hardware ou mídia, como unidades de disco e fitas de backup.O TDE criptografa todo o banco de dados usando um algoritmo de criptografia AES, que não exige que os desenvolvedores de aplicativos façam alterações nos aplicativos existentes.

No Azure, todos os bancos de dados recém-criados são criptografados por padrão e a chave de criptografia do banco de dados é protegida por um certificado de servidor interno.  A manutenção e a rotação do certificado são gerenciadas pelo serviço e não exigem nenhuma entrada do usuário. Os clientes que preferirem assumir controle das chaves de criptografia podem gerenciá-las no [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Gerenciamento de chaves com o Azure Key Vault

O suporte a [Bring your own Key](transparent-data-encryption-byok-overview.md) (BYOK) para [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) permite que os clientes apropriem-se do gerenciamento e da rotação de chaves usando [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md), o sistema de gerenciamento de chaves externas baseado em nuvem do Azure. Se o acesso do banco de dados ao cofre de chaves for revogado, um banco de dados não poderá ser descriptografado nem lido na memória. O Azure Key Vault fornece uma plataforma de gerenciamento central de chaves, utiliza HSMs (Módulos de Segurança de Hardware) rigidamente monitorados e permite a separação de funções entre o gerenciamento de chaves e dados para ajudar nos requisitos de conformidade de segurança.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (Criptografia em uso)

![Diagrama mostrando os conceitos básicos do recurso Always Encrypted. Um banco de dados SQL com um bloqueio é acessado somente por um aplicativo que contém uma chave.](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) é um recurso criado para proteger dados confidenciais armazenados em colunas de banco de dados específicas contra acesso (por exemplo, números de cartão de crédito, números de identificação nacional ou dados de _conhecimento obrigatório_). Isso inclui administradores de banco de dados ou outros usuários com privilégios que têm autorização para acessar o banco de dados para executar tarefas de gerenciamento, mas não têm a necessidade comercial de acessar os dados específicos nas colunas criptografadas. Os dados sempre são criptografados, o que significa que eles são descriptografados apenas para o processamento realizado por aplicativos cliente com acesso à chave de criptografia. A chave de criptografia nunca é exposta ao banco de dados SQL ou ao SQL Instância Gerenciada e pode ser armazenada no [repositório de certificados do Windows](always-encrypted-certificate-store-configure.md) ou no [Azure Key Vault](always-encrypted-azure-key-vault-configure.md).

### <a name="dynamic-data-masking"></a>Mascaramento de dados dinâmicos

![Diagrama mostrando a máscara de dados dinâmicos. Um aplicativo de negócios envia dados para um banco de dados SQL, que mascara os dados antes de enviá-los de volta ao aplicativo de negócios.](./media/security-overview/azure-database-ddm.png)

O mascaramento de dados dinâmicos limita a exposição de dados confidenciais mascarando-os para usuários sem privilégios. O mascaramento de dados dinâmicos descobre automaticamente os dados potencialmente confidenciais no Azure SQL Database e no SQL Instância Gerenciada e fornece recomendações acionáveis para mascarar esses campos, com impacto mínimo sobre a camada de aplicativo. Funciona ao ocultar os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designado, enquanto os dados no banco de dados não são alterados. Para obter mais informações, consulte Introdução [ao banco de dados SQL e o sql instância gerenciada mascaramento de dados dinâmicos](dynamic-data-masking-overview.md).

## <a name="security-management"></a>Gerenciamento de segurança

### <a name="vulnerability-assessment"></a>Avaliação de vulnerabilidade

A [avaliação de vulnerabilidade](sql-vulnerability-assessment.md) é um serviço fácil de ser configurado que pode descobrir, acompanhar e ajudar a corrigir vulnerabilidades potenciais do banco de dados com o objetivo de melhorar de maneira proativa a segurança geral do banco de dados. A avaliação de vulnerabilidades (VA) faz parte da oferta do Azure defender para SQL, que é um pacote unificado para recursos avançados de segurança do SQL. A avaliação de vulnerabilidade pode ser acessada e gerenciada por meio do portal central do Azure defender para SQL.

### <a name="data-discovery-and-classification"></a>Descoberta e classificação de dados

A descoberta e a classificação de dados (atualmente em versão prévia) fornecem recursos avançados incorporados ao banco de dados SQL do Azure e ao SQL Instância Gerenciada para descobrir, classificar, rotular e proteger as informações confidenciais em seus bancos. Descobrir e classificar seus dados confidenciais mais importantes (negócios/financeiros, serviços de saúde, dados pessoais, etc.) pode desempenhar uma função essencial na estatura de proteção das informações organizacionais. Isso pode servir como infraestrutura para:

- Vários cenários de segurança, como monitoramento (auditoria) e alertas sobre acesso anômalo a dados confidenciais.
- Controlando o acesso e fortalecendo a segurança de bancos de dados que contêm dados altamente confidenciais.
- Ajudar a cumprir os padrões de privacidade de dados e os requisitos de conformidade a normas.

Para obter mais informações, consulte Introdução [à classificação e descoberta de dados](data-discovery-and-classification-overview.md).

### <a name="compliance"></a>Conformidade

Além dos recursos e funcionalidades acima, que podem ajudar seu aplicativo a atender a vários requisitos de segurança, o Banco de Dados SQL do Azure também participa de auditorias regulares e foi certificado em relação a vários padrões de conformidade. Para obter mais informações, consulte a [central de confiabilidade do Microsoft Azure](https://www.microsoft.com/trust-center/compliance/compliance-overview) , em que você pode encontrar a lista mais atual de certificações de conformidade do banco de dados SQL.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma discussão sobre o uso de logons, contas de usuário, funções de banco de dados e permissões no banco de dados SQL e no SQL Instância Gerenciada, consulte [gerenciar logons e contas de usuário](logins-create-manage.md).
- Para uma discussão sobre a auditoria de banco de dados, consulte [auditoria](../../azure-sql/database/auditing-overview.md).
- Para obter uma discussão sobre a detecção de ameaças, consulte [detecção de ameaças](threat-detection-configure.md).