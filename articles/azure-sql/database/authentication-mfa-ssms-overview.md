---
title: Usando a autenticação de Azure Active Directory multifator
description: O banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o Azure Synapse Analytics dão suporte a conexões do SQL Server Management Studio (SSMS) usando Active Directory autenticação universal.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/28/2020
tags: azure-synapse
ms.openlocfilehash: 9afad44bcf67478a81e75c17d0ff8ffc6d8c65aa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94841105"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Usando a autenticação de Azure Active Directory multifator
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

O banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o Azure Synapse Analytics dão suporte a conexões do [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) usando o *Azure Active Directory-universal com autenticação MFA* . Este artigo discute as diferenças entre as várias opções de autenticação e também as limitações associadas ao uso da autenticação universal no Azure Active Directory (AD do Azure) para SQL do Azure.

**Baixar a última versão do SSMS** - No computador cliente, baixe a última versão do SSMS em [Baixar o SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]


Para todos os recursos neste artigo, use a versão 17.2 de julho de 2017 ou posterior. A caixa de diálogo de conexão mais recente deve ser semelhante à seguinte imagem:

  ![Captura de tela da caixa de diálogo conectar ao servidor no SQL Server Management Studio, mostrando as configurações de tipo de servidor, nome do servidor e autenticação.](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png)

## <a name="authentication-options"></a>Opções de autenticação

Há dois modelos de autenticação não interativa para o Azure AD, que podem ser usados em vários aplicativos diferentes (ADO.NET, JDCB, ODC e assim por diante). Esses dois métodos nunca resultam em caixas de diálogo pop-up:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

O método interativo que também dá suporte à MFA (autenticação multifator) do Azure AD é: 

- `Azure Active Directory - Universal with MFA`

A MFA do Azure AD ajuda a proteger o acesso a dados e aplicativos, ao mesmo tempo em que atende à demanda dos usuários por um processo de entrada simples. Ele fornece autenticação eficiente com uma variedade de opções de verificação fáceis, como chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação por aplicativos móveis, os quais permitem que os usuários escolham seu método de preferência. O MFA interativo com o Azure AD pode resultar em uma caixa de diálogo pop-up para validação.

Para obter uma descrição da autenticação multifator do Azure AD, consulte [autenticação multifator](../../active-directory/authentication/concept-mfa-howitworks.md).
Para etapas de configuração, consulte [Configurar Autenticação Multifator do Banco de Dados SQL do Azure para o SQL Server Management Studio](authentication-mfa-ssms-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Parâmetro de ID do locatário ou nome de domínio do Azure AD

A partir do [SSMS versão 17](/sql/ssms/download-sql-server-management-studio-ssms), os usuários que são importados para o Azure ad atual de outros diretórios ativos do Azure como usuários convidados podem fornecer o nome de domínio do Azure ad ou a ID de locatário quando eles se conectam. Usuários convidados incluem usuários convidados de outros Azure ADs e contas Microsoft como outlook.com, hotmail.com, live.com ou outras contas como gmail.com. Essas informações permitem que `Azure Active Directory - Universal with MFA` a autenticação identifique a autoridade de autenticação correta. Essa opção também é necessária para dar suporte a contas da Microsoft (MSA), como outlook.com, hotmail.com, live.com ou contas que não são MSA. 

Todos os usuários convidados que desejam ser autenticados usando a autenticação universal devem inserir seu nome de domínio ou ID de locatário do Azure AD. Esse parâmetro representa o nome de domínio ou ID de locatário do Azure AD atual ao qual o servidor lógico do Azure SQL está associado. Por exemplo, se o servidor lógico do SQL estiver associado ao domínio do Azure AD `contosotest.onmicrosoft.com` , em que `joe@contosodev.onmicrosoft.com` o usuário é hospedado como um usuário importado do domínio do Azure ad `contosodev.onmicrosoft.com` , o nome de domínio necessário para autenticar esse usuário é `contosotest.onmicrosoft.com` . Quando o usuário é um usuário nativo do Azure AD associado ao servidor lógico do SQL e não é uma conta do MSA, nenhum nome de domínio ou ID de locatário é necessário. Para inserir o parâmetro (começando com o SSMS versão 17,2):


1. Abra uma conexão no SSMS. Insira o nome do servidor e selecione **Azure Active Directory-universal com autenticação MFA** . Adicione o **nome de usuário** com o qual você deseja entrar.
1. Selecione a caixa **Opções** e vá para a guia **Propriedades da conexão** . Na caixa de diálogo **conectar ao banco de dados** , preencha a caixa de diálogo do seu banco de dados. Marque a caixa **ID de locatário ou nome de domínio do AD** e forneça a autoridade de autenticação, como o nome de domínio (**contosotest.onmicrosoft.com**) ou o GUID da ID do locatário. 

   ![Captura de tela da guia Propriedades da conexão realçando as configurações para conectar ao banco de dados e ao nome de domínio do AD ou à ID do locatário.](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Se você estiver executando o SSMS 18. x ou posterior, o nome de domínio do AD ou a ID do locatário não será mais necessário para usuários convidados, pois 18. x ou posterior o reconhecerá automaticamente.

   ![Captura de tela da guia Propriedades da conexão na caixa de diálogo conectar ao servidor em s s M. "MyDatabase" está selecionado no campo conectar ao banco de dados.](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Suporte entre empresas do Azure AD

> [!IMPORTANT]
> O suporte para usuários convidados para se conectar ao banco de dados SQL do Azure, o SQL Instância Gerenciada e o Azure Synapse sem a necessidade de fazer parte de um grupo está atualmente em **Visualização pública**. Para obter mais informações, consulte [criar usuários convidados do Azure AD e definir como um administrador do Azure ad](authentication-aad-guest-users.md).

Os usuários do Azure AD com suporte para cenários B2B do Azure AD como usuários convidados (consulte [o que é a colaboração do Azure B2B](../../active-directory/external-identities/what-is-b2b.md)) podem se conectar ao banco de dados SQL e ao Azure Synapse somente como parte dos membros de um grupo criado no Azure ad associado e mapeados manualmente usando a instrução [Create User (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) em um determinado banco de dados. Por exemplo, se `steve@gmail.com` o for convidado para o Azure ad `contosotest` (com o domínio do Azure ad `contosotest.onmicrosoft.com` ), um grupo do Azure AD, como `usergroup` deve ser criado no Azure AD que contém o `steve@gmail.com` membro. Em seguida, esse grupo deve ser criado para um banco de dados específico (por exemplo, `MyDatabase` ) por um administrador do SQL do Azure ad ou um dbo do Azure AD, executando a instrução Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . 

Depois que o usuário do banco de dados for criado, o usuário `steve@gmail.com` poderá entrar `MyDatabase` usando a opção de autenticação do SSMS `Azure Active Directory – Universal with MFA` . Por padrão, o `usergroup` tem apenas a permissão Connect. Qualquer acesso a dados adicional precisará ser [concedido](/sql/t-sql/statements/grant-transact-sql) no banco de dado por um usuário com privilégio suficiente. 

> [!NOTE]
> Para o SSMS 17. x, usando `steve@gmail.com` como um usuário convidado, você deve verificar a caixa **nome de domínio do AD ou ID de locatário** e adicionar o nome de domínio do AD `contosotest.onmicrosoft.com` na caixa de diálogo **propriedade de conexão** . A opção de **ID de locatário ou nome de domínio do AD** só tem suporte para o **Azure Active Directory-universal com autenticação MFA** . Caso contrário, a caixa de seleção ficará esmaecida.

## <a name="universal-authentication-limitations"></a>Limitações de autenticação universal

- O SSMS e o SqlPackage.exe são as únicas ferramentas atualmente habilitadas para MFA por meio da Autenticação Universal do Active Directory.
- O SSMS versão 17,2 dá suporte ao acesso simultâneo de vários usuários usando a autenticação universal com MFA. Para o SSMS versão 17,0 e 17,1, a ferramenta restringe um logon para uma instância do SSMS usando a autenticação universal para uma única conta de Azure Active Directory. Para entrar como outra conta do Azure AD, você deve usar outra instância do SSMS. Essa restrição é limitada a Active Directory autenticação universal; Você pode entrar em um servidor diferente usando `Azure Active Directory - Password` autenticação, `Azure Active Directory - Integrated` autenticação ou `SQL Server Authentication` .
- O SSMS dá suporte à Autenticação Universal do Active Directory para a visualização do Pesquisador de Objetos, do Editor de Consultas e do Repositório de Consultas.
- O SSMS versão 17.2 fornece suporte ao Assistente de DacFx para Eportação/Extração/Implantação de Dados de banco de dados. Depois que um usuário específico é autenticado por meio da caixa de diálogo de autenticação inicial usando a Autenticação Universal, o Assistente de DacFx funciona da mesma maneira que faz para todos os outros métodos de autenticação.
- O Designer de Tabela de SSMS não dá suporte à Autenticação Universal.
- Não há nenhum requisito de software adicional para a Autenticação Universal do Active Directory, exceto que você deve usar uma versão do SSMS com suporte.  
- Consulte o link a seguir para obter a versão mais recente do Biblioteca de Autenticação do Active Directory (ADAL) para autenticação universal: [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Próximas etapas

- Para etapas de configuração, consulte [Configurar Autenticação Multifator do Banco de Dados SQL do Azure para o SQL Server Management Studio](authentication-mfa-ssms-configure.md).
- Conceder acesso a outros a seu banco de dados: [Autenticação e Autorização do Banco de Dados SQL: Concessão de Acesso](logins-create-manage.md)  
- Verifique se outras pessoas podem se conectar por meio do firewall: [Configurar uma regra de firewall no nível de servidor usando o portal do Azure](firewall-configure.md)  
- [Configurar e gerenciar a autenticação Azure Active Directory com o banco de dados SQL ou o Azure Synapse](authentication-aad-configure.md)
- [Criar usuários convidados do Azure AD e definir como um administrador do Azure AD](authentication-aad-guest-users.md) 
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](/sql/tools/sqlpackage)  
- [Importar um arquivo BACPAC para um novo banco de dados](database-import.md)  
- [Exportar um banco de dados para um arquivo BACPAC](database-export.md)  
- Interface C# [Interface IUniversalAuthProvider](/dotnet/api/microsoft.sqlserver.dac.iuniversalauthprovider)  
- Ao usar o **Azure Active Directory-universal com** autenticação de MFA, o rastreamento de Adal está disponível a partir do [SSMS 17,3](/sql/ssms/download-sql-server-management-studio-ssms). Desativado por padrão, você pode ativar o rastreamento ADAL usando o menu **Ferramentas**, no menu **Opções**, em **Serviços do Azure**, **Nuvem do Azure**, **Nível de rastreamento de janela de saída ADAL** e, em seguida, habilitando **Saída** no menu **Exibição**. Os rastreamentos estão disponíveis na janela de saída ao selecionar a **opção do Active Directory do Azure**.