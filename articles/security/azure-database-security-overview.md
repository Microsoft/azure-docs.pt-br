---
title: "Visão geral de segurança do banco de dados do Azure | Microsoft Docs"
description: "Este artigo fornece uma visão geral dos recursos de segurança do banco de dados do Azure."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: TomSh
ms.openlocfilehash: 036ce3dce28e7951bb39754c4351661fae85f06c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-security-overview"></a>Visão geral de segurança do banco de dados do Azure

A segurança é uma das principais preocupações durante o gerenciamento de bancos de dados e sempre foi uma prioridade para o Banco de Dados SQL do Azure. O Banco de Dados SQL do Azure dá suporte à segurança de conexão com regras de firewall e criptografia da conexão. Ele dá suporte à autenticação com nome de usuário e senha e a Autenticação do Azure Active Directory, que usa identidades gerenciadas pelo Azure Active Directory. A autorização usa o controle de acesso baseado em função.

O Banco de Dados SQL do Azure dá suporte à criptografia executando criptografia e descriptografia em tempo real de banco de dados, de backups associados e de arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo.

A Microsoft fornece maneiras adicionais para criptografar os dados da empresa:

-   Criptografia no nível de célula para criptografar colunas específicas ou até mesmo células de dados com chaves de criptografia diferentes.
-   Se você precisar de um Módulo de Segurança de Hardware ou do gerenciamento central da hierarquia de chaves de criptografia, considere o uso do Azure Key Vault com o SQL Server em uma VM do Azure.
-   O Always Encrypted (atualmente, em versão prévia) torna a criptografia transparente para os aplicativos e permite que os clientes criptografem dados confidenciais em aplicativos cliente sem compartilhar as chaves de criptografia com o Banco de Dados SQL.

