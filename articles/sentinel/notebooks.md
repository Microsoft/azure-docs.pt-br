---
title: Usar notebooks com o Azure Sentinel para a busca de segurança
description: Este artigo descreve como usar notebooks com os recursos de busca do Azure Sentinel.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 43d7a697b3cb013a73a0b14db8ec1758244ae3b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97092179"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Usar o Jupyter Notebook para procurar ameaças à segurança

A base do Azure Sentinel é o armazenamento de dados; Ele combina consultas de alto desempenho, esquema dinâmico e escalas para grandes volumes de dados. O portal do Azure e todas as ferramentas do Azure Sentinel usam uma API comum para acessar esse armazenamento de dados. A mesma API também está disponível para ferramentas externas, como notebooks [Jupyter](https://jupyter.org/) e Python. Embora muitas tarefas comuns possam ser executadas no portal, o Jupyter estende o escopo do que você pode fazer com esses dados. Ele combina programação completa com uma enorme coleção de bibliotecas para aprendizado de máquina, visualização e análise de dados. Esses atributos fazem do Jupyter uma ferramenta interessante para investigação de segurança e busca.

![notebook de exemplo](./media/notebooks/sentinel-notebooks-map.png)

Integramos a experiência do Jupyter ao portal do Azure, facilitando a criação e a execução de notebooks para analisar seus dados. A biblioteca *Kqlmagic* permite fazer consultas do Azure Sentinel e executá-las diretamente dentro de um notebook. As consultas usam a [linguagem de consulta Kusto](https://kusto.azurewebsites.net/docs/kusto/query/index.html). Vários notebooks, desenvolvidos por alguns analistas de segurança da Microsoft, são empacotados com o Azure Sentinel. Alguns desses notebooks são criados para um cenário específico e podem ser usados no estado em que se encontram. Outros são destinados como exemplos para ilustrar técnicas e recursos que você pode copiar ou adaptar para uso em seus notebooks. Outros blocos de anotações também podem ser importados do GitHub da Comunidade do Azure Sentinel.

A experiência integrada do Jupyter usa [o Azure Notebooks](https://notebooks.azure.com/) para armazenar, compartilhar e executar notebooks. Você também pode executar esses notebooks localmente se tiver um ambiente de Python e Jupyter em seu computador ou em outros ambientes do JupterHub, como o Azure Databricks.

Os notebooks têm dois componentes:

- A interface baseada em navegador na qual você insere e executa consultas e código, e onde os resultados da execução são exibidos.
- Um *kernel* que é responsável pela análise e pela execução do próprio código.

O kernel do notebook do Azure Sentinel é executado em uma VM (máquina virtual) do Azure. Existem várias opções de licenciamento para aproveitar máquinas virtuais mais poderosas se os notebooks incluírem modelos de aprendizado de máquina complexos.

Os notebooks do Azure Sentinel usam muitas bibliotecas populares de Python, como pandas, matplotlib, bokeh e outros. Há muitos outros pacotes do Python para você escolher, cobrindo áreas como:

- Visualizações e gráficos
- Processamento de dados e análise
- Estatísticas e computação numérica
- Aprendizado de máquina e aprendizado profundo

Também lançamos algumas ferramentas de segurança de open-source do Jupyter em um pacote chamado [msticpy](https://github.com/Microsoft/msticpy/). Esse pacote é usado em muitos dos notebooks incluídos. As ferramentas Msticpy foram projetadas especificamente para ajudar na criação de notebooks para busca e investigação, e estamos trabalhando ativamente em novos recursos e aprimoramentos.

O [repositório GitHub da Comunidade do Azure Sentinel](https://github.com/Azure/Azure-Sentinel) é o local para os notebooks futuros do Azure Sentinel criados pela Microsoft ou contribuídos pela comunidade.

Para usar os blocos de anotações, você deve primeiro criar um espaço de trabalho de Azure Machine Learning (ML).

## <a name="create-an-azure-ml-workspace"></a>Criar um espaço de trabalho do Azure ML

1. No portal do Azure, navegue até **Azure Sentinel**  >  **Threat Management**  >  **notebooks** e selecione Iniciar o **Notebook**.

    > [!div class="mx-imgBorder"]
    > ![iniciar o bloco de anotações para iniciar o espaço de trabalho do Azure ml](./media/notebooks/sentinel-notebooks-launch.png)

1. Em **espaço de trabalho do AzureML**, selecione **criar novo**.

    > [!div class="mx-imgBorder"]
    > ![criar espaço de trabalho](./media/notebooks/sentinel-notebooks-azureml-create.png)

1. Na página **Machine Learning** , forneça as seguintes informações e, em seguida, selecione **revisar + criar**.

    |Campo|Descrição|
    |--|--|
    |Subscription|Selecione a assinatura do Azure que você deseja usar.|
    |Resource group|Use um grupo de recursos existente na sua assinatura ou insira um nome para criar um grupo de recursos. Um grupo de recursos mantém os recursos relacionados a uma solução do Azure. Neste exemplo, usamos **AzureMLRG**.|
    |Nome do workspace|Insira um nome único que identifique seu workspace. Neste exemplo, usamos **testworkspace1**. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de lembrar e diferenciar de workspaces criados por outras pessoas.|
    |Região|Selecione a localização mais próxima aos usuários e recursos de dados para criar o workspace.|
    |Edição do Workspace|Selecione **básico** como o tipo de espaço de trabalho neste exemplo. O tipo de espaço de trabalho (básico & Enterprise) determina os recursos aos quais você terá acesso e preços.|

    > [!div class="mx-imgBorder"]
    > ![fornecer detalhes do espaço de trabalho](./media/notebooks/sentinel-notebooks-azureml-basics.png)

1. Examine as informações, verifique se elas estão corretas e, em seguida, selecione **criar** para iniciar a implantação do seu espaço de trabalho.

    > [!div class="mx-imgBorder"]
    > ![examinar detalhes do espaço de trabalho](./media/notebooks/sentinel-notebooks-azureml-review.png)

    Pode levar vários minutos para criar seu espaço de trabalho na nuvem durante o tempo em que a página de **visão geral** exibe o status de implantação atual.

    > [!div class="mx-imgBorder"]
    > ![implantação de espaço de trabalho](./media/notebooks/sentinel-notebooks-azureml-deploy.png)

Quando a implantação for concluída, você poderá iniciar os blocos de anotações em seu novo espaço de trabalho do Azure ML.

> [!div class="mx-imgBorder"]
> ![implantação do espaço de trabalho bem-sucedida](./media/notebooks/sentinel-notebooks-azureml-complete.png)

## <a name="launch-a-notebook-using-your-azure-ml-workspace"></a>Iniciar um bloco de anotações usando seu espaço de trabalho do Azure ML

1. No portal do Azure, navegue até **Azure Sentinel** > **Gerenciamento de ameaças** > **Notebooks**, em que você pode ver os notebooks que o Azure Sentinel fornece.

    > [!TIP]
    > Selecione **guias & comentários** para abrir um painel com ajuda adicional e orientação sobre blocos de anotações.
    > ![Exibir guias do bloco de anotações](./media/notebooks/sentinel-azure-notebooks-guides.png)

1. Selecione blocos de anotações individuais para exibir suas descrições, os tipos de dados necessários e as fontes de dados.

    > [!div class="mx-imgBorder"]
    > ![Exibir detalhes do bloco de anotações](./media/notebooks/sentinel-azure-notebooks-view.png)

1. Selecione o bloco de anotações que você deseja usar e, em seguida, selecione **iniciar o bloco de anotações** para clonar e configurar o bloco de anotações em um novo projeto Azure notebooks que se conecta ao seu espaço de trabalho do Azure Sentinel. Quando o processo estiver concluído, o notebook será aberto dentro do Azure Notebooks para que você execute.

    > [!div class="mx-imgBorder"]
    > ![selecionar bloco de anotações](./media/notebooks/sentinel-azure-notebooks-select.png)

1. Em espaço de trabalho do AzureML, selecione seu espaço de trabalho do Azure ML e, em seguida, selecione **Iniciar**.

    > [!div class="mx-imgBorder"]
    > ![iniciar notebook](./media/notebooks/sentinel-azure-notebooks-launch.png)

1. Selecione uma instância de computação. Se você não tiver uma instância de computação, faça o seguinte:
    1. Selecione o sinal de adição (+) para iniciar o assistente de **nova instância de computação** .

        > [!div class="mx-imgBorder"]
        > ![Iniciar assistente de instância de computação](./media/notebooks/sentinel-azure-notebooks-compute-wizard.png)

    1. Na página **nova instância de computação** , forneça as informações necessárias e, em seguida, selecione **criar**.

        > [!div class="mx-imgBorder"]
        > ![criar instância de computação](./media/notebooks/sentinel-azure-notebooks-compute-create.png)

1. Depois que o servidor de notebook for criado, dentro de cada célula, selecione o ícone de execução para executar o código nos blocos de anotações.

    > [!div class="mx-imgBorder"]
    > ![executar bloco de anotações](./media/notebooks/sentinel-azure-notebooks-run.png)

Recomendações:

- Para obter uma breve introdução à consulta de dados no Azure Sentinel, consulte a [introdução com os blocos de anotações do Azure ml e o guia do Azure Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb) .

- Você encontrará blocos de anotações de exemplo adicionais na subpasta do GitHub [**de exemplo-blocos de anotações**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) . Esses notebooks de exemplo foram salvos com dados, para que seja mais fácil ver a saída pretendida. É recomendável exibir esses notebooks em [nbviewer](https://nbviewer.jupyter.org/).

- A subpasta [**HOWTOs**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) do GitHub contém blocos de anotações que descrevem, por exemplo: definindo a versão padrão do Python, configurando um DSVM, criando indicadores do Azure Sentinel de um notebook e outros assuntos.

Os notebooks fornecidos são destinados como ferramentas úteis, ilustrações e exemplos de código que você pode usar no desenvolvimento de seus notebooks.

Mande comentários, sugestões, solicitações de recursos, contribuições de notebooks, relatórios de bugs ou melhorias e adições a notebooks existentes. Acesse o [GitHub da Comunidade do Azure Sentinel](https://github.com/Azure/Azure-Sentinel) para criar uma questão ou fork e carregar uma contribuição.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a começar a usar o Jupyter Notebook no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- [Buscar ameaças proativamente](hunting.md)
- [Use indicadores para salvar informações interessantes durante a busca](bookmarks.md)
