---
title: 'Início Rápido: criar um pool do Apache Spark sem servidor usando o portal do Azure'
description: Crie um pool do Apache Spark sem servidor usando o portal do Azure seguindo as etapas deste guia.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3fc7a7f137701590e4bddd791a3fdd2d1eaeca4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461601"
---
# <a name="quickstart-create-a-new-serverless-apache-spark-pool-using-the-azure-portal"></a>Início Rápido: criar um pool do Apache Spark sem servidor usando o portal do Azure

O Azure Synapse Analytics oferece vários mecanismos de análise para ajudar você a ingerir, transformar, modelar, analisar e distribuir seus dados. Um Pool do Apache Spark fornece funcionalidades de software livre de computação de Big Data. Após a criação de um pool do Apache Spark em seu workspace do Synapse, os dados podem ser carregados, modelados, processados e distribuídos para chegar mais rapidamente a insights analíticos.

Neste início rápido, você aprenderá a usar o portal do Azure para criar um Pool do Apache Spark em um workspace do Synapse.

> [!IMPORTANT]
> A cobrança das instâncias do Spark será proporcional por minuto, independentemente de elas estarem sendo usadas ou não. Desligue a instância do Spark depois de terminar de usá-la ou defina um tempo limite curto. Para saber mais, confira a seção **Recursos de limpeza** deste artigo.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [Workspace do Synapse Analytics](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navegue até o workspace do Synapse 
1. Navegue até o workspace do Synapse em que o pool do Apache Spark será criado digitando o nome do serviço (ou o nome do recurso diretamente) na barra de pesquisa.
![Barra de pesquisa do portal do Azure com workspaces do Synapse digitados.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Na lista de workspaces, digite o nome (ou parte do nome) do workspace a ser aberto. Para este exemplo, vamos usar um workspace chamado **contosoanalytics**.
![Lista de workspaces do Synapse filtrados para mostrar os que contêm o nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)


## <a name="create-new-apache-spark-pool"></a>Criar um Pool do Apache Spark

1. No workspace do Synapse em que deseja criar o Pool do Apache Spark, selecione em **Novo Pool do Apache Spark**.
    ![Visão geral do workspace do Synapse com uma caixa vermelha em torno do comando usado para criar um Pool do Apache Spark](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-01.png)
2. Insira os seguintes detalhes na guia **Informações Básicas**:

    |Configuração | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome do Pool do Apache Spark** | Um nome de pool válido | Esse é o nome que o Pool do Apache Spark terá. |
    | **Tamanho do nó** | Pequeno (4 vCPU/32 GB) | Defina isso com o menor tamanho para reduzir os custos deste início rápido |
    | **Autoescala** | Desabilitado | Não precisamos de dimensionamento automático para este início rápido |
    | **Número de nós** | 5 | Use um tamanho pequeno para limitar os custos deste início rápido |


    ![Fluxo de criação do Pool do Apache Spark – guia Informações Básicas.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-02.png)
    > [!IMPORTANT]
    > Observe que há limitações específicas para os nomes que os pools do Apache Spark podem usar. Os nomes precisam conter apenas letras ou números, ter 15 caracteres ou menos, começar com uma letra, ser exclusivos no workspace e não devem conter palavras reservadas.

3. Selecione **Avançar: configurações adicionais** e examine as configurações padrão. Não modifique nenhuma configuração padrão.
    ![Captura de tela que mostra a página "Criar pool do Apache Spark" com a guia "Configurações adicionais" selecionada.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-03.png)

4. Selecione **Avançar: marcas**. Não adicione nenhuma marca.
    ![Fluxo de criação do Pool do Apache Spark – guia Configurações adicionais.](media/quickstart-create-apache-spark-pool/create-spark-pool-03-tags.png)

5. Selecione **Examinar + criar**.

6. Verifique se os detalhes estão corretos com base no que foi inserido anteriormente e selecione **Criar**.
    ![Fluxo de criação do Pool do Apache Spark – guia Examinar configurações.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-05.png)

7. Neste ponto, o fluxo de provisionamento de recursos será iniciado, indicando quando estiver concluído.
    ![Captura de tela que mostra a página "Visão geral" com a mensagem "Implantação concluída" exibida.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-06.png)

8. Após a conclusão do provisionamento, se você navegar novamente até o workspace, será mostrada uma nova entrada para o Pool do Apache Spark recém-criado.
    ![Fluxo de criação do Pool do Apache Spark – provisionamento de recursos.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-07.png)

9. Neste ponto, não há recursos em execução, nenhum custo pelo Spark e você criou metadados sobre as instâncias do Spark que deseja criar.

## <a name="clean-up-resources"></a>Limpar os recursos

Siga as etapas abaixo para excluir o Pool do Apache Spark do workspace.
> [!WARNING]
> A exclusão de um Pool do Apache Spark removerá o mecanismo de análise do workspace. Não será mais possível se conectar ao pool e todas as consultas, os pipelines e os notebooks que usam esse Pool do Apache Spark deixarão de funcionar.

Caso deseje excluir o Pool do Apache Spark, faça o seguinte:

1. Navegue até a folha de pools do Apache Spark no workspace.
2. Escolha o pool do Apache Spark a ser excluído (neste caso, **contosospark**).
3. Selecione **delete**.
 ![Listagem de pools do Apache Spark, com o pool recém-criado selecionado.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-08.png)
4. Confirme a exclusão e selecione o botão **Delete**.
 ![Caixa de diálogo de confirmação de exclusão do Pool do Apache Spark selecionado.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-10.png)
5. Quando o processo for concluído com êxito, o Pool do Apache Spark não estará mais listado nos recursos do workspace.

## <a name="next-steps"></a>Próximas etapas

- Confira [Início Rápido: Criar um notebook do Apache Spark](quickstart-apache-spark-notebook.md).
- Confira [Início Rápido: criar um pool de SQL dedicado usando o portal do Azure](quickstart-create-sql-pool-portal.md).
