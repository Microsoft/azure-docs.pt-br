---
title: Protegendo bancos de dados de PaaS no Azure | Microsoft Docs
description: 'Saiba mais sobre as melhores práticas de segurança do Banco de Dados SQL do Azure e do SQL Data Warehouse para proteger aplicativos Web e móveis PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 22db43413b5c752decf6785a75dff22ff4a68039
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596666"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Melhores práticas para proteger bancos de dados de PaaS no Azure

Neste artigo, discutiremos uma coleção de práticas recomendadas de segurança do [Banco de Dados SQL do Azure](../sql-database/sql-database-technical-overview.md) e do [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) para proteger seus aplicativos Web e móveis de PaaS (plataforma como serviço). Essas práticas recomendadas derivam da nossa experiência com o Azure e da experiência de clientes como você.

O Banco de Dados SQL do Azure e o SQL Data Warehouse fornecem um serviço de banco de dados relacional para seus aplicativos baseados na Internet. Vamos examinar os serviços que ajudam a proteger seus aplicativos e dados ao usar o Banco de Dados SQL do Azure e o SQL Data Warehouse em uma implantação de PaaS:

- Autenticação do Azure Active Directory (em vez da autenticação do SQL Server)
- Firewall do SQL do Azure
- Transparent Data Encryption (TDE)

## <a name="use-a-centralized-identity-repository"></a>Usar um repositório de identidades centralizado
Os bancos de dados SQL do Azure podem ser configurados para usar um dos dois tipos de autenticação:

- A **autenticação do SQL** usa um nome de usuário e senha. Quando você criou o servidor lógico do banco de dados, especificou um logon de "administrador de servidor" com um nome de usuário e uma senha. Usando essas credenciais, é possível se autenticar em qualquer banco de dados nesse servidor como o proprietário do banco de dados.

- A **autenticação do Azure Active Directory** usa identidades gerenciadas pelo Azure Active Directory e tem suporte para domínios gerenciados e integrados. Para usar a Autenticação do Azure Active Directory, você deve criar outro administrador de servidor chamado “admin do Azure AD”, que tenha permissão para administrar usuários e grupos do Azure AD. Este administrador também pode executar todas as operações executadas por um administrador de servidor comum.

