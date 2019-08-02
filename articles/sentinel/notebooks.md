---
title: Recursos de procura usando blocos de anotações na visualização do Azure Sentinel | Microsoft Docs
description: Este artigo descreve como usar blocos de anotações com os recursos de procura Sentinel do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: cc84db4bd3dc06d4e2dbecb3be8ceee37d554ae7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619845"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Usar Notebooks Jupyter Procurar ameaças de segurança

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A base do Azure Sentinel é o armazenamento de dados; Ele combina consultando, esquema dinâmico e é dimensionada para volumes de dados em massa de alto desempenho. O portal do Azure Sentinel e todas as ferramentas do Azure Sentinel usam uma API comum para acessar esse armazenamento de dados. A mesma API, como também está disponível para ferramentas externas [Jupyter](https://jupyter.org/) blocos de anotações e Python. Embora muitas tarefas comuns podem ser executadas no portal, o Jupyter amplia o escopo do que você pode fazer com esses dados. Ele combina programação completa com uma grande coleção de bibliotecas para análise de dados, visualização e aprendizado de máquina. Esses atributos tornam Jupyter uma ferramenta interessante para investigação de segurança e de procura.

![Bloco de anotações de exemplo](./media/notebooks/sentinel-nb-mapandtimeline.png)

Nós integramos a experiência do Jupyter no portal do Azure Sentinel, tornando mais fácil para você criar e executar os blocos de anotações para analisar seus dados. O *Kqlmagic* biblioteca fornece a cola que permite realizar consultas Sentinel do Azure e executá-los diretamente dentro de um bloco de anotações. As consultas usam o [linguagem de consulta Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Vários blocos de anotações, desenvolvidos por alguns dos analistas de segurança da Microsoft, são empacotados com o Azure Sentinel. Alguns desses blocos são criados para um cenário específico e podem ser usados como-está. Outros são fornecidos como exemplos para ilustrar técnicas e recursos que você pode copiar ou adaptar para uso em seus próprios blocos de anotações. Outros blocos de anotações também podem ser importados da comunidade Azure Sentinel GitHub.

Usa a experiência integrada do Jupyter [Azure Notebooks](https://notebooks.azure.com/) para armazenar, compartilhar e executar os blocos de anotações. Você também pode executar esses notebooks localmente (se você tiver um ambiente do Python e Jupyter em seu computador) ou em outros ambientes de JupterHub como o Azure Databricks.

Blocos de anotações têm dois componentes:

- a interface baseada em navegador em que você inserir e executar consultas e código, e onde os resultados da execução são exibidos.
- uma *kernel* que é responsável por analisar e executar o código em si. 

Em blocos de anotações do Azure, esse kernel é executado no Azure *livre de computação em nuvem e armazenamento* por padrão. Se seus blocos de anotações incluem modelos de aprendizado de máquina complexos ou visualizações você deve considerar o uso mais eficiente e dedicado, os recursos de computação, como [as máquinas virtuais de ciência de dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Blocos de anotações em sua conta são mantidos privados, a menos que você optar por compartilhá-los.

Os blocos de anotações do Azure Sentinel usam muitas bibliotecas populares do Python como pandas, matplotlib, bokeh e outras pessoas. Há um grande número de outros pacotes do Python para sua escolha, abrangendo áreas como:

- visualizações e elementos gráficos
- análise e processamento de dados
- as estatísticas e computação numérica
- aprendizado de máquina e aprendizado profundo

Também lançamos algumas ferramentas de segurança do código-fonte aberto Jupyter em um pacote denominado [msticpy](https://github.com/Microsoft/msticpy/). Esse pacote é usado em muitos dos notebooks incluídos. Ferramentas de Msticpy foram projetadas especificamente para ajudar com a criação de blocos de anotações para procura e investigação e estamos trabalhando ativamente para novos recursos e aprimoramentos.

Os blocos de anotações inicias incluem:

- **Interativa de investigação - processo alertas**: Permite que você rapidamente, disparar alertas, analisando a atividade nos hosts do afetado.
- **Guiado caça - Gerenciador de host do Windows**: Permite que você explore a atividade da conta, execuções do processo, atividade de rede e outros eventos em um host.  
- **Guiado caça - Explorando o Office 365**: Busca de atividades suspeitas do Office 365 em vários conjuntos de dados do O365.

O [repositório GitHub de comunidade do Azure Sentinel](https://github.com/Azure/Azure-Sentinel) é o local para qualquer futuras blocos de anotações do Azure Sentinel criado pela Microsoft ou da comunidade.

## <a name="run-a-notebook"></a>Executar um bloco de anotações

No exemplo a seguir, podemos criar um projeto de blocos de anotações do Azure no portal do Azure Sentinel, preenchendo o projeto com os blocos de anotações. Antes de usar esses blocos de anotações, é uma boa ideia fazer uma cópia do bloco de anotações e trabalhar na cópia. Trabalhar em cópias permite que você atualize-se com segurança para versões futuras do blocos de anotações sem substituir qualquer um dos seus dados.

1. No portal do Azure Sentinel, clique em **Notebooks** no menu de navegação. Para criar um novo projeto de blocos de anotações do Azure, clique em **Clone Azure Sentinel Notebooks** ou para abrir os blocos de anotações existentes projetos clique **ir para os blocos de anotações**.
  
   ![Selecione os blocos de anotações](./media/notebooks/sentinel-az-notebooks-home.png)

2. Se você escolheu **Clone Azure Sentinel Notebooks** na etapa anterior, a seguinte caixa de diálogo será exibida. Clique em **importação** para clonar o repositório do GitHub em seu projeto de blocos de anotações do Azure. Se você não tiver uma conta existente de blocos de anotações do Azure, você será solicitado a criar uma e entrar.

   ![Bloco de anotações de importação](./media/notebooks/sentinel-nb-signin-and-clone.png)

3. Ao criar um novo projeto, você precisará Nomeie o projeto - use o nome padrão ou digite um novo. Não marque a **clonar recursivamente** opção - esta opção se refere a repositórios de GitHub vinculados. Clicar em **importação** começa a clonar o conteúdo do GitHub, que pode levar alguns minutos para ser concluída.

   ![Bloco de anotações de importação](./media/notebooks/sentinel-create-nb-project.png)

4. Abra o **Notebooks** pasta para ver os blocos de anotações. Cada notebook orienta você pelas etapas para executar uma busca ou uma investigação. Bibliotecas e outras dependências necessárias para o bloco de anotações podem ser instaladas do próprio bloco de notas ou por meio de um procedimento de configuração simples. Configuração que vincula seu projeto de bloco de anotações para sua assinatura do Azure Sentinel é provisionada automaticamente nas etapas anteriores. Os blocos de anotações estão prontos para executar em relação a seu espaço de trabalho do Azure Log Analytics Sentinel.

   ![Importar do repositório](./media/notebooks/sentinel-open-notebook1.png)

5. Abra um bloco de anotações. Computação gratuita é selecionada por padrão para executar os blocos de anotações (realçados). Se você tiver configurado um DSVM para usar (veja acima), selecione a DSVM e autenticar antes de abrir o notebook primeiro. Clique em um bloco de anotações para abri-lo.

   ![Selecione o bloco de anotações](./media/notebooks/sentinel-open-notebook2.png)

6. Selecionando a versão do Python. Quando você abre um notebook pela primeira vez, ele pode solicitar que você selecione uma versão do kernel. Caso contrário, selecione o kernel de usar da seguinte maneira. Python 3.6 ou posterior deve ser o kernel selecionado (no canto superior direito da janela do bloco de anotações).

   ![Selecione o bloco de anotações](./media/notebooks/sentinel-select-kernel.png)

Para obter uma introdução rápida para consultar dados no Azure Sentinel, examine os [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) notebook na pasta principal de blocos de anotações. Notebooks de exemplo adicionais podem ser encontradas na **Notebooks de amostra** subpasta. Os notebooks de exemplo foram salvas com dados, para que ele seja mais fácil de ver a saída desejada (é recomendável exibi-los no [nbviewer](https://nbviewer.jupyter.org/)). O **HowTos** pasta contém blocos de anotações que descrevem, por exemplo: configuração padrão de versão do Python, configurando uma DSVM, criar Azure Sentinel indicadores de um bloco de anotações e outros assuntos.

Esses notebooks destinam-se como as duas ferramentas úteis e exemplos de código que você pode usar no desenvolvimento de seus próprios blocos de anotações e ilustrações.

Comentários são bem-vindos, se sugestões, as solicitações de recursos, contribuiu com blocos de anotações, relatórios de bugs ou aprimoramentos e adições para blocos de anotações existentes. Vá para o [Azure Sentinel comunidade GitHub](https://github.com/Azure/Azure-Sentinel) para criar um problema ou uma bifurcação e carregue uma contribuição.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como começar a usar o Jupyter notebooks no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- [Proativamente de busca de ameaças](hunting.md)
- [Usar indicadores para salvar informações interessantes durante a procura](bookmarks.md)
