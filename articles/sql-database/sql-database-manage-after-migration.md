---
title: Gerenciar bancos de dados individuais e em pool após a migração - Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como gerenciar o banco de dados após a migração para o Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: a83bc6518409add8a0732e5a0b17ab46c36564af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703245"
---
# <a name="new-dba-in-the-cloud--managing-your-single-and-pooled-databases-in-azure-sql-database"></a>DBA novo na nuvem – gerenciamento de bancos de dados individuais e em pool no Banco de Dados SQL do Azure

Mover do ambiente autogerenciado autocontrolado tradicional para um ambiente PaaS pode parecer um pouco difícil primeiro. Como desenvolvedor de aplicativos ou um DBA, você deseja saber os principais recursos da plataforma que ajudam a manter seu aplicativo disponível, com bom desempenho, seguro e flexível – sempre. Este artigo visa fazer exatamente isso. O artigo organiza sucintamente os recursos e fornece algumas orientações sobre como usar melhor os principais recursos do Banco de Dados SQL com bancos de dados individuais e em pool para gerenciar e fazer a manutenção de seu aplicativo em execução com eficiência e obter os melhores resultados na nuvem. O público-alvo típico para este artigo seria aqueles que:

- Esteja avaliando a migração de seu(s) aplicativo(s) para o Banco de Dados SQL do Azure – Modernizando seu(s) aplicativo(s).
- Estão no processo de migração de seus aplicativos – cenário de migração em andamento.
- Recentemente concluiram a migração para o Azure SQL DB – DBA novo na nuvem.

Este artigo discute algumas das principais características do Banco de Dados SQL do Azure como uma plataforma da qual você pode se beneficiar prontamente ao trabalhar com bancos de dados individuais e em pools elásticos. Eles são os seguintes:

- BCDR (continuidade de negócios e recuperação de desastres)
- Segurança e conformidade
- Monitoramento e manutenção de banco de dados inteligente
- Movimentação de dados

> [!NOTE]
> Este artigo se aplica às seguintes opções de implantação do Banco de Dados SQL do Azure: bancos de dados individuais e pools elásticos. Ele não se aplica à opção de implantação de instância gerenciada do Banco de Dados SQL.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>BCDR (continuidade de negócios e recuperação de desastres)

Capacidades de continuidade de negócios e recuperação de desastres permitem continuar seu negócio, como de costume, em caso de um desastre. O desastre pode ser um evento de nível de banco de dados (por exemplo, alguém por engano descarta uma tabela crucial) ou um evento de nível no centro de dados (catástrofe regional, por exemplo, um tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Como fazer para criar e gerenciar backups no Banco de Dados SQL?

Você não cria backups no Azure SQL DB e isso porque você não precisa. O Banco de Dados SQL faz backup de bancos de dados automaticamente, para que você não tenha que se preocupar com agendamento, realização e gerenciamento de backups. A plataforma faz um backup completo toda semana, um backup diferencial que backup em intervalos de algumas horas e um backup de log a cada 5 minutos para garantir que a recuperação de desastres seja eficiente e a mínimo de perda de dados. O primeiro backup completo acontece assim que você criar um banco de dados. Esses backups ficam disponíveis por um determinado período chamado de "Período de retenção" e varia de acordo com a camada de serviço que você escolhe. O Banco de Dados SQL fornece a capacidade de restaurar em qualquer ponto no tempo nessa retenção usando [Recuperação de ponto no tempo (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

|Camada de serviço|Período de retenção em dias|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

Além disso, o recurso [Retenção de Longo Prazo (LTR)](sql-database-long-term-retention.md) permite que você mantenha seus arquivos de backup por um período mais longo especificamente, por até 10 anos, e faça a restauração desses backups em qualquer ponto nesse período. Além disso, os backups de banco de dados são mantidos em um armazenamento replicado geograficamente para garantir a resiliência de uma catástrofe regional. Você também pode restaurar esses backups em qualquer região do Azure em qualquer momento dentro do período de retenção. Confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Como fazer para garantir a continuidade no caso de um desastre no nível do datacenter ou catástrofe regional?

Os backups de banco de dados são armazenados em um armazenamento replicado geograficamente para garantir que, no caso de um desastre regional, você possa restaurar o backup para outra região do Azure. Isso é chamado de restauração geográfica. O RPO (Objetivo de Ponto de Recuperação) para isso geralmente é de < 1 hora e o ERT (Tempo Estimado de Recuperação – alguns minutos a horas).

Para bancos de dados de missão crítica, o Azure SQL DB oferece replicação geográfica ativa. Isso essencialmente cria uma cópia secundária replicada geograficamente de seu banco de dados original em outra região. Por exemplo, se seu banco de dados está inicialmente hospedado na região Oeste dos EUA do Azure e você deseja resiliência a desastre regional. Você precisará criar uma replicação geográfica do banco de dados ativa no Oeste dos EUA para dizer Leste dos EUA. Quando a calamidade atingir o Oeste dos EUA, você pode realizar failover na região Leste dos EUA. Configurá-los em um Grupo de Failover Automático é ainda melhor, pois isso garante que o banco de dados passa realizar failover automaticamente para o secundário no Leste dos EUA em caso de desastre. O RPO para isso é de < 5 segundos e o ERT de <30 segundos.

Se um grupo de failover automático não estiver configurado, seu aplicativo precisará monitorar um desastre e iniciar um failover para o secundário ativamente. Você pode criar até 4 dessas réplicas geográficas ativas em diferentes regiões do Azure. E fica ainda melhor. Você também pode acessar essas réplicas geográficas secundárias ativas para acesso somente leitura. Isso é muito útil para reduzir a latência para um cenário de aplicativo distribuído geograficamente.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Como o plano de recuperação de desastres muda dos locais para o Banco de Dados SQL

Em resumo, a instalação do SQL Server local tradicional requer que você gerencie ativamente sua disponibilidade usando recursos como clustering de failover, espelhamento de banco de dados, replicação de transação ou envio de logs e mantenha e gerencie os backups para garantir a Continuidade dos Negócios. Com o Banco de Dados SQL, a plataforma gerencia isso para você, para que você possa se concentrar no desenvolvimento e otimização do seu aplicativo de banco de dados e não se preocupe tanto com o gerenciamento de desastres. É possível ter planos de recuperação de desastres e backup configurados e funcionando com apenas alguns cliques no Portal do Azure (ou alguns comandos usando as APIs do PowerShell).

Para saber mais sobre a Recuperação de desastre, consulte: [Recuperação de Desastre do Azure SQL DB 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Segurança e conformidade

O Banco de Dados SQL leva muito a sério a segurança e a privacidade. A segurança no Banco de Dados SQL do Microsoft Azure está disponível no nível do banco de dados e no nível da plataforma e é melhor compreendida quando categorizada em várias camadas. Em cada camada, você tem que controlar e fornecer segurança ideal para seu aplicativo. As camadas são:

- Identidade e autenticação ([autenticação do Windows/SQL e autenticação do Azure Active Directory [AAD]](sql-database-control-access.md)).
- Atividade de monitoramento ([Auditoria](sql-database-auditing.md) e [detecção de ameaças](sql-database-threat-detection.md)).
- Proteção de dados reais ([Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) e [Always Encrypted [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Controlando o acesso a dados confidenciais e privilegiados ([Segurança em nível de linha](/sql/relational-databases/security/row-level-security) e [Máscara de Dados Dinâmicos do Banco de Dados SQL](/sql/relational-databases/security/dynamic-data-masking)).

A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) oferece gerenciamento de segurança centralizado entre cargas de trabalho em execução no Azure, no local e em outras nuvens. Você pode ver se a proteção essencial para o Banco de Dados SQL, como a [Auditoria](sql-database-auditing.md) e a [Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), está configurada em todos os recursos e criar políticas com base em seus próprios requisitos.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Quais métodos de autenticação de usuários são oferecidos no Banco de Dados SQL

Há [dois métodos de autenticação](sql-database-control-access.md#authentication) oferecidos no Banco de Dados SQL:

- [Autenticação do Azure Active Directory](sql-database-aad-authentication.md)
- Autenticação do SQL

Não há suporte para a Autenticação Integrada do Windows. O Azure Active Directory (AD) é um serviço de gerenciamento de identidades e acesso centralizado. Com isso você pode fornecer muito convenientemente um acesso de logon único (SSO) para todo o pessoal em sua organização. Isso significa que as credenciais são compartilhadas entre todos os serviços do Azure para autenticação mais simples. O ADD oferece suporte a [MFA (Autenticação multifator)](sql-database-ssms-mfa-authentication.md) e, com [alguns cliques](../active-directory/hybrid/how-to-connect-install-express.md), pode ser integrado ao Windows Server Active Directory. A autenticação do SQL funciona exatamente como você o vinha usando no passado. Você fornece uma nome de usuário/senha e pode autenticar usuários para qualquer banco de dados em um determinado servidor do Banco de Dados SQL. Isso também permite que o Banco de Dados SQL e o SQL Data Warehouse ofereçam autenticação multifator e contas de usuário convidado em um domínio do Azure AD. Se você já tiver um Active Directory local, poderá federar o diretório com o Azure Active Directory para estender seu diretório do Azure.

|**Se você...**|**Banco de Dados SQL / SQL Data Warehouse**|
|---|---|
|Preferir não usar o Azure AD (Active Directory) no Azure|Use a [autenticação do SQL](sql-database-security-overview.md)|
|Usou AD no SQL Server local|[Federe o AD com o Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)e use a autenticação do Azure AD. Com isso, você pode usar o logon único.|
|Precisar impor MFA (autenticação multifator)|Exija MFA como uma política por meio de [Acesso Condicional da Microsoft](sql-database-conditional-access.md)e use a [Autenticação universal do Azure AD com suporte a MFA](sql-database-ssms-mfa-authentication.md).|
|Tiver contas de convidado de contas da Microsoft (live.com, outlook.com) ou outros domínios (gmail.com)|Use a [Autenticação universal do Azure AD](sql-database-ssms-mfa-authentication.md) no Banco de Dados SQL/Data Warehouse, que aproveita a [colaboração B2B do Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Estiver conectado ao Windows usando suas credenciais do Azure AD de um domínio federado|Use a [autenticação integrada do Azure AD](sql-database-aad-authentication-configure.md).|
|Estiver conectado ao Windows usando credenciais de um domínio não federado com o Azure|Use a [autenticação integrada do Azure AD](sql-database-aad-authentication-configure.md).|
|Tiver serviços de camada intermediária que precisam se conectar ao Banco de Dados SQL ou ao SQL Data Warehouse|Use a [autenticação integrada do Azure AD](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Como fazer para limitar ou controlar o acesso de conectividade ao meu banco de dados?

Há várias técnicas à sua disposição que podem ser usadas para obter a organização de conectividade ideal para seu aplicativo.

- Regras de firewall
- Pontos de Extremidade de Serviço VNet
- IPs Reservados

#### <a name="firewall"></a>Firewall

Um firewall impede o acesso ao seu servidor de uma entidade externa, permitindo somente acesso de entidades específicas a seu servidor do Banco de Dados SQL. Por padrão, todas as conexões e bancos de dados dentro do servidor do Banco de Dados SQL são rejeitadas, exceto as conexões de outros serviços do Azure. Com uma regra de firewall, você pode abrir o acesso ao seu servidor somente para entidades (por exemplo, uma máquina de desenvolvedor) que você aprovar, concedendo permissão a esse endereço IP do computador por meio do firewall. Ele também permite que você especifique um intervalo de endereços IP para os quais deseja permitir o acesso ao servidor do Banco de Dados SQL. Por exemplo, endereços IP de computador de desenvolvedor na sua organização podem ser adicionados de uma só vez especificando-se um intervalo na página de configurações Firewall.

Você pode criar regras de firewall no nível de servidor ou no nível de banco de dados. Regras de firewall de IP no nível do servidor podem ser criadas usando o portal do Azure ou com o SSMS. Para saber mais sobre como configurar uma regra de firewall no nível de servidor e no nível de banco de dados, consulte: [Criar regras de firewall de IP no Banco de Dados SQL](sql-database-security-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Pontos de extremidade de serviço

Por padrão, o Banco de Dados SQL está configurado para "Permitir que os serviços do Azure acessem o servidor", o que significa que todas as Máquinas Virtuais no Azure podem tentar se conectar ao banco de dados. Essas tentativas ainda precisam ser autenticadas. No entanto, se não quiser que seu banco de dados esteja acessível para nenhum IP do Azure, você pode desabilitar "Permitir que os serviços do Azure acessem o servidor". Além disso, você pode configurar [Pontos de Extremidade de Serviço VNET](sql-database-vnet-service-endpoint-rule-overview.md).

Pontos de extremidade de serviço (SE) permitem que você exponha seus recursos essenciais do Azure apenas para a sua própria rede virtual privada no Azure. Fazendo isso, você essencialmente elimina o acesso público aos seus recursos. O tráfego entre sua rede virtual para o Azure permanece na rede de backbone do Azure. Sem SE você, obtém um roteamento de pacotes com tunelamento forçado. Sua rede virtual força o tráfego de Internet para sua organização e o tráfego do serviço do Azure a passarem pela mesma rota. Com pontos de extremidade de serviço, você pode otimizar isso desde o fluxo de pacotes diretamente de sua rede virtual para o serviço de rede de backbone do Azure.

![Ponto de extremidade de serviço VNet](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>IPs Reservados

Outra opção é provisionar [IPs reservados](../virtual-network/virtual-networks-reserved-public-ip.md) para suas VMs e a colocar na lista de permissões os endereços IP dessas VMs nas configurações de firewall do servidor. Ao atribuir IPs reservados, você evita a necessidade de ter que atualizar as regras de firewall com a alteração dos endereços IP.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Em qual porta eu me conecto ao Banco de Dados SQL

Porta 1433. O Banco de Dados SQL se comunica por essa porta. Para se conectar de uma rede corporativa, você precisa adicionar uma regra de saída nas configurações do firewall da sua organização. Como uma orientação, evite a exposição da porta 1433 fora do limite do Azure. Você pode executar o SSMS no Azure usando o [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Isso não exige que você abra conexões de saída para a porta 1433; o IP é estático para que o banco de dados só possa ser aberto para o RemoteApp e dá suporte à MFA (Autenticação Multifator).

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Como eu monitoro e controlo a atividade no meu servidor e banco de dados no Banco de Dados SQL

#### <a name="sql-database-auditing"></a>Auditoria do Banco de Dados SQL

Com o Banco de Dados SQL, você pode ativar ON Auditing para controlar eventos de banco de dados. A [Auditoria do Banco de Dados SQL](sql-database-auditing.md) registra eventos de banco de dados e grava-os em um arquivo de log de auditoria em sua Conta de Armazenamento do Microsoft Azure. A auditoria é especialmente útil se você pretende ter uma noção de possíveis violações de segurança e política, manter a conformidade normativa etc. Ela permite definir e configurar certas categorias de eventos que você acha que precisa de auditoria e com base no que você pode obter relatórios pré-configurados e um painel para obter uma visão geral de eventos que ocorrem em seu banco de dados. Você pode aplicar essas políticas de auditoria no nível do banco de dados ou no nível do servidor. Um guia sobre como ativar a auditoria para o servidor/banco de dados, consulte: [Habilitar a Auditoria do Banco de Dados SQL](sql-database-security-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Detecção de ameaças

Com a [detecção de ameaças](sql-database-threat-detection.md), você tem a capacidade de agir facilmente sobre violações de segurança ou política descobertas por auditoria. Não é preciso ser um especialista em segurança para tratar potenciais ameaças ou violações no seu sistema. A detecção de ameaças também tem alguns recursos internos, como detecção de SQL Injection. SQL Injection é uma tentativa de alterar ou comprometer os dados e uma maneira muito comum de ataque a um aplicativo de banco de dados em geral. A detecção de ameaças é executada em vários conjuntos de algoritmos que detectam possíveis vulnerabilidades e ataques de injeção de SQL, bem como padrões de acesso anormais do banco de dados (como o acesso de um local fora do comum ou por uma entidade desconhecida). Os agentes de segurança ou outros administradores designados recebem uma notificação por email se uma ameaça é detectada no banco de dados. Cada notificação fornece detalhes da atividade suspeita e recomendações sobre como investigar e minimizar a ameaça. Para saber como ativar a Detecção de ameaças, consulte: [Habilitar detecção de ameaças](sql-database-security-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Como fazer para proteger meus dados em geral no Banco de Dados SQL?

A criptografia fornece um forte mecanismo de proteção e segurança de dados confidenciais contra intrusos. Seus dados criptografados não têm nenhuma utilidade sem a chave de descriptografia. Portanto, ele adiciona uma camada extra de proteção sobre as camadas de segurança existentes criadas no Banco de Dados SQL. Há dois aspectos para proteger seus dados no Banco de Dados SQL:

- Os dados que estão em repouso nos arquivos de dados e log
- Os dados que estão em andamento

No Banco de Dados SQL, por padrão, os dados em repouso nos arquivos de dados e de log no subsistema de armazenamento são completamente e sempre criptografados por meio de [Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Seus backups também são criptografados. Com TDE, não é necessária nenhuma alteração no lado do aplicativo que está acessando esses dados. A criptografia e descriptografia ocorrem de modo transparente; por isso o nome.
Para proteger dados confidenciais em trânsito e em repouso, o Banco de Dados SQL fornece um recurso chamado [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). O AE é uma forma de criptografia no lado do cliente que criptografa as colunas confidenciais no banco de dados (para que fiquem em texto cifrado para administradores de banco de dados e usuários não autorizados). O servidor recebe os dados criptografados em primeiro lugar. A chave para o Always Encrypted também é armazenada no lado do cliente, para que somente clientes autorizados possam descriptografar as colunas confidenciais. O servidor e os administradores de dados não podem ver os dados confidenciais, já que as chaves de criptografia ficam armazenadas no cliente. O AE criptografa colunas confidenciais na tabela de ponta a ponta, de clientes não autorizados para o disco físico. O AE dá suporte a comparações de igualdade atualmente, para que os DBAs possam continuar a consultar colunas criptografadas como parte dos comandos SQL. O Always Encrypted pode ser usado com uma variedade de opções de armazenamento de chaves, como o [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), o repositório de certificados do Windows e os módulos de segurança de hardware locais.

|**Características**|**Always Encrypted**|**Transparent Data Encryption**|
|---|---|---|
|**Expansão de criptografia**|Ponta a ponta|Dados em repouso|
|**O servidor de banco de dados pode acessar dados confidenciais**|Não |Sim, desde que a criptografia seja para os dados em repouso|
|**Operações de T-SQL permitidas**|Comparação de igualdade|Toda a área de superfície do T-SQL está disponível|
|**Alterações de aplicativo necessárias para usar o recurso**|Mínimo|Muito Mínimo|
|**Granularidade de criptografia**|Nível de coluna|Nível de banco de dados|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Como posso limitar o acesso a dados confidenciais em meu banco de dados

Cada aplicativo tem uma determinado quantidade de dados confidenciais no banco de dados que precisam ser protegidos de ficarem visíveis para todos. Uma determinada equipe dentro da organização precisa visualizar esses dados, porém outras não devem ser capazes de visualizá-los. Um exemplo é os salários de funcionários. Um gerente precisaria de acesso às informações de salário de seus funcionários diretos, no entanto, os membros individuais da equipe não devem ter acesso às informações de salário de seus colegas. Outro cenário é os desenvolvedores de dados que podem interagir com dados confidenciais durante as fases de desenvolvimento ou teste, por exemplo, CPFs de clientes. Essas informações, novamente, não precisam ser expostas para o desenvolvedor. Nesses casos, dados confidenciais precisam ser mascarados ou não serem expostos de forma alguma. O Banco de Dados SQL oferece duas abordagens para impedir que usuários não autorizados possam visualizar dados confidenciais:

A [Máscara de Dados Dinâmicos](sql-database-dynamic-data-masking-get-started.md) é um recurso de mascaramento de dados que permite que você limite a exposição de dados confidenciais mascarando-os para usuários sem privilégios na camada de aplicativo. Defina uma regra de mascaramento que pode criar um padrão de mascaramento (por exemplo, para mostrar apenas os quatro últimos dígitos de um SSN de ID nacional: XXX-XX-0000 e marque a maior parte dele como Xs) e identifique quais usuários devem ser excluídos da regra de mascaramento. O mascaramento ocorre durante a execução e várias funções de mascaramento estão disponíveis para várias categorias de dados. O mascaramento de dados dinâmicos permite automaticamente detectar dados confidenciais no banco de dados e aplicar mascaramento a eles.

A [segurança em nível de linha](/sql/relational-databases/security/row-level-security) permite que você controle o acesso no nível de linha. Ou seja, determinadas linhas em uma tabela de banco de dados com base no usuário que executa a consulta (uma associação de grupo ou um contexto de execução) são ocultadas. A restrição de acesso é feita no nível do banco de dados em vez de em uma camada de aplicativo, para simplificar a lógica do aplicativo. Comece criando um predicado de filtro, filtrando linhas que não devem ser expostas e a política de segurança e, em seguida, definindo quem tem acesso a essas linhas. Por fim, o usuário final executa sua consulta e, dependendo do privilégio de usuário, ele visualiza essas linhas restritas ou não pode vê-las de forma alguma.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Como fazer para gerenciar chaves de criptografia na nuvem?

Há opções de gerenciamento de chaves para Always Encrypted (criptografia no lado do cliente) e Transparent Data Encryption (criptografia em repouso). É recomendável que você regularmente gire as chaves de criptografia. A frequência de rotação deve ser alinhada com os regulamentos internos da organização e requisitos de conformidade.

#### <a name="transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE)

Há uma hierarquia de duas chaves na TDE – os dados em cada banco de dados do usuário são criptografados por uma DEK (chave de criptografia de banco de dados) exclusiva do banco de dados AES-256 simétrica que, por sua vez, é criptografada por uma chave mestra assimétrica RSA 2048 exclusiva do servidor. A chave mestra pode ser gerenciada:

- Automaticamente pela plataforma – Banco de Dados SQL.
- Ou por você usando o [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) como o repositório de chaves.

Por padrão, a chave mestra para Transparent Data Encryption é gerenciada pelo serviço Banco de Dados SQL para sua conveniência. Se sua organização deseja ter controle sobre a chave mestra, há uma opção para usar o Azure Key Vault (sql-database-always-encrypted-azure-key-vault.md) como o repositório de chaves. Usando o Azure Key Vault, sua organização assume o controle sobre o provisionamento, a rotação e a permissão das chaves. [Girar ou alternar o tipo de uma chave mestra de TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) é rápido, pois isso só criptografa novamente a DEK. Para organizações com separação de funções entre segurança e gerenciamento de dados, um administrador de segurança pode provisionar o material da chave para a chave mestra de TDE no Azure Key Vault e fornecer um identificador de chave do Azure Key Vault para o administrador de banco de dados para uso na criptografia em repouso em um servidor. O Cofre de Chaves foi projetado para que a Microsoft não veja nem extraia nenhuma chave de criptografia. Você também pode obter um gerenciamento centralizado de chaves para sua organização.

#### <a name="always-encrypted"></a>Always Encrypted

Há também uma [hierarquia de duas chaves](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) em Always Encrypted - uma coluna de dados confidenciais é criptografada por uma CEK (chave de criptografia de coluna) AES 256 que, por sua vez, é criptografada por uma CMK (chave mestra da coluna). Os drivers de cliente fornecidos para Always Encrypted não têm nenhuma limitação de tamanho das CMKs. O valor criptografado da CEK é armazenado no banco de dados, e a CMK é armazenada em um repositório de chaves confiável, como o repositório de certificados do Windows, o Azure Key Vault ou um módulo de segurança de hardware.

- Tanto a [CEK quanto a CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) podem ser giradas.
- A rotação da CEK é um tamanho de operação de dados e pode ser demorada, dependendo do tamanho das tabelas contendo colunas criptografadas. Portanto, é recomendável planejar rotações de CEK adequadamente.
- No entanto, a rotação da CMK não interfere no desempenho do banco de dados e pode ser feita com funções separadas.

O diagrama a seguir mostra as opções de armazenamento de chaves para as chaves mestras de coluna no Always Encrypted

![Provedores de repositório CMK Always Encrypted](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Como otimizar e proteger o tráfego entre a minha organização e o Banco de Dados SQL

O tráfego de rede entre sua organização e o Banco de Dados SQL geralmente seria é roteado pela rede pública. No entanto, se optar por otimizar esse caminho e torná-lo mais seguro, você pode examinar Rota Expressa. A Rota Expressa essencialmente permite estender sua rede corporativa na plataforma Azure por meio de uma conexão privada. Fazendo isso, você não passa pela Internet pública. Você também obtém maior segurança, confiabilidade e otimização de roteamento que resulta em menores latências de rede e muito mais rapidez do que normalmente conseguiria passando pela Internet pública. Se está planejando transferir uma parte significativa dos dados entre sua organização e o Azure, usar a Rota Expressa pode gerar benefícios de custo. Você pode escolher entre três modelos diferentes de conectividade para a conexão de sua organização com o Azure:

- [Co-posicionamento do Exchange de Nuvem](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [De qualquer ponto a qualquer ponto](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Ponto a ponto](../expressroute/expressroute-connectivity-models.md#Ethernet)

A Rota Expressa permite aumentar até 2 vezes o limite da largura de banda adquirida sem custos adicionais. Também é possível configurar conectividade entre regiões usando a Rota expressa. Para ver uma lista de provedores de conectividade de ER, consulte: [Localizações de Emparelhamento e Parceiros do ExpressRoute](../expressroute/expressroute-locations.md). Os artigos abaixo descrevem a Rota Expressa em mais detalhes:

- [Introdução à Rota Expressa](../expressroute/expressroute-introduction.md)
- [Pré-requisitos](../expressroute/expressroute-prerequisites.md)
- [Fluxos de trabalho](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>O Banco de Dados SQL está em conformidade com os requisitos regulatórios? Como isso ajuda na conformidade da minha organização?

O Banco de Dados SQL atende a várias regras de conformidade regulatória. Para exibir o último conjunto de conformidades atendido pelo banco de dados SQL, visite o [Microsoft Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) e fazer uma busca detalhada sobre as conformidades que são importantes para sua organização para saber se o banco de dados SQL está incluído na conformidade Serviços do Azure. É importante observar que, embora o Banco de Dados SQL possa ser certificado como um serviço em conformidade, ele ajuda na conformidade do serviço de sua organização, mas não garante essa conformidade automaticamente.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Monitoramento e manutenção de banco de dados inteligente após a migração

Depois que tiver migrado seu banco de dados para o Banco de Dados SQL, você vai querer monitorar seu banco de dados (por exemplo, vrificar como a utilização de recursos está ou verificações de DBCC) e executar manutenção regular (por exemplo, recriar ou reorganizar índices, estatísticas, etc.). Felizmente, o Banco de Dados SQL é inteligente no sentido de que ele usa as tendências históricas e métricas e estatísticas registradas de forma proativa ajudá-lo a monitorar e fazer a manutenção do banco de dados, para que seu aplicativo seja executado de forma ideal sempre. Em alguns casos, o Azure SQL DB pode automaticamente executar tarefas de manutenção, dependendo de sua configuração. Há três facetas para monitoramento de banco de dados no Banco de Dados SQL:

- Monitoramento e gerenciamento de otimização.
- Otimização de segurança.
- Custo da otimização.

### <a name="performance-monitoring-and-optimization"></a>Monitoramento e otimização de desempenho

Com a Análise de Desempenho de Consultas, é possível obter recomendações personalizadas para a carga de trabalho de banco de dados para que os aplicativos possam continuar em execução no nível ideal - sempre. Também é possível defini-lo para que essas recomendações sejam aplicadas automaticamente e você não precise se preocupar em executar tarefas de manutenção. Com o Index Advisor, você pode implementar automaticamente as recomendações de índice com base na sua carga de trabalho – isso é chamado de Ajuste Automático. As recomendações evoluem conforme a carga de trabalho do aplicativo muda para fornecer sugestões mais relevantes. Você também pode obter a opção de revisar manualmente essas recomendações e aplicá-las a seu critério.  

### <a name="security-optimization"></a>Otimização de segurança

O Banco de Dados SQL fornece recomendações viáveis de segurança para ajudá-lo a proteger os dados e detectar ameaças para identificar e investigar atividades suspeitas do banco de dados que podem representar uma potencial ameaça para o banco de dados. A [avaliação de vulnerabilidade](sql-vulnerability-assessment.md) é um serviço de exame e relatório de banco de dados que permite monitorar o estado de segurança de seus bancos de dados em grande escala e identificar os riscos de segurança e o desvio de uma linha de base de segurança definida por você. Após cada verificação, uma lista personalizada de scripts de correção e etapas acionáveis são fornecidos, bem como um relatório de avaliação que pode ser usado para ajudar a alcançar os requisitos de conformidade.

Com a Central de Segurança do Azure, você identifica as recomendações de segurança em todos os segmentos e as aplica com um único clique.

### <a name="cost-optimization"></a>Otimização de custos

A plataforma Azure SQL analisa o histórico de utilização entre todos os bancos de dados em um servidor para avaliar e recomendar opções de otimização de custo para você. Essa análise normalmente leva duas semanas para analisar e criar recomendações viáveis. O pool elástico é uma dessas opções. A recomendação é exibida no portal como um banner:

![recomendações de pool elástico](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

Você também pode exibir essa análise na seção "Assistente":

![assistente de recomendações de pool elástico](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Como fazer para monitorar o desempenho e a utilização de recursos no Banco de Dados SQL?

No Banco de Dados SQL, você pode aproveitar as informações inteligentes da plataforma para monitorar o desempenho e ajustar adequadamente. Também é possível monitorar o desempenho e a utilização de recursos em um Banco de Dados SQL usando os métodos a seguir:

#### <a name="azure-portal"></a>Portal do Azure

O portal do Azure exibe a utilização de um banco de dados, selecionando esse banco de dados e clicando no gráfico no painel Visão geral. Você pode modificar o gráfico para mostrar várias métricas, incluindo a porcentagem de CPU, a porcentagem de DTU, a porcentagem de E/S de dados, a porcentagem de sessões e a porcentagem do tamanho do banco de dados.

![Gráfico de monitoramento](./media/sql-database-manage-after-migration/monitoring-chart.png)

![Gráfico de monitoramento2](./media/sql-database-manage-after-migration/chart.png)

Nesse gráfico, você também pode configurar alertas por recurso. Esses alertas permitem que você responda às condições dos recursos com um email, grave em um ponto de extremidade HTTP/HTTPS ou execute uma ação. Para obter mais informações, consulte [Criar alertas](sql-database-insights-alerts-portal.md).

#### <a name="dynamic-management-views"></a>Exibições de Gerenciamento Dinâmico

É possível consultar o modo de exibição de gerenciamento dinâmico [sys.DM db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) para retornar o histórico de estatísticas de consumo de recursos na última hora e o modo de exibição de catálogo do sistema [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) para retornar o histórico dos últimos 14 dias.

#### <a name="query-performance-insight"></a>Análise de Desempenho de Consultas

A [Análise de Desempenho de Consultas](sql-database-query-performance.md) permite visualizar um histórico das principais consultas de consumo de recursos e consultas de longa execução para um banco de dados específico. Você pode identificar rapidamente as PRINCIPAIS consultas por utilização de recursos, duração e frequência de execução. Você pode acompanhar as consultas e detectar de regressão. Este recurso requer que o [Repositório de Consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) esteja habilitado e ativo para o banco de dados.

![Análise de Desempenho de Consultas](./media/sql-database-manage-after-migration/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Logs de análise do SQL Azure (visualização) no Azure Monitor

[Os logs do Azure Monitor](../azure-monitor/insights/azure-sql.md) permite coletar e visualizar as principais métricas de desempenho do Azure SQL Azure, dando suporte a até 150.000 bancos de dados SQL e a 5.000 pools Elásticos de SQL por espaço de trabalho. Você pode usá-lo para monitorar e receber notificações. Você pode monitorar métricas do Banco de Dados SQL e de pool elástico em várias assinaturas do Azure e pools elásticos, e isso pode ser usado para identificar problemas em cada camada da pilha de um aplicativo.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Estou tendo problemas de desempenho: Como a minha metodologia de solução de problemas do Banco de Dados SQL difere do SQL Server?

Uma parte importante das técnicas de solução de problemas que você deseja usar para diagnosticar a consulta e problemas de desempenho do banco de dados permanecem as mesmas. Afinal, o mesmo mecanismo do SQL Server aciona a nuvem. No entanto, a plataforma – DB do SQL Azure tem “inteligência” incorporada. Ela pode ajudá-lo a solucionar e diagnosticar problemas de desempenho ainda mais facilmente. Ela pode também executar algumas dessas ações corretivas em seu nome e, em alguns casos, proativamente corrigi-los – automaticamente.

Sua abordagem para solucionar problemas de desempenho pode se beneficiar bastante usando recursos inteligentes como [Análise de Desempenho de Consultas (QPI)](sql-database-query-performance.md) e [Assistente do Banco de Dados](sql-database-advisor.md) em conjunto, portanto a diferença na metodologia difere nesse aspecto: você não precisa mais fazer o trabalho manual de replicar os detalhes essenciais que podem ajudá-lo a solucionar o problema em questão. A plataforma faz o trabalho pesado por você. Um exemplo disso é a QPI. Com a QPI, você pode detalhar tudo até o nível da consulta e observar as tendências históricas e descobrir quando exatamente a consulta foi retornada. O Assistente do Banco de Dados fornece recomendações sobre coisas que podem ajudá-lo a melhorar o desempenho geral em geral, como: índices ausentes, índices descartados, parametrização de suas consultas etc.

Com a solução de problemas do desempenho, é importante identificar se é apenas o aplicativo ou o banco de dados de backup que está afetando o desempenho do seu aplicativo. Geralmente, o problema de desempenho está na camada de aplicativo. Ele pode estar na arquitetura ou no padrão de acesso a dados. Por exemplo, considere que você tem um aplicativo de conversação que é sensível a latência de rede. Nesse caso, o aplicativo é prejudicado porque deve haver muitas solicitações curtas indo e vindo ("tagarelas") entre o aplicativo e o servidor e em uma rede congestionada, essas idas e voltas se acumulam rapidamente. Para melhorar o desempenho nesse caso, você pode usar [Consultas em lote](sql-database-performance-guidance.md#batch-queries). Usar lotes ajuda muito porque agora as suas solicitações são processadas em um lote; ajudando, assim, a reduzir a latência de ida e volta e a melhorar o desempenho do seu aplicativo.

Além disso, se observar uma degradação no desempenho geral de seu banco de dados, você pode monitorar as exibições de gerenciamento dinâmico [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) para compreender o consumo de CPU, E/S e memória. Seu desempenho pode estar sendo afetado porque o banco de dados está sem recursos. Talvez seja necessário alterar o tamanho da computação e/ou a camada de serviço com base nas demandas de carga de trabalho crescentes e decrescentes.

Para obter um conjunto abrangente de recomendações de ajuste dos problemas de desempenho, consulte: [Ajustar seu banco de dados](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Como fazer para garantir que estou usando o tamanho da computação e a camada de serviço apropriados?

O Banco de Dados SQL oferece várias camadas de serviço: Básica, Standard e Premium. Cada camada de serviço garante um desempenho previsível associado a essa camada de serviço. Dependendo de sua carga de trabalho, você pode ter intermitência de atividade em que a utilização de recursos pode atingir o limite do tamanho da computação em que você está. Nesses casos, é útil primeiro iniciar avaliando se qualquer ajuste pode ajudá-lo (por exemplo, adicionar ou alterar um índice etc.). Se ainda tiver problemas com o limite, considere a possibilidade de passar para um tamanho de computação ou camada de serviço superior.

|**Camada de serviço**|**Cenários de caso de uso comuns**|
|---|---|
|**Básico**|Aplicativos com usuários de uma série e um banco de dados que não tem requisitos de alta simultaneidade, escala e desempenho. |
|**Standard**|Aplicativos com requisitos de simultaneidade considerável, escala e desempenho juntamente com baixas a médias demandas de E/S. |
|**Premium**|Aplicativos com muitos usuários simultâneos, alto consumo de CPU/memória e altas demandas de E/S. Aplicativos confidenciais de alta simultaneidade, alta taxa de transferência e latência podem aproveitar o nível Premium. |
|||

Para assegurar-se de que está usando o tamanho da computação adequado, você pode monitorar o consumo de recursos de consulta e banco de dados de uma das maneiras mencionadas acima em "Como fazer para monitorar o desempenho e a utilização de recursos no Banco de Dados SQL". Se perceber que suas consultas/bancos de dados frequentemente têm escassez de CPU/memória etc., considere passar para um tamanho da computação maior. Da mesma forma, se você observar que, mesmo durante horários de pico, não parece que você está usando tanto os recursos, considere reduzir o tamanho da computação atual.

Se tiver um cenário de padrão de aplicativo SaaS ou de consolidação de banco de dados, considere o uso de um Pool elástico para otimizar os custos. O pool elástico é uma ótima maneira de obter a consolidação de banco de dados e a otimização de custoa. Para ler mais sobre o gerenciamento de vários bancos de dados usando o pool elástico, confira: [Gerenciar pools e bancos de dados](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Com que frequência preciso executar verificações de integridade do banco de dados para o meu banco de dados?

O Banco de Dados SQL usa algumas técnicas inteligentes que permitem manipular determinadas classes de corrupção de dados automaticamente e sem qualquer perda de dados. Essas técnicas são incorporadas ao serviço e são usadas por ele quando necessário. Regularmente, seus backups de banco de dados no serviço são testados restaurando-os e executando DBCC CHECKDB nele. Se houver problemas, o Banco de Dados SQL trata deles proativamente. O [reparo automático de página](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) é realizado para corrigir páginas que estejam corrompidas ou que tenham problemas de integridade dos dados. As páginas de banco de dados sempre são verificadas com a configuração CHECKSUM padrão, que verifica a integridade da página. O Banco de Dados SQL monitora e revisa proativamente a integridade de dados do banco de dados e, se ocorrerem problemas, trata deles com a mais alta prioridade. Além disso, você pode opcionalmente executar suas próprias verificações de integridade à vontade.  Para saber mais, consulte [Integridade dos dados no Banco de Dados SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Movimentação de dados após a migração

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Como fazer para exportar e importar dados como arquivos BACPAC do Banco de Dados SQL?

- **Exportar**: É possível exportar o Banco de Dados SQL do Azure como um arquivo BACPAC no portal do Azure

   ![exportação de banco de dados](./media/sql-database-export/database-export1.png)

- **Importar**: Também é possível importar dados em um arquivo BACPAC para o banco de dados usando o portal do Azure.

   ![importação de dados](./media/sql-database-import/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Como fazer para sincronizar dados entre o Banco de Dados SQL e o SQL Server?

Há várias maneiras fazer isso:

- **[Sincronização de Dados](sql-database-sync-data.md)** – Esse recurso ajuda a sincronizar dados bidirecionalmente entre vários bancos de dados locais do SQL Server e do Banco de Dados SQL. Para sincronizar com bancos de dados do SQL Server local, você precisa instalar e configurar o agente de sincronização em um computador local e abrir a porta TCP de saída 1433.
- **[Replicação de Transação](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** – Com replicação de transação, você pode sincronizar os dados do local para o Azure SQL DB com o local que está sendo o publicador e o Azure SQL DB que está sendo o assinante. Por ora, apenas há suporte apenas para esta configuração. Para obter mais informações sobre como migrar seus dados do local para o Azure SQL com o mínimo tempo de inatividade, consulte: [Usar Replicação da Transação](sql-database-single-database-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Próximos passos

Conheça o [Banco de Dados SQL](sql-database-technical-overview.md).