A [autenticação do Azure Active Directory](../active-directory/develop/authentication-scenarios.md) é um mecanismo de conexão com o Banco de Dados SQL do Azure e o SQL Data Warehouse usando identidades no Azure AD (Active Directory). O Azure AD fornece uma alternativa para a autenticação do SQL Server para que você possa parar a proliferação de identidades de usuário entre os servidores de banco de dados. A autenticação do Azure AD permite que você gerencie centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento central de IDs fornece um único local para gerenciar os usuários do banco de dados e simplifica o gerenciamento de permissões.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Benefícios de usar o Azure AD em vez da autenticação SQL
- Permite o rodízio de senhas em um único lugar.
- Gerencia as permissões de banco de dados usando grupos externos do Azure AD.
- Elimina o armazenamento de senhas permitindo a autenticação integrada do Windows e outras formas de autenticação com suporte pelo Azure AD.
- Usa usuários de banco de dados independente para autenticar identidades no nível de banco de dados.
- Dá suporte à autenticação baseada em token em aplicativos que se conectam ao Banco de Dados SQL.
- Suporte à federação de domínio com os ADFS (Serviços de Federação do Active Directory) ou à autenticação nativa de senha/usuário para um local do Azure AD sem a sincronização de domínio.
- Dá suporte a conexões do SQL Server Management Studio que usam a Autenticação Universal do Active Directory, que inclui o [MFA (Autenticação Multifator)](../active-directory/authentication/multi-factor-authentication.md). A MFA inclui autenticação eficiente com uma variedade de opções de verificação fáceis como chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação por aplicativos móveis. Para saber mais, confira [Autenticação universal com o Banco de Dados SQL e SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Para saber mais sobre a autenticação do Azure AD, consulte:

- [Usar a Autenticação do Azure Active Directory para autenticação com o Banco de Dados SQL, a Instância Gerenciada ou o SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md)
- [Autenticação do SQL Data Warehouse do Azure](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Token-based authentication support for Azure SQL DB using Azure AD authentication](../sql-database/sql-database-aad-authentication.md) (Suporte para autenticação baseada em token para o Banco de Dados SQL do Azure usando a autenticação do Azure AD)

> [!NOTE]
> Para garantir que o Azure Active Directory seja uma boa opção para seu ambiente, confira [Limitações e recursos do Azure AD](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Restringir o acesso com base no endereço IP
Você pode criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Essas regras podem ser direcionadas nos níveis do servidor e do banco de dados. É recomendável o uso de regras de firewall no nível do banco de dados sempre que possível, a fim de tornar seu banco de dados mais portátil. As regras de firewall no nível do servidor são melhor usadas para administradores e quando você tem muitos bancos de dados com os mesmos requisitos de acesso, mas não quer gastar tempo configurando cada um individualmente.

As restrições de endereço IP de origem do Banco de Dados SQL permitem o acesso de qualquer endereço do Azure, incluindo outras assinaturas e locatários. Você pode restringir isso para permitir apenas que seus endereços IP acessem a instância. Mesmo com as restrições de endereço IP e firewall do SQL, a autenticação forte ainda é necessária. Consulte as recomendações feitas anteriormente neste artigo.

Para saber mais sobre as restrições de IP e o Firewall do SQL do Azure, consulte:

- [Controle de acesso ao SQL Data Warehouse e ao Banco de Dados SQL do Azure](../sql-database/sql-database-control-access.md)
- [Regras de firewall do SQL Data Warehouse e do Banco de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Criptografar dados em repouso
A [TDE (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption) é habilitada por padrão. A TDE criptografa de forma transparente os arquivos de log e de dados do SQL Server, do Banco de Dados SQL do Azure e do SQL Data Warehouse do Azure. A TDE protege contra o comprometimento de acesso direto aos arquivos ou aos backups dos arquivos. Isso permite que você criptografe os dados em repouso sem alterar os aplicativos existentes. A TDE deverá permanecer sempre habilitada. No entanto, isso não interromperá um invasor que utilizar o caminho de acesso normal. A TDE proporciona a capacidade de entrar em conformidade com muitas leis, regulamentações e diretrizes estabelecidas em vários setores.

O SQL do Azure gerencia problemas relacionados a chave para a TDE. Assim como com a TDE, devem ser tomados cuidados especiais locais a fim de garantir a capacidade de recuperação e ao mover bancos de dados. Em cenários mais sofisticados, as chaves podem ser explicitamente gerenciadas no Azure Key Vault por meio do gerenciamento extensível de chaves. Confira [Habilitar TDE no SQL Server usando EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Isso também permite o BYOK (Bring Your Own Key) por meio da capacidade BYOK do Azure Key Vault.

O SQL do Azure fornece a criptografia para colunas por meio do [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Isso permite o acesso somente de aplicativos autorizados às colunas confidenciais. O uso dessa variante de criptografia limita as consultas SQL às colunas criptografadas à valores com base em igualdade.

A criptografia do nível do aplicativo também deve ser usada para dados seletivos. Algumas vezes, as preocupações com a soberania de dados podem ser reduzidas com a criptografia de dados com uma chave que seja mantida no país correto. Isso impede que até uma transferência de dados acidental cause um problema, uma vez que é impossível descriptografar os dados sem a chave, supondo que um algoritmo forte seja usado (como AES 256).

Você pode usar precauções adicionais para ajudar a proteger o banco de dados, como a criação de um sistema seguro, a criptografia de ativos confidenciais e a criação de um firewall em torno de servidores de bancos de dados.

## <a name="next-steps"></a>Próximas etapas
Este artigo apresentou uma coleção de práticas recomendadas de segurança do Banco de Dados SQL e SQL Data Warehouse para proteger seus aplicativos Web e móveis de PaaS. Para saber mais sobre como proteger suas implantações de PaaS, confira:

- [Proteção de implantações de PaaS](security-paas-deployments.md)
- [Securing PaaS web and mobile applications using Azure App Services](security-paas-applications-using-app-services.md) (Proteção dos aplicativos Web e móveis de PaaS usando os Serviços de Aplicativos do Azure)
