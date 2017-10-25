---
title: Gerenciar o Azure Data Lake Analytics usando o portal do Azure | Microsoft Docs
description: "Saiba como gerenciar contas, fontes de dados, usuários e trabalhos da Análise Data Lake."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: e49d1a0e0ccc6567d0a6841817667717ff5dba76
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-data-lake-analytics-by-using-the-azure-portal"></a>Gerenciar o Azure Data Lake Analytics usando o portal do Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerenciar contas, fontes de dados da conta, usuários e trabalhos do Azure Data Lake Analytics usando o portal do Azure. Para ver os tópicos de gerenciamento sobre como usar outras ferramentas, clique na guia na parte superior da página.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Gerenciar contas do Data Lake Analytics

### <a name="create-an-account"></a>Criar uma conta

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Intelligence + análise** > **Data Lake Analytics**.
3. Selecione os valores para os seguintes itens: 
   1. **Nome**: o nome da conta do Data Lake Analytics.
   2. **Assinatura**: a assinatura do Azure usada para a conta.
   3. **Grupo de recursos**: o grupo de recursos do Azure no qual a conta será criada. 
   4. **Local**: o datacenter do Azure para a conta do Data Lake Analytics. 
   5. **Data Lake Store**: o repositório padrão a ser usado para a conta do Data Lake Analytics. A conta do Azure Data Lake Store e a conta do Data Lake Analytics devem estar no mesmo local.
4. Clique em **Criar**. 

### <a name="delete-a-data-lake-analytics-account"></a>Excluir uma conta do Data Lake Analytics

Antes de excluir uma conta do Data Lake Analytics, exclua sua conta padrão do Data Lake Store.

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Excluir**.
3. Digite o nome da conta.
4. Clique em **Excluir**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Gerenciar as fontes de dados

O Data Lake Analytics dá suporte às seguintes fontes de dados:

* Repositório Data Lake
* Armazenamento do Azure

Você pode usar o Data Explorer para procurar fontes de dados e executar operações básicas de gerenciamento de arquivo. 

### <a name="add-a-data-source"></a>Adicionar uma fonte de dados

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Fontes de Dados**.
3. Clique em **Adicionar Fonte de Dados**.
    
   * Para adicionar uma conta do Data Lake Store, são necessários o nome da conta e o acesso à conta para poder consultá-la.
   * Para adicionar o armazenamento de Blobs do Azure, são necessárias a conta de armazenamento e a chave de conta. Para encontrá-las, acesse a conta de armazenamento no portal.

## <a name="set-up-firewall-rules"></a>Configurar regras de firewall

Você pode usar o Data Lake Analytics para bloquear ainda mais o acesso à sua conta do Data Lake Analytics no nível da rede. Você pode habilitar um firewall e definir um endereço IP ou um intervalo de endereços IP para seus clientes confiáveis. Depois de habilitar essas medidas, somente os clientes que tiverem os endereços IP dentro do intervalo definido poderão se conectar ao repositório.

Se outros serviços do Azure, como o Azure Data Factory ou VMs, se conectam à conta do Data Lake Analytics, verifique se **Permitir Serviços do Azure** está **Ativado**. 

### <a name="set-up-a-firewall-rule"></a>Configurar uma regra de firewall

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. No menu à esquerda, clique em **Firewall**.

## <a name="add-a-new-user"></a>Adicione um novo usuário

Você pode usar o **Assistente para Adicionar Usuário** para provisionar facilmente novos usuários do Data Lake.

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. À esquerda, em **Introdução**, clique em **Assistente para Adicionar Usuário**.
3. Selecione um usuário e, em seguida, clique em **Selecionar**.
4. Selecione uma função e, em seguida, clique em **Selecionar**. Para configurar um novo desenvolvedor para usar o Azure Data Lake, selecione a função **Desenvolvedor do Data Lake Analytics**.
5. Selecione as ACLs (listas de controle de acesso) para os bancos de dados U-SQL. Quando estiver satisfeito com suas escolhas, clique em **Selecionar**.
6. Selecione as ACLs de arquivos. Para o repositório padrão, não altere as ACLs da pasta raiz "/" e da pasta /system. Clique em **Selecionar**.
7. Examine todas as alterações selecionadas e, em seguida, clique em **Executar**.
8. Quando o assistente for concluído, clique em **Concluído**.

## <a name="manage-role-based-access-control"></a>Gerenciar o controle de acesso baseado em função

Como outros serviços do Azure, você pode usar o RBAC (controle de acesso baseado em função) para controlar como os usuários interagem com o serviço.

As funções padrão do RBAC têm os seguintes recursos:
* **Proprietário**: pode enviar, monitorar e cancelar trabalhos de qualquer usuário e configurar a conta.
* **Colaborador**: pode enviar, monitorar e cancelar trabalhos de qualquer usuário e configurar a conta.
* **Leitor**: pode monitorar trabalhos.

Use a função de desenvolvedor do Data Lake Analytics para permitir que os desenvolvedores de U-SQL usem o serviço do Data Lake Analytics. Você pode usar a função de desenvolvedor do Data Lake Analytics:
* Enviar trabalhos.
* Monitorar o status do trabalho e o andamento dos trabalhos enviados por qualquer usuário.
* Consultar os scripts de U-SQL de trabalhos enviados por qualquer usuário.
* Cancelar apenas seus próprios trabalhos.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Adicionar usuários ou grupos de segurança a uma conta do Data Lake Analytics

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Controle de acesso (IAM)** > **Adicionar**.
3. Selecione uma função.
4. Adicione um usuário.
5. Clique em **OK**.

>[!NOTE]
>Se um usuário ou um grupo de segurança precisar enviar trabalhos, eles também precisarão de permissão na conta de repositório. Para obter mais informações, consulte [Proteger os dados armazenados no Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Gerenciar trabalhos

### <a name="submit-a-job"></a>Enviar um trabalho

1. No portal do Azure, acesse sua conta do Data Lake Analytics.

2. Clique em **Novo Trabalho**. Para cada trabalho, configure:

    1. **Nome do Trabalho**: o nome do trabalho.
    2. **Prioridade**: números menores têm prioridade mais alta. Se dois trabalhos estiverem enfileirados, aquele com o menor valor de prioridade será executado primeiro.
    3. **Paralelismo**: o número máximo de processos de computação a serem reservados para este trabalho.

3. Clique em **Enviar Trabalho**.

### <a name="monitor-jobs"></a>Monitorar trabalhos

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Exibir Todos os Trabalhos**. Uma lista de todos os trabalhos ativos e concluídos recentemente é exibida.
3. Opcionalmente, clique em **Filtrar** para ajudá-lo a localizar os trabalhos por valores de **Intervalo de Tempo**, **Nome do Trabalho** e **Autor**. 

### <a name="monitoring-pipeline-jobs"></a>Monitorando trabalhos de pipeline
Trabalhos que fazem parte de um pipeline trabalham juntos, geralmente em sequência, para realizar um cenário específico. Por exemplo, você pode ter um pipeline que limpa, extrai, transforma, agrega o uso de Customer Insights. Trabalhos do pipeline são identificados usando a propriedade "Pipeline" quando o trabalho foi enviado. Trabalhos agendados usando o ADF V2 terão, automaticamente, essa propriedade populada. 

Para exibir uma lista de trabalhos de U-SQL que fazem parte dos pipelines: 

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Insights de trabalho**. A guia "Todos os trabalhos" será padronizada, mostrando trabalhos em execução, na fila e encerrados.
3. Clique na guia **Trabalhos de pipeline**. Uma lista de trabalhos do pipeline será mostrada juntamente com estatísticas agregadas para cada pipeline.

### <a name="monitoring-recurring-jobs"></a>Monitorando trabalhos recorrentes
Um trabalho recorrente é aquele que tem a mesma lógica de negócios, mas usa dados de entrada diferentes toda vez que é executado. Idealmente, trabalhos recorrentes devem sempre ter êxito e ter um tempo de execução relativamente estável. O monitoramento desses comportamentos ajuda a garantir que o trabalho tenha integridade. Trabalhos recorrentes são identificados usando a propriedade "Recorrente". Trabalhos agendados usando o ADF V2 terão, automaticamente, essa propriedade populada.

Para exibir uma lista de trabalhos de U-SQL que são recorrentes: 

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Insights de trabalho**. A guia "Todos os trabalhos" será padronizada, mostrando trabalhos em execução, na fila e encerrados.
3. Clique na guia **Trabalhos recorrentes**. Uma lista de trabalhos recorrentes será mostrada juntamente com estatísticas agregadas para cada trabalho recorrente.

## <a name="manage-policies"></a>Gerenciar políticas

### <a name="account-level-policies"></a>Políticas no nível da conta

Essas políticas aplicam-se a todos os trabalhos em uma conta do Data Lake Analytics.

#### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Número máximo de AUs em uma conta do Data Lake Analytics
Uma política controla o número total de AUs (Unidades de Análise) que a conta do Data Lake Analytics pode usar. Por padrão, o valor é definido como 250. Por exemplo, se esse valor for definido como 250 AUs, você poderá ter um trabalho em execução com 250 AUs atribuídas ou 10 trabalhos em execução com 25 AUs cada um. Os trabalhos adicionais enviados são enfileirados até que os trabalhos em execução sejam concluídos. Quando os trabalhos em execução são concluídos, as AUs são liberadas para que os trabalhos na fila sejam executados.

Para alterar o número de AUs da sua conta do Data Lake Analytics:

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **Máximo de AUs**, mova o controle deslizante para selecionar um valor ou insira o valor na caixa de texto. 
4. Clique em **Salvar**.

> [!NOTE]
> Se você precisar de mais do que as AUs padrão (250), no portal, clique em **Ajuda + Suporte** para enviar uma solicitação de suporte. O número de AUs disponíveis em sua conta do Data Lake Analytics pode ser aumentado.
>

#### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Número máximo de trabalhos que podem ser executados simultaneamente
Uma política controla quantos trabalhos podem ser executados ao mesmo tempo. Por padrão, esse valor é definido como 20. Se o Data Lake Analytics tiver AUs disponíveis, novos trabalhos serão agendados para execução imediata até que o número total de trabalhos em execução atinja o valor dessa política. Quando você alcançar o número máximo de trabalhos que podem ser executados simultaneamente, os próximos trabalhos serão enfileirados em ordem de prioridade até que um ou mais trabalhos em execução sejam concluídos (dependendo da disponibilidade de AU).

Para alterar o número de trabalhos que podem ser executadas simultaneamente:

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **Número Máximo de Trabalhos em Execução**, mova o controle deslizante para selecionar um valor ou insira o valor na caixa de texto. 
4. Clique em **Salvar**.

> [!NOTE]
> Se você precisar executar mais do que o número de trabalhos padrão (20) no portal, clique em **Ajuda + Suporte** para enviar uma solicitação de suporte. O número de trabalhos que podem ser executados simultaneamente em sua conta do Data Lake Analytics pode ser aumentado.
>

#### <a name="how-long-to-keep-job-metadata-and-resources"></a>Por quanto tempo os metadados e recursos de trabalho devem ser mantidos 
Quando os usuários executam trabalhos de U-SQL, o serviço do Data Lake Analytics mantém todos os arquivos relacionados. Os arquivos relacionados incluem o script U-SQL, os arquivos DLL referenciados no script U-SQL, os recursos compilados e as estatísticas. Os arquivos estão na pasta /system/ da conta de armazenamento padrão do Azure Data Lake. Esta política controla por quanto tempo esses recursos serão armazenados antes de serem excluídos automaticamente (o padrão é 30 dias). Você pode usar esses arquivos para depuração e ajuste de desempenho de trabalhos que serão executados novamente no futuro.

Para alterar o tempo para reter os recursos e metadados de trabalho:

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **Dias para Reter Consultas de Trabalho**, mova o controle deslizante para selecionar um valor ou insira o valor na caixa de texto.  
4. Clique em **Salvar**.

### <a name="job-level-policies"></a>Políticas no nível do trabalho
Com as políticas no nível do trabalho, você pode controlar o máximo de AUs e a prioridade máxima que podem ser definidos por usuários individuais (ou membros de grupos de segurança específicos) nos trabalhos que eles enviam. Isso lhe permite controlar os custos gerados pelos usuários. Ele também permite que você controle o efeito que os trabalhos agendados podem ter nos trabalhos de produção de alta prioridade que estão em execução na mesma conta do Data Lake Analytics.

O Data Lake Analytics tem duas políticas que podem ser definidas no nível do trabalho:

* **Limite de AU por trabalho**: os usuários apenas podem enviar trabalhos que tenham até esse número de AUs. Por padrão, esse limite é o mesmo que o limite máximo de AUs da conta.
* **Prioridade**: os usuários apenas podem enviar trabalhos com prioridade menor ou igual a esse valor. Observe que um número maior significa uma prioridade mais baixa. Por padrão, ela é definida como 1, que é a prioridade mais alta possível.

Há uma política padrão definida em cada conta. A política padrão aplica-se a todos os usuários da conta. Você pode definir políticas adicionais para usuários e grupos específicos. 

> [!NOTE]
> As políticas no nível da conta e no nível do trabalho aplicam-se ao mesmo tempo.
>

#### <a name="add-a-policy-for-a-specific-user-or-group"></a>Adicionar uma política para um usuário ou grupo específico

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **Limites de Envio de Trabalho**, clique no botão **Adicionar Política**. Em seguida, selecione ou insira as seguintes configurações:
    1. **Nome da Política de Computação**: insira um nome de política para lembrá-lo da finalidade da política.
    2. **Selecionar Usuário ou Grupo**: selecione o usuário ou o grupo ao qual essa política se aplica.
    3. **Definir o Limite de AUs de Trabalho**: defina o limite de AUs que se aplica ao usuário ou ao grupo selecionado.
    4. **Definir o Limite de Prioridade**: defina o limite de prioridade que se aplica ao usuário ou ao grupo selecionado.

4. Clique em **OK**.

5. A nova política é listada na tabela de política **Padrão**, em **Limites de Envio de Trabalho**. 

#### <a name="delete-or-edit-an-existing-policy"></a>Excluir ou editar uma política existente

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **Limites de Envio de Trabalho**, localize a política que deseja editar.
4.  Para ver as opções **Excluir** e **Editar**, na coluna mais à direita da tabela, clique em **...**.

### <a name="additional-resources-for-job-policies"></a>Recursos adicionais para políticas de trabalho
* [Postagem no blog de visão geral de política](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Postagem no blog de políticas no nível da conta](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Postagem no blog de políticas no nível do trabalho](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerenciar o Azure Data Lake Analytics usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

