---
title: Fontes de dados com suporte no Azure Analysis Services | Microsoft Docs
description: Descreve as fontes de fonte de dados com suporte para modelos de dados no Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: 8bd6c3b5a923ce2f3cd0f60af82e59c5cc27cbb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Fontes de dados com suporte no Azure Analysis Services
Os servidores do Azure Analysis Services oferecem suporte à conexão às fontes de dados na nuvem e locais na sua organização. O tempo todo são adicionadas mais fontes de dados com suporte. Verifique com frequência. 

Atualmente, há suporte às seguintes fontes de dados:

| Nuvem  |
|---|
| Armazenamento de Blobs do Azure*  |
| Banco de Dados SQL do Azure  |
| Data Warehouse do Azure |


| Configuração local  |   |   |   |
|---|---|---|---|
| Banco de Dados do Access  | Pasta* | Banco de dados Oracle  | Banco de dados Teradata |
| Active Directory*  | Documento JSON*  | Banco de Dados SQL Postgre*  |Tabela XML* |
| Serviços de análise  | Linhas de binário*  | SAP HANA*  |
| Analytics Platform System  | Banco de Dados MySQL  | SAP Business Warehouse*  | |
| Dynamics CRM*  | Feed OData*  | SharePoint*  |
| Pasta de trabalho do Excel  | Consulta ODBC  | Banco de Dados SQL  |
| Exchange*  | OLE DB  | Banco de dados Sybase  |

\*Somente modelos Tabular 1400. 

> [!IMPORTANT]
> A conexão com fontes de dados locais exige um [gateway de dados local](analysis-services-gateway.md) instalado em um computador em seu ambiente.

## <a name="data-providers"></a>Provedores de dados

Os modelos de dados no Azure Analysis Services podem exigir diferentes provedores de dados durante a conexão com certas fontes de dados. Em alguns casos, modelos de tabela que se conectam a fontes de dados usando provedores nativos, como o SQL Server Native Client (SQLNCLI11), podem retornar um erro.

Para modelos de dados que se conectam a uma fonte de dados de nuvem, como o Banco de Dados SQL do Azure, se você usar provedores nativos diferentes de SQLOLEDB, verá a mensagem de erro: **"O provedor 'SQLNCLI11.1' não está registrado"**. Ou, se você tiver um modelo DirectQuery que se conecta a fontes de dados locais, se você usar provedores nativos, verá a mensagem de erro: **"Erro ao criar o conjunto de linhas do OLE DB. Sintaxe incorreta próxima a 'LIMIT' "**.

Os provedores de fonte de dados a seguir têm suporte para modelos de dados na memória ou DirectQuery ao se conectar a fontes de dados da nuvem ou locais:

### <a name="cloud"></a>Nuvem
| **Fonte de dados** | **Na memória** | **DirectQuery** |
|  --- | --- | --- |
| SQL Data Warehouse do Azure |Provedor de Dados .NET Framework para SQL Server |Provedor de Dados .NET Framework para SQL Server |
| Banco de Dados SQL do Azure |Provedor de Dados .NET Framework para SQL Server |Provedor de Dados .NET Framework para SQL Server | |

### <a name="on-premises-via-gateway"></a>Local (via Gateway)
|**Fonte de dados** | **Na memória** | **DirectQuery** |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 |Provedor de Dados .NET Framework para SQL Server |
| SQL Server |Provedor Microsoft OLE DB para SQL Server |Provedor de Dados .NET Framework para SQL Server | |
| SQL Server |Provedor de Dados .NET Framework para SQL Server |Provedor de Dados .NET Framework para SQL Server | |
| Oracle |Provedor Microsoft OLE DB para Oracle |Provedor de Dados Oracle para .NET | |
| Oracle |Provedor de Dados Oracle para .NET |Provedor de Dados Oracle para .NET | |
| Teradata |Provedor OLE DB para Teradata |Provedor de Dados Teradata para .NET | |
| Teradata |Provedor de Dados Teradata para .NET |Provedor de Dados Teradata para .NET | |
| Analytics Platform System |Provedor de Dados .NET Framework para SQL Server |Provedor de Dados .NET Framework para SQL Server | |

> [!NOTE]
> Certifique-se de que os provedores de 64 bits estejam instalados ao usar o gateway Local.
> 
> 

Ao migrar um modelo de tabela local do SQL Server Analysis Services para o Azure Analysis Services, talvez seja necessário alterar o provedor.

**Para especificar um provedor de fonte de dados**

1. No SSDT > **Gerenciador de Modelo de Tabela** > **Fontes de Dados**, clique com o botão direito em uma conexão de fonte de dados e, em seguida, clique em **Editar Fonte de Dados**.
2. Em **Editar Conexão**, clique em **Avançado** para abrir a janela Propriedades avançadas.
3. Em **Definir Propriedades Avançadas** > **Provedores**, selecione o provedor apropriado.

## <a name="impersonation"></a>Representação
Em alguns casos, pode ser necessário especificar uma conta de representação diferente. A conta de representação pode ser especificada no SSDT ou o no SSMS.

Para fontes de dados locais:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.
* Se estiver usando a autenticação do Windows, defina o usuário/senha do Windows. Para o SQL Server, há suporte para a autenticação do Windows com uma conta de representação específica apenas para modelos de dados na memória.

Para fontes de dados de nuvem:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.

## <a name="next-steps"></a>Próximas etapas
Se você tiver fontes de dados locais, instale o [Gateway local](analysis-services-gateway.md).   
Para saber mais sobre como gerenciar seu servidor no SSDT ou SSMS, consulte [Gerenciar seu servidor](analysis-services-manage.md).

