---
title: Analisar dados no Azure Data Lake Storage Gen1 – Power BI
description: Saiba como usar Power BI Desktop para analisar e Visualizar dados armazenados no Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bcc561cd5eea4372d798fff4580362ba0879c3a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91574180"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Analisar dados no Azure Data Lake Storage Gen1 usando Power BI
Neste artigo, você aprenderá como usar o Power BI Desktop para analisar e visualizar dados armazenados no Azure Data Lake Storage Gen1.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Data Lake Storage Gen1**. Siga as instruções em [Introdução ao Azure Data Lake Storage Gen1 usando o portal do Azure](data-lake-store-get-started-portal.md). Este artigo pressupõe que você já criou uma conta do Data Lake Storage Gen1, chamada **myadlsg1** e carregou um arquivo de dados de exemplo (**Drivers.txt**) nela. Esse exemplo de arquivo está disponível para download no [Repositório Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Você pode baixá-lo no [Centro de Download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop
1. Inicie o Power BI Desktop em seu computador.
2. Na faixa de opções **Início**, clique em **Obter Dados** e clique em Mais. Na caixa de diálogo **Obter Dados**, clique em **Azure**, clique em **Azure Data Lake Store** e clique em **Conectar**.
   
    ![Captura de tela da caixa de diálogo obter dados com a opção Azure Data Lake Store realçada e a opção de conexão chamada out.](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Conectar-se ao Data Lake Storage Gen1")
3. Se você vir uma caixa de diálogo informando que o conector está em uma fase de desenvolvimento, escolha continuar.
4. Na caixa de diálogo **Azure Data Lake Store** forneça a URL para a conta do Data Lake Storage Gen1 e, em seguida, clique em **OK**.
   
    ![URL para Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL para Data Lake Storage Gen1")
5. Na próxima caixa de diálogo, clique em **Entrar** para entrar na conta do Data Lake Storage Gen1. Você será redirecionado à página de entrada de sua organização. Siga os prompts para entrar na conta.
   
    ![Entrar no Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Entrar no Data Lake Storage Gen1")
6. Depois de entrar com sucesso, clique em **Conectar**.
   
    ![Captura de tela da caixa de diálogo Azure Data Lake Store com a opção Connect chamada out.](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Conectar-se ao Data Lake Storage Gen1")
7. A próxima caixa de diálogo mostra o arquivo que você carregou para a conta do Data Lake Storage Gen1. Verifique as informações e clique em **Carregar**.
   
    ![Carregar dados do Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Carregar dados do Data Lake Storage Gen1")
8. Após os dados serem carregados com êxito no Power BI, você verá os seguintes campos na guia **Campos** .
   
    ![Campos importados](./media/data-lake-store-power-bi/imported-fields.png "Campos importados")
   
    No entanto, para visualizar e analisar os dados, preferimos que eles fiquem disponíveis conforme os seguintes campos
   
    ![Campos desejados](./media/data-lake-store-power-bi/desired-fields.png "Campos desejados")
   
    Nas próximas etapas, atualizaremos a consulta para converter os dados importados no formato desejado.
9. Na faixa de opções **Início**, clique em **Editar Consultas**.
   
    ![Captura de tela da faixa de opções página inicial com a opção editar consultas chamada out.](./media/data-lake-store-power-bi/edit-queries.png "Editar Consultas")
10. No Editor de Consultas, na coluna **Conteúdo**, clique em **Binário**.
    
    ![Captura de tela do editor de consultas com a coluna de conteúdo chamada out.](./media/data-lake-store-power-bi/convert-query1.png "Editar Consultas")
11. Você verá um ícone de arquivo que representa o arquivo **Drivers.txt** carregado. Clique no arquivo com o botão direito do mouse e clique em **CSV**.    
    
    ![Captura de tela do editor de consultas com a opção CSV chamada out.](./media/data-lake-store-power-bi/convert-query2.png "Editar Consultas")
12. Você deve ver uma saída parecida com a que está abaixo. Os dados agora estão disponíveis em um formato que você pode usar para criar visualizações.
    
    ![Captura de tela do editor de consultas com a saída exibida como esperado.](./media/data-lake-store-power-bi/convert-query3.png "Editar Consultas")
13. Na faixa de opções **Início**, clique em **Fechar e Aplicar** e, em seguida, clique em **Fechar e Aplicar**.
    
    ![Captura de tela da faixa de opções página inicial com a opção fechar e aplicar chamada.](./media/data-lake-store-power-bi/load-edited-query.png "Editar Consultas")
14. Depois que a consulta for atualizada, a guia **Campos** mostrará os novos campos disponíveis para visualização.
    
    ![Campos atualizados](./media/data-lake-store-power-bi/updated-query-fields.png "Campos atualizados")
15. Vamos criar um gráfico de pizza para representar os drivers em cada cidade para determinado país/região. Para fazer isso, faça as seleções a seguir.
    
    1. Na guia Visualizações, clique no símbolo do gráfico de pizza.
       
        ![Criar gráfico de pizza](./media/data-lake-store-power-bi/create-pie-chart.png "Criar um gráfico de pizza")
    2. As colunas que vamos usar são **Coluna 4** (nome da cidade) e **Coluna 7** (nome do país/região). Arraste essas colunas da guia **Campos** para a guia **Visualizações** conforme mostrado abaixo.
       
        ![Criar visualizações](./media/data-lake-store-power-bi/create-visualizations.png "Criar visualizações")
    3. O gráfico de pizza agora deve ser semelhante ao mostrado abaixo.
       
        ![Gráfico de pizza](./media/data-lake-store-power-bi/pie-chart.png "Criar visualizações")
16. Ao selecionar um país/região específico nos filtros de nível de página, você poderá ver o número de drivers em cada cidade do país/região selecionado. Por exemplo, na guia **Visualizações**, em **Filtros de nível de página**, escolha **Brasil**.
    
    ![Selecionar um país/região](./media/data-lake-store-power-bi/select-country.png "Selecionar um país/região")
17. O gráfico de pizza é atualizado automaticamente para exibir os drivers em cidades do Brasil.
    
    ![Drivers em um país/região](./media/data-lake-store-power-bi/driver-per-country.png "Drivers por país/região")
18. No menu **Arquivo**, clique em **Salvar** para salvar a visualização como um arquivo do Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publicar o relatório no serviço Power BI
Depois de criar as visualizações no Power BI Desktop, compartilhe-as com outras pessoas publicando-as no serviço Power BI. Para obter instruções sobre como fazer isso, confira [Publicar a partir do Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Confira também
* [Analisar dados no Data Lake Storage Gen1 usando Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

