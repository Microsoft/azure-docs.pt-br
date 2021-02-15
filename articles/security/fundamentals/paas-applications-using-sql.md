---
title: Protegendo bancos de dados de PaaS no Azure | Microsoft Docs
description: 'Saiba mais sobre as práticas recomendadas de segurança do banco de dados SQL do Azure e do Azure Synapse Analytics para proteger seus aplicativos móveis e Web de PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 278812754c636d434bf579c0408832f1e99d3445
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408066"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Melhores práticas para proteger bancos de dados de PaaS no Azure

Neste artigo, discutiremos uma coleção de práticas recomendadas de segurança do [banco de dados SQL do Azure](../../azure-sql/database/sql-database-paas-overview.md) e do [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) para proteger seus aplicativos móveis e Web de PaaS (plataforma como serviço). Essas práticas recomendadas derivam da nossa experiência com o Azure e da experiência de clientes como você.

O banco de dados SQL do Azure e o Azure Synapse Analytics fornecem um serviço de banco de dados relacional para seus aplicativos baseados na Internet. Vamos dar uma olhada nos serviços que ajudam a proteger seus aplicativos e dados ao usar o Azure SQL Database e o Azure Synapse Analytics em uma implantação de PaaS:

- Autenticação do Azure Active Directory (em vez da autenticação do SQL Server)
- Firewall do SQL do Azure
- Criptografia de Dados Transparente (TDE)

## <a name="use-a-centralized-identity-repository"></a>Usar um repositório de identidades centralizado

O banco de dados SQL do Azure pode ser configurado para usar um dos dois tipos de autenticação:

- A **autenticação do SQL** usa um nome de usuário e senha. Quando você criou o servidor para seu banco de dados, você especificou um logon de "administrador do servidor" com um nome de usuário e senha. Usando essas credenciais, é possível se autenticar em qualquer banco de dados nesse servidor como o proprietário do banco de dados.

- A **autenticação do Azure Active Directory** usa identidades gerenciadas pelo Azure Active Directory e tem suporte para domínios gerenciados e integrados. Para usar a Autenticação do Azure Active Directory, você deve criar outro administrador de servidor chamado “admin do Azure AD”, que tenha permissão para administrar usuários e grupos do Azure AD. Este administrador também pode executar todas as operações executadas por um administrador de servidor comum.

[Azure Active Directory autenticação](../../active-directory/develop/authentication-vs-authorization.md) é um mecanismo de conexão ao banco de dados SQL do Azure e ao Azure Synapse Analytics usando identidades no Azure Active Directory (AD). O Azure AD fornece uma alternativa para a autenticação do SQL Server para que você possa parar a proliferação de identidades de usuário entre os servidores de banco de dados. A autenticação do Azure AD permite que você gerencie centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento central de IDs fornece um único local para gerenciar os usuários do banco de dados e simplifica o gerenciamento de permissões.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Benefícios de usar o Azure AD em vez da autenticação SQL

- Permite o rodízio de senhas em um único lugar.
- Gerencia as permissões de banco de dados usando grupos externos do Azure AD.
- Elimina o armazenamento de senhas permitindo a autenticação integrada do Windows e outras formas de autenticação com suporte pelo Azure AD.
- Usa usuários de banco de dados independente para autenticar identidades no nível de banco de dados.
- Dá suporte à autenticação baseada em token em aplicativos que se conectam ao Banco de Dados SQL.
- Suporte à federação de domínio com os ADFS (Serviços de Federação do Active Directory) ou à autenticação nativa de senha/usuário para um local do Azure AD sem a sincronização de domínio.
- Dá suporte a conexões do SQL Server Management Studio que usam a Autenticação Universal do Active Directory, que inclui o [MFA (Autenticação Multifator)](../../active-directory/authentication/concept-mfa-howitworks.md). A MFA inclui autenticação eficiente com uma variedade de opções de verificação fáceis como chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação por aplicativos móveis. Para obter mais informações, consulte [autenticação universal com o banco de dados SQL e o Azure Synapse Analytics](../../azure-sql/database/authentication-mfa-ssms-overview.md).

Para saber mais sobre a autenticação do Azure AD, consulte:

