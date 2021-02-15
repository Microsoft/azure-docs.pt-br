---
title: Integrar Data Lake Storage Gen1 com o catálogo de dados do Azure
description: Saiba como registrar dados de Azure Data Lake Storage Gen1 no catálogo de dados do Azure para tornar os dados detectáveis em sua organização.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 02544489816f5711ca6e599c2bce03737c747934
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106613"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Registrar dados do Azure Data Lake Storage Gen1 no Catálogo de Dados do Azure
Neste artigo, você aprenderá como integrar o Azure Data Lake Storage Gen1 com Catálogo de Dados do Azure para tornar os dados detectáveis em uma organização, integrando-os ao Catálogo de Dados. Para obter mais informações sobre a catalogação de dados, consulte [Catálogo de Dados do Azure](../data-catalog/overview.md). Para compreender os cenários em que você pode usar o Catálogo de Dados, consulte [Cenários comuns do Catálogo de Dados do Azure](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Habilitar sua assinatura do Azure** para Data Lake Storage Gen1. Consulte [as instruções](data-lake-store-get-started-portal.md).
* **Uma conta do Data Lake Storage Gen1**. Siga as instruções em [Introdução ao Azure Data Lake Storage Gen1 usando o portal do Azure](data-lake-store-get-started-portal.md). Para este tutorial, criaremos uma conta do Data Lake Storage Gen1 chamada **datacatalogstore**.

    Depois de criar a conta, carregue um conjunto de dados de exemplo para ela. Para este tutorial, vamos carregar todos os arquivos .csv para na pasta **AmbulanceData** para o [Repositório Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Você pode usar vários clientes, como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/), para carregar dados em um contêiner de blob.
* **Catálogo de dados do Azure**. Sua organização já deve ter um Catálogo de Dados do Azure criados para sua organização. É permitido somente um catálogo para cada organização.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Registrar o Data Lake Storage Gen1 como uma fonte para o Catálogo de Dados

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Vá para `https://azure.microsoft.com/services/data-catalog`e clique em **Introdução**.
1. Faça logon no portal do Catálogo de Dados do Azure e clique em **Publicar dados**.

    ![Registrar uma fonte de dados](./media/data-lake-store-with-data-catalog/register-data-source.png "Registrar uma fonte de dados")
1. Na página seguinte, clique em **Iniciar Aplicativo**. Isso baixará o arquivo de manifesto do aplicativo no seu computador. Clique duas vezes no arquivo de manifesto para iniciar o aplicativo.
1. Na página de boas-vindas, clique em **entrar**e insira suas credenciais.

    ![Tela de boas-vindas](./media/data-lake-store-with-data-catalog/welcome.screen.png "Tela de boas-vindas")
1. Na página Selecionar uma Fonte de Dados, selecione **Azure Data Lake Store**e, em seguida, clique em **Avançar**.

    ![Selecionar fonte de dados](./media/data-lake-store-with-data-catalog/select-source.png "Selecione uma fonte de dados")
1. Na próxima página, forneça o nome da conta do Data Lake Storage Gen1 que você quer registrar no Catálogo de Dados. Deixe as outras opções como padrão e clique em **Conectar**.

    ![Conectar-se a uma fonte de dados](./media/data-lake-store-with-data-catalog/connect-to-source.png "Conectar-se a uma fonte de dados")
1. A próxima página pode ser dividida nos seguintes segmentos.

    a. A caixa **Hierarquia do Servidor** representa a estrutura da pasta da conta do Data Lake Storage Gen1. **$Root** representa a raiz da conta do Data Lake Storage Gen1 e **AmbulanceData** representa a pasta criada na raiz da conta do Data Lake Storage Gen1.

    b. A caixa **Objetos disponíveis** lista os arquivos e pastas na pasta **AmbulanceData**.

    c. A caixa **objetos a serem registrados** lista os arquivos e pastas que você deseja registrar no catálogo de dados do Azure.

    ![Captura de tela da caixa de diálogo Microsoft Azure conta do repositório de dados.](./media/data-lake-store-with-data-catalog/view-data-structure.png "Exibir estrutura de dados")
1. Para este tutorial, você deve registrar todos os arquivos no diretório. Para fazer isso, clique no botão (![mover objetos](./media/data-lake-store-with-data-catalog/move-objects.png "Mover objetos")) para mover todos os arquivos para a caixa **Objetos a serem registrados** .

    Como os dados serão registrados em um catálogo de dados de toda a organização, a abordagem recomendada é adicionar alguns metadados que você possa usar posteriormente para localizar rapidamente os dados. Por exemplo, você pode adicionar um endereço de email para o proprietário dos dados (por exemplo, que está carregando os dados) ou adicionar uma marca para identificar os dados. A captura de tela abaixo mostra uma marcação que você adicionou aos dados.

    ![Captura de tela da caixa de diálogo Microsoft Azure conta do repositório de dados com a marca que foi adicionada aos dados chamados.](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Exibir estrutura de dados")

    Clique em **Registrar**.
1. A captura de tela a seguir indica que os dados foram registrados com êxito no Catálogo de Dados.

    ![Registro concluído](./media/data-lake-store-with-data-catalog/registration-complete.png "Exibir estrutura de dados")
1. Clique em **Exibir Portal** para voltar ao portal do Catálogo de Dados e verificar se agora você pode acessar os dados registrados no portal. Para pesquisar os dados, você pode usar a marca que usada ao registrar os dados.

     ![Pesquisar dados no catálogo](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Pesquisar dados no catálogo")
1. Agora você pode executar operações como adicionar anotações e documentação aos dados. Para obter mais informações, consulte os links a seguir.

    * [Anotar fontes de dados no Catálogo de Dados](../data-catalog/data-catalog-how-to-annotate.md)
    * [Documentar fontes de dados no Catálogo de Dados](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Veja também
* [Anotar fontes de dados no Catálogo de Dados](../data-catalog/data-catalog-how-to-annotate.md)
* [Documentar fontes de dados no Catálogo de Dados](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrar Data Lake Storage Gen1 com outros serviço do Azure](data-lake-store-integrate-with-other-services.md)