---
title: Obter a cadeia de conexão no portal do Azure
description: Obter a cadeia de conexão no portal do Azure
keywords: conexão sql, cadeia de conexão
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60202067"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Obter a cadeia de conexão no portal do Azure
Obtenha no [portal do Azure](https://portal.azure.com/) a cadeia de conexão que é necessária para o seu programa cliente interagir com o Banco de Dados SQL do Azure.

1. Selecione **Todos os serviços** > **Bancos de Dados SQL**.

2. Insira o nome do banco de dados na caixa de texto de filtro próximo ao canto superior esquerdo da folha dos **bancos de dados SQL**.

3. Selecione a linha do banco de dados.

4. Depois que a folha aparecer para o banco de dados, para conveniência visual, selecione os botões de **Minimização** para recolher as folhas que você usou para navegar e filtrar o banco de dados.

5. Na folha do banco de dados, selecione **Mostrar cadeias de conexão de banco de dados**.

6. Copie a cadeia de conexão apropriada. Ou seja, se pretende utilizar a biblioteca de conexões ADO.NET, copie a cadeia de caracteres apropriada da guia **ADO.NET**.

    ![Copie a cadeia de conexão do ADO do banco de dados][20-CopyAdoConnectionString]

7. Edite a cadeia de conexão, conforme necessário. Ou seja, insira a senha na cadeia de conexão ou remova "@&lt;nome do servidor&gt;" do nome de usuário, se o nome de usuário ou do servidor for muito longo.

8. Em um formato ou outro, cole as informações da cadeia de conexão no código do programa cliente.

Para obter mais informações, consulte [Cadeias de conexão e arquivos de configuração](https://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