A Auditoria do Banco de Dados SQL do Azure permite que as empresas registrem eventos no Armazenamento do Azure de um logon de auditoria. A Auditoria do Banco de Dados SQL também se integra ao Microsoft Power BI para facilitar análises e relatórios detalhados.

 Os bancos de dados SQL do Azure podem ser totalmente protegidos para atender a maioria dos requisitos regulamentares ou de segurança, como HIPAA, ISO 27001/27002 e PCI DSS Nível 1, entre outros. Uma lista atual das certificações de conformidade de segurança está disponível no [site da Central de Confiabilidade do Microsoft Azure](http://azure.microsoft.com/support/trust-center/services/).

Este artigo explica os conceitos básicos de proteção de Bancos de Dados SQL do Microsoft Azure para Dados Estruturados, de Tabela e Relacionais. Em particular, este artigo mostrará uma introdução aos recursos de proteção de dados, de controle de acesso e de monitoramento proativo.

Este artigo de Visão geral da segurança do Banco de Dados do Azure tem como foco as seguintes áreas:

-   Proteger dados
-   Controle de acesso
-   Monitoramento proativo
-   Gerenciamento de segurança centralizado
-   Azure Marketplace

## <a name="protect-data"></a>Proteger dados

O Banco de Dados SQL protege os dados fornecendo criptografia de dados em movimento usando o [protocolo TLS](https://support.microsoft.com/kb/3135244), para dados em repouso usando a [Transparent Data Encryption](http://go.microsoft.com/fwlink/?LinkId=526242) e para dados em uso usando o [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Nesta seção, vamos falar sobre:

-   Criptografia em movimento
-   Criptografia em repouso
-   Criptografia em uso (Cliente)

Para outras maneiras de criptografar seus dados, considere:

-   [Criptografia no nível de célula](https://msdn.microsoft.com/library/ms179331.aspx) para criptografar colunas específicas ou até mesmo células de dados com diferentes chaves de criptografia.
-   Se você precisar de um Módulo de Segurança de Hardware ou do gerenciamento central de sua hierarquia de chaves de criptografia, considere o uso do [cofre da chave do Azure com o SQL Server em uma VM do Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="encryption-in-motion"></a>Criptografia em movimento

Um problema comum para todos os aplicativos cliente/servidor é a necessidade de privacidade, conforme os dados são transmitidos em redes públicas e privadas. Se a movimentação de dados em uma rede não for criptografada, haverá a possibilidade de que eles sejam capturados e roubados por usuários não autorizados. Ao lidar com serviços de banco de dados, você precisa verificar se os dados são criptografados entre o cliente de banco de dados e o servidor, bem como entre servidores de banco de dados que se comunicam entre si e com aplicativos de camada intermediária.

Um problema ao administrar uma rede é proteger os dados que estão sendo enviados entre aplicativos em uma rede não confiável. Você pode usar o [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) para autenticar servidores e clientes e, em seguida, usá-lo para criptografar mensagens entre as partes autenticadas.

No processo de autenticação, um cliente TLS/SSL envia uma mensagem para um servidor TLS/SSL e o servidor responde com as informações de que o servidor precisa para se autenticar. O cliente e o servidor executam uma troca adicional de chaves da sessão e a caixa de diálogo de autenticação é encerrada. Quando a autenticação é concluída, a comunicação protegida por SSL pode ser iniciada entre o servidor e o cliente usando as chaves de criptografia simétricas estabelecidas durante o processo de autenticação.

Todas as conexões do Banco de Dados SQL do Azure exigem criptografia (SSL/TLS) todo o tempo que os dados estiverem "em trânsito", entrando e saindo do banco de dados. O SQL Azure usa o TLS/SSL para autenticar servidores e clientes e, em seguida, usa-o para criptografar mensagens entre as partes autenticadas. Na cadeia de conexão do aplicativo, você deve especificar os parâmetros para criptografar a conexão e para não confiar no certificado do servidor (isso será feito para você se você copiar a cadeia de conexão do Portal Clássico do Azure); caso contrário, a conexão não verificará a identidade do servidor e estará suscetível a ataques “man-in-the-middle”. Para o driver do ADO.NET, por exemplo, esses parâmetros da cadeia de conexão são Encrypt=True e TrustServerCertificate=False.

### <a name="encryption-at-rest"></a>Criptografia em repouso
Você pode adotar várias precauções para ajudar a proteger o banco de dados, como a criação de um sistema seguro, a criptografia de ativos confidenciais e a criação de um firewall em torno de servidores de bancos de dados. No entanto, em um cenário em que a mídia física (como unidades ou fitas de backup) é roubada, uma entidade mal-intencionada pode simplesmente restaurar ou anexar o banco de dados e procurar os dados.

Uma solução é criptografar os dados confidenciais no banco de dados e proteger as chaves usadas para criptografar os dados com um certificado. Isso impede que alguém sem as chaves use os dados, mas esse tipo de proteção deve ser planejado.

Para resolver esse problema, o SQL Server e o SQL do Azure dão suporte à [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde). A TDE criptografa os arquivos de dados do SQL Server e do Banco de Dados SQL do Azure, conhecidos como dados de criptografia em repouso.

A Transparent Data Encryption do Banco de Dados SQL do Azure ajuda a proteger contra a ameaça de atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo.  

A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados. No Banco de Dados SQL, a chave de criptografia do banco de dados é protegida por um certificado do servidor interno. O certificado de servidor interno é exclusivo para cada servidor de Banco de Dados SQL.

Se um banco de dados estiver em uma relação de GeoDR, ele será protegido por outra chave em cada servidor. Se dois bancos de dados estiverem conectados ao mesmo servidor, eles compartilharão o mesmo certificado interno. A Microsoft alterna automaticamente esses certificados pelo menos a cada 90 dias. Para obter uma descrição geral da TDE, consulte [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Criptografia em uso (cliente)

A maioria das violações de dados envolve o roubo de dados críticos, como números de cartão de crédito ou informações de identificação pessoal. Bancos de dados podem ser um tesouro escondido de informações confidenciais. Eles podem conter dados pessoais dos clientes, informações confidenciais sobre a concorrência e propriedade intelectual. Dados perdidos ou roubados, especialmente dados do cliente, podem resultar em danos à marca, desvantagem competitiva e multas graves – até mesmo processos judiciais.

![Always Encrypted](./media/azure-databse-security-overview/azure-database-fig1.png)

O [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) é um recurso criado para proteger dados confidenciais, como números de cartão de crédito ou números de identificação nacional (por exemplo, números do seguro social dos EUA), armazenados no Banco de Dados SQL do Azure ou nos bancos de dados SQL Server. Esse recurso permite que os clientes criptografem dados confidenciais em aplicativos clientes e nunca revelem as chaves de criptografia para o mecanismo de banco de dados (Banco de dados SQL ou SQL Server).

O Always Encrypted fornece uma separação entre os proprietários dos dados (e que podem exibi-los) de quem gerencia os dados (mas que não devem ter acesso). Ao garantir que os administradores de banco de dados locais, operadores de banco de dados na nuvem ou outros usuários com altos privilégios, mas não autorizados, não podem acessar os dados criptografados,

Além disso, o Always Encrypted torna a criptografia transparente para aplicativos. Um driver habilitado para o Always Encrypted instalado no computador cliente, de modo que ele possa criptografar e descriptografar dados confidenciais automaticamente no aplicativo cliente. O driver criptografa os dados em colunas confidenciais antes de passá-los para o Mecanismo de Banco de Dados e reescreve as consultas automaticamente para que a semântica do aplicativo seja preservada. Da mesma forma, o driver descriptografa os dados de maneira transparente, armazenados em colunas de banco de dados criptografadas, contidas nos resultados da consulta.

## <a name="access-control"></a>Controle de acesso
Para fornecer segurança, o Banco de Dados SQL controla o acesso com regras de firewall que limitam a conectividade por endereço IP,mecanismos de autenticação que exigem que usuários comprovem suas identidade e mecanismos de autorização que limitam os usuários a ações e dados específicos.

### <a name="database-access"></a>Acesso ao banco de dados

A proteção de dados começa com o controle do acesso aos dados. O datacenter que hospeda os dados gerencia o acesso físico, embora seja possível configurar um firewall para gerenciar a segurança na camada de rede. Você também pode controlar o acesso, configurar logons para autenticação e definir permissões para funções de servidor e de banco de dados.

Nesta seção, vamos falar sobre:

-   Firewall e regras de firewall
-   Autenticação
-   Autorização

#### <a name="firewall-and-firewall-rules"></a>Firewall e regras de firewall

O Banco de Dados SQL do Microsoft Azure fornece um serviço de banco de dados relacional para o Azure e outros aplicativos baseados na Internet. Para ajudar a proteger seus dados, os firewalls impedem todo acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação. Para saber mais, veja [Visão geral de regras de firewall do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

O serviço [Banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) só está disponível por meio da porta TCP 1433. Para acessar um Banco de dados SQL do seu computador, certifique-se de que o firewall do computador cliente permita a comunicação TCP de saída na porta TCP 1433. Se não for necessário a outros aplicativos, bloqueie as conexões de entrada na porta TCP 1433.

#### <a name="authentication"></a>Autenticação

A autenticação do banco de dados SQL refere-se a como você comprova sua identidade durante a conexão com o banco de dados. O Banco de Dados SQL dá suporte a dois tipos de autenticação:

-   **Autenticação do SQL:** uma conta de logon único é criada quando uma instância lógica do SQL é criada, chamada Conta de Assinante do Banco de Dados SQL. Essa conta é conectada usando a [autenticação do SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (nome de usuário e senha). Essa conta é um administrador na instância do servidor lógico e em todos os bancos de dados do usuário conectados a essa instância. As permissões da Conta de assinante não podem ser restringidas. Só pode existir uma dessas contas.
-   **Autenticação do Azure Active Directory:** [a autenticação do Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) é um mecanismo de conexão com o Banco de Dados SQL do Microsoft Azure e o SQL Data Warehouse usando identidades no Azure AD (Azure Active Directory). Isso permite gerenciar as identidades de usuários de banco de dados de maneira centralizada.

![Autenticação](./media/azure-databse-security-overview/azure-database-fig2.png)

 As vantagens da autenticação do Azure Active Directory incluem:
  - Ele fornece uma alternativa para autenticação do SQL Server.
  - Também ajuda a acabar com a proliferação de identidades de usuário entre servidores de banco de dados e permite a rotação de senha em um único lugar.
  - Gerencie permissões de banco de dados usando grupos externos (Azure Active Directory).
  - Pode eliminar o armazenamento de senhas, permitindo a autenticação integrada do Windows e outras formas de autenticação às quais o Active Directory do Azure dá suporte.

#### <a name="authorization"></a>Autorização
[Autorização](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) refere-se ao que um usuário pode fazer em um Banco de Dados SQL do Azure e isso é controlado pelas [associações a uma função](https://msdn.microsoft.com/library/ms189121) de banco de dados e pelas [permissões no nível de objeto](https://msdn.microsoft.com/library/ms191291.aspx) da conta de usuário. A autorização é o processo usado para determinar quais recursos protegíveis podem ser acessados por uma entidade de segurança e quais operações são permitidas para esses recursos.

### <a name="application-access"></a>Acesso a aplicativos

Nesta seção, vamos falar sobre:

-   Mascaramento de dados dinâmicos
-   Segurança em nível de linha

#### <a name="dynamic-data-masking"></a>Mascaramento de dados dinâmicos
Um representante de serviço em um call center pode identificar os chamadores por vários dígitos de seus números do seguro social ou do cartão de crédito, mas esses itens de dados não devem ser totalmente expostos para o representante de serviço.

Uma regra de mascaramento pode ser definida para mascarar tudo menos os quatro últimos dígitos de qualquer número do seguro social ou o número de cartão de crédito no conjunto de resultados de qualquer consulta.

![Mascaramento de dados dinâmicos](./media/azure-databse-security-overview/azure-database-fig3.png)

Como outro exemplo, uma máscara de dados apropriada pode ser definida para proteger os dados de informações de identificação pessoal (PII), de forma que um desenvolvedor possa consultar os ambientes de produção para fins de solução de problemas sem violar os regulamentos de conformidade.

A [Máscara de Dados Dinâmicos do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais por meio da máscara dos dados para usuários sem privilégios. O mascaramento de dados dinâmicos tem suporte para a versão V12 do Banco de Dados SQL do Azure.

A [máscara de dados dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que você designe a quantidade de dados confidenciais a ser revelada, com impacto mínimo sobre a camada de aplicativo. É um recurso de segurança baseado em políticas que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designado, enquanto os dados no banco de dados não são alterados.


> [!Note]
> A Máscara de dados dinâmica pode ser configurada através de funções do administração do banco de dados do Azure, administrador do servidor ou responsável pela segurança.

#### <a name="row-level-security"></a>Segurança em nível de linha
Outro requisito de segurança comum para bancos de dados multilocatários é a [Segurança em Nível de Linha](https://msdn.microsoft.com/library/dn765131.aspx). Esse recurso permite controlar o acesso às linhas em uma tabela de banco de dados com base nas características do usuário que executa uma consulta (por exemplo, uma associação a um grupo ou um contexto de execução).

![Segurança em nível de linha](./media/azure-databse-security-overview/azure-database-fig4.png)

A lógica de restrição de acesso está localizada na camada do banco de dados, em vez de estar longe dos dados em outra camada de aplicativo. O sistema do banco de dados aplica as restrições de acesso sempre que houver uma tentativa de acessar esses dados em qualquer camada. Isso torna o sistema de segurança mais robusto e confiável, reduzindo a área de superfície do sistema de segurança.

A segurança em nível de linha introduz o controle de acesso baseado em predicado. Ela apresenta uma avaliação flexível, centralizada e baseada em predicado que pode levar em consideração metadados ou outros critérios que o administrador determinar apropriados. O predicado é usado como critério para determinar se o usuário tem o acesso apropriado aos dados com base nos atributos de usuário. O controle de acesso baseado em rótulo pode ser implementado usando o controle de acesso baseado em predicado.

## <a name="proactive-monitoring"></a>Monitoramento proativo
O Banco de Dados SQL protege os dados fornecendo funcionalidades de **auditoria** e **detecção de ameaças**.

### <a name="auditing"></a>Auditoria
A Auditoria do Banco de Dados SQL aumenta a capacidade de obter informações sobre eventos e as alterações que ocorrem no banco de dados, incluindo atualizações e consultas nos dados.

A [Auditoria do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) acompanha eventos de banco de dados e grava-os em um log de auditoria em sua conta de Armazenamento do Azure. A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança. A auditoria permite e facilita a adoção de padrões de conformidade, mas não garante a conformidade.

A auditoria de Banco de Dados SQL permite:

-   **Retenha** uma trilha de auditoria dos eventos selecionados. Definir categorias de ações de banco de dados a ser auditadas.
-   **Relate** sobre a atividade do banco de dados. Utilizar relatórios pré-configurados e um painel para iniciar rapidamente um relatório de atividades e eventos.
-   **Analise** relatórios. Encontrar eventos suspeitos, atividades incomuns e tendências.

Há dois métodos de Auditoria:

-   **Auditoria de blob** – os logs são gravados no Armazenamento de Blobs do Azure. Esse é um método de auditoria mais recente, que fornece desempenho maior, dá suporte à auditoria do nível de objeto de granularidade mais alta e é mais econômico.
-   **Auditoria de tabela** – os logs são gravados no Armazenamento de Tabelas do Azure.

### <a name="threat-detection"></a>Detecção de ameaças
A [detecção de ameaças do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) detecta atividades suspeitas que indicam possíveis ameaças à segurança. A detecção de ameaças permite responder a eventos suspeitos no banco de dados, como Injeções de SQL, conforme elas ocorrem. Ela fornece alertas e permite o uso da Auditoria do Banco de Dados SQL do Azure para explorar os eventos suspeitos.

![Detecção de ameaças](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Por exemplo, a injeção de SQL é um dos problemas comuns de segurança do aplicativo da Web na Internet, usada para atacar os aplicativos orientados a dados. Os invasores aproveitam as vulnerabilidades do aplicativo para inserir instruções SQL mal-intencionadas nos campos de entrada do aplicativo, violando ou modificando os dados no banco de dados.

Os agentes de segurança ou outros administradores designados podem obter uma notificação imediata sobre atividades suspeitas de banco de dados conforme eles ocorrem. Cada notificação fornece detalhes da atividade suspeita e recomenda como investigar e minimizar a ameaça.        

## <a name="centralized-security-management"></a>Gerenciamento de segurança centralizado

A [Central de Segurança do Azure](https://azure.microsoft.com/documentation/services/security-center/) ajuda você a evitar, detectar e responder a ameaças. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

A [Central de Segurança](https://docs.microsoft.com/azure/security-center/security-center-sql-database) ajuda você a proteger os dados no Banco de Dados SQL, fornecendo visibilidade sobre a segurança de todos os servidores e bancos de dados. Com a Central de Segurança, você pode:

-   Definir políticas de criptografia e auditoria do Banco de Dados SQL.
-   Monitore a segurança dos recursos de Banco de Dados SQL em todas as suas assinaturas.
-   Identifique e corrija rapidamente problemas de segurança.
-   Integre os alertas da [detecção de ameaças do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
-   A Central de segurança dá suporte ao acesso baseado em função.

## <a name="azure-marketplace"></a>Azure Marketplace

O Azure Marketplace é um marketplace online de aplicativos e serviços que permite que empresas em fase inicial e ISVs (fornecedores independentes de software) ofereçam suas soluções aos clientes do Azure no mundo todo.
O Azure Marketplace combina ecossistemas de parceiros do Microsoft Azure em uma única plataforma unificada, para melhor atender nossos clientes e parceiros. Clique [aqui](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) para ver os produtos de segurança de banco de dados disponíveis no Azure Marketplace.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [Proteger o Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
- Saiba mais sobre a [Central de Segurança do Azure e o serviço Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/security-center/security-center-sql-database).
- Para saber mais sobre a detecção de ameaças, consulte [Detecção de ameaças do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
- Para saber mais, consulte [Melhorar o desempenho do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial). 
