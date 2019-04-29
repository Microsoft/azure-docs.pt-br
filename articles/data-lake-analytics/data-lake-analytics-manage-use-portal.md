---
title: Gerenciar o Azure Data Lake Analytics usando o portal do Azure
description: Este artigo descreve como usar o portal do Azure para gerenciar contas, fontes de dados, usuários e trabalhos do Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8b2f16f45be1d095e9be8042611de328af36f064
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813441"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Gerenciar o Azure Data Lake Analytics usando o portal do Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Este artigo descreve como gerenciar contas, fontes de dados, usuários e trabalhos do Azure Data Lake Analytics usando o portal do Azure.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Gerenciar contas do Data Lake Analytics

### <a name="create-an-account"></a>Criar uma conta

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Criar um recurso** > **Intelligence + analytics** > **Data Lake Analytics**.
3. Selecione os valores para os seguintes itens: 
   1. **Nome**: O nome da conta do Data Lake Analytics.
   2. **Assinatura**: A assinatura do Azure usada para a conta.
   3. **Grupo de Recursos**: O grupo de recursos do Azure no qual a tarefa será criada. 
   4. **Local**: O datacenter do Azure para a conta do Data Lake Analytics. 
   5. **Data Lake Store**: Repositório padrão a ser usado para a conta do Data Lake Analytics. A conta do Azure Data Lake Store e a conta do Data Lake Analytics devem estar no mesmo local.
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

* Data Lake Store
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
* **Proprietário**: Pode enviar, monitorar e cancelar trabalhos de qualquer usuário e configurar a conta.
* **Colaborador**: Pode enviar, monitorar e cancelar trabalhos de qualquer usuário e configurar a conta.
* **Leitor**: Pode monitorar trabalhos.

Use a função de desenvolvedor do Data Lake Analytics para permitir que os desenvolvedores de U-SQL usem o serviço do Data Lake Analytics. Você pode usar a função de desenvolvedor do Data Lake Analytics:
* Enviar trabalhos.
* Monitorar o status do trabalho e o andamento dos trabalhos enviados por qualquer usuário.
* Consultar os scripts de U-SQL de trabalhos enviados por qualquer usuário.
* Cancelar apenas seus próprios trabalhos.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Adicionar usuários ou grupos de segurança a uma conta do Data Lake Analytics

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **IAM (Controle de acesso)** > **Adicionar atribuição de função**.
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

    1. **Nome do trabalho**: O nome do trabalho.
    2. **Prioridade**: Números menores têm prioridade mais alta. Se dois trabalhos estiverem enfileirados, aquele com o menor valor de prioridade será executado primeiro.
    3. **Paralelismo**: O número máximo de processos de computação a serem reservados para este trabalho.

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

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Gerenciar o Azure Data Lake Analytics usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Gerenciar o Azure Data Lake Analytics usando políticas](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-policies)
