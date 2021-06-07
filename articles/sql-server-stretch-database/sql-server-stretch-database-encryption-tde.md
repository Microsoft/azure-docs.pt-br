---
title: Habilitar Transparent Data Encryption para Stretch Database
description: Habilitar TDE (Transparent Data Encryption) para SQL Server Stretch Database no Azure
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b016987162cc8202b7ad28d4dd8e5ab2953469d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95024238"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Habilitar TDE (Transparent Data Encryption) para Stretch Database no Azure
> [!div class="op_single_selector"]
> * [Azure portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

O TDE (Transparent Data Encryption) ajuda a proteger contra ameaças de atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem exigir mudanças no aplicativo.

A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados. A chave de criptografia do banco de dados está protegida por um certificado do servidor interno. O certificado do servidor interno é exclusivo para cada servidor do Azure. A Microsoft gira automaticamente esses certificados pelo menos a cada 90 dias. Para obter uma descrição geral do TDE, consulte [TDE (Transparent Data Encryption)].

## <a name="enabling-encryption"></a>Habilitando a criptografia
Para habilitar a TDE para um banco de dados do Azure que armazena os dados migrados de um banco de dados do SQL Server habilitado para ampliação, faça o seguinte:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na folha do banco de dados, clique no botão **Configurações**
3. Selecione a opção de **Transparent Data Encryption** ![ captura de portal do Azure, com a folha configurações visíveis. Na seção geral, a Transparent Data Encryption é realçada.][1]
4. Selecione a configuração **ativado** e, em seguida, selecione **salvar** 
    ![ captura de tela da portal do Azure, com a folha Transparent Data Encryption visível. A criptografia de dados está ativada e o botão salvar está realçado.][2]

## <a name="disabling-encryption"></a>Desabilitando a criptografia
Para desabilitar a TDE para um banco de dados do Azure que armazena os dados migrados de um banco de dados do SQL Server habilitado para ampliação, faça o seguinte:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na folha do banco de dados, clique no botão **Configurações**
3. Selecione a opção **Transparent Data Encryption**
4. Selecione a configuração **Desativar** e, em seguida, selecione **Salvar**

<!--Anchors-->
[TDE (Transparent Data Encryption)]: /sql/relational-databases/security/encryption/transparent-data-encryption


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->