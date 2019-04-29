---
title: Melhores práticas de segurança do banco de dados do Azure | Microsoft Docs
description: Este artigo fornece um conjunto de melhores práticas de segurança do banco de dados do Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: tomsh
ms.openlocfilehash: 3e244f89904ce9aca161ed1ea435f4137e42bc5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587506"
---
# <a name="azure-database-security-best-practices"></a>Melhores práticas de segurança do banco de dados do Azure
A segurança é uma das principais preocupações para o gerenciamento de bancos de dados e sempre foi uma prioridade para o [Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/). Seus bancos de dados podem ser totalmente protegidos para ajudar a atender a maioria dos requisitos regulamentares ou de segurança, como HIPAA, ISO 27001/27002 e PCI DSS Nível 1. Uma lista atual das certificações de conformidade de segurança está disponível no [site da Central de Confiabilidade da Microsoft](https://azure.microsoft.com/support/trust-center/services/). Você também pode optar por colocar seus bancos de dados em datacenters do Azure específico com base em requisitos regulamentares.

Neste artigo, veremos uma coleção de melhores práticas de segurança de banco de dados do Azure. Essas melhores práticas derivam da nossa experiência com segurança de banco de dados do Azure e da experiência de clientes como você.

Para cada prática recomendada, vamos explicar:

-   O que é a prática recomendada
-   Por que é ideal habilitar essa prática recomendada
-   O que poderá acontecer se você não habilitar a prática recomendada
-   Como você pode aprender a habilitar a prática recomendada

Este artigo Melhores práticas de segurança de banco de dados do Azure baseia-se em um consenso e nos recursos da plataforma Azure e em conjuntos de recursos tal como existiam no momento em que o artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Usar regras de firewall para restringir o acesso ao banco de dados
O Banco de Dados SQL do Microsoft Azure fornece um serviço de banco de dados relacional para o Azure e outros aplicativos baseados na Internet. Para fornecer segurança de acesso, Banco de Dados SQL controla o acesso com:

- Regras de firewall que limitam a conectividade por endereço IP.
- Mecanismos de autenticação que exigem que os usuários comprovem sua identidade.
- Mecanismos de autorização que limitam os usuários a ações e dados específicos.

Os firewalls impedem todo acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.

A figura a seguir mostra em que local você defini um firewall de servidor no Banco de Dados SQL:

![Regras de firewall](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

O serviço Banco de dados SQL do Azure só está disponível na porta TCP 1433. Para acessar um banco de dados SQL do seu computador, certifique-se de que o firewall do computador cliente permita a comunicação TCP de saída na porta TCP 1433. Bloqueie conexões de entrada na porta TCP 1433 usando regras de firewall se você não precisar dessas conexões para outros aplicativos.

Como parte do processo de conexão, as conexões de máquinas virtuais do Azure são redirecionadas para um endereço IP e uma porta exclusivos para cada função de trabalho. O número da porta está no intervalo de 11000 a 11999. Para obter mais informações sobre portas TCP, confira [Portas além da 1433 para o ADO.NET 4.5](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Para obter mais informações sobre as regras de firewall no Banco de Dados SQL, confira [Regras de firewall de Banco de Dados SQL](../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Além das regras IP, o firewall gerencia regras da rede virtual. As regras de rede virtual são baseadas nos pontos de extremidade de serviço de rede virtual. Regras da rede virtual podem ser preferíveis a regras de IP em alguns casos. Para saber mais, confira [Pontos de extremidade de serviço de rede virtual para o Banco de Dados SQL do Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Habilitar a autenticação de banco de dados
O Banco de Dados SQL dá suporte a dois tipos de autenticação: autenticação do SQL Server e autenticação do Azure AD.

### <a name="sql-server-authentication"></a>*Autenticação do SQL Server*

Os benefícios incluem o seguinte:

- Ela permite que o Banco de Dados SQL dê suporte a ambientes com sistemas operacionais mistos, em que nem todos os usuários são autenticados por um domínio do Windows.
- Permite que o Banco de Dados SQL dê suporte a aplicativos mais antigos e aplicativos fornecidos por parceiros que exigem a autenticação do SQL Server.
- Permite que os usuários se conectem de domínios desconhecidos ou não confiáveis. Por exemplo é um aplicativo em que os clientes estabelecidos se conectam com logons do SQL Server atribuídos para receber o status das suas ordens.
- Permite que o Banco de Dados SQL dê suporte a aplicativos baseados na Web em que os usuários criam suas próprias identidades.
- Permite que os desenvolvedores de software distribuam seus aplicativos usando uma hierarquia de permissões complexa com base em logons do SQL Server predefinidos e conhecidos.

> [!NOTE]
> A autenticação do SQL Server não pode usar o protocolo de segurança Kerberos.

Se você usar a autenticação do SQL Server, deverá:

- Gerenciar você mesmo as credenciais de alta segurança.
- Proteger as credenciais na cadeia de conexão.
- (Possivelmente) proteger as credenciais passadas pela rede do servidor Web para o banco de dados. Para obter mais informações, confira [Como: Conectar-se ao SQL Server usando Autenticação do SQL no ASP.NET 2.0](/previous-versions/msp-n-p/ff648340(v=pandp.10)).

### <a name="azure-active-directory-ad-authentication"></a>*Autenticação do Azure AD (Active Directory)*
A autenticação do Azure AD é um mecanismo de conexão com o Banco de Dados SQL do Azure e o [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando identidades no Azure AD. Com a autenticação do Azure AD, é possível gerenciar as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento central de IDs fornece um único local para gerenciar os usuários do banco de dados e simplifica o gerenciamento de permissões.

> [!NOTE]
> Recomendamos o uso de autenticação do Azure AD, em vez de autenticação do SQL Server.

Os benefícios incluem o seguinte:

- Ele fornece uma alternativa para autenticação do SQL Server.
- Ajuda a interromper a proliferação de identidades de usuário entre os servidores de banco de dados.
- Permite o rodízio de senhas em um único lugar.
- Os clientes podem gerenciar permissões de banco de dados usando grupos (Azure AD) externos.
- Pode eliminar o armazenamento de senhas, permitindo a autenticação integrada do Windows e outras formas de autenticação às quais o Active Directory do Azure dá suporte.
- Usa usuários de banco de dados independente para autenticar identidades no nível de banco de dados.
- Dá suporte à autenticação baseada em token em aplicativos que se conectam ao Banco de Dados SQL.
- Dá suporte ao AD FS (federação de domínio) ou à autenticação nativa de senha/usuário para um local da instância do Azure Active Directory sem a sincronização de domínio.
- O Azure AD dá suporte a conexões do SQL Server Management Studio que usam a Autenticação Universal do Active Directory, que inclui Autenticação Multifator. A Autenticação Multifator fornece autenticação forte com uma variedade de opções de verificação: chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação por aplicativos móveis. Para obter mais informações, confira [Suporte do SSMS para Autenticação Multifator do Azure AD com Banco de Dados SQL e SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

As etapas de configuração incluem os procedimentos a seguir para configurar e usar a autenticação do Azure AD:

- Criar e popular o Azure AD.
- Opcional: associe ou altere a instância do Active Directory associada atualmente à sua assinatura do Azure.
- Crie um administrador do Azure Active Directory para o Banco de Dados SQL do Azure ou o [SQL Data Warehouse do Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
- Configure os computadores cliente.
- Crie usuários de banco de dados independente em seu banco de dados, mapeados para identidades do Azure AD.
- Conecte-se ao banco de dados usando identidades do Azure AD.

Encontre informações detalhadas em [Usar a autenticação do Azure Active Directory para autenticação com o Banco de Dados SQL, a Instância Gerenciada ou o SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Proteger seus dados usando criptografia e segurança em nível de linha
[Transparent Data Encryption do Banco de Dados SQL do Azure](../sql-database/transparent-data-encryption-azure-sql.md) ajuda a proteger dados em disco e protege contra acesso não autorizado ao hardware. Ela realiza a criptografia e a descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo. A Transparent Data Encryption criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados.

Mesmo quando todo o armazenamento é criptografado, também é muito importante criptografar o banco de dados em si. Essa é uma implementação com abordagem de defesa profunda para proteção de dados. Se você estiver usando o Banco de Dados SQL do Azure e quiser proteger dados confidenciais (por exemplo, números de cartão de crédito ou previdência social), poderá criptografar os bancos de dados com o criptografia AES de 256 bits validada para FIPS 140-2. Essa criptografia cumpre aos requisitos de muitos padrões do setor (por exemplo, HIPAA e PCI).

Os arquivos relacionados à [BPE (extensão do pool de buffers)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) não são criptografados quando você criptografa um banco de dados usando a Transparent Data Encryption. Você deve usar ferramentas de criptografia no nível do sistema de arquivos, como [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) ou [EFS (Encrypting File System)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) para arquivos relacionados à BPE.

Já que um usuário autorizado, como um administrador de segurança ou um administrador de banco de dados, pode acessar os dados mesmo se o banco de dados está criptografado com Transparent Data Encryption, você também deve seguir estas recomendações:

- Habilite a autenticação do SQL Server no nível do banco de dados.
- Use a autenticação do Azure AD com [funções RBAC](../role-based-access-control/overview.md).
- Verifique se os usuários e aplicativos usam contas separadas para autenticação. Dessa forma, você pode limitar as permissões concedidas a usuários e aplicativos e reduzir o risco de atividades mal-intencionadas.
- Implementar a segurança em nível de banco de dados usando funções de banco de dados fixas (como db_datareader ou db_datawriter). Outra opção é criar funções personalizadas para seu aplicativo conceder permissões explícitas para objetos de banco de dados selecionados.

Para outras maneiras de proteger seus dados, considere:

- [Criptografia no nível de célula](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) para criptografar colunas específicas ou até mesmo células de dados com diferentes chaves de criptografia.
- O [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) permite que os clientes criptografem dados confidenciais em aplicativos clientes e nunca revelem as chaves de criptografia para o Mecanismo de Banco de Dados (Banco de Dados SQL ou SQL Server). Consequentemente, o Always Encrypted diferencia os proprietários dos dados (que podem vê-los) de quem gerencia os dados (mas não deve ter acesso).
- [Segurança em Nível de Linha](/sql/relational-databases/security/row-level-security), que permite aos clientes controlar o acesso às linhas em uma tabela de banco de dados com base nas características do usuário que está executando uma consulta. (As características de exemplo são contexto de execução e associação de grupo.)

As organizações que não estiverem usando criptografia no nível do banco de dados estarão mais suscetíveis a ataques que podem comprometer os dados localizados em bancos de dados SQL.

Você pode aprender mais sobre Transparent Data Encryption do Banco de Dados SQL lendo o artigo [Transparent Data Encryption com o Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Habilitar a auditoria do banco de dados
A auditoria de uma instância do Mecanismo de Banco de Dados do SQL Server ou de um banco de dados individual envolve o acompanhamento e o registro em log de eventos. Para o SQL Server, você pode criar as auditorias que contêm as especificações para eventos no nível de servidor e especificações para eventos no nível de banco de dados. Os eventos auditados podem ser gravados nos logs de eventos ou nos arquivos de auditoria.

Há vários níveis de auditoria do SQL Server, dependendo dos requisitos padrão ou governamentais de sua instalação. Auditoria do SQL Server fornece ferramentas e processos para habilitar, armazenar e exibir auditorias em vários servidor e objetos de banco de dados.

A [Auditoria do Banco de Dados SQL do Azure](../sql-database/sql-database-auditing.md) acompanha eventos de banco de dados e grava-os em um log de auditoria em sua conta de Armazenamento do Azure.

A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a encontrar discrepâncias e anomalias que podem apontar para preocupações empresariais ou violações de segurança. A auditoria facilita a adoção de padrões de conformidade, mas não garante a conformidade.

Para saber mais sobre a auditoria de banco de dados e como habilitá-la, confira [Introdução à auditoria de Banco de Dados SQL](../sql-database/sql-database-auditing.md).

## <a name="enable-database-threat-detection"></a>Habilitar detecção de ameaças ao banco de dados
Proteção contra ameaças vai além da detecção. A proteção contra ameaças do banco de dados inclui:

- Descobrir e classificar seus dados mais confidenciais para que você possa proteger seus dados.
- Implementar configurações seguras no seu banco de dados para que você possa proteger seu banco de dados.
- Detectar e responder às ameaças potenciais conforme elas ocorrem para que você possa responder e corrigi-las rapidamente.

**Melhor prática**: descobrir, classificar e rotular os dados confidenciais em seus bancos de dados.   
**Detalhe**: classificar os dados no Banco de Dados SQL, habilitando [Dados de Descoberta e Classificação](../sql-database/sql-database-data-discovery-and-classification.md) no Banco de Dados SQL do Azure. Você pode monitorar o acesso aos seus dados confidenciais no painel do Azure ou baixar relatórios.

**Melhor prática**: acompanhar vulnerabilidades de banco de dados para que você possa melhorar a segurança do banco de dados de maneira proativa.   
**Detalhe**: use o serviço de [Avaliação de Vulnerabilidade](../sql-database/sql-vulnerability-assessment.md) do Banco de Dados SQL do Azure, que verifica a existência de possíveis vulnerabilidades de banco de dados. O serviço emprega uma base de dados de conhecimento de regras que mostra vulnerabilidades de segurança e destaca desvios das práticas recomendadas, como configurações incorretas, excesso de permissões e dados confidenciais sem proteção.

As regras se baseiam nas melhores práticas da Microsoft e enfocam os problemas de segurança que apresentam os maiores riscos para o banco de dados e seus dados importantes. Elas abordam problemas no nível do banco de dados e problemas de segurança no nível do servidor, como configurações de firewall do servidor e permissões no nível do servidor. Essas regras também podem representar muitos dos requisitos de agências reguladoras para atender aos seus padrões de conformidade.

**Melhor prática**: Habilitar a detecção de ameaças.  
**Detalhe**:  habilite a [Detecção de Ameaças](../sql-database/sql-database-threat-detection.md) do Banco de Dados SQL do Azure para obter alertas de segurança e recomendações sobre como investigar e atenuar as ameaças. Você recebe alertas sobre atividades suspeitas no banco de dados possíveis vulnerabilidades e ataques de injeção de SQL, bem como padrões anômalos de consultas e acesso a banco de dados.

[Proteção Avançada contra Ameaças](../sql-database/sql-advanced-threat-protection.md) é um pacote unificado para recursos de segurança avançados do SQL. Ele inclui os serviços mencionados anteriormente: Descoberta e Classificação de Dados, Avaliação de Vulnerabilidades e Detecção de Ameaças. Fornece um local único para habilitar e gerenciar essas funcionalidades.

Habilitar essas funcionalidades ajuda você a:

- Cumprir os padrões de privacidade de dados e os requisitos de conformidade a normas.
- Controlar o acesso aos seus bancos de dados e fortalecer a segurança.
- Monitorar um ambiente de banco de dados dinâmico em que é difícil acompanhar as alterações.
- Detectar e responder a possíveis ameaças.

Além disso, a Detecção de Ameaças integra alertas com a Central de Segurança do Azure para uma exibição central do estado de segurança de todos os seus recursos do Azure.

## <a name="next-steps"></a>Próximas etapas
Veja [Melhores práticas e padrões de segurança do Azure](security-best-practices-and-patterns.md) para obter melhores práticas segurança complementares a serem usadas ao projetar, implantar e gerenciar as soluções de nuvem, usando o Azure.

Os seguintes recursos estão disponíveis para fornecer mais informações gerais sobre a segurança do Azure e os serviços da Microsoft relacionados:
* [Blog da equipe de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre as últimas novidades de Segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – o local em que vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser relatadas ou por email para secure@microsoft.com
