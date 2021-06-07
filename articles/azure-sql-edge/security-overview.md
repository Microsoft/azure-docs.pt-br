---
title: Proteger o Azure SQL Edge
description: Saiba mais sobre segurança no Azure SQL Edge
keywords: Borda do SQL, segurança
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56d5eb981aa02d9da83973d49e8df79fcd9c7e9c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95021007"
---
# <a name="securing-azure-sql-edge"></a>Protegendo o Azure SQL Edge

Com o aumento na adoção de IoT e computação de borda entre os setores, há um aumento no número de dispositivos e os dados gerados desses dispositivos. O aumento do volume de dados e o número de pontos de extremidade do dispositivo representa um desafio significativo em termos de segurança de dados e dispositivos. 

O Azure SQL Edge oferece vários recursos e funcionalidades que tornam relativamente mais fácil proteger os dados de IoT dentro dos bancos de dado SQL Server. O Azure SQL Edge é criado usando o mesmo mecanismo SQL que capacita Microsoft SQL Server e o SQL do Azure, compartilhando os mesmos recursos de segurança, o que facilita estender as mesmas políticas de segurança e práticas da nuvem para a borda.

Assim como Microsoft SQL Server e SQL do Azure, a proteção das implantações do Azure SQL Edge pode ser exibida como uma série de etapas que envolvem quatro áreas: a plataforma, a autenticação, os objetos (incluindo dados) e os aplicativos que acessam o sistema. 

## <a name="platform-and-system-security"></a>Segurança de plataforma e sistema

A plataforma do Azure SQL Edge inclui o host do Docker físico, o sistema operacional no host e os sistemas de rede que conectam o dispositivo físico a aplicativos e clientes. 

A implementação da segurança da plataforma começa com a manutenção de usuários não autorizados fora da rede. Algumas das práticas recomendadas incluem, mas não se limitam a:
- Implementando regras de firewall para garantir a política de segurança organizacional. 
- Verifique se o sistema operacional no dispositivo físico tem todas as atualizações de segurança mais recentes aplicadas. 
- Especificando e restringindo as portas de host que estão usando para o Azure SQL Edge
- Garantir que o controle de acesso adequado seja aplicado a todos os volumes de dados que hospedam dados do Azure SQL Edge. 

Para obter mais informações sobre protocolos de rede do Azure SQL Edge e pontos de extremidade TDS, consulte [protocolos de rede e pontos de extremidade TDS](/previous-versions/sql/sql-server-2008-r2/ms191220(v=sql.105)).

## <a name="authentication-and-authorization"></a>Autenticação e autorização 

### <a name="authentication"></a>Autenticação  
A autenticação é o processo de provar que o usuário é quem diz ser. O Azure SQL Edge atualmente dá suporte apenas ao `SQL Authentication` mecanismo.

- *Autenticação do SQL*:

    A autenticação do SQL refere-se à autenticação de um usuário ao se conectar ao Azure SQL Edge usando o nome de usuário e a senha. A senha de logon **SA** do SQL deve ser especificada durante a implantação do SQL Edge. Depois disso, os usuários e logons do SQL adicionais podem ser criados pelo administrador do servidor, que permite que os usuários se conectem usando nome de usuário e senha.

    Para obter mais informações sobre como criar e gerenciar logons e usuários no SQL Edge, consulte [criar um logon](/sql/relational-databases/security/authentication-access/create-a-login) e [criar um usuário de banco de dados](/sql/relational-databases/security/authentication-access/create-a-database-user).

### <a name="authorization"></a>Autorização   

A autorização refere-se às permissões atribuídas a um usuário em um banco de dados no Azure SQL Edge e determina o que o usuário tem permissão para fazer. As permissões são controladas adicionando contas de usuário a [funções de banco de dados](/sql/relational-databases/security/authentication-access/database-level-roles) e atribuindo permissões de nível de banco de dados a essas funções ou concedendo ao usuário determinadas [permissões de nível de objeto](/sql/relational-databases/security/permissions-database-engine). Para obter mais informações, consulte [logons e usuários](../azure-sql/database/logins-create-manage.md).

Como prática recomendada, crie funções personalizadas quando necessário. Adicione usuários à função com os privilégios mínimos necessários para realizar sua função de trabalho. Não atribua permissões diretamente aos usuários. A conta do administrador do servidor é um membro da função de db_owner interna, que tem permissões extensivas e só deve ser concedida a poucos usuários com tarefas administrativas. Para aplicativos, use [Executar como](/sql/t-sql/statements/execute-as-clause-transact-sql) para especificar o contexto de execução do módulo chamado ou usar [funções de aplicativo](/sql/relational-databases/security/authentication-access/application-roles) com permissões limitadas. Essa prática garante que o aplicativo que se conecta ao banco de dados tenha os privilégios mínimos necessários para o aplicativo. Seguir essas práticas recomendadas também promove a separação de tarefas.

## <a name="database-object-security"></a>Segurança de objeto de banco de dados

As entidades são as pessoas, os grupos e os processos com acesso concedido ao SQL Edge. "Protegíveis" são servidores, bancos de dados e objetos que o banco de dados contém. Cada um tem um conjunto de permissões que podem ser configuradas para ajudar a reduzir a área da superfície. A tabela a seguir contém informações sobre entidades e protegíveis.

|Para obter informações sobre|Consulte|  
|---------------------------|---------|  
|Usuários, funções e processos do servidor e do banco de dados|[Mecanismo de Banco de Dados de entidades](/sql/relational-databases/security/authentication-access/principals-database-engine)|  
|Segurança de objetos do servidor e do banco de dados|[Protegíveis](/sql/relational-databases/security/securables)|
| &nbsp; | &nbsp; |

### <a name="encryption-and-certificates"></a>Criptografia e certificados  
 
A criptografia não resolve problemas de controle de acesso. Porém, aumenta a segurança, limitando a perda de dados mesmo se os controles de acesso forem ignorados, o que é raro. Por exemplo, se o computador host do banco de dados for malconfigurado e um usuário malicioso obtiver dados confidenciais, como números de cartão de crédito, essas informações roubadas poderão ser inúteis se estiverem criptografadas. A tabela a seguir contém mais informações sobre criptografia no Azure SQL Edge.  
  
|Para obter informações sobre|Consulte|  
|---------------------------|---------|  
|Implementando conexões seguras|[Criptografando conexões](/sql/linux/sql-server-linux-encrypted-connections)|  
|Funções de criptografia|[Funções criptográficas &#40;Transact-SQL&#41;](/sql/t-sql/functions/cryptographic-functions-transact-sql)|
|Criptografia de dados em repouso|[Criptografia de Dados Transparente](/sql/relational-databases/security/encryption/transparent-data-encryption)|
|Always Encrypted|[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)|
| &nbsp; | &nbsp; |

> [!NOTE]
> As limitações de segurança descritas para [SQL Server em Linux](/sql/linux/sql-server-linux-security-overview) também se aplicam ao Azure SQL Edge. 


> [!NOTE]
> O Azure SQL Edge não inclui o utilitário MSSQL-conf. Todas as configurações que incluem a configuração relacionada à criptografia precisam ser executadas por meio do [arquivo MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file) ou das [variáveis de ambiente](configure.md#configure-by-using-environment-variables). 


Semelhante ao Azure SQL e Microsoft SQL Server, o Azure SQL Edge fornece o mesmo mecanismo para criar e usar certificados para aprimorar a segurança do objeto e da conexão. Para obter mais informações, consulte [criar certificado (TRANSACT-SQL)](/sql/t-sql/statements/create-certificate-transact-sql).


## <a name="application-security"></a>Segurança do aplicativo

### <a name="client-programs"></a>Programas clientes

As práticas recomendadas de segurança do Azure SQL Edge incluem a gravação de aplicativos cliente seguros. Para obter mais informações sobre como ajudar a proteger aplicativos cliente na camada de rede, consulte [Configuração de Rede Cliente](/sql/database-engine/configure-windows/client-network-configuration).

### <a name="security-catalog-views-and-functions"></a>Exibições e funções do catálogo de segurança  
As informações de segurança são expostas em várias exibições e funções que são otimizadas para desempenho e utilitário. A tabela a seguir contém informações sobre as exibições e funções de segurança no Azure SQL Edge.  
  
|Funções e exibições|Links|  
|---------------------------|---------|  
|Exibições do catálogo de segurança, que retornam informações sobre permissões no nível de banco de dados e de servidor, entidades, funções e assim por diante. Além disso, há exibições do catálogo que fornecem informações sobre chaves de criptografia, certificados e credenciais.|[Exibições de catálogo de segurança &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)|  
|Funções de segurança, que retornam informações sobre o usuário atual, permissões e esquemas.|[Funções de segurança &#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)|  
|Exibições de gerenciamento dinâmico de segurança.|[Funções e exibições de gerenciamento dinâmico relacionadas à segurança &#40;Transact-SQL&#41;](/sql/relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql)|  
| &nbsp; | &nbsp; |

### <a name="auditing"></a>Auditoria 

O Azure SQL Edge fornece os mesmos mecanismos de auditoria que SQL Server. Para obter mais informações, consulte [SQL Server Audit (mecanismo de banco de dados)](/sql/relational-databases/security/auditing/sql-server-audit-database-engine).


## <a name="next-steps"></a>Próximas etapas

- [Introdução com recursos de segurança](/sql/linux/sql-server-linux-security-get-started)
- [Executando o Azure SQL Edge como um usuário não raiz](configure.md#run-azure-sql-edge-as-non-root-user)
- [Central de Segurança do Azure para IoT](../defender-for-iot/overview.md)