---
title: "Fontes de dados com suporte no Catálogo de Dados do Azure | Microsoft Docs"
description: "Este artigo lista especificações das fontes de dados com suporte no momento."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstevens
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: d6867c73bc6ea3c238cceef8a68466d451f3365c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="supported-data-sources-in-azure-data-catalog"></a>Fontes de dados com suporte no Catálogo de Dados do Azure

Você pode publicar os metadados usando uma API pública, uma ferramenta de registro com um clique ou inserindo manualmente as informações de forma direta no portal da Web do Catálogo de Dados do Azure. A tabela a seguir resume todas as fontes de dados com suporte hoje pelo catálogo e os recursos de publicação para cada uma. Também estão listadas as ferramentas de dados externos que cada fonte de dados pode inicializar na nossa experiência "aberta" do portal. A segunda tabela contém uma especificação mais técnica de cada propriedade de conexão das fontes de dados.


## <a name="list-of-supported-data-sources"></a>Lista das fontes de dados com suporte

<table>
    <tr>
       <td><b>Objeto da fonte de dados</b></td>
       <td><b>API</b></td>
       <td><b>Entrada manual</b></td>
       <td><b>Ferramenta de registro</b></td>
       <td><b>Ferramentas abertas</b></td>
       <td><b>Observações</b></td>
    </tr>
    <tr>
      <td>Diretório do Azure Data Lake Store</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Arquivo do Azure Data Lake Store</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Armazenamento de Blobs do Azure</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Diretório de Armazenamento do Azure</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela do Armazenamento do Azure</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>
    <tr>
      <td>Diretório do HDFS</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Arquivo do HDFS</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela do Hive</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Exibição de Hive</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela do MySQL</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Exibição do MySQL</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela do Oracle Database</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Exibição do Oracle Database</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Outros (ativo genérico)</td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela do SQL Data Warehouse do Azure</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel, Power BI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Exibição do SQL Data Warehouse</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel, Power BI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Dimensão do SQL Server Analysis Services</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>KPI do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Medida do SQL Server Analysis Services</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Relatório do SQL Server Reporting Services</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Navegador</font></td>
      <td><font size=2>Somente os servidores de modo nativo. Não há suporte para o modo do SharePoint.</font></td>
    </tr>
    <tr>
      <td>Tabela do SQL Server</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel, Power BI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Exibição do SQL Server</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel, Power BI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela do Teradata</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Exibição do Teradata</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Exibição do SAP HANA</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela do DB2</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Exibição do DB2</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Arquivo do sistema de arquivos</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Diretório do FTP</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Arquivo do FTP</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Relatório HTTP</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Ponto de extremidade HTTP</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Arquivo HTTP</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Conjunto de entidades do OData</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Função do OData</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela do PostgreSQL</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Exibição do PostgreSQL</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Exibição do SAP HANA</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td> Objeto do SalesForce</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Lista do SharePoint </td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Coleção do Azure Cosmos DB</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela ODBC Genérica</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Exibição ODBC Genérica</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela do Cassandra</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2>Publicar como um ativo ODBC genérico</font></td>
    </tr>
    <tr>
      <td>Exibição do Cassandra</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2>Publicar como um ativo ODBC genérico</font></td>
    </tr>
    <tr>
      <td>Tabela do Sybase</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Exibição do Sybase</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabela do MongoDB</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2>Publicar como um ativo ODBC genérico</font></td>
    </tr>
    <tr>
      <td>Exibição do MongoDB</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2>Publicar como um ativo ODBC genérico</font></td>
    </tr>
</table>

