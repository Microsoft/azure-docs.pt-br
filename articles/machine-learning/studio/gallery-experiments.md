---
title: Experimentos na Galeria do Cortana Intelligence | Microsoft Docs
description: Descubra e compartilhe experimentos na Galeria do Cortana Intelligence.
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: f4248922-c961-4d3a-9e1b-aec743210166
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: roopalik;garye
ms.openlocfilehash: 472ecac4ac1d9a86f0a3ad8271dd96257c331e5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="discover-experiments-in-cortana-intelligence-gallery"></a>Descubra experimentos na Galeria do Cortana Intelligence
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="experiments-for-machine-learning-studio"></a>Experimentos para o Machine Learning Studio
A Galeria contém uma ampla variedade de [experimentos](https://gallery.cortanaintelligence.com/experiments) que foram desenvolvidos no [Azure Machine Learning Studio](https://studio.azureml.net). Eles variam de experimentos de verificação de conceito rápidos que demonstram uma técnica de aprendizado de máquina específica a soluções totalmente desenvolvidas para problemas de aprendizado de máquina complexos.

> [!NOTE]
> Um ***experimento*** é uma tela em branco no Machine Learning Studio que você pode usar para construir um modelo de análise de previsão. Crie o modelo conectando dados com vários módulos analíticos. Você pode experimentar ideias diferentes, realizar execuções de teste, eventualmente, implantar seu modelo como um serviço Web no Azure. Para ver um exemplo de criação de um experimento simples, consulte o [Tutorial de aprendizado de máquina: crie seu primeiro experimento no Azure Machine Learning Studio](create-experiment.md). Para ver uma explicação mais abrangente da criação de uma solução de análise preditiva, consulte [Instrução passo a passo: desenvolver uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning](walkthrough-develop-predictive-solution.md).
>
>

## <a name="discover"></a>Descobrir
Para procurar experimentos [na Galeria](http://gallery.cortanaintelligence.com), na parte superior da home page da Galeria, selecione **Experimentos**.

A página **[Experimentos](https://gallery.cortanaintelligence.com/experiments)** exibe uma lista dos experimentos mais populares e adicionados recentemente. Para ver todos os experimentos, selecione o botão **Ver todos**. Para procurar um experimento específico, selecione **Ver todos** e selecione os critérios de filtro. Também é possível inserir os termos de pesquisa na caixa **Pesquisar** na parte superior da página da Galeria.

Obtenha mais informações sobre um experimento em sua página de detalhes. Para abrir uma página de detalhes do experimento, selecione-o. Na página de detalhes de um experimento, na seção **Comentários**, é possível comentar ou fazer perguntas sobre o experimento. Você pode até mesmo compartilhar o experimento com amigos ou colegas no Twitter ou LinkedIn. Você também pode enviar um link por email para a página de detalhes do experimento a fim de convidar outros usuários para ver a página.

![Compartilhar este item com amigos](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Adicionar seus próprios comentários](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="download"></a>Baixar
Você pode baixar uma cópia de qualquer experimento da Galeria em seu espaço de trabalho do Machine Learning Studio. Em seguida, você pode modificar sua cópia para criar suas próprias soluções.

A Galeria do Cortana Intelligence oferece duas maneiras de importar uma cópia de um experimento:

* **Da Galeria**. Se você encontrar um experimento de seu interesse na Galeria, baixe uma cópia e abra-a em seu espaço de trabalho do Machine Learning Studio.
* **Do Machine Learning Studio**. No Machine Learning Studio, você pode usar qualquer experimento na Galeria como um modelo para criar um novo experimento.

### <a name="from-the-gallery"></a>Da Galeria

1. Na Galeria, abra a página de detalhes do experimento.
2. Selecione **Abrir no Studio**.

    ![Abrir um experimento da Galeria](./media/gallery-experiments/open-experiment-from-gallery.png)

Quando você seleciona **Abrir no Studio**, o experimento é aberto em seu espaço de trabalho do Machine Learning Studio. (Se você ainda não estiver conectado ao Machine Learning Studio, será necessário primeiro entrar usando sua conta da Microsoft.)

### <a name="from-within-machine-learning-studio"></a>Do Machine Learning Studio

1. No Machine Learning Studio, selecione **NOVO**.
2. Selecione **Experimento**. Você pode escolher de uma lista de experimentos da Galeria ou encontrar um experimento específico usando a caixa **Pesquisa**.
3. Aponte o mouse para um experimento e selecione **Abrir no Studio**. (Para ver informações sobre o experimento, selecione **Exibir na Galeria**. Isso levará você para a página de detalhes do experimento na Galeria.)

    ![Abrir experimento da Galeria de dentro do Machine Learning Studio](./media/gallery-experiments/open-experiment-from-studio.png)

Você poderá personalizar, iterar e implantar um experimento como qualquer outro experimento criado no Machine Learning Studio.

![Experimento aberto no Studio](./media/gallery-experiments/experiment-open-in-studio.png)

## <a name="contribute"></a>Contribuir
Ao entrar na Galeria, você vira um membro da comunidade da Galeria. Como um membro dessa comunidade, você pode contribuir com seus próprios experimentos, para que outros usuários possam usufruir das soluções que você descobriu.

### <a name="publish-your-experiment-to-the-gallery"></a>Publicar seu experimento na Galeria

1. Entre no Machine Learning Studio usando sua conta da Microsoft.
2. Crie seu experimento e execute-o.
3. Quando você estiver pronto para publicar seu experimento na Galeria, selecione **Publicar na Galeria** abaixo da tela do experimento.

    ![Selecione “Publicar na Galeria”](./media/gallery-experiments/publish-experiment-to-gallery.png)
4. Na página **Descrição do Experimento**, insira um título e marcas. Use um título e marcas descritivos. Destaque as técnicas usadas ou o problema real resolvido. Um exemplo de um título de experimento descritivo é “Classificação binária: Análise de Sentimento do Twitter”.

    ![Insira o título e marcas para publicação](./media/gallery-experiments/experiment-description.png)
5. Na caixa **RESUMO**, digite um resumo do seu experimento. Descreva resumidamente o problema que o experimento resolve e como você o abordou.
6. Na caixa **DESCRIÇÃO DETALHADA**, descreva as etapas executadas em cada parte do seu experimento. Alguns tópicos úteis a serem incluídos são:
   * Captura de tela de gráfico do experimento
   * Explicação e fontes de dados
   * Processamento de dados
   * Engenharia de recursos
   * Descrição do modelo
   * Resultados e avaliação de desempenho do modelo

   Você pode usar o Markdown para formatar sua descrição. Para ver qual será a aparência das suas entradas na página de descrição do experimento quando ele for publicado, selecione **Visualização**.

   ![Selecione “Visualizar” para ver a aparência de seu texto](./media/gallery-experiments/preview-markdown-text.png)

   > [!TIP]
   > A caixa fornecida para a edição e visualização de Markdown são pequenas. Recomendamos que você escreva a documentação do experimento em um editor de Markdown, copie e cole o documento concluído na caixa de texto na Galeria. Depois de publicar seu experimento, você poderá fazer correções usando ferramentas Web padrão que usam o Markdown para editar e visualizar.

7. Na página **Seleção de Imagem**, escolha uma imagem em miniatura para o seu experimento. A imagem em miniatura é exibida na parte superior da página de detalhes do experimento e no bloco do experimento. Outros usuários poderão ver a imagem em miniatura quando navegarem na Galeria. Você pode carregar uma imagem do seu computador ou selecionar uma imagem de estoque da Galeria.
    </br>
    ![Carregar ou selecionar uma imagem da Galeria](./media/gallery-experiments/select-gallery-image.png)
8. Na página **Configurações**, em **Visibilidade**, opte por publicar seu conteúdo publicamente (**Público**) ou que ele seja acessível somente a pessoas que têm um link para a página (**Não listado**).

    ![Escolha se deseja publicar publicamente ou como não listado](./media/gallery-experiments/choose-public-or-unlisted.png)

    <!-- -->

   > [!TIP]
   > Se quiser verificar se a documentação está correta antes de lançá-lo publicamente, primeiro você poderá publicar o experimento como **Não listado**. Posteriormente, você poderá alterar a configuração de visibilidade para **Pública** na página de detalhes do experimento.
   >
   >
9. Para publicar o experimento na Galeria, selecione a marca de seleção **OK**.

    ![Selecione a marca de seleção OK para publicar o experimento](./media/gallery-experiments/ok-checkmark.png)

Para ver dicas sobre como publicar um experimento de alta qualidade na Galeria, consulte [Dicas para documentar e publicar seu experimento](#tips-for-documenting-and-publishing-your-experiment).

Tudo pronto.

Agora você pode exibir seu experimento na Galeria e compartilhar o link com outras pessoas. Se você publicou seu experimento usando a configuração de visibilidade **Público**, o experimento aparecerá nos resultados de navegação e pesquisa na Galeria. É possível editar a documentação do experimento na página de detalhes do experimento sempre que você entrar na Galeria.

Para ver a lista das suas contribuições, selecione a imagem no canto superior direito de qualquer página da Galeria. Em seguida, selecione seu nome para abrir a página da sua conta.

![Selecione o nome da conta](./media/gallery-experiments/click-account-name.png)

### <a name="update-your-experiment"></a>Atualize seu experimento
Se necessário, você pode fazer alterações no fluxo de trabalho (módulos, parâmetros e assim por diante) em um experimento que você publicou na Galeria. No Machine Learning Studio, faça as alterações desejadas no experimento e publique-o novamente. Seu experimento publicado será atualizado com as alterações.

Você pode alterar qualquer uma das seguintes informações para o seu experimento diretamente na Galeria:

* Nome do experimento
* Resumo ou descrição
* Marcas
* Imagem
* Configuração de visibilidade (**Público** ou **Não listado**)

Você também pode excluir o experimento da Galeria.

Você pode fazer essas alterações, ou excluir o experimento, na página de detalhes do experimento ou na página do seu perfil na Galeria.


#### <a name="from-the-experiment-details-page"></a>Na página de detalhes do experimento
Na página de detalhes do experimento, clique em **Editar** para alterar os detalhes do seu experimento.

![Selecione Editar para editar o experimento](./media/gallery-experiments/edit-button.png)

A página de detalhes entra em modo de edição. Para fazer alterações, selecione **Editar** ao lado do nome, resumo ou marcas do experimento. Quando você terminar de fazer alterações, clique em **Concluído**.

![Clique em “Editar” para editar os detalhes e selecione “Concluído” quando terminar](./media/gallery-experiments/edit-details-page.png)

Para alterar as configurações de visibilidade para o experimento (**Público** ou **Não listado**) para excluí-lo da Galeria, selecione o ícone **Configurações**.

![Selecione “Configurações” para alterar a visibilidade ou excluir o experimento](./media/gallery-experiments/settings-button.png)

#### <a name="from-your-profile-page"></a>Na página do perfil
Na página do seu perfil, selecione a seta para baixo no experimento e escolha **Editar**. Isso levará você até a página de detalhes do experimento, no modo de edição. Quando terminar de fazer alterações, selecione **Concluído**.

Para excluir o experimento da Galeria, selecione **Excluir**.

![Selecione “Editar” ou “Excluir”](./media/gallery-experiments/edit-delete-buttons.png)

### <a name="tips-for-documenting-and-publishing-your-experiment"></a>Dicas para documentar e publicar seu experimento
* Você pode supor que o leitor esteja familiarizado com ciência de dados, porém pode ser útil usar uma linguagem simples. Explique as coisas detalhadamente sempre que possível.
* O Cortana Intelligence Suite é relativamente novo. Nem todos os leitores estão familiarizados com seu uso. Forneça informações e explicações detalhadas suficientes para ajudar os leitores a navegar por seu experimento.
* Elementos visuais podem ser úteis para os leitores interpretarem e usarem a documentação do seu experimento corretamente. Elementos visuais incluem elementos gráficos do experimento e capturas de tela de dados. Para obter mais informações sobre como incluir imagens na documentação do experimento, consulte o [Diretrizes de publicação e coleção de exemplos](https://gallery.cortanaintelligence.com/Collection/Publishing-Guidelines-and-Examples-1).
* Se você incluir um conjunto de dados em seu experimento (ou seja, não importar o conjunto de dados por meio do módulo Importar Dados), o conjunto de dados fará parte do seu experimento e será publicado na Galeria. Verifique se o conjunto de dados que você publicou tem os termos de licença que permitem compartilhamento e download por qualquer pessoa. As contribuições à Galeria são tratadas nos [Termos de Uso](https://azure.microsoft.com/support/legal/website-terms-of-use/)do Azure.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
**Quais são os requisitos para enviar ou editar uma imagem para meu experimento?**

As imagens que você enviar com seu experimento serão usadas para criar um bloco de experimento para sua contribuição. Recomendamos que as imagens sejam menores do que 500 KB, com uma taxa de proporção de 3:2 e uma resolução de 960 &#215; 640.

**O que acontece com o conjunto de dados que usei no experimento? O conjunto de dados também é publicado na Galeria?**

Se o conjunto de dados fizer parte se seu experimento e não tiver sido importado por meio do módulo Importar Dados, ele será publicado na Galeria como parte do experimento. Verifique se o conjunto de dados publicado com o experimento tem os termos de licenciamento apropriados. Os termos de licenciamento devem permitir que qualquer pessoa compartilhe e baixe os dados.

**Tenho um experimento que usa um módulo Importar Dados para obter dados do Azure HDInsight ou do SQL Server. Ele usa minhas credenciais para recuperar os dados. Posso publicar esse tipo de experimento? Como pode posso ter certeza de que minhas credenciais não serão compartilhadas?**

No momento, não é possível publicar um experimento que usa credenciais na Galeria.

**Como inserir várias marcas?**

Depois de inserir uma marcação, pressione a tecla Tab para inserir outra marca.

**[Vá para a Galeria](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
