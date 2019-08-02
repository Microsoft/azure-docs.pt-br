---
title: Analisar dados em Azure Data Lake Storage Gen2 usando Power BI | Microsoft Docs
description: Use Power BI para analisar os dados armazenados no Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 07/18/2019
ms.author: normesta
ms.openlocfilehash: ea8ceddbcbc26b2dfad5ac0618b30cb8bdc052e7
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336459"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen2-by-using-power-bi"></a>Analisar dados em Azure Data Lake Storage Gen2 usando Power BI

Neste artigo, você aprenderá a usar Power BI Desktop para analisar e Visualizar dados armazenados em uma conta de armazenamento que tem um namespace hierárquico (Azure Data Lake Storage Gen2).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

> [!div class="checklist"]
> * Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem um namespace hierárquico. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> Este artigo pressupõe que você criou uma conta chamada `myadlsg2`.
> * Um arquivo de dados de `Drivers.txt` exemplo chamado localizado na sua conta de armazenamento.
> Você pode baixar esse exemplo de [Azure data Lake repositório git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceDataDrivers.txt)e, em seguida, carregar esse arquivo em sua conta de armazenamento.
> * **Power BI Desktop**. Você pode baixá-lo no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop

1. Inicie o Power BI Desktop em seu computador.
2. Na guia **início** da faixa de faixas, clique em **obter dados**e em **mais**.
3. Na caixa de diálogo **obter dados** , clique em **Azure**, clique em **Azure data Lake Store Gen2 (beta)** e, em seguida, clique em **conectar**.

    ![Página obter dados](media/data-lake-storage-use-power-bi/get-data-page.png)

4. Na caixa de diálogo **Azure data Lake Storage Gen2** , você pode fornecer a URL para sua conta de Azure data Lake Storage Gen2, sistema de arquivos ou subpasta usando o formato de ponto de extremidade do sistema de arquivos. As URLs para data Lake Storage Gen2 têm o seguinte `https://<accountname>.dfs.core.windows.net/<filesystemname>/<subfolder>` padrão e, em seguida, clicam em **OK**.

    ![URL](media/data-lake-storage-use-power-bi/adls-url.png)

5. Na próxima caixa de diálogo, clique em **entrar** para entrar na sua conta de armazenamento. Você será redirecionado para a página de entrada da sua organização. Siga os prompts para entrar na conta.

    ![Página de entrada](media/data-lake-storage-use-power-bi/sign-in.png)

6. Depois de entrar com êxito, clique em **conectar**.

    ![Página conectada](media/data-lake-storage-use-power-bi/signed-in.png)

7. A próxima caixa de diálogo mostra todos os arquivos sob a URL fornecida na etapa 4 acima, incluindo o arquivo que você carregou em sua conta de armazenamento. Verifique as informações e clique em **carregar**.

    ![Sistemas de arquivos](media/data-lake-storage-use-power-bi/file-systems.png)

8. Depois que os dados tiverem sido carregados com êxito no Power BI, você verá os campos a seguir na guia **campos** .

    ![Guia campos](media/data-lake-storage-use-power-bi/fields.png)

    No entanto, para visualizar e analisar os dados, preferimos que os dados estejam disponíveis de acordo com os campos a seguir.

    ![Campos](media/data-lake-storage-use-power-bi/preferred-fields.png)

    Nas próximas etapas, atualizaremos a consulta para converter os dados importados no formato desejado.

9. Na guia **início** da faixa de faixas, clique em **editar consultas**.

    ![Consultas](media/data-lake-storage-use-power-bi/queries.png)

10. No **Editor de consultas**, na coluna **conteúdo** , clique em **binário**. O arquivo será detectado automaticamente como CSV e você deverá ver uma saída, conforme mostrado abaixo. Os dados agora estão disponíveis em um formato que você pode usar para criar visualizações.

    ![Saída](media/data-lake-storage-use-power-bi/binary.png)

11. Na guia **início** da faixa de opções, clique em **fechar** e **aplicar**e, em seguida, clique em **fechar** e **aplicar**.

    ![Fechar e aplicar](media/data-lake-storage-use-power-bi/close-apply.png)

12. Depois que a consulta for atualizada, a guia **Campos** mostrará os novos campos disponíveis para visualização.

    ![Novos campos](media/data-lake-storage-use-power-bi/new-fields.png)

13. Vamos criar um gráfico de pizza para representar os drivers em cada cidade de um determinado país. Para fazer isso, faça as seleções a seguir.

    Na guia **visualizações** , clique no símbolo de um gráfico de pizza.

    ![Visualizações](media/data-lake-storage-use-power-bi/visualizations.png)

    As colunas que vamos usar são a coluna 4 (nome da cidade) e a coluna 7 (nome do país). Arraste essas colunas da guia **Campos** para a guia **Visualizações** conforme mostrado abaixo.

    ![Arrastar campos](media/data-lake-storage-use-power-bi/visualizations-drag-fields.png)

    O gráfico de pizza agora deve ser semelhante ao mostrado abaixo.

    ![Gráfico de pizza](media/data-lake-storage-use-power-bi/pie-chart.png)

14. Ao selecionar um país específico nos filtros de nível de página, você poderá ver o número de drivers em cada cidade do país selecionado. Por exemplo, na guia **Visualizações**, em **Filtros de nível de página**, escolha **Brasil**.

    ![Filtros de página](media/data-lake-storage-use-power-bi/page-filters.png)

15. O gráfico de pizza é atualizado automaticamente para exibir os drivers em cidades do Brasil.

    ![Brasil](media/data-lake-storage-use-power-bi/pie-chart-updated.png)

16. No menu **Arquivo**, clique em **Salvar** para salvar a visualização como um arquivo do Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publicar o relatório no serviço Power BI

Depois de criar as visualizações no Power BI Desktop, você pode compartilhá-las com outras pessoas publicando-as no serviço do Power BI. Para obter instruções sobre como fazer isso, confira [Publicar a partir do Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).