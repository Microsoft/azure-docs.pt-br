---
title: "Estruturar projetos com o modelo de Processo de Ciência de Dados da Equipe | Microsoft Docs"
description: "Como criar uma instância de modelos de Processo de Ciência de Dados da Equipe (TDSP) no Azure Machine Learning que estruture projetos para colaboração"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: bradsev
ms.openlocfilehash: d47c219dfd1e62351d7113d930b17ac45f64c369
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>Estruturar projetos com modelo de Processo de Ciência de Dados da Equipe

Este documento apresenta instruções sobre como criar projetos de ciência de dados no Azure Machine Learning com modelos de TDSP (Processo de Ciência de Dados da Equipe). Esses modelos ajudam a estruturar projetos para colaboração e reprodução. 


## <a name="what-is-the-team-data-science-process"></a>O que é o Processo de Ciência de Dados de Equipe?
O TDSP é um processo de ciência de dados ágil e iterativo para executar e fornecer soluções de análise avançada. É projetado para melhorar a colaboração e a eficiência das equipes de ciência de dados em organizações empresariais. Ele dá suporte a esses objetivos com quatro componentes principais:

   * Uma definição de [ciclo de vida de ciência de dados](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md) padrão.
   * [Modelos de relatório e documentação de projeto](https://github.com/Azure/Azure-TDSP-ProjectTemplate) de uma estrutura de projeto padronizada.
   * Uma infraestrutura e recursos para execução do projeto, como infraestrutura de computação e armazenamento e repositórios de código.
   * [Ferramentas e utilitários](https://github.com/Azure/Azure-TDSP-Utilities) para tarefas de projeto de ciência de dados, como:
      - Controle de versão e revisão de código colaborativos
      - Exploração de dados e modelagem
      - Planejamento de trabalho

Para uma discussão mais completa sobre TDSP, consulte [Visão geral do Processo de Ciência de Dados da Equipe](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/README.md).

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>Por que você deve usar a estrutura e os modelos de TDSP?
A padronização da estrutura, do ciclo de vida e da documentação de projetos de ciência de dados são essenciais para facilitar a colaboração eficaz em equipes de ciência de dados. A capacidade de criar de projetos do Machine Learning com um modelo TDSP oferece uma estrutura para trabalho em equipe coordenado.

Lançamos anteriormente um [repositório do GitHub para a estrutura e os modelos do projeto TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate), a fim de ajudar a atingir esses objetivos. Mas não foi possível, até agora, instanciar a estrutura TDSP e os modelos em uma ferramenta de ciência de dados. Agora, é possível criar um projeto do Machine Learning que instancie os modelos de estrutura e documentação do TDSP. 

## <a name="things-to-note-before-creating-a-new-project"></a>O que observar antes de criar um novo projeto
Observe ou examine o seguinte *antes de* criar um novo projeto:
* Examine o [modelo](https://aka.ms/tdspamlgithubrepo) do Machine Learning do TDSP.
* Os conteúdos (que não seja os que já estão na pasta “docs”) deve ser menor que 25 MB. Consulte a observação após esta lista.
* A pasta de dados de\_exemplo é somente para arquivos de dados pequenos (menos de 5 MB) com os quais você pode testar seu código ou começar o desenvolvimento inicial.
* Armazenar arquivos, como Word, PowerPoint etc. pode aumentar substancialmente o tamanho da pasta “docs”. Aconselhamos que você encontre um Wiki de colaboração, [SharePoint](https://products.office.com/en-us/sharepoint/collaboration) ou outros recursos colaborativos para armazenar esses arquivos.
* Para saber como manipular arquivos grandes e saídas no Machine Learning, consulte [Como manter alterações e lidar com arquivos grandes](http://aka.ms/aml-largefiles).

> [!NOTE]
> Verifique se, além dos arquivos readme.md, todo o conteúdo relacionado com a documentação (texto, markdowns, imagens e outros arquivos de documento) que *não* sejam usados durante a execução do projeto estão na pasta chamada “docs” (todas em minúsculas). A pasta “docs” é uma pasta especial ignorada pela execução do Machine Learning para que os conteúdos nessa pasta não sejam copiados para os destinos de computação desnecessariamente. Os objetos nesta pasta também não contam para o limite de 25 MB do tamanho do projeto. A pasta “docs”, por exemplo, é o local para armazenar arquivos de imagem grandes necessários na sua documentação. Esses arquivos ainda são acompanhados pelo Git por meio do histórico de execuções. 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>Instanciar a estrutura e os modelos TDSP da galeria de modelos do Machine Learning
Para criar um novo projeto com os modelos de estrutura e documentação do TDSP, conclua os procedimentos a seguir.

### <a name="create-a-new-project"></a>Criar um novo projeto
Para criar um novo projeto, abra o Azure Machine Learning. Em **Projetos**, no painel superior esquerdo, selecione o sinal de adição (**+**) e, em seguida, selecione **Novo Projeto**.

![Novo Projeto](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>Crie um novo projeto estruturado TDSP
   1. Especifique os parâmetros e as informações nas caixas ou listas relevantes:

      - Nome do projeto
      - Diretório do projeto
      - Descrição do projeto
      - Um caminho do repositório Git vazio
      - Nome do espaço de trabalho

   2. Em seguida, na caixa de **Pesquisa**, insira **TDSP**. 
   3. Quando a opção **Estruturar um projeto com TDSP** for exibida, selecione o modelo. 
   4. Selecione o botão **Criar** para criar o novo projeto com a estrutura TDSP. Se você fornecer um repositório Git vazio durante a criação do projeto (na caixa de texto adequada), então, esse repositório será preenchido com a estrutura e os conteúdos do projeto após sua criação.

![Criar um projeto TDSP](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Examinar a estrutura do projeto TDSP
Depois que o novo projeto for criado, será possível examinar sua estrutura (veja o painel esquerdo na figura a seguir). Ela contém todos os aspectos da documentação padronizada para entendimento empresarial. Esses itens incluem os estágios do ciclo de vida do TDSP, o local dos dados, as definições e a arquitetura desse modelo de documentação. 

A estrutura mostrada é derivada da estrutura TDSP publicada na [estrutura do projeto, nos documentos e nos modelos de artefato do TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate), com algumas modificações. Por exemplo, vários modelos de documento são mesclados em um markdown, ou seja, [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Estrutura da pasta de projeto
O modelo de projeto TDSP contém as pastas de nível superior a seguir:
   - **código**: contém código.
   - **docs**: contém a documentação necessária sobre o projeto (por exemplo, arquivos markdown, mídia relacionada etc.).
   - **sample_data**: contém dados **EXEMPLO (pequeno)** que podem ser usados para teste ou desenvolvimento inicial. Normalmente, esses conjuntos não são maiores do que vários (5) MB. Essa pasta não é destinada a conjuntos de dados completos ou grandes.

![Dados de amostra](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>Usar a estrutura e os modelos de TDSP
É necessário adicionar informações específicas do projeto à estrutura e aos modelos. Você deve preenchê-los com código e a informação necessária para executar e entregar o projeto. O arquivo [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) é um modelo que deve ser modificado com informações relevantes para seu projeto. Ele vem com um conjunto de perguntas que ajudam a preencher as informações de cada um dos quatro estágios do [ciclo de vida do TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md).

Um exemplo da aparência de uma estrutura de projeto durante a execução ou após a conclusão é mostrado a seguir no painel esquerdo da figura a seguir. Esse projeto é do [Projeto de exemplo de Processo de Ciência de Dados da Equipe: classificar rendas dos dados de censo dos EUA no Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).

![Exemplo de estrutura de projeto](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>Documente o projeto
Consulte o [Modelos de documentação TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) para obter informações sobre como documentar o projeto. No modelo de documentação TDSP do Machine Learning atual, recomendamos que você inclua todas as informações no arquivo [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). Esse modelo deve ser preenchido com informações específicas do seu projeto. 

Também fornecemos um modelo [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings). É possível usar esse modelo para incluir quaisquer informações não incluídas no documento do projeto primário, mas que ainda são úteis para documentar. 

### <a name="example-project-report"></a>Exemplo de relatório de projeto
É possível obter um [exemplo de relatório de projeto](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Esse é o relatório de projeto para o [Projeto de exemplo de classificação de renda dos EUA](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome), que mostra como o modelo TDSP pode ser instanciado e usado em um projeto de ciência de dados.

## <a name="next-steps"></a>Próximas etapas
Para facilitar sua compreensão de como a estrutura e os modelos TDSP podem ser usados em projetos do Machine Learning, apresentamos vários exemplos de projetos concluídos na documentação do Machine Learning:

- Para obter um exemplo de como criar um projeto TDSP no Machine Learning, consulte [Projeto de exemplo do Processo de Ciência de Dados da Equipe: classificar rendas dos dados de censo dos EUA no Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).
- Para obter um exemplo que use aprendizado profundo em NLP (processamento de linguagem natural) em um projeto instanciado por TDSP no Machine Learning, consulte [Reconhecimento de entidade biomédica usando processamento de linguagem natural com aprendizado profundo](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction).

