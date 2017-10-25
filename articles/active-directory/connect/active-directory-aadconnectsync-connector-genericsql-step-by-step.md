---
title: "Passo a passo do Conector SQL Genérico | Microsoft Docs"
description: "Este artigo explicará um passo a passo simples do sistema de RH usando o Conector do SQL Genérico."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 28c1cc60-24fd-4d0d-a36d-b4aba6de86e7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 3fdc1b405b95180d031aa4ad45b406f7fc149d8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="generic-sql-connector-step-by-step"></a>Passo a passo do Conector do SQL Genérico
Este tópico é um guia passo a passo. Ele cria um banco de dados de RH de exemplo simples e o usará para a importação de alguns usuários e sua associação a um grupo.

## <a name="prepare-the-sample-database"></a>Preparar o banco de dados de exemplo
Em um servidor que executa o SQL Server, execute o script SQL encontrado no [Apêndice A](#appendix-a). Esse script cria um banco de dados de exemplo com o nome GSQLDEMO. O modelo de objeto para o banco de dados criado é semelhante a esta imagem:   
![Modelo de objeto](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/objectmodel.png)

Também crie um usuário que deseja usar para se conectar ao banco de dados. Neste passo a passo, o usuário é chamado FABRIKAM\SQLUser e está localizado no domínio.

## <a name="create-the-odbc-connection-file"></a>Criar o arquivo de conexão ODBC
O Conector do SQL Genérico está usando o ODBC para se conectar ao servidor remoto. Primeiro, precisamos criar um arquivo com as informações de conexão ODBC.

1. Inicie o utilitário de gerenciamento ODBC no servidor:   
   ![ODBC](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc.png)
2. Selecione a guia **DSN de Arquivo**. Clique em **Adicionar...**.  
   ![ODBC1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc1.png)
3. O driver pronto para uso funciona bem, então, selecione-o e clique em **Avançar>**.  
   ![ODBC2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc2.png)
4. Nomeie o arquivo, como **GenericSQL**.  
   ![ODBC3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc3.png)
5. Clique em **Concluir**.  
   ![ODBC4](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc4.png)
6. Hora de configurar a conexão. Forneça uma boa descrição à fonte de dados e o nome do servidor que executa o SQL Server.  
   ![ODBC5](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc5.png)
7. Selecione como deseja se autenticar com o SQL. Nesse caso, usamos a Autenticação do Windows.  
   ![ODBC6](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc6.png)
8. Forneça o nome do banco de dados de exemplo, **GSQLDEMO**.  
   ![ODBC7](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc7.png)
9. Mantenha todas as opções como padrão nesta tela. Clique em **Concluir**.  
   ![ODBC8](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc8.png)
10. Para verificar se tudo está funcionando como esperado, clique em **Testar Fonte de Dados**.  
    ![ODBC9](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc9.png)
11. Verifique se o teste foi bem-sucedido.  
    ![ODBC10](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc10.png)
12. O arquivo de configuração ODBC agora deverá estar visível no DSN de Arquivo.  
    ![ODBC11](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc11.png)

Agora temos o arquivo necessário e podemos começar a criar o Conector.

## <a name="create-the-generic-sql-connector"></a>Criar o Conector do SQL Genérico
1. Na interface do usuário do Synchronization Service Manager, selecione **Conectores** e **Criar**. Selecione **SQL Genérico (Microsoft)** e dê a ele um nome descritivo.  
   ![Connector1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector1.png)
2. Encontre o arquivo DSN criado na seção anterior e o carregue no servidor. Forneça as credenciais para se conectar ao banco de dados.  
   ![Connector2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector2.png)
3. Neste passo a passo, estamos facilitando nosso trabalho dizendo que há dois tipos de objeto, **User** e **Group**.
   ![Connector3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector3.png)
4. Para encontrar os atributos, queremos que o Conector os detecte observando a própria tabela. Já que **Users** é uma palavra reservada no SQL, precisamos fornecê-la entre colchetes [ ].  
   ![Connector4](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector4.png)
5. Hora de definir o atributo de âncora e o atributo DN. Para **Users**, usamos a combinação dos dois atributos username e EmployeeID. Para **Group**, usamos GroupName (não realista na vida real, mas funcionará nesse passo a passo).
   ![Connector5](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector5.png)
6. Nem todos os tipos de atributo podem ser detectados em um banco de dados SQL. Em particular, o tipo de atributo de referência não tem essa capacidade. Para o tipo de objeto de grupo, precisamos alterar a OwnerID e a MemberID para fazer referência a elas.  
   ![Connector6](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector6.png)
7. Os atributos que selecionamos como referência na etapa anterior exigem o tipo de objeto para o qual esses valores são uma referência. Em nosso caso, o tipo de objeto de Usuário.  
   ![Connector7](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector7.png)
8. Na página Parâmetros Globais, selecione **Marca-d'água** como a estratégia delta. Digite também o formato de data/hora **aaaa-MM-dd HH:mm:ss**.
   ![Connector8](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector8.png)
9. Na página **Configurar Partições e Hierarquias**, escolha ambos os tipos de objeto.
   ![Connector9](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector9.png)
10. Em **Selecionar Tipos de Objeto** e **Selecionar Atributos**, selecione os dois tipos de objeto e todos os atributos. Na página **Configurar Âncoras**, clique em **Concluir**.

## <a name="create-run-profiles"></a>Criar perfis de execução
1. Na interface do usuário do Synchronization Service Manager, selecione **Conectores** e **Configurar Perfis de Execução**. Clique em **Novo Perfil**. Começamos com **Importação Completa**.  
   ![Runprofile1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile1.png)
2. Selecione o tipo **Importação Completa (Somente Estágio)**.  
   ![Runprofile2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile2.png)
3. Selecione a partição **OBJECT=User**.  
   ![Runprofile3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile3.png)
4. Selecione **Tabela** e digite **[USERS]**. Role para baixo até a seção de tipo de objeto com vários valores e insira os dados como na imagem a seguir. Selecione **Concluir** para salvar a etapa.  
   ![Runprofile4a](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile4a.png)  
   ![Runprofile4b](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile4b.png)  
5. Selecione **Nova Etapa**. Desta vez, selecione **OBJECT=Group**. Na última página, use a configuração como na imagem a seguir. Clique em **Concluir**.  
   ![Runprofile5a](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile5a.png)  
   ![Runprofile5b](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile5b.png)  
6. Opcional: se quiser, você poderá configurar perfis de execução adicionais. Para este passo a passo, foi usada apenas a Importação Completa.
7. Clique em **OK** para concluir a alteração de perfis de execução.

## <a name="add-some-test-data-and-test-the-import"></a>Adicionar alguns dados de teste e testar a importação
Preencha alguns dados de teste no banco de dados de exemplo. Quando estiver pronto, selecione **Executar** e **Importação completa**.

Aqui está um usuário com dois números de telefone e um grupo com alguns membros.  
![cs1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/cs1.png)  
![cs2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/cs2.png)  

## <a name="appendix-a"></a>Apêndice A
**Script SQL para criar o banco de dados de exemplo**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
    [MemberID] [int] NOT NULL,
    [Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
    [GroupID] [int] NOT NULL,
    [GROUPNAME] [nvarchar](200) NOT NULL,
    [DESCRIPTION] [nvarchar](200) NULL,
    [WATERMARK] [datetime] NULL,
    [OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
    [GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
    [USER_ID] [int] NULL,
    [Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
    [USERID] [int] NOT NULL,
    [USERNAME] [nvarchar](200) NOT NULL,
    [FirstName] [nvarchar](100) NULL,
    [LastName] [nvarchar](100) NULL,
    [DisplayName] [nvarchar](100) NULL,
    [ACCOUNTDISABLED] [bit] NULL,
    [EMPLOYEEID] [int] NOT NULL,
    [WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
    [USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```
