---
title: Protegendo bancos de dados de PaaS no Azure | Microsoft Docs
description: " Saiba mais sobre as melhores práticas de segurança do Banco de Dados SQL do Azure e do SQL Data Warehouse para proteger aplicativos Web e móveis PaaS. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: terrylan
ms.openlocfilehash: 18509b3fc3a73118f67583a0b087c58f0e51993c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="securing-paas-databases-in-azure"></a>Protegendo bancos de dados de PaaS no Azure

Neste artigo, discutiremos uma coleção de práticas recomendadas de segurança do [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) e do [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) para proteger seus aplicativos Web e móveis de PaaS. Essas práticas recomendadas derivam da nossa experiência com o Azure e da experiência de clientes como você.

## <a name="azure-sql-database-and-sql-data-warehouse"></a>Banco de Dados SQL do Azure e SQL Data Warehouse
O [Banco de Dados SQL do Azure](../sql-database/sql-database-technical-overview.md) e o [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) fornecem um serviço de banco de dados relacional para seus aplicativos baseados na Internet. Vamos examinar os serviços que ajudam a proteger seus aplicativos e dados ao usar o Banco de Dados SQL do Azure e o SQL Data Warehouse em uma implantação de PaaS:

- Autenticação do Azure Active Directory (em vez da autenticação do SQL Server)
- Firewall do SQL do Azure
- Transparent Data Encryption (TDE)

## <a name="best-practices"></a>Práticas recomendadas

### <a name="use-a-centralized-identity-repository-for-authentication-and-authorization"></a>Usar um repositório de identidades centralizado para autenticação e autorização

Os bancos de dados SQL do Azure podem ser configurados para usar um dos dois tipos de autenticação:

- A **Autenticação do SQL** usa um nome de usuário e senha. Quando você criou o servidor lógico do banco de dados, especificou um logon de "administrador de servidor" com um nome de usuário e uma senha. Usando essas credenciais, é possível se autenticar em qualquer banco de dados nesse servidor como o proprietário do banco de dados.

- A **Autenticação do Azure Active Directory** usa identidades gerenciadas pelo Azure Active Directory e que tem suporte para domínios gerenciados e integrados. Para usar a Autenticação do Azure Active Directory, você deve criar outro administrador de servidor chamado “admin do Azure AD”, que tenha permissão para administrar usuários e grupos do Azure AD. Este administrador também pode executar todas as operações executadas por um administrador de servidor comum.

A [autenticação do Azure Active Directory](../active-directory/develop/active-directory-authentication-scenarios.md) é um mecanismo de conexão com o Banco de Dados SQL do Azure e o SQL Data Warehouse usando identidades no Azure AD (Active Directory). O Azure AD fornece uma alternativa para a autenticação do SQL Server para que você possa parar a proliferação de identidades de usuário entre os servidores de banco de dados. A autenticação do Azure AD permite que você gerencie centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento central de IDs fornece um único local para gerenciar os usuários do banco de dados e simplifica o gerenciamento de permissões.  

Os benefícios de usar a autenticação do Azure AD em vez da autenticação do SQL incluem:

- Permite o rodízio de senhas em um único lugar.
- Gerencia as permissões de banco de dados usando grupos externos do Azure AD.
- Elimina o armazenamento de senhas permitindo a autenticação integrada do Windows e outras formas de autenticação com suporte pelo Azure AD.
- Usa usuários de banco de dados independente para autenticar identidades no nível de banco de dados.
- Dá suporte à autenticação baseada em token em aplicativos que se conectam ao Banco de Dados SQL.
- Dá suporte ao ADFS (federação de domínio) ou à autenticação nativa de senha/usuário para um Azure AD local sem a sincronização de domínio.
- Dá suporte a conexões do SQL Server Management Studio que usam a Autenticação Universal do Active Directory, que inclui o [MFA (Autenticação Multifator)](../multi-factor-authentication/multi-factor-authentication.md). A MFA inclui autenticação eficiente com uma variedade de opções de verificação fáceis como chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação por aplicativos móveis. Para saber mais, confira [Suporte do SSMS para MFA do Azure AD com o Banco de Dados SQL e o SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Para saber mais sobre a autenticação do Azure AD, consulte:

- [Conexão ao Banco de Dados SQL ou ao SQL Data Warehouse usando a autenticação do Azure Active Directory](../sql-database/sql-database-aad-authentication.md)
- [Autenticação do SQL Data Warehouse do Azure](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Token-based authentication support for Azure SQL DB using Azure AD authentication](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/) (Suporte para autenticação baseada em token para o Banco de Dados SQL do Azure usando a autenticação do Azure AD)

> [!NOTE]
> Para garantir que o Azure Active Directory seja uma boa opção para seu ambiente, confira [Limitações e recursos do Azure AD](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), especificamente as considerações adicionais.
>
>

### <a name="restrict-access-based-on-ip-address"></a>Restringir o acesso com base no endereço IP
Você pode criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Essas regras podem ser direcionadas nos níveis do servidor e do banco de dados. É recomendável o uso de regras de firewall no nível do banco de dados sempre que possível, a fim de tornar seu banco de dados mais portátil. As regras de firewall no nível do servidor são melhor usadas para administradores e quando você tem muitos bancos de dados com os mesmos requisitos de acesso, mas não quer gastar tempo configurando cada um individualmente.

As restrições de endereço IP de origem do Banco de Dados SQL permitem o acesso de qualquer endereço do Azure (incluindo outras assinaturas e locatários). Você pode restringir isso para permitir apenas que seus endereços IP acessem a instância. Mesmo com as restrições de endereço IP e firewall do SQL, a autenticação forte ainda é necessária. Consulte as recomendações feitas anteriormente neste artigo.

Para saber mais sobre as restrições de IP e o Firewall do SQL do Azure, consulte:

- [Controle de acesso do Banco de Dados SQL do Azure](../sql-database/sql-database-control-access.md)
- [Configurar regras de firewall do Banco de Dados SQL do Azure – visão geral](../sql-database/sql-database-firewall-configure.md)
- [Configurar uma regra de firewall no nível de servidor de Banco de Dados SQL do Azure usando o Portal do Azure](../sql-database/sql-database-configure-firewall-settings.md)

### <a name="encryption-of-data-at-rest"></a>Criptografia de dados em repouso
A [TDE (Transparent Data Encryption)](https://msdn.microsoft.com/library/azure/bb934049) é habilitada por padrão. A TDE criptografa de forma transparente os arquivos de log e de dados do SQL Server, do Banco de Dados SQL do Azure e do SQL Data Warehouse do Azure. A TDE protege contra o comprometimento de acesso direto aos arquivos ou aos backups dos arquivos. Isso permite que você criptografe os dados em repouso sem alterar os aplicativos existentes. A TDE deverá permanecer sempre habilitada. No entanto, isso não interromperá um invasor que utilizar o caminho de acesso normal. A TDE proporciona a capacidade de entrar em conformidade com muitas leis, regulamentações e diretrizes estabelecidas em vários setores.

O SQL do Azure gerencia problemas relacionados a chave para a TDE. Assim como com a TDE, devem ser tomados cuidados especiais locais a fim de garantir a capacidade de recuperação e ao mover bancos de dados. Em cenários mais sofisticados, as chaves podem ser explicitamente gerenciadas no Azure Key Vault por meio do gerenciamento extensível de chaves (consulte [Habilitar TDE no SQL Server usando EKM](/security/encryption/enable-tde-on-sql-server-using-ekm)). Isso também permite o BYOK (Bring Your Own Key) por meio da capacidade BYOK do Azure Key Vault.

O SQL do Azure fornece a criptografia para colunas por meio do [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Isso permite o acesso somente de aplicativos autorizados às colunas confidenciais. O uso dessa variante de criptografia limita as consultas SQL às colunas criptografadas à valores com base em igualdade.

A criptografia do nível do aplicativo também deve ser usada para dados seletivos. Algumas vezes, as preocupações com a soberania de dados podem ser reduzidas com a criptografia de dados com uma chave que seja mantida no país correto. Isso impede que até uma transferência de dados acidental cause um problema, uma vez que é impossível descriptografar os dados sem a chave, supondo que um algoritmo forte seja usado (como AES 256).

Você pode usar precauções adicionais para ajudar a proteger o banco de dados, como a criação de um sistema seguro, a criptografia de ativos confidenciais e a criação de um firewall em torno de servidores de bancos de dados.

## <a name="next-steps"></a>Próximas etapas
Este artigo apresentou uma coleção de práticas recomendadas de segurança do Banco de Dados SQL e SQL Data Warehouse para proteger seus aplicativos Web e móveis de PaaS. Para saber mais sobre como proteger suas implantações de PaaS, confira:

- [Proteção de implantações de PaaS](security-paas-deployments.md)
- [Securing PaaS web and mobile applications using Azure App Services](security-paas-applications-using-app-services.md) (Proteção dos aplicativos Web e móveis de PaaS usando os Serviços de Aplicativos do Azure)