Se você precisar de suporte para outras fontes, envie uma solicitação de recurso para o [Fórum do Catálogo de Dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


## <a name="data-source-reference-specification"></a>Especificação de referência da fonte de dados
> [!NOTE]
> A coluna **Estrutura de DSL** na tabela a seguir lista apenas as propriedades de conexão do recipiente de propriedades "address" que são usadas pelo Catálogo de Dados do Azure. Ou seja, o recipiente de propriedades "address" pode conter outras propriedades de conexão da fonte de dados que o Catálogo de Dados do Azure persiste, mas não usa.

<table>
    <tr>
       <td><b>Tipo de fonte</b></td>
       <td><b>Tipo de ativo</b></td>
       <td><b>Tipos de objeto</b></td>
       <td><b>Estrutura de DSL<b></td>
    </tr>
    <tr>
      <td>Repositório Azure Data Lake</td>
      <td>Contêiner</td>
      <td>Data Lake</td>
      <td>
        <font size=2> Protocolo: webhdfs <br>Autenticação: {básica, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Repositório Azure Data Lake</td>
      <td>Tabela</td>
      <td>Diretório, arquivo</td>
      <td>
        <font size=2> Protocolo: webhdfs <br>Autenticação: {básica, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento do Azure</td>
      <td>Contêiner</td>
      <td>Contêiner</td>
      <td>
        <font size=2> Protocolo: azure-blobs <br>Autenticação: {azure-access-key} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; contêiner </font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento do Azure</td>
      <td>Tabela</td>
      <td>Blob, diretório</td>
      <td>
        <font size=2> Protocolo: azure-blobs <br>Autenticação: {azure-access-key} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; contêiner <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; nome </font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento do Azure</td>
      <td>Contêiner</td>
      <td>Contêiner</td>
      <td>
        <font size=2> Protocolo: azure-tables <br>Autenticação: {azure-access-key} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; conta </font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento do Azure</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>
        <font size=2> Protocolo: azure-tables <br>Autenticação: {azure-access-key} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; nome </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Contêiner</td>
      <td>Cluster virtual</td>
      <td>
        <font size=2> Protocolo: cosmos <br>Autenticação: {básica, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Tabela</td>
      <td>Fluxo, conjunto de fluxos, exibição</td>
      <td>
        <font size=2> Protocolo: cosmos <br>Autenticação: {básica, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Contêiner</td>
      <td>Site</td>
      <td>
        <font size=2> Protocolo: http <br>Autenticação: {nenhuma, básica, windows, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Relatório</td>
      <td>Relatório, painel</td>
      <td>
        <font size=2> Protocolo: http <br>Autenticação: {nenhuma, básica, windows, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> Protocolo: db2 <br>Autenticação: {básica, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> Protocolo: db2 <br>Autenticação: {básica, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema </font>
      </td>
    </tr>
    <tr>
      <td>Sistema de arquivos</td>
      <td>Tabela</td>
      <td>Arquivo</td>
      <td>
        <font size=2> Protocolo: file <br>Autenticação: {nenhuma, básica, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; caminho </font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Tabela</td>
      <td>Diretório, arquivo</td>
      <td>
        <font size=2> Protocolo: ftp <br>Autenticação: {nenhuma, básica, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Sistema de Arquivos Distribuído Hadoop</td>
      <td>Contêiner</td>
      <td>HDInsight</td>
      <td>
        <font size=2> Protocolo: webhdfs <br>Autenticação: {básica, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Sistema de Arquivos Distribuído Hadoop</td>
      <td>Tabela</td>
      <td>Diretório, arquivo</td>
      <td>
        <font size=2> Protocolo: webhdfs <br>Autenticação: {básica, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> Protocolo: hive <br>Autenticação: {HDInsight, básica, nome de usuário, nenhuma} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> Protocolo: hive <br>Autenticação: {HDInsight, básica, nome de usuário, nenhuma} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Contêiner</td>
      <td>Site</td>
      <td>
        <font size=2> Protocolo: http <br>Autenticação: {nenhuma, básica, windows, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Relatório</td>
      <td>Relatório, painel</td>
      <td>
        <font size=2> Protocolo: http <br>Autenticação: {nenhuma, básica, windows, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Tabela</td>
      <td>Ponto de extremidade, arquivo</td>
      <td>
        <font size=2> Protocolo: http <br>Autenticação: {nenhuma, básica, windows, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> Protocolo: mysql <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> Protocolo: mysql <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Contêiner</td>
      <td>Contêiner de entidade</td>
      <td>
        <font size=2> Protocolo: odata <br>Autenticação: {nenhuma, básica, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Tabela</td>
      <td>Conjunto de entidades, função</td>
      <td>
        <font size=2> Protocolo: odata <br>Autenticação: {nenhuma, básica, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; recurso </font>
      </td>
    </tr>
    <tr>
      <td>Banco de dados Oracle</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> Protocolo: oracle <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>Banco de dados Oracle</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> Protocolo: oracle <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> Protocolo: postgresql <br>Autenticação: {básica, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> Protocolo: postgresql <br>Autenticação: {básica, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Contêiner</td>
      <td>Site</td>
      <td>
        <font size=2> Protocolo: http <br>Autenticação: {nenhuma, básica, windows, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Relatório</td>
      <td>Relatório, painel</td>
      <td>
        <font size=2> Protocolo: http <br>Autenticação: {nenhuma, básica, windows, oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Tabela</td>
      <td>Mashup de dados</td>
      <td>
        <font size=2> Protocolo: power-query <br>Autenticação: {oauth} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Tabela</td>
      <td>Objeto</td>
      <td>
        <font size=2> Protocolo: salesforce-com <br>Autenticação: {básica, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; classe <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; itemName </font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Contêiner</td>
      <td>Servidor</td>
      <td>
        <font size=2> Protocolo: sap-hana-sql <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor </font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Tabela</td>
      <td>Visualizar</td>
      <td>
        <font size=2> Protocolo: sap-hana-sql <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Tabela</td>
      <td>Listar</td>
      <td>
        <font size=2> Protocolo: sharepoint-list <br>Autenticação: {básica, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Command</td>
      <td>Procedimento armazenado</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>Função com valor de tabela</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Command</td>
      <td>Procedimento armazenado</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Função com valor de tabela</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Multidimensional do SQL Server Analysis Services</td>
      <td>Contêiner</td>
      <td>Modelo</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticação: {windows, básica, anônima, nenhuma} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo </font>
      </td>
    </tr>
    <tr>
      <td>Multidimensional do SQL Server Analysis Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticação: {windows, básica, anônima, nenhuma} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>Multidimensional do SQL Server Analysis Services</td>
      <td>Medida</td>
      <td>Medida</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticação: {windows, básica, anônima, nenhuma} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Medida} </font>
      </td>
    </tr>
    <tr>
      <td>Multidimensional do SQL Server Analysis Services</td>
      <td>Tabela</td>
      <td>Dimensão</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticação: {windows, básica, anônima, nenhuma} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Dimensão} </font>
      </td>
    </tr>
    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>Contêiner</td>
      <td>Modelo</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticação: {windows, básica, anônima, nenhuma} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo </font>
      </td>
    </tr>
    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticação: {windows, básica, anônima, nenhuma} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>Medida</td>
      <td>Medida</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticação: {windows, básica, anônima, nenhuma} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Medida} </font>
      </td>
    </tr>
    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticação: {windows, básica, anônima, nenhuma} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Tabela} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Contêiner</td>
      <td>Servidor</td>
      <td>
        <font size=2> Protocolo: reporting-services <br>Autenticação: {windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; versão: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Relatório</td>
      <td>Relatório</td>
      <td>
        <font size=2> Protocolo: reporting-services <br>Autenticação: {windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; caminho <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; versão: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> Protocolo: teradata <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> Protocolo: teradata <br>Autenticação: {protocolo, windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Contêiner</td>
      <td>Modelo</td>
      <td>
        <font size="2"> Protocolo: mssql-mds <br>Autenticação: {windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; versão </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Tabela</td>
      <td>Entidade</td>
      <td>
        <font size="2"> Protocolo: mssql-mds <br>Autenticação: {windows} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; versão <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; entidade </font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> Protocolo: document-db <br>Autenticação: {azure-access-key} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Coleção</td>
      <td>Coleção</td>
      <td>
        <font size=2> Protocolo: document-db <br>Autenticação: {azure-access-key} <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>Coleção &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </font>
      </td>
    </tr>
    <tr>
      <td>ODBC Genérico</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> Protocolo: odbc <br>Autenticação: {básica, windows} <br>Endereço: <br>Opções &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>ODBC Genérico</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> Protocolo: odbc <br>Autenticação: {básica, windows} <br>Endereço: <br>Opções &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema </font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> Protocolo: sybase <br>autenticação: {básica, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> Protocolo: sybase <br>autenticação: {básica, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Outro (nenhum dos anteriores)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2> Protocolo: generic-asset <br>Endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId </font>
      </td>
    </tr>
</table>
