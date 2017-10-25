---
title: Analisar dados no Data Lake Store usando o Power BI | Microsoft Docs
description: "Usar o Power BI para analisar os dados armazenados no Repositório Azure Data Lake"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 32ae9949806e0cb762b87cb739f94ddf3704c2ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Analisar dados no Repositório Data Lake usando o Power BI
Neste artigo, você aprenderá a usar o Power BI Desktop para analisar e visualizar dados armazenados no Repositório Azure Data Lake.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Conta do Repositório Azure Data Lake**. Siga as instruções em [Introdução ao Repositório Azure Data Lake usando o Portal do Azure](data-lake-store-get-started-portal.md). Este artigo pressupõe que você já tenha criado uma conta do Data Lake Store chamada **mybidatalakestore** e carregado um arquivo de dados de exemplo (**Drivers.txt**) para ela. Esse exemplo de arquivo está disponível para download no [Repositório Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Você pode baixá-lo no [Centro de Download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop
1. Inicie o Power BI Desktop em seu computador.
2. Na faixa de opções **Início**, clique em **Obter Dados** e clique em Mais. Na caixa de diálogo **Obter Dados**, clique em **Azure**, clique em **Azure Data Lake Store** e clique em **Conectar**.
   
    ![Conectar-se ao Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Conectar ao Data Lake Store")
3. Se você vir uma caixa de diálogo informando que o conector está em uma fase de desenvolvimento, escolha continuar.
4. Na caixa de diálogo **Microsoft Azure Data Lake Store**, forneça a URL da sua conta do Data Lake Store e clique em **OK**.
   
    ![URL do Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL para Data Lake Store")
5. Na caixa de diálogo seguinte, clique em **Entrar** para entrar na conta do Repositório Data Lake. Você será redirecionado à página de logon de sua organização. Siga os prompts para entrar na conta.
   
    ![Entrar no Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Entrar no Data Lake Store")
6. Depois de entrar com sucesso, clique em **Conectar**.
   
    ![Conectar-se ao Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Conectar-se ao Data Lake Store")
7. A próxima caixa de diálogo mostra o arquivo carregado para sua conta do Repositório Data Lake. Verifique as informações e clique em **Carregar**.
   
    ![Carregar dados no Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Carregar dados no Data Lake Store")
8. Após os dados serem carregados com êxito no Power BI, você verá os seguintes campos na guia **Campos** .
   
    ![Importar campos](./media/data-lake-store-power-bi/imported-fields.png "importado campos")
   
    No entanto, para visualizar e analisar os dados, preferimos que eles fiquem disponíveis conforme os seguintes campos
   
    ![Desejado campos](./media/data-lake-store-power-bi/desired-fields.png "desejado campos")
   
    Nas próximas etapas, atualizaremos a consulta para converter os dados importados no formato desejado.
9. Na faixa de opções **Início**, clique em **Editar Consultas**.
   
    ![Editar consultas](./media/data-lake-store-power-bi/edit-queries.png "editar consultas")
10. No Editor de Consultas, na coluna **Conteúdo**, clique em **Binário**.
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query1.png "editar consultas")
11. Você verá um ícone de arquivo que representa o arquivo **Drivers.txt** carregado. Clique no arquivo com o botão direito do mouse e clique em **CSV**.    
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query2.png "editar consultas")
12. Você deve ver uma saída parecida com a que está abaixo. Os dados agora estão disponíveis em um formato que você pode usar para criar visualizações.
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query3.png "editar consultas")
13. Na faixa de opções **Início**, clique em **Fechar e Aplicar** e, em seguida, clique em **Fechar e Aplicar**.
    
    ![Editar consultas](./media/data-lake-store-power-bi/load-edited-query.png "editar consultas")
14. Depois que a consulta for atualizada, a guia **Campos** mostrará os novos campos disponíveis para visualização.
    
    ![Atualizado campos](./media/data-lake-store-power-bi/updated-query-fields.png "atualizado campos")
15. Vamos criar um gráfico de pizza para representar os drivers em cada cidade de um determinado país. Para fazer isso, faça as seleções a seguir.
    
    1. Na guia Visualizações, clique no símbolo do gráfico de pizza.
       
        ![Criar um gráfico de pizza](./media/data-lake-store-power-bi/create-pie-chart.png "criar o gráfico de pizza")
    2. As colunas que vamos usar são a **Coluna 4** (nome da cidade) e a **Coluna 7** (nome do país). Arraste essas colunas da guia **Campos** para a guia **Visualizações** conforme mostrado abaixo.
       
        ![Criar visualizações](./media/data-lake-store-power-bi/create-visualizations.png "Criar visualizações")
    3. O gráfico de pizza agora deve ser semelhante ao mostrado abaixo.
       
        ![Gráfico de pizza](./media/data-lake-store-power-bi/pie-chart.png "Criar visualizações")
16. Ao selecionar um país específico nos filtros de nível de página, você poderá ver o número de drivers em cada cidade do país selecionado. Por exemplo, na guia **Visualizações**, em **Filtros de nível de página**, escolha **Brasil**.
    
    ![Selecione um país](./media/data-lake-store-power-bi/select-country.png "selecione um país")
17. O gráfico de pizza é atualizado automaticamente para exibir os drivers em cidades do Brasil.
    
    ![Drivers em um país](./media/data-lake-store-power-bi/driver-per-country.png "Drivers por país")
18. No menu **Arquivo**, clique em **Salvar** para salvar a visualização como um arquivo do Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publicar o relatório no serviço Power BI
Depois de criar as visualizações no Power BI Desktop, compartilhe-as com outras pessoas publicando-as no serviço Power BI. Para obter instruções sobre como fazer isso, confira [Publicar a partir do Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Confira também
* [Analisar dados no Repositório Data Lake usando o Análise Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

