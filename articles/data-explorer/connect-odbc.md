---
title: Conectar-se ao Gerenciador de dados do Azure com o ODBC
description: Neste artigo, você aprenderá como configurar uma conexão ODBC Open Database Connectivity () para o Data Explorer do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 65795b5b4dea8d2cdeecf5f78f9de751f275dac0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537598"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Conectar-se ao Gerenciador de dados do Azure com o ODBC

Abrir a conectividade de banco de dados ([ODBC](/sql/odbc/reference/odbc-overview)) é uma interface de programação de aplicativo amplamente aceito (API) para acesso ao banco de dados. Use ODBC para se conectar ao Data Explorer do Azure em aplicativos que não têm um conector dedicado.

Nos bastidores, aplicativos chamam funções na interface do ODBC, que são implementadas em módulos específicos de banco de dados chamados *drivers*. O Gerenciador de dados do Azure dá suporte a um subconjunto do protocolo de comunicação do SQL Server ([MS-TDS](/azure/kusto/api/tds/)), portanto, ele pode usar o driver ODBC para SQL Server.

Usando o vídeo a seguir, você pode aprender a criar uma conexão ODBC. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Como alternativa, você pode [configurar a fonte de dados ODBC](#configure-the-odbc-data-source) conforme detalhado abaixo. 

Neste artigo, você aprenderá como usar o driver ODBC do SQL Server, portanto, você pode se conectar ao Data Explorer do Azure de qualquer aplicativo que oferece suporte ao ODBC. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte:

* [Microsoft ODBC Driver para SQL Server versão 17.2.0.1 ou posterior](/sql/connect/odbc/download-odbc-driver-for-sql-server) para seu sistema operacional.

## <a name="configure-the-odbc-data-source"></a>Configurar a fonte de dados ODBC

Siga estas etapas para configurar uma fonte de dados ODBC usando o driver ODBC para SQL Server.

1. No Windows, pesquise *fontes de dados ODBC*e abra o aplicativo de área de trabalho de fontes de dados ODBC.

1. Selecione **Adicionar**.

    ![Adicionar fonte de dados](media/connect-odbc/add-data-source.png)

1. Selecione **ODBC Driver 17 for SQL Server** , em seguida, **concluir**.

    ![Selecione o driver](media/connect-odbc/select-driver.png)

1. Insira um nome e uma descrição para a conexão e o cluster que você deseja se conectar, em seguida, selecione **próxima**. O cluster de URL deve estar no formato  *\<ClusterName\>.\< Região\>. kusto.windows.net*.

    ![Selecione o servidor](media/connect-odbc/select-server.png)

1. Selecione **integrada do Active Directory** , em seguida, **próxima**.

    ![Integrada ao Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Selecione o banco de dados com os dados de exemplo, em seguida, **próxima**.

    ![Alterar banco de dados padrão](media/connect-odbc/change-default-database.png)

1. Na próxima tela, deixe todas as opções como padrões em seguida, selecione **concluir**.

1. Selecione **testar fonte de dados**.

    ![Fonte de dados de teste](media/connect-odbc/test-data-source.png)

1. Verifique se que o teste foi bem-sucedido em seguida, selecione **Okey**. Se o teste não obteve êxito, verifique os valores que você especificou nas etapas anteriores e verifique se que você tem permissões suficientes para conectar-se ao cluster.

    ![Teste foi bem-sucedido](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Próximas etapas

* [Conectar-se ao Gerenciador de dados do Azure da Tableau](tableau.md)