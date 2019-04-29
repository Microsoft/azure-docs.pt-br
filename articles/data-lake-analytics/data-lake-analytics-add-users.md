---
title: Adicionar usuários a uma conta do Azure Data Lake Analytics
description: Saiba como adicionar usuários à conta do Data Lake Analytics de forma correta
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 8323c4e1b236444f55dab826d2567491f5f0f736
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629315"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Adicionar um usuário ao portal do Azure

## <a name="start-the-add-user-wizard"></a>Inicie o Assistente Adicionar Usuário
1. Abra o Azure Data Lake Analytics por meio do https://portal.azure.com.
2. Clique no **Assistente Adicionar Usuário**.
3. Na etapa **Selecionar usuário**, encontre o usuário que deseja adicionar. Clique em **Selecionar**.
4. Na etapa **Selecionar função**, escolha **Desenvolvedor do Data Lake Analytics**. Essa função tem o conjunto mínimo de permissões necessárias para enviar/monitorar/gerenciar trabalhos de U-SQL. Atribua a essa função se o grupo não for destinado a gerenciar os serviços do Azure.
5. Na etapa **Selecionar permissões de catálogo**, selecione os bancos de dados adicionais que o usuário precisará acessar. É necessário ter acesso de leitura e gravação no banco de dados mestre para enviar trabalhos. Quando terminar, clique em **OK**.
6. Na etapa final, **Atribuir permissões selecionadas**, examine as alterações que o assistente fará. Clique em **OK**.


## <a name="configure-acls-for-data-folders"></a>Configurar ACLs para pastas de dados
Conceda "R-X" ou "RWX", conforme necessário, nas pastas que contêm dados de entrada e saída.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Opcionalmente, adicione o usuário à função **Leitor** da função de Armazenamento do Microsoft Data Lake Gen1.
1.  Localize sua conta do Azure Data Lake armazenamento Gen1.
2.  Clique em **Usuários**.
3. Clique em **Adicionar**.
4.  Selecione uma função de RBAC do Azure para atribuir esse grupo.
5.  Atribua à função Leitor. Essa função tem o conjunto mínimo de permissões necessárias para procurar/gerenciar dados armazenados em ADLSGen1. Atribua a essa função se o grupo não for destinado a gerenciar os serviços do Azure.
6.  Digite o nome do grupo.
7.  Clique em **OK**.

## <a name="adding-a-user-using-powershell"></a>Adicionar um usuário usando o PowerShell

1. Siga as instruções neste guia: [Como instalar e configurar o Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Baixe o script do PowerShell [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1).
3. Execute o script do PowerShell. 

O comando de exemplo para dar acesso ao usuário para enviar trabalhos, exibir novos metadados de trabalho e exibir metadados antigos é:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Próximas etapas

* [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerenciar o Azure Data Lake Analytics usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

