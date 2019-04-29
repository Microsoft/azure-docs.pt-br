---
title: Visão geral de segurança do banco de dados do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos recursos de segurança do banco de dados do Azure.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: 7e0e93c82279ec1a4fbecbbf27c7a1866286b2f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587561"
---
# <a name="azure-database-security-overview"></a>Visão geral de segurança do banco de dados do Azure

A segurança é uma das principais preocupações para o gerenciamento de bancos de dados e sempre foi uma prioridade para o Banco de Dados SQL do Azure. O Banco de Dados SQL do Azure dá suporte à segurança de conexão com regras de firewall e criptografia da conexão. Ele dá suporte à autenticação com nome de usuário e senha e a autenticação do Azure Active Directory (Azure AD), que usa identidades gerenciadas pelo Azure Active Directory. A autorização usa o controle de acesso baseado em função.

O Banco de Dados SQL do Azure dá suporte à criptografia executando criptografia e descriptografia em tempo real de banco de dados, de backups associados e de arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo.

A Microsoft fornece maneiras adicionais para criptografar os dados da empresa:

-   A criptografia no nível de célula está disponível para criptografar colunas específicas ou até mesmo células de dados com chaves de criptografia diferentes.
-   Se você precisar de um módulo de segurança de hardware ou do gerenciamento central da hierarquia de chaves de criptografia, considere o uso do Azure Key Vault com o SQL Server em uma máquina virtual (VM) do Azure.
-   O Always Encrypted (atualmente em versão prévia) torna a criptografia transparente para aplicativos. Ele também permite aos clientes criptografar dados confidenciais dentro de aplicativos cliente sem compartilhar as chaves de criptografia para o Banco de Dados SQL.

A Auditoria do Banco de Dados SQL do Azure permite que as empresas registrem eventos no Armazenamento do Azure de um logon de auditoria. A Auditoria do Banco de Dados SQL também se integra ao Microsoft Power BI para facilitar análises e relatórios detalhados.

Os bancos de dados SQL do Azure podem ser totalmente protegidos para atender a maioria dos requisitos regulamentares ou de segurança, como HIPAA, ISO 27001/27002 e PCI DSS Nível 1. Uma lista atual das certificações de conformidade de segurança está disponível no [site da Central de Confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/services/).

Este artigo explica os conceitos básicos de proteção de bancos de dados SQL do Microsoft Azure para dados estruturados, de tabela e relacionais. Em particular, este artigo mostrará uma introdução aos recursos de proteção de dados, de controle de acesso e de monitoramento proativo.

## <a name="protection-of-data"></a>Proteção de dados

O Banco de dados SQL ajuda a proteger seus dados fornecendo criptografia:

- Para dados em movimento por meio do [Protocolo TLS](https://support.microsoft.com/kb/3135244).
- Para dados em repouso por meio de [criptografia de dados transparente](https://go.microsoft.com/fwlink/?LinkId=526242).
- Para dados em uso por meio do [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Para outras maneiras de criptografar seus dados, considere:

-   [Criptografia no nível de célula](https://msdn.microsoft.com/library/ms179331.aspx) para criptografar colunas específicas ou até mesmo células de dados com diferentes chaves de criptografia.
-   [Azure Key Vault com SQL Server em uma VM Azure](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), se você precisar de um módulo de segurança de hardware ou do gerenciamento central da hierarquia de chaves de criptografia.

### <a name="encryption-in-motion"></a>Criptografia em movimento

Um problema comum para todos os aplicativos cliente/servidor é a necessidade de privacidade, conforme os dados são transmitidos em redes públicas e privadas. Se a movimentação de dados em uma rede não for criptografada, haverá a possibilidade de que eles sejam capturados e roubados por usuários não autorizados. Quando você estiver lidando com os serviços de banco de dados, certifique-se de que os dados sejam criptografados entre o cliente e o servidor de banco de dados. Além disso, certifique-se de que os dados sejam criptografados entre servidores de banco de dados que se comunicam entre si e com aplicativos de camada intermediária.

Um problema ao administrar uma rede é proteger os dados que estão sendo enviados entre aplicativos em uma rede não confiável. Você pode usar o [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) para autenticar servidores e clientes e, em seguida, usá-lo para criptografar mensagens entre as partes autenticadas.

No processo de autenticação, um cliente TLS/SSL envia uma mensagem para um servidor TLS/SSL. O servidor responde com as informações de que o servidor precisa para se autenticar. O cliente e o servidor executam uma troca adicional de chaves da sessão e a caixa de diálogo de autenticação é encerrada. Quando a autenticação é concluída, a comunicação protegida por SSL pode ser iniciada entre o servidor e o cliente por meio das chaves de criptografia simétricas estabelecidas durante o processo de autenticação.

Todas as conexões do Banco de Dados SQL do Azure exigem criptografia (TLS/SSL) todo o tempo que os dados estiverem "em trânsito", entrando e saindo do banco de dados. O Banco de Dados SQL usa o TLS/SSL para autenticar servidores e clientes e, em seguida, usa-o para criptografar mensagens entre as partes autenticadas. 

Na cadeia de conexão do seu aplicativo, você deverá especificar parâmetros para criptografar a conexão e não confiar no certificado do servidor. (Isso será feito para você se copiar a cadeia de conexão fora do portal do Azure.) Caso contrário, a conexão não irá verificar a identidade do servidor e estará suscetível a ataques "man-in-the-middle". Para o driver do ADO.NET, por exemplo, esses parâmetros da cadeia de conexão são `Encrypt=True` e `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Criptografia em repouso

Você pode adotar várias precauções para ajudar a proteger o banco de dados. Por exemplo, projete um sistema seguro, criptografe ativos confidenciais e crie um firewall em torno dos servidores de banco de dados. Mas, em um cenário em que a mídia física (como unidades ou fitas de backup) é roubada, uma entidade mal-intencionada pode simplesmente restaurar ou anexar o banco de dados e procurar os dados.

Uma solução é criptografar os dados confidenciais no banco de dados e proteger as chaves usadas para criptografar os dados com um certificado. Essa solução impede que alguém sem as chaves use os dados, mas esse tipo de proteção deve ser planejado.

Para resolver esse problema, o SQL Server e o Banco de Dados SQL dão suporte a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). A Transparent Data Encryption criptografa os arquivos de dados do SQL Server e do Banco de Dados SQL, conhecidos como dados de criptografia em repouso.

A Transparent Data Encryption ajuda a proteger contra a ameaça de atividades mal-intencionadas. Ela realiza a criptografia e a descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo.  

A Transparent Data Encryption criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados. No Banco de Dados SQL, a chave de criptografia do banco de dados é protegida por um certificado do servidor interno. O certificado de servidor interno é exclusivo para cada servidor do Banco de Dados SQL.

Se um banco de dados estiver em uma relação de Geo-DR, ele será protegido por outra chave em cada servidor. Se dois bancos de dados estiverem conectados ao mesmo servidor, eles compartilharão o mesmo certificado interno. A Microsoft alterna automaticamente esses certificados pelo menos a cada 90 dias. 

Para obter mais informações, consulte [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Criptografia em uso (cliente)

A maioria das violações de dados envolve o roubo de dados críticos, como números de cartão de crédito ou informações de identificação pessoal. Bancos de dados podem ser um tesouro escondido de informações confidenciais. Eles podem conter dados pessoais dos clientes (como números de identificação nacionais), informações confidenciais sobre a concorrência e propriedade intelectual. Dados perdidos ou roubados, especialmente dados do cliente, podem resultar em danos à marca, desvantagem competitiva e multas graves – até mesmo processos judiciais.

![O recurso Always Encrypted ilustrado como um cadeado e chave](./media/azure-databse-security-overview/azure-database-fig1.png)

O [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) é um recurso projetado para proteger dados confidenciais armazenados no Banco de Dados SQL do Microsoft Azure ou em bancos de dados SQL Server. O Always Encrypted permite que os clientes criptografem dados confidenciais em aplicativos clientes e nunca revelem as chaves de criptografia para o mecanismo de banco de dados (Banco de Dados SQL ou SQL Server).

O Always Encrypted fornece uma separação entre os proprietários dos dados (e que podem exibi-los) e as pessoas que os gerencia (mas que não devem ter acesso). Ele ajuda a garantir que administradores de banco de dados locais, operadores de banco de dados na nuvem ou outros usuários com altos privilégios, mas não autorizados, não consigam acessar os dados criptografados.

Além disso, o Always Encrypted torna a criptografia transparente para aplicativos. Um driver habilitado para o Always Encrypted é instalado no computador cliente, de modo que ele possa criptografar e descriptografar dados confidenciais automaticamente no aplicativo cliente. O driver criptografa os dados nas colunas confidenciais antes de passá-los para o mecanismo de banco de dados. O driver reescreve automaticamente as consultas para que a semântica do aplicativo seja preservada. Da mesma forma, o driver descriptografa os dados de maneira transparente, armazenados em colunas de banco de dados criptografadas, contidas nos resultados da consulta.

## <a name="access-control"></a>Controle de acesso

Para fornecer segurança, o Banco de Dados SQL controla o acesso usando:

- Regras de firewall que limitam a conectividade por endereço IP.
- Mecanismos de autenticação que exigem que os usuários comprovem sua identidade.
- Mecanismos de autorização que limitam os usuários a ações e dados específicos.

### <a name="database-access"></a>Acesso ao banco de dados

A proteção de dados começa com o controle do acesso aos dados. O datacenter que hospeda os dados gerencia o acesso físico. Você pode configurar um firewall para gerenciar a segurança na camada de rede. Você também pode controlar o acesso, configurar logons para autenticação e definir permissões para funções de servidor e de banco de dados.

#### <a name="firewall-and-firewall-rules"></a>Firewall e regras de firewall

O [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) fornece um serviço de banco de dados relacional para o Azure e outros aplicativos baseados na Internet. Para ajudar a proteger seus dados, os firewalls impedem todo acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação. Para saber mais, veja [Visão geral de regras de firewall do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

O serviço Banco de dados SQL do Azure só está disponível na porta TCP 1433. Para acessar um banco de dados SQL do seu computador, certifique-se de que o firewall do computador cliente permita a comunicação TCP de saída na porta TCP 1433. Se as conexões de entrada não forem necessários para outros aplicativos, bloqueie-as na porta TCP 1433.

#### <a name="authentication"></a>Authentication

A Autenticação refere-se a como você comprova sua identidade durante a conexão com o banco de dados. O Banco de Dados SQL dá suporte a dois tipos de autenticação:

-   **Autenticação do SQL Server**: Uma conta de logon único é criada quando uma instância lógica do SQL é criada, chamada Conta de assinante do Banco de Dados SQL. Essa conta é conectada usando a [autenticação do SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (nome de usuário e senha). Essa conta é um administrador na instância do servidor lógico e em todos os bancos de dados do usuário conectados a essa instância. As permissões da conta de assinante não podem ser restringidas. Só pode existir uma dessas contas.
-   **Autenticação do Azure Active Directory**: A [autenticação do Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) é um mecanismo de conexão com o Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure usando identidades no Azure AD. Você pode usá-la para gerenciar as identidades de usuários de banco de dados de maneira centralizada.

![Autenticação do AD do Azure com Banco de Dados SQL](./media/azure-databse-security-overview/azure-database-fig2.png)

 As vantagens da autenticação do Azure AD incluem:
  - Ele fornece uma alternativa para autenticação do SQL Server.
  - Ajuda a acabar com a proliferação de identidades de usuário entre servidores de banco de dados e permite a rotação de senha em um único lugar.
  - Você pode gerenciar permissões de banco de dados usando grupos (Azure AD) externos.
  - Pode eliminar o armazenamento de senhas, permitindo a autenticação integrada do Windows e outras formas de autenticação às quais o Azure AD dá suporte.

#### <a name="authorization"></a>Autorização

[Autorização](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) indica o que um usuário pode fazer em um banco de dados SQL do Azure. Ela é controlada pelas [associações de função](https://msdn.microsoft.com/library/ms189121) e [permissões no nível do objeto](https://msdn.microsoft.com/library/ms191291.aspx) do banco de dados da conta do usuário. A autorização é o processo usado para determinar quais recursos protegíveis podem ser acessados por uma entidade de segurança e quais operações são permitidas para esses recursos.

### <a name="application-access"></a>Acesso a aplicativos

#### <a name="dynamic-data-masking"></a>Mascaramento de dados dinâmicos

Um representante do serviço em um call center pode identificar os chamadores usando vários dígitos de seu número do seguro social ou número do cartão de crédito. Mas esses itens de dados não devem ser totalmente expostos ao representante do serviço.

Você pode definir uma regra de mascaramento para mascarar tudo menos os quatro últimos dígitos de qualquer número do seguro social ou o número do cartão de crédito no conjunto de resultados de qualquer consulta.

![Mascaramento em um número enviado entre um banco de dados SQL e aplicativos de negócios](./media/azure-databse-security-overview/azure-database-fig3.png)

Como outro exemplo, uma máscara de dados apropriada pode ser definida para proteger as informações de identificação pessoal. Um desenvolvedor pode, em seguida, consultar os ambientes de produção para fins de solução de problemas sem violar as normas de conformidade.

A [máscara de dados dinâmicos no Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais através do mascaramento dos dados para usuários sem privilégios. O mascaramento de dados dinâmicos tem suporte para a versão V12 do Banco de Dados SQL do Azure.

A [máscara de dados dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que você designe a quantidade de dados confidenciais a ser revelada, com impacto mínimo sobre a camada de aplicativo. É um recurso de segurança baseado em políticas que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designado, enquanto os dados no banco de dados não são alterados.

> [!Note]
> A Máscara de dados dinâmica pode ser configurada através de funções do administração do banco de dados do Azure, administrador do servidor ou responsável pela segurança.

#### <a name="row-level-security"></a>Segurança em nível de linha

Outro requisito de segurança comum para bancos de dados multilocatários é a [Segurança em Nível de Linha](https://msdn.microsoft.com/library/dn765131.aspx). Você pode usar esse recurso para controlar o acesso às linhas em uma tabela de banco de dados com base nas características do usuário que executa uma consulta. (As características de exemplo são contexto de execução e associação de grupo.)

![Segurança de nível de linha, permitindo que um usuário acesse linhas em uma tabela por meio de um aplicativo cliente](./media/azure-databse-security-overview/azure-database-fig4.png)

A lógica de restrição de acesso está localizada na camada do banco de dados, em vez de estar longe dos dados em outra camada de aplicativo. O sistema do banco de dados aplica as restrições de acesso sempre que houver uma tentativa de acessar esses dados em qualquer camada. Isso torna o sistema de segurança mais robusto e confiável, reduzindo a área de superfície do sistema de segurança.

A Segurança em Nível de Linha introduz o controle de acesso baseado em predicado. Ela apresenta uma avaliação flexível, centralizada que pode levar em consideração metadados ou outros critérios que o administrador determinar apropriados. O predicado é usado como critério para determinar se o usuário tem o acesso apropriado aos dados com base nos atributos de usuário. Você pode implementar o controle de acesso baseado em rótulos usando o controle de acesso baseado em predicado.

## <a name="proactive-monitoring"></a>Monitoramento proativo

O Banco de Dados SQL ajuda a proteger os dados fornecendo funcionalidades de *auditoria* e *detecção de ameaças*.

### <a name="auditing"></a>Auditoria

A [Auditoria do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) aumenta a capacidade de obter insight sobre eventos e as alterações que ocorrem no banco de dados. Os exemplos são atualizações e consultas com base nos dados.

A Auditoria do Banco de Dados SQL rastreia eventos do banco de dados e os grava em um log de auditoria em sua conta de armazenamento do Azure. A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança. A auditoria permite e facilita a adoção de padrões de conformidade, mas não garante a conformidade.

É possível usar a auditoria do Banco de Dados SQL para:

- **Retenha** uma trilha de auditoria dos eventos selecionados. Definir categorias de ações de banco de dados a ser auditadas.
- **Relate** sobre a atividade do banco de dados. Você pode usar relatórios pré-configurados e um painel para se familiarizar rapidamente com a atividade e o relatório de eventos.
- **Analise** relatórios. Encontrar eventos suspeitos, atividades incomuns e tendências.

Há dois métodos de auditoria:

-   **Auditoria de blobs**: Os logs são gravados no Armazenamento de Blobs do Azure. Este é um método de auditoria mais recente. Esse é um método de auditoria mais recente, que fornece desempenho maior, dá suporte à auditoria do nível de objeto de granularidade mais alta e é mais econômico.
-   **Auditoria de tabela**: Os logs são gravados no armazenamento de Tabelas do Azure.

### <a name="threat-detection"></a>Detecção de ameaças

A [Proteção Avançada contra Ameaças do Azure do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-advanced-threat-protection) detecta atividades suspeitas que indicam possíveis ameaças à segurança. Você pode usar a detecção de ameaças para responder a eventos suspeitos no banco de dados, como injeções de SQL, conforme elas ocorrem. Ela fornece alertas e permite o uso da auditoria do Banco de Dados SQL do Azure para explorar os eventos suspeitos.

![Detecção de ameaças para o Banco de Dados SQL e um aplicativo web, com um invasor externo e uma pessoa mal-intencionada de dentro da empresa](./media/azure-databse-security-overview/azure-database-fig5.jpg)

A Proteção Contra Ameaça Avançada SQL (ATP) fornece um conjunto de recursos avançados de segurança SQL, incluindo a descoberta de dados e classificação, avaliação de vulnerabilidades e detecção de ameaças. 

- [Descoberta e classificação de dados](../sql-database/sql-database-data-discovery-and-classification.md)
- [Avaliação de vulnerabilidade](../sql-database/sql-vulnerability-assessment.md)  
- [Detecção de Ameaças](../sql-database/sql-database-threat-detection.md)

A [Proteção Avançada contra Ameaças do Azure para o Banco de Dados do Azure para PostgreSQL](../postgresql/concepts-data-access-and-security-threat-protection.md) fornece uma nova camada de segurança que possibilita detectar e responder a possíveis ameaças conforme elas ocorrem, apresentando alertas de segurança sobre atividades anômalas. Os usuários receberão um alerta em caso de atividades suspeitas em bancos de dados, possíveis vulnerabilidades, bem como padrões anômalos de consultas e acesso a banco de dados. A Proteção Avançada contra Ameaças do Azure para Banco de Dados do Azure para PostgreSQL integra seus alertas à Central de Segurança do Azure. Os tipos de alertas incluem:

- Acesso de um local incomum
- Acesso de um data center do Azure incomum 
- Acesso de uma entidade de segurança desconhecida 
- Acesso de um aplicativo potencialmente prejudicial 
- Credenciais de força bruta do Banco de dados do Azure para PostgreSQL 

A [Proteção Avançada contra Ameaças do Azure do Banco de Dados do Azure para MySQL](../mysql/concepts-data-access-and-security-threat-protection.md) fornece proteção semelhante à Proteção Avançada do PostgreSQL.  

## <a name="centralized-security-management"></a>Gerenciamento de segurança centralizado

A [Central de Segurança do Azure](https://azure.microsoft.com/documentation/services/security-center/) ajuda você a evitar, detectar e responder a ameaças. Ela fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure. Ela ajuda a detectar ameaças que poderiam não ser notadas de outra forma e funciona com um amplo ecossistema de soluções de segurança.

A [Central de Segurança](https://docs.microsoft.com/azure/security-center/security-center-sql-database) ajuda você a proteger os dados no Banco de Dados SQL, fornecendo visibilidade sobre a segurança de todos os servidores e bancos de dados. Com a Central de Segurança, você pode:

- Definir políticas de criptografia e auditoria do Banco de Dados SQL.
- Monitore a segurança dos recursos de Banco de Dados SQL em todas as suas assinaturas.
- Identifique e corrija rapidamente problemas de segurança.
- Integre os alertas da [detecção de ameaças do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

A Central de segurança dá suporte ao acesso baseado em função.

## <a name="sql-information-protection"></a>SQL Information Protection

O [SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) descobre e classifica automaticamente dados potencialmente confidenciais, fornece um mecanismo de rotulagem para marcar persistentemente os dados confidenciais com atributos de classificação e fornece um painel detalhado mostrando o estado de classificação do banco de dados.  

Além disso, calcula a sensibilidade do conjunto de resultados das consultas SQL, para que as consultas que extraem dados confidenciais possam ser explicitamente auditadas e os dados possam ser protegidos. Para obter mais detalhes sobre o SQL Information Protection, consulte Descoberta e Classificação de Dados do Banco de Dados SQL do Azure.

É possível configurar as [Políticas do SQL Information Protection](../security-center/security-center-info-protection-policy.md) na Central de Segurança do Azure.

## <a name="azure-marketplace"></a>Azure Marketplace

O Azure Marketplace é um marketplace online de aplicativos e serviços que permite que empresas em fase inicial e ISVs (fornecedores independentes de software) ofereçam suas soluções aos clientes do Azure no mundo todo.
O Azure Marketplace combina ecossistemas de parceiros do Microsoft Azure em uma plataforma unificada, para melhor atender aos clientes e parceiros. Você pode [executar uma pesquisa](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) para exibir os produtos de segurança de banco de dados disponíveis no Azure Marketplace.

## <a name="next-steps"></a>Próximas etapas

- [Proteger o Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Central de Segurança do Azure e serviço do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [Detecção de ameaças do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Melhorar o desempenho do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)