- [Usar a autenticação Azure Active Directory para autenticação com Banco de dados SQL, Instância Gerenciada ou Azure Synapse Analytics](../../azure-sql/database/authentication-aad-overview.md)
- [Autenticação para o Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Suporte à autenticação baseada em token para o banco de dados SQL do Azure usando a autenticação do Azure AD](../../azure-sql/database/authentication-aad-overview.md)

> [!NOTE]
> Para garantir que o Azure Active Directory seja uma boa opção para seu ambiente, confira [Limitações e recursos do Azure AD](../../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

## <a name="restrict-access-based-on-ip-address"></a>Restringir o acesso com base no endereço IP

Você pode criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Essas regras podem ser direcionadas nos níveis do servidor e do banco de dados. É recomendável o uso de regras de firewall no nível do banco de dados sempre que possível, a fim de tornar seu banco de dados mais portátil. As regras de firewall no nível do servidor são melhor usadas para administradores e quando você tem muitos bancos de dados com os mesmos requisitos de acesso, mas não quer gastar tempo configurando cada um individualmente.

As restrições de endereço IP de origem do Banco de Dados SQL permitem o acesso de qualquer endereço do Azure, incluindo outras assinaturas e locatários. Você pode restringir isso para permitir apenas que seus endereços IP acessem a instância. Mesmo com as restrições de endereço IP e firewall do SQL, a autenticação forte ainda é necessária. Consulte as recomendações feitas anteriormente neste artigo.

Para saber mais sobre as restrições de IP e o Firewall do SQL do Azure, consulte:

- [Banco de dados SQL do Azure e controle de acesso do Azure Synapse Analytics](../../azure-sql/database/logins-create-manage.md)
- [Regras de firewall do banco de dados SQL do Azure e do Azure Synapse Analytics](../../azure-sql/database/firewall-configure.md)

## <a name="encrypt-data-at-rest"></a>Criptografar dados em repouso

A [TDE (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption) é habilitada por padrão. O TDE criptografa de forma transparente SQL Server, banco de dados SQL do Azure e arquivos de log e de logs do Synapse do Azure. A TDE protege contra o comprometimento de acesso direto aos arquivos ou aos backups dos arquivos. Isso permite que você criptografe os dados em repouso sem alterar os aplicativos existentes. A TDE deverá permanecer sempre habilitada. No entanto, isso não interromperá um invasor que utilizar o caminho de acesso normal. A TDE proporciona a capacidade de entrar em conformidade com muitas leis, regulamentações e diretrizes estabelecidas em vários setores.

O SQL do Azure gerencia problemas relacionados a chave para a TDE. Assim como com a TDE, devem ser tomados cuidados especiais locais a fim de garantir a capacidade de recuperação e ao mover bancos de dados. Em cenários mais sofisticados, as chaves podem ser explicitamente gerenciadas no Azure Key Vault por meio do gerenciamento extensível de chaves. Confira [Habilitar TDE no SQL Server usando EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Isso também permite o BYOK (Bring Your Own Key) por meio da capacidade BYOK do Azure Key Vault.

O SQL do Azure fornece a criptografia para colunas por meio do [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Isso permite o acesso somente de aplicativos autorizados às colunas confidenciais. O uso dessa variante de criptografia limita as consultas SQL às colunas criptografadas à valores com base em igualdade.

A criptografia do nível do aplicativo também deve ser usada para dados seletivos. Às vezes, as preocupações com a soberania de dados podem ser atenuadas com a criptografia de dados com uma chave que é mantida no país/região correto. Isso impede que até uma transferência de dados acidental cause um problema, uma vez que é impossível descriptografar os dados sem a chave, supondo que um algoritmo forte seja usado (como AES 256).

Você pode usar precauções adicionais para ajudar a proteger o banco de dados, como a criação de um sistema seguro, a criptografia de ativos confidenciais e a criação de um firewall em torno de servidores de bancos de dados.

## <a name="next-steps"></a>Próximas etapas

Este artigo apresentou a você uma coleção de práticas recomendadas de segurança do banco de dados SQL e do Azure Synapse Analytics para proteger seus aplicativos móveis e Web de PaaS. Para saber mais sobre como proteger suas implantações de PaaS, confira:

- [Proteção de implantações de PaaS](paas-deployments.md)
- [Securing PaaS web and mobile applications using Azure App Services](paas-applications-using-app-services.md) (Proteção dos aplicativos Web e móveis de PaaS usando os Serviços de Aplicativos do Azure)