---
title: "Melhores práticas de segurança do banco de dados do Azure | Microsoft Docs"
description: "Este artigo fornece um conjunto de melhores práticas de segurança do banco de dados do Azure."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2017
ms.author: tomsh
ms.openlocfilehash: 5bd6fe0dd369b3bbc7ca0d697c964badda557cb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-security-best-practices"></a>Melhores práticas de segurança do banco de dados do Azure

A segurança é uma das principais preocupações durante o gerenciamento de bancos de dados e sempre foi uma prioridade para o Banco de Dados SQL do Azure. Seus bancos de dados podem ser totalmente protegidos para ajudar a atender a maioria dos requisitos regulamentares ou de segurança, como HIPAA, ISO 27001/27002 e PCI DSS Nível 1, entre outros. Uma lista atual das certificações de conformidade de segurança está disponível no [site da Central de Confiabilidade da Microsoft](http://azure.microsoft.com/support/trust-center/services/). Você também pode optar por colocar seus bancos de dados em datacenters do Azure específico com base em requisitos regulamentares.

Neste artigo, veremos uma coleção de melhores práticas de segurança de banco de dados do Azure. Essas melhores práticas derivam da nossa experiência com segurança de banco de dados do Azure e da experiência de clientes como você.

Para cada prática recomendada, vamos explicar:

-   O que é a prática recomendada
-   Por que é ideal habilitar essa prática recomendada
-   O que poderá acontecer se você não habilitar a prática recomendada
-   Como você pode aprender a habilitar a prática recomendada

Este artigo Melhores práticas de segurança de banco de dados do Azure baseia-se em um consenso e nos recursos da plataforma Azure e em conjuntos de recursos tal como existiam no momento em que o artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

As melhores práticas de segurança de banco de dados do Azure discutidas neste artigo incluem:

-   Usar regras de firewall para restringir o acesso ao banco de dados
-   Habilitar a autenticação de banco de dados
-   Proteger seus dados usando criptografia
-   Proteger dados em trânsito
-   Habilitar a auditoria do banco de dados
-   Habilitar detecção de ameaças ao banco de dados


## <a name="use-firewall-rules-to-restrict-database-access"></a>Usar regras de firewall para restringir o acesso ao banco de dados

O Banco de Dados SQL do Microsoft Azure fornece um serviço de banco de dados relacional para o Azure e outros aplicativos baseados na Internet. Para fornecer segurança de acesso, o Banco de Dados SQL controla o acesso com regras de firewall que limitam a conectividade por endereço IP,mecanismos de autenticação que exigem que usuários comprovem suas identidade e mecanismos de autorização que limitam os usuários a ações e dados específicos. Os firewalls impedem todo acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.

![Regras de firewall](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

O serviço de Banco de dados SQL do Azure só está disponível por meio da porta TCP 1433. Para acessar um Banco de dados SQL do seu computador, certifique-se de que o firewall do computador cliente permita a comunicação TCP de saída na porta TCP 1433. Se não for necessário a outros aplicativos, bloqueie as conexões de entrada na porta TCP 1433 usando regras de firewall.

Como parte do processo de conexão, as conexões de máquinas virtuais do Azure são redirecionadas para um endereço IP e uma porta diferentes, exclusivos para cada função de trabalho. O número da porta está no intervalo de 11000 a 11999. Para obter mais informações sobre portas TCP, consulte [Portas além da 1433 para o ADO.NET 4.5 e o Banco de Dados SQL 2](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12).

> [!Note]
> Para obter mais informações sobre as regras de firewall no Banco de Dados SQL, confira [Regras de firewall de Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="enable-database-authentication"></a>Habilitar a autenticação de banco de dados
O Banco de Dados SQL dá suporte a dois tipos de autenticação: a autenticação do SQL e a autenticação do Azure AD (Autenticação do Azure Active Directory).

A **Autenticação do SQL** é recomendável nos seguintes casos:

-   Ela permite que o SQL Azure dê suporte a ambientes com sistemas operacionais mistos, onde nem todos os usuários são autenticados por um domínio do Windows.
-   Permite que o SQL Azure dê suporte a aplicativos mais antigos e a aplicativos fornecidos por terceiros que exigem a autenticação do SQL Server.
-   Permite que os usuários se conectem de domínios desconhecidos ou não confiáveis. Por exemplo, um aplicativo em que os clientes estabelecidos se conectam com logons do SQL Server atribuídos para receber o status das suas ordens.
-   Permite que o SQL Azure dê suporte a aplicativos baseados na Web em que os usuários criam suas próprias identidades.
-   Permite que os desenvolvedores de software distribuam seus aplicativos usando uma hierarquia de permissões complexa com base em logons do SQL Server predefinidos e conhecidos.

> [!Note]
> No entanto, a autenticação do SQL Server não pode usar o protocolo de segurança Kerberos.

Se você usa a **autenticação SQL**, deve:

-   Gerenciar você mesmo as credenciais de alta segurança.
-   Proteger as credenciais na cadeia de conexão.
-   (Possivelmente) proteger as credenciais passadas pela rede do servidor Web para o banco de dados. Para saber mais, confira [Como conectar-se ao SQL Server usando autenticação do SQL no ASP.NET 2.0](https://msdn.microsoft.com/library/ms998300.aspx).

A **autenticação do Azure Active Directory** é um mecanismo de conexão com o Banco de Dados SQL do Microsoft Azure e o [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) usando identidades no Azure Active Directory (Azure AD). Com a autenticação do Azure AD, é possível gerenciar centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento central de IDs fornece um único local para gerenciar os usuários do banco de dados e simplifica o gerenciamento de permissões. Os benefícios incluem o seguinte:

-   Ele fornece uma alternativa para autenticação do SQL Server.
-   Ajuda a interromper a proliferação de identidades de usuário entre os servidores de banco de dados.
-   Permite o rodízio de senhas em um único lugar.
-   Os clientes podem gerenciar permissões de banco de dados usando grupos (AAD) externos.
-   Pode eliminar o armazenamento de senhas, permitindo a autenticação integrada do Windows e outras formas de autenticação às quais o Active Directory do Azure dá suporte.
-   A autenticação do Azure AD usa usuários de banco de dados independente para autenticar identidades no banco de dados.
-   O Azure AD dá suporte à autenticação baseada em token em aplicativos que se conectam ao Banco de Dados SQL.
-   A autenticação do Azure AD dá suporte ao ADFS (federação de domínio) ou à autenticação nativa de senha/usuário para um local do Azure Active Directory sem a sincronização de domínio.
-   O Azure AD dá suporte a conexões do SQL Server Management Studio que usam a Autenticação Universal do Active Directory, que inclui o MFA (Autenticação Multifator). A MFA inclui autenticação eficiente com uma variedade de opções de verificação fáceis como chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação por aplicativos móveis. Para saber mais, confira [Suporte do SSMS para MFA do Azure AD com o Banco de Dados SQL e o SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication).

As etapas de configuração incluem os procedimentos a seguir para configurar e usar a autenticação do Active Directory do Azure.

-   Criar e popular o Azure AD.
-   Opcional: associar ou alterar o Active Directory que está associado atualmente à sua Assinatura do Azure.
-   Crie um administrador do Azure Active Directory para o Azure SQL Server ou o [SQL Data Warehouse do Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
- Configure os computadores cliente.
-   Crie usuários de banco de dados independente em seu banco de dados, mapeados para identidades do Azure AD.
-   Conecte-se ao banco de dados usando identidades do Azure AD.

Você pode encontrar informações detalhadas [aqui](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

## <a name="protect-your-data-using-encryption"></a>Proteger seus dados usando criptografia

O [TDE (Transparent Data Encryption) de Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/dn948096.aspx) ajuda a proteger contra atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem exigir mudanças no aplicativo. A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados.

Mesmo quando todo o armazenamento é criptografado, é muito importante também criptografar seu próprio banco de dados. Essa é uma implementação com abordagem de defesa profunda para proteção de dados. Se você estiver usando o Banco de Dados SQL Azure e desejar proteger dados confidenciais, como números de previdência social ou de cartão de crédito, poderá criptografar os bancos de dados com criptografia AES de 256 bits validada por FIPS 140-2, que atende aos requisitos de muitos padrões do setor (por exemplo, HIPAA, PCI).

É importante compreender que os arquivos relacionados à [BPE](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) (extensão do pool de buffers) não são criptografados quando um banco de dados é criptografado usando TDE. Você deve usar ferramentas de criptografia no nível do sistema de arquivos, como [BitLocker](https://technet.microsoft.com/library/cc732774) ou [EFS](https://technet.microsoft.com/library/cc700811.aspx) (Encrypting File System) para arquivos relacionados à BPE.

Já que um usuário autorizado, como um administrador de segurança ou um administrador de banco de dados, pode acessar os dados mesmo se o banco de dados estiver criptografado com TDE, você também deve seguir as recomendações abaixo:

-   Habilite a autenticação SQL no nível do banco de dados.
-   Use a autenticação do Azure AD com [funções RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is).
-   Os usuários e os aplicativos devem usar contas separadas para autenticar. Dessa forma, você pode limitar as permissões concedidas a usuários e aplicativos e reduzir os riscos de atividades mal-intencionadas.
-   Implemente segurança no nível do banco de dados usando funções de banco de dados fixas (como db_datareader ou db_datawriter) ou crie funções personalizadas para seu aplicativo conceder permissões explícitas para objetos de banco de dados selecionados.

Para outras maneiras de criptografar seus dados, considere:

-   [Criptografia no nível de célula](https://msdn.microsoft.com/library/ms179331.aspx) para criptografar colunas específicas ou até mesmo células de dados com diferentes chaves de criptografia.
-   Criptografia em uso com Always Encrypted: o [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) permite que os clientes criptografem dados confidenciais em aplicativos clientes e nunca revelem as chaves de criptografia para o mecanismo de banco de dados (Banco de dados SQL ou SQL Server). Consequentemente, o Always Encrypted diferencia os proprietários dos dados (que podem vê-los) de quem gerencia os dados (mas não deve ter acesso).
-   Usando segurança em nível de linha: A Segurança em Nível de Linha permite aos clientes controlar o acesso às linhas em uma tabela de banco de dados com base nas características do usuário que executa uma consulta (por exemplo, uma associação de grupo ou um contexto de execução). Para saber mais, confira [Segurança em nível de linha](https://msdn.microsoft.com/library/dn765131).

## <a name="protect-data-in-transit"></a>Proteger dados em trânsito
A proteção dos dados em trânsito deve ser parte essencial de sua estratégia de proteção de dados. Já que os dados se moverão entre vários locais, a recomendação geral é que você sempre use protocolos SSL/TLS para trocar os dados entre diferentes locais. Em alguns casos, convém isolar todo o canal de comunicação entre suas infraestruturas locais e na nuvem usando uma VPN (rede virtual privada).

Para dados que se movem entre sua infraestrutura local e o Azure, você deve considerar proteções adequadas, como HTTPS ou VPN.

Para organizações que precisam proteger o acesso de várias estações de trabalho localizadas localmente ao Azure, use [VPN site a site do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Para organizações que precisam proteger o acesso de estações de trabalho individuais localizadas no local ou externo ao Azure, considere o uso de [VPN Ponto a Site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Conjuntos de dados maiores podem ser movidos em um link WAN de alta velocidade dedicado, como o [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Se você optar por usar o ExpressRoute, também poderá criptografar os dados no nível do aplicativo usando [SSL/TLS](https://support.microsoft.com/kb/257591) ou outros protocolos para proteção adicional.

Se você estiver interagindo com o Armazenamento do Azure pelo Portal do Azure, todas as transações ocorrerão via HTTPS. A [API REST de armazenamento](https://msdn.microsoft.com/library/azure/dd179355.aspx) sobre HTTPS também pode ser usada para interagir com o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) e com o [Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/).

As organizações que não protegem os dados em trânsito são mais suscetíveis de [ataques man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [eavesdropping](https://technet.microsoft.com/library/gg195641.aspx) e sequestro de sessão. Esses ataques podem ser a primeira etapa na obtenção de acesso a dados confidenciais.

Para aprender mais sobre a opção de VPN do Azure lendo o artigo [Planejamento e design para o Gateway de VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-plan-design).

## <a name="enable-database-auditing"></a>Habilitar a auditoria do banco de dados
A auditoria de uma instância do Mecanismo de Banco de Dados do SQL Server ou de um banco de dados individual envolve o acompanhamento e o registro em log de eventos que ocorrem no Mecanismo de Banco de Dados. A auditoria do SQL Server permite criar auditorias de servidor, que podem conter especificações de auditoria de servidor para eventos no nível do servidor e especificações de auditoria de banco de dados para eventos no nível do banco de dados. Os eventos auditados podem ser gravados nos logs de eventos ou nos arquivos de auditoria.

Há vários níveis de auditoria do SQL Server, dependendo dos requisitos padrão ou governamentais de sua instalação. A auditoria do SQL Server fornece as ferramentas e os processos necessários para habilitar, armazenar e exibir auditorias em vários objetos de servidor e de banco de dados.

A [Auditoria do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) rastreia eventos do banco de dados e os grava em um log de auditoria em sua conta do Azure Storage.

A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança.

A auditoria permite e facilita a adoção de padrões de conformidade, mas não garante a conformidade.

Para saber mais sobre a auditoria de banco de dados e como habilitá-la, leia o artigo [Habilitar a detecção de ameaças e auditoria em servidores SQL na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="enable-database-threat-detection"></a>Habilitar detecção de ameaças ao banco de dados
A Detecção de Ameaças do SQL permite detectar e reagir às possíveis ameaças conforme elas ocorrem emitindo alertas de segurança em caso de atividades anômalas. Você receberá um alerta em caso de atividades suspeitas em bancos de dados, possíveis vulnerabilidades e ataques de injeção de SQL, bem como padrões anômalos de acesso ao banco de dados. Os alertas da Detecção de Ameaças do SQL fornecem detalhes de atividades suspeitas e recomendam ação de como investigar e atenuar a ameaça.

Por exemplo, a injeção de SQL é um dos problemas comuns de segurança do aplicativo da Web na Internet, usada para atacar os aplicativos orientados a dados. Os invasores aproveitam as vulnerabilidades do aplicativo para inserir instruções SQL mal-intencionadas nos campos de entrada do aplicativo, violando ou modificando os dados no banco de dados.

Para saber mais sobre como configurar a detecção de ameaças do banco de dados no portal do Azure, confira [Detecção de Ameaças do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Além disso, a Detecção de Ameaças do SQL integra seus alertas à [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/). Convidamos você a testar o recurso por 60 dias gratuitamente.

Para saber mais sobre a Detecção de Ameaças do Banco de Dados e como habilitá-la, leia o artigo [Habilitar a detecção de ameaças e auditoria em servidores SQL na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="conclusion"></a>Conclusão
O Banco de Dados do Azure é uma plataforma robusta de banco de dados, com uma gama completa de recursos de segurança que atendem aos vários requisitos de conformidade organizacional e regulamentar. Você pode ajudar a proteger os dados controlando o acesso físico aos seus dados e usando uma variedade de opções de segurança de dados no nível de arquivo, coluna ou linha com Transparent Data Encryption, criptografia no nível de célula ou segurança no nível de linha . Always Encrypted também permite operações em dados criptografados, simplificando o processo de atualizações do aplicativo. Por sua vez, o acesso a logs de auditoria da atividade do Banco de Dados SQL fornece a você as informações que você precisa, permitindo que você saiba como e quando os dados são acessados.

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre regras de firewall, consulte [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).
- Para saber mais sobre logons e usuários, veja [Gerenciar logons](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).
- Para obter um tutorial, consulte [Proteger o Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
