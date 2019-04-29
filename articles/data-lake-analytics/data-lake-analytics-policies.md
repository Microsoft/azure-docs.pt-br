---
title: Gerenciar políticas do Azure Data Lake Analytics
description: Saiba como usar políticas para controlar o uso de uma conta do Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 64095f6706bb978cd33b8fe7833fe4e65fc3b0f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813417"
---
# <a name="manage-azure-data-lake-analytics-using-policies"></a>Gerenciar o Azure Data Lake Analytics usando políticas

Usando políticas da conta, você pode controlar como os recursos de uma conta do Azure Data Lake Analytics são usados. Essas políticas permitem que você controle o custo do uso do Azure Data Lake Analytics. Por exemplo, com essas políticas você evita picos inesperados de custo limitando quantas AUs a conta pode usar simultaneamente.

## <a name="account-level-policies"></a>Políticas no nível da conta

Essas políticas aplicam-se a todos os trabalhos em uma conta do Data Lake Analytics.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Número máximo de AUs em uma conta do Data Lake Analytics
Uma política controla o número total de AUs (Unidades de Análise) que a conta do Data Lake Analytics pode usar. Por padrão, o valor é definido como 250. Por exemplo, se esse valor for definido como 250 AUs, você poderá ter um trabalho em execução com 250 AUs atribuídas ou 10 trabalhos em execução com 25 AUs cada um. Os trabalhos adicionais enviados são enfileirados até que os trabalhos em execução sejam concluídos. Quando os trabalhos em execução são concluídos, as AUs são liberadas para que os trabalhos na fila sejam executados.

Para alterar o número de AUs da sua conta do Data Lake Analytics:

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **Máximo de AUs**, mova o controle deslizante para selecionar um valor ou insira o valor na caixa de texto. 
4. Clique em **Salvar**.

> [!NOTE]
> Se você precisar de mais do que as AUs padrão (250), no portal, clique em **Ajuda + Suporte** para enviar uma solicitação de suporte. O número de AUs disponíveis em sua conta do Data Lake Analytics pode ser aumentado.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Número máximo de trabalhos que podem ser executados simultaneamente
Uma política controla quantos trabalhos podem ser executados ao mesmo tempo. Por padrão, esse valor é definido como 20. Se o Data Lake Analytics tiver AUs disponíveis, novos trabalhos serão agendados para execução imediata até que o número total de trabalhos em execução atinja o valor dessa política. Quando você alcançar o número máximo de trabalhos que podem ser executados simultaneamente, os próximos trabalhos serão enfileirados em ordem de prioridade até que um ou mais trabalhos em execução sejam concluídos (dependendo da disponibilidade de AU).

Para alterar o número de trabalhos que podem ser executadas simultaneamente:

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **Número Máximo de Trabalhos em Execução**, mova o controle deslizante para selecionar um valor ou insira o valor na caixa de texto. 
4. Clique em **Salvar**.

> [!NOTE]
> Se você precisar executar mais do que o número de trabalhos padrão (20) no portal, clique em **Ajuda + Suporte** para enviar uma solicitação de suporte. O número de trabalhos que podem ser executados simultaneamente em sua conta do Data Lake Analytics pode ser aumentado.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Por quanto tempo os metadados e recursos de trabalho devem ser mantidos 
Quando os usuários executam trabalhos de U-SQL, o serviço do Data Lake Analytics mantém todos os arquivos relacionados. Os arquivos relacionados incluem o script U-SQL, os arquivos DLL referenciados no script U-SQL, os recursos compilados e as estatísticas. Os arquivos estão na pasta /system/ da conta de armazenamento padrão do Azure Data Lake. Esta política controla por quanto tempo esses recursos serão armazenados antes de serem excluídos automaticamente (o padrão é 30 dias). Você pode usar esses arquivos para depuração e ajuste de desempenho de trabalhos que serão executados novamente no futuro.

Para alterar o tempo para reter os recursos e metadados de trabalho:

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **Dias para Reter Consultas de Trabalho**, mova o controle deslizante para selecionar um valor ou insira o valor na caixa de texto.  
4. Clique em **Salvar**.

## <a name="job-level-policies"></a>Políticas no nível do trabalho

Com as políticas no nível do trabalho, você pode controlar o máximo de AUs e a prioridade máxima que podem ser definidos por usuários individuais (ou membros de grupos de segurança específicos) nos trabalhos que eles enviam. Essa política lhe permite controlar os custos gerados pelos usuários. Ele também permite que você controle o efeito que os trabalhos agendados podem ter nos trabalhos de produção de alta prioridade que estão em execução na mesma conta do Data Lake Analytics.

O Data Lake Analytics tem duas políticas que podem ser definidas no nível do trabalho:

* **Limite de AU por trabalho**: Os usuários apenas podem enviar trabalhos que tenham até esse número de AUs. Por padrão, esse limite é o mesmo que o limite máximo de AUs da conta.
* **Prioridade**: Os usuários só podem enviar trabalhos que têm uma prioridade menor do que ou igual a esse valor. Um número maior indica uma prioridade mais baixa. Por padrão, esse limite é definido como 1, que é a prioridade mais alta possível.

Há uma política padrão definida em cada conta. A política padrão aplica-se a todos os usuários da conta. Você pode definir políticas adicionais para usuários e grupos específicos. 

> [!NOTE]
> As políticas no nível da conta e no nível do trabalho aplicam-se ao mesmo tempo.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Adicionar uma política para um usuário ou grupo específico

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **Limites de Envio de Trabalho**, clique no botão **Adicionar Política**. Em seguida, selecione ou insira as seguintes configurações:
    1. **Nome da política de computação**: Insira um nome de política para lembrá-lo da finalidade da política.
    2. **Selecionar usuário ou grupo**: Selecione o usuário ou grupo que esta política é aplicável.
    3. **Definir o limite de AU do trabalho**: Defina o limite de AU aplica-se para o usuário ou grupo selecionado.
    4. **Definir o limite de prioridade**: Defina o limite de prioridade que se aplica para o usuário ou grupo selecionado.

4. Clique em **OK**.

5. A nova política é listada na tabela de política **Padrão**, em **Limites de Envio de Trabalho**. 

### <a name="delete-or-edit-an-existing-policy"></a>Excluir ou editar uma política existente

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **Limites de Envio de Trabalho**, localize a política que deseja editar.
4.  Para ver as opções **Excluir** e **Editar**, na coluna mais à direita da tabela, clique em `...`.

## <a name="additional-resources-for-job-policies"></a>Recursos adicionais para políticas de trabalho
* [Postagem no blog de visão geral de política](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Postagem no blog de políticas no nível da conta](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Postagem no blog de políticas no nível do trabalho](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerenciar o Azure Data Lake Analytics usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

