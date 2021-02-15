---
title: Conectar-se aos servidores dos Azure Analysis Services | Microsoft Docs
description: Saiba como se conectar e obter dados de um servidor do Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 4abe1e9c6f9d7b62792936f816b9c46a937be41a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499417"
---
# <a name="connecting-to-servers"></a>Conectando-se a servidores

Este artigo descreve como se conectar a um servidor usando aplicativos de modelagem e gerenciamento de dados como o SQL Server Management Studio (SSMS) ou o Visual Studio com projetos Analysis Services ou com aplicativos de relatório de cliente como o Microsoft Excel, Power BI Desktop ou aplicativos personalizados. Conexões ao Azure Analysis Services usam HTTPS.

## <a name="client-libraries"></a>Bibliotecas de cliente

[Obter as bibliotecas de Cliente mais recentes](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)

Todas as conexões a um servidor, independentemente do tipo, exigem bibliotecas de cliente OLEDB, ADOMD.NET e AMO atualizadas para a conexão e interface com um servidor do Analysis Services. Para SSMS, Visual Studio, Excel 2016 e posterior e Power BI, as bibliotecas de cliente mais recentes são instaladas ou atualizadas com versões mensais. No entanto, em alguns casos, é possível que um aplicativo não tenha a mais recente. Por exemplo, quando as políticas atrasam atualizações ou Microsoft 365 atualizações estão no canal adiado.

> [!NOTE]
> As bibliotecas de cliente não podem se conectar a Azure Analysis Services por meio de servidores proxy que exigem um nome de usuário e senha. 

## <a name="server-name"></a>Nome do servidor

Quando você cria um servidor do Azure Analysis Services, é necessário especificar um nome exclusivo e a região na qual o servidor será criado. Ao especificar o nome do servidor em uma conexão, o esquema de nomenclatura do servidor será:

```
<protocol>://<region>/<servername>
```
 Quando o protocolo for a cadeia de caracteres **asazure**, a região será o URI de onde o servidor foi criado (por exemplo, westus.asazure.windows.net) e servername será o nome do seu servidor exclusivo dentro da região.

### <a name="get-the-server-name"></a>Obter o nome do servidor

No **Portal do Azure** > servidor > **Visão geral** > **Nome do servidor**, copie todo o nome do servidor. Se outros usuários em sua organização também forem se conectar a esse servidor, compartilhe o nome do servidor com eles. Ao especificar um nome de servidor, todo o caminho deve ser usado.

![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> O protocolo para a região leste dos EUA 2 é **aspaaseastus2**.

## <a name="connection-string"></a>Cadeia de conexão

Ao conectar-se ao Azure Analysis Services usando o Modelo de objeto de tabela, use os seguintes formatos de cadeia de conexão:

###### <a name="integrated-azure-active-directory-authentication"></a>Autenticação integrada do Azure Active Directory

A autenticação integrada seleciona o cache de credencial do Azure Active Directory, se estiver disponível. Caso contrário, a janela de logon do Azure será exibida.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Autenticação do Azure Active Directory com nome de usuário e senha

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Autenticação do Windows (segurança integrada)

Use a conta do Windows executando o processo atual.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Conectar usando um arquivo .odc

Com versões mais antigas do Excel, os usuários podem se conectar a um servidor do Azure Analysis Service usando um arquivo Office Data Connectionn (.odc). Para obter mais informações, consulte [Criar um arquivo Office Data Connection (.odc)](analysis-services-odc.md).

## <a name="connect-as-a-linked-server-from-sql-server"></a>Conectar-se como um servidor vinculado do SQL Server

SQL Server pode se conectar a um recurso de Azure Analysis Services como um [servidor vinculado](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine) ESPECIFICANDO o MSOLAP como o provedor de fonte de dados. Antes de configurar uma conexão de servidor vinculado, certifique-se de instalar a [biblioteca de cliente MSOLAP](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) mais recente (provedor). 

Para conexões de servidor vinculadas a Azure Analysis Services, o provedor MSOLAP deve ser instanciado fora do processo de SQL Server. Ao configurar opções de servidor vinculado, verifique se a opção **permitir inprocessar** **não está selecionada**.

Se **permitir inprocess** estiver selecionado e o provedor for instanciado no processo de SQL Server, o seguinte erro será retornado:

```
OLE DB provider "MSOLAP" for linked server "(null)" returned message "The following system error occurred: ".

OLE DB provider "MSOLAP" for linked server "(null)" returned message "The connection failed because user credentials are needed and Sign-In UI is not allowed.".

Msg 7303, Level 16, State 1, Line 2
Cannot initialize the data source object of OLE DB provider "MSOLAP" for linked server "(null)".
```



## <a name="next-steps"></a>Próximas etapas

[Conectar-se ao Excel](analysis-services-connect-excel.md)    
[Conectar-se com Power BI](analysis-services-connect-pbi.md)   
[Gerenciar seu serviço](analysis-services-manage.md)