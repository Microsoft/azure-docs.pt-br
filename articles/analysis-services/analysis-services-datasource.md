---
title: Fontes de dados com suporte no Azure Analysis Services | Microsoft Docs
description: Descreve as fontes de fonte de dados e os conectores com suporte para modelos de dados tabulares nos níveis de compatibilidade 1200 e superior no Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 03f4d151fc948e4c060989a7d101ad91aecdecb6
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981486"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Fontes de dados com suporte no Azure Analysis Services

As fontes de dados e conectores mostrados no Assistente Obter Dados ou de Importação de Tabela no Visual Studio com os projetos do Analysis Services são mostrados para o Azure Analysis Services e para o SQL Server Analysis Services. No entanto, nem todas as fontes de dados e conectores mostrados têm suporte no Azure Analysis Services. Os tipos de fontes de dados que você pode conectar dependem de vários fatores, como nível de compatibilidade do modelo, conectores de dados disponíveis, tipo de autenticação e suporte para gateway de dados local. As tabelas a seguir descrevem as fontes de dados com suporte para Azure Analysis Services.

## <a name="azure-data-sources"></a>Fontes de dados do Azure

|Fonte de dados  |Na memória  |DirectQuery  |Observações |
|---------|---------|---------|---------|
|Banco de Dados SQL do Azure      |   Sim      |    Sim      |<sup>[2](#azprovider)</sup>, <sup>[3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL DW)      |   Sim      |   Sim       |<sup>[2](#azprovider)</sup>|
|Armazenamento do Blobs do Azure      |   Sim       |    Não      | <sup>[1](#tab1400a)</sup> |
|Armazenamento de Tabelas do Azure     |   Sim       |    Não      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Sim        |  Não        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Storage Gen1      |   Sim       |    Não      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Storage Gen2       |   Sim       |    Não      |<sup>[1](#tab1400a)</sup>, <sup>[5](#gen2)</sup>|
|Azure HDInsight HDFS    |     Sim     |   Não       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Sim       |   Não       |<sup>[1](#tab1400a)</sup>, <sup>[4](#databricks)</sup>|
||||

**Observações:**

<a name="tab1400a">1</a> - Tabular 1.400 e modelos superiores somente.  
<a name="azprovider">2</a> -quando especificado como uma fonte de dados de *provedor* nos modelos tabulares 1200 e superior, os modelos na memória e DirectQuery exigem o Microsoft OLE DB driver para SQL Server MSOLEDBSQL (recomendado) ou .NET Framework provedor de dados para SQL Server.  
<a name="azsqlmanaged">3</a> -há suporte para o Azure SQL instância gerenciada. Como o SQL Instância Gerenciada é executado na VNet do Azure com um endereço IP privado, o ponto de extremidade público deve ser habilitado na instância. Caso não esteja habilitado, é necessário um [Gateway de dados local](analysis-services-gateway.md).  
<a name="databricks">4</a> - No momento, não há suporte para uso do conector do Spark pelo Azure Databricks.  
<a name="gen2">5</a> - No momento, não há suporte para o conector ADLS Gen2, no entanto, o conector de Armazenamento de Blobs do Azure pode ser usado com uma fonte de dados do ADLS Gen2.

## <a name="other-data-sources"></a>Outras fontes de dados

|Fonte de dados | Na memória | DirectQuery |Observações   |
|  --- | --- | --- | --- |
|Banco de dados do Access     |  Sim | Não |  |
|Active Directory     |  Sim | Não | <sup>[6](#tab1400b)</sup>  |
|Serviços de análise     |  Sim | Não |  |
|Sistema de plataforma de análise     |  Sim | Não |  |
|Arquivo CSV  |Sim | Não |  |
|Dynamics 365     |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Pasta de trabalho do Excel     |  Sim | Não |  |
|Exchange      |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Pasta      |Sim | Não | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Sim | Não |  |
|Documento JSON      |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Linhas de binário      | Sim | Não | <sup>[6](#tab1400b)</sup> |
|Banco de dados MySQL     | Sim | Não |  |
|Feed OData      |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Consulta ODBC     | Sim | Não |  |
|OLE DB     |   Sim | Não |  |
|Oracle  | Sim  |Sim  | <sup>[9](#oracle)</sup> |
|Banco de dados PostgreSQL   | Sim | Não | <sup>[6](#tab1400b)</sup> |
|Objetos do Salesforce|  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Relatórios do Salesforce |Sim | Não | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  Sim | Não |  |
|SAP Business Warehouse    |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Lista do SharePoint      |   Sim | Não | <sup>[6](#tab1400b)</sup>, <sup>[11](#filesSP)</sup> |
|SQL Server |Sim   | Sim  | <sup>[7](#sqlim)</sup>, <sup>[8](#instgw)</sup> |
|SQL Server Data Warehouse |Sim   | Sim  | <sup>[7](#sqlim)</sup>, <sup>[8](#instgw)</sup> |
|Banco de dados Sybase     |  Sim | Não |  |
|Teradata | Sim  | Sim  | <sup>[10](#teradata)</sup> |
|Arquivos TXT  |Sim | Não |  |
|Tabela XML    |  Sim | Não | <sup>[6](#tab1400b)</sup> |
| | | |

**Observações:**  
<a name="tab1400b">6</a> - Tabular 1400 e modelos superiores somente.  
<a name="sqlim">7</a> - Quando especificado como uma fonte de dados do *provedor* em modelos tabulares nos níveis de compatibilidade 1200 e superiores, especifique o Driver do Microsoft OLE DB para SQL Server MSOLEDBSQL (recomendado), SQL Server Native Client 11.0 ou Provedor de Dados .NET Framework para SQL Server.  
<a name="instgw">8</a> - Se MSOLEDBSQL for especificado como o provedor de dados, será necessário baixar e instalar o [Driver do Microsoft OLE DB para SQL Server](/sql/connect/oledb/oledb-driver-for-sql-server) no mesmo computador que o gateway de dados local.  
<a name="oracle">9</a> - Para modelos tabulares de nível de compatibilidade 1200, ou como uma fonte de dados do *provedor* nos modelos tabulares de nível de compatibilidade 1400 e superiores, especifique o Provedor de Dados Oracle para .NET. Se especificado como uma fonte de dados estruturada, certifique-se de [habilitar o provedor gerenciado Oracle](#enable-oracle-managed-provider).   
<a name="teradata">10</a> - Para modelos tabulares de nível de compatibilidade 1200, ou como uma fonte de dados do *provedor* nos modelos tabulares de nível de compatibilidade 1400 e superiores, especifique o Provedor de Dados Teradata para .NET.  
<a name="filesSP">11</a> - Arquivos no SharePoint local não são suportados.

Conectar a fontes de dados locais e ao servidor do Azure Analysis Services requer um [Gateway local](analysis-services-gateway.md). Ao usar um gateway, serão necessários provedores de 64 bits.

## <a name="understanding-providers"></a>Como funcionam os provedores

Durante a criação de projetos de modelo tabular de níveis de compatibilidade 1400 e superiores no Visual Studio, por padrão, não é necessário especificar um provedor de dados ao se conectar a uma fonte de dados usando **Obter Dados**. Os modelos tabulares de níveis de compatibilidade 1400 e superiores usam conectores do [Power Query](/power-query/power-query-what-is-power-query) para gerenciar conexões, consultas de dados e mashups entre a fonte de dados e o Analysis Services. Elas são chamadas também de conexões de fonte de dados *estruturadas*, já que as configurações das propriedades da conexão são definidas para você. No entanto, você pode habilitar fontes de dados herdadas para um projeto de modelo no Visual Studio. Quando habilitado, você pode usar o **Assistente de Importação de Tabela** para se conectar a determinadas fontes de dados que normalmente são suportadas nos modelos tabulares de nível de compatibilidade 1200 e inferiores, como fontes de dados *herdadas* ou de *provedor*. Quando especificada como uma fonte de dados de provedor, você pode especificar um provedor de dados específico e outras propriedades avançadas de conexão. Por exemplo, você pode se conectar a uma instância do SQL Server data warehouse ou mesmo a um banco de dados SQL do Azure como uma fonte de dado herdada. Em seguida, você pode selecionar o provedor de dados MSOLEDBSQL do Driver do OLE DB para SQL Server. Nesse caso, a seleção de um provedor de dados OLE DB pode oferecer um melhor desempenho para o conector do Power Query. 

Quando o Assistente de Importação de Tabela no Visual Studio é usado, as conexões com qualquer fonte de dados exigem um provedor de dados. Um provedor de dados padrão é selecionado para você. Você pode alterar o provedor de dados, se necessário. O tipo de provedor escolhido pode depender do desempenho, não importando se o modelo está ou não usando armazenamento na memória ou DirectQuery, e da plataforma do Analysis Services onde o modelo será implantado.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Especificação de fontes de dados do provedor em projetos de modelo tabular de níveis de compatibilidade 1400 e superiores

Para habilitar fontes de dados de provedor, no Visual Studio, clique em **Ferramentas** > **Opções** > **Analysis Services Tabular** > **Importação de Dados**, selecione **Habilitar fontes de dados herdadas**.

![Habilitar fontes de dados herdadas](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Após habilitar as fontes de dados herdadas, no **Gerenciador de Modelos Tabulares**, clique com o botão direito do mouse em **Fontes de Dados** > **Importar de Fonte de Dados (Herdada)** .

![Fontes de dados herdadas no Gerenciador de Modelos Tabulares](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Assim como nos projetos de modelo tabular de nível de compatibilidade 1200, use o **Assistente de Importação de Tabela** para se conectar a uma fonte de dados. Na página conectar, clique em **Avançado**. Especifique o provedor de dados e outras configurações de conexão em **Definir Propriedades Avançadas**.

![Propriedades avançadas de fontes de dados herdadas](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>Representação
Em alguns casos, pode ser necessário especificar uma conta de representação diferente. A conta de representação pode ser especificada no Visual Studio ou no SQL Server Management Studio (SSMS).

Para fontes de dados locais:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.
* Se estiver usando a autenticação do Windows, defina o usuário/senha do Windows. Para o SQL Server, há suporte para a autenticação do Windows com uma conta de representação específica apenas para modelos de dados na memória.

Para fontes de dados de nuvem:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.


## <a name="oauth-credentials"></a>Credenciais OAuth

Para modelos de tabela no nível de compatibilidade 1400 e superior usando o modo *na memória* , o banco de dados SQL do Azure, o Azure Synapse, o Dynamics 365 e a lista do SharePoint dão suporte a credenciais OAuth. Para gerar tokens válidos, defina as credenciais usando Power Query. O Azure Analysis Services gerencia a atualização de token para fontes de dados OAuth para evitar tempos limite para operações de atualização de execução de longa duração. 
> [!NOTE]
> A atualização de token gerenciada não tem suporte para fontes de dados acessadas por meio de um gateway. Por exemplo, uma ou mais fontes de dados de consulta de mashup são acessadas por meio de um gateway e/ou a propriedade [ASPaaS\AlwaysUseGateway](analysis-services-vnet-gateway.md) é definida como **true**. 

Não há suporte para o modo Direct Query com credenciais OAuth.

## <a name="enable-oracle-managed-provider"></a>Habilitar o provedor gerenciado da Oracle

Em alguns casos, as consultas DAX a uma fonte de dados Oracle podem retornar resultados inesperados. Isso pode ser devido ao provedor que está sendo usado para a conexão de fonte de dados.

Conforme descrito na seção [noções básicas de provedores](#understanding-providers) , os modelos de tabela se conectam a fontes de dados como uma fonte de dados *estruturada* ou uma fonte de dados de *provedor* . Para modelos com uma fonte de dados Oracle especificada como uma fonte de dados de provedor, verifique se o provedor especificado é Oracle Provedor de Dados para .NET (Oracle. DataAccess. Client). 

Se a fonte de dados Oracle for especificada como uma fonte de dados estruturada, habilite a propriedade de servidor **MDataEngine\UseManagedOracleProvider** . Definir essa propriedade garante que seu modelo se conecte à fonte de dados Oracle usando o provedor gerenciado do Oracle Provedor de Dados for .NET recomendado.
 
Para habilitar o provedor gerenciado da Oracle:

1. No SQL Server Management Studio, conecte-se ao servidor.
2. Crie uma consulta XMLA com o script a seguir. Substitua **ServerName** pelo nome completo do servidor e, em seguida, execute a consulta.

    ```xml
    <Alter AllowCreate="true" ObjectExpansion="ObjectProperties" xmlns="http://schemas.microsoft.com/analysisservices/2003/engine">
        <Object />
        <ObjectDefinition>
            <Server xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ddl2="http://schemas.microsoft.com/analysisservices/2003/engine/2" xmlns:ddl2_2="http://schemas.microsoft.com/analysisservices/2003/engine/2/2" 
    xmlns:ddl100_100="http://schemas.microsoft.com/analysisservices/2008/engine/100/100" xmlns:ddl200="http://schemas.microsoft.com/analysisservices/2010/engine/200" xmlns:ddl200_200="http://schemas.microsoft.com/analysisservices/2010/engine/200/200" 
    xmlns:ddl300="http://schemas.microsoft.com/analysisservices/2011/engine/300" xmlns:ddl300_300="http://schemas.microsoft.com/analysisservices/2011/engine/300/300" xmlns:ddl400="http://schemas.microsoft.com/analysisservices/2012/engine/400" 
    xmlns:ddl400_400="http://schemas.microsoft.com/analysisservices/2012/engine/400/400" xmlns:ddl500="http://schemas.microsoft.com/analysisservices/2013/engine/500" xmlns:ddl500_500="http://schemas.microsoft.com/analysisservices/2013/engine/500/500">
                <ID>ServerName</ID>
                <Name>ServerName</Name>
                <ServerProperties>
                    <ServerProperty>
                        <Name>MDataEngine\UseManagedOracleProvider</Name>
                        <Value>1</Value>
                    </ServerProperty>
                </ServerProperties>
            </Server>
        </ObjectDefinition>
    </Alter>
    ```

3. Reinicie o servidor.


## <a name="next-steps"></a>Próximas etapas

* [Gateway Local](analysis-services-gateway.md)
* [Gerenciar seu serviço](analysis-services-manage.md)
