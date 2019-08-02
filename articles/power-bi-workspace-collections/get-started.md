---
title: Introdução às Coleções de Workspaces do Microsoft Power BI
description: As Coleções de Workspaces do Power BI são um serviço do Azure que permite que os desenvolvedores de aplicativos adicionem relatórios interativos do Power BI a seus próprios aplicativos.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: 2b3381166e40f6d9586306e351b5f3ea77cc2ad8
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669137"
---
# <a name="get-started-with-microsoft-power-bi-workspace-collections"></a>Introdução às Coleções de Workspaces do Microsoft Power BI

As **Coleções de Workspaces do Power BI** são um serviço do Azure que permite que os desenvolvedores de aplicativos adicionem relatórios interativos do Power BI a seus próprios aplicativos. As **Coleções de Workspaces do Power BI** funcionam com aplicativos existentes sem precisar reprojetar ou alterar os maneira como os usuários entram.

> [!IMPORTANT]
> As Coleções de Workspaces do Power BI foram preteridas e só estarão disponíveis até junho de 2018 ou conforme a indicação do seu contrato. Recomendamos planejar a migração para o Power BI Embedded a fim de evitar interrupções em seu aplicativo. Para saber mais sobre como migrar seus dados para o Power BI Embedded, confira [Como migrar o conteúdo das Coleções de Workspaces do Power BI para o Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Os recursos para **Coleções de Workspaces do Microsoft Power BI** são provisionados pelas [APIs do Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). Nesse caso, o recurso provisionado é uma **Coleção de Workspaces do Power BI**.

![Fluxo geral das Coleções de Workspaces do Power BI](media/get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Criar uma coleção de workspaces

Uma **Coleção de Workspaces** é o recurso mais avançado do Azure e um contêiner para o conteúdo que será inserido em seu aplicativo. Uma **Coleção de Workspaces** pode ser criada de duas maneiras:

* Manualmente, usando o Portal do Azure
* Programaticamente, usando as APIs Azure Resource Manager

Vamos percorrer as etapas para criar uma **Coleção de Workspaces** usando o portal do Azure.

1. Abra e entre no **portal do Azure**: [https://portal.azure.com](https://portal.azure.com).
2. Selecione **+ Novo** no painel superior.
   
   ![+ Novo no portal do Azure](media/get-started/create-workspace-1.png)
3. Em **Dados + Análise**, selecione **Coleção de Workspaces do Power BI**.
4. Na mensagem de introdução, se você já tem uma assinatura existente da Coleção de Workspaces do Power BI, selecione **Criar uma coleção de workspaces** na parte inferior.

5. Em **Coleção de Workspaces**, insira as informações necessárias.
   
   ![Criação da coleção de workspaces](media/get-started/create-workspace-2.png)
1. Selecione **Criar**.

A **Coleção de workspaces** leva alguns minutos para ser provisionada. Quando concluído, você será direcionado para a **Coleção de Workspaces**.

   ![Coleção de workspaces no portal do Azure](media/get-started/create-workspace-3.png)

Os resultados da **Criação** contêm as informações necessárias para chamar as APIs que criam workspaces e implantam conteúdo nelas.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Exibir chaves de acesso da API do Power BI

Uma das informações mais importantes necessárias para chamar as APIs REST do Power BI são as **Chaves de Acesso**. Elas são usadas para gerar os **tokens do aplicativo** que são usados na autenticação das solicitações de API. Para exibir suas **Chaves de Acesso**, clique em **Chaves de Acesso** em **Configurações**. Para saber mais sobre **tokens de aplicativo**, confira [Autenticando e autorizando com as Coleções de Workspaces do Power BI](app-token-flow.md).

   ![Chaves de acesso nas configurações de Coleção de Workspaces no portal do Azure](media/get-started/access-keys.png)

Você notará que tem duas chaves.

   ![Duas chaves nas Chaves de acesso](media/get-started/access-keys-2.png)

Copie essas chaves e armazene-as com segurança em seu aplicativo. É importante tratar essas chaves como faria com uma senha, pois elas dão acesso a todo o conteúdo em sua **Coleção de Workspaces**.

Embora duas chaves estejam listadas, somente uma chave é usada de cada vez. A segunda chave é fornecida para regenerar as chaves periodicamente sem interromper o acesso ao serviço.

Agora que você tem uma instância do Power BI para seu aplicativo e as **Chaves de Acesso**, pode importar um relatório em seu próprio aplicativo. Antes de aprender como importar um relatório, a próxima seção descreve a criação de relatórios e conjuntos de dados do Power BI para inserir em um aplicativo.

## <a name="working-with-workspaces"></a>Trabalhando com workspaces

Depois que você criou sua coleção de workspaces, você precisará criar um workspace que conterá seus relatórios e conjuntos de dados. Para criar um workspace, você precisará usar a [API REST de Postagem do Workspace](https://msdn.microsoft.com/library/azure/mt711503.aspx).

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Criar relatórios e conjuntos de dados do Power BI para inserir em um aplicativo usando o Power BI Desktop

Agora que você criou uma instância do Power BI para seu aplicativo e tem **Chaves de Acesso**, precisará criar os relatórios e conjuntos de dados do Power BI que deseja inserir. Conjuntos de dados e relatórios podem ser criados usando o **Power BI Desktop**. Você pode baixar o [Power BI Desktop gratuitamente](https://go.microsoft.com/fwlink/?LinkId=521662). Ou, para começar rapidamente, você pode baixar o [Retail Analysis Sample PBIX](https://go.microsoft.com/fwlink/?LinkID=780547)(PBIX de exemplo de análise de varejo).

> [!NOTE]
> Para saber mais sobre como usar o **Power BI Desktop**, confira [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Com o **Power BI Desktop**, você se conecta à fonte de dados importando uma cópia dos dados para o **Power BI Desktop** ou se conectando diretamente à fonte de dados usando o **DirectQuery**.

Estas são as diferenças entre o uso de **Importar** e **DirectQuery**.

| Importar | DirectQuery |
| --- | --- |
| Tabelas, colunas, *e dados* são importados ou copiados para o **Power BI Desktop**. Ao trabalhar com visualizações, o **Power BI Desktop** consulta uma cópia dos dados. Para ver as alterações ocorridas nos dados subjacentes, você deve atualizar ou importar novamente um conjunto de dados completo e atual. |Somente *tabelas e colunas* são importadas ou copiadas para o **Power BI Desktop**. Ao trabalhar com as visualizações, o **Power BI Desktop** consulta a fonte de dados subjacente, o que significa que você sempre estará exibindo os dados atuais. |

Para saber mais sobre como se conectar a uma fonte de dados, confira [Conectar-se a uma fonte de dados](connect-datasource.md).

Depois de salvar seu trabalho no **Power BI Desktop**, um arquivo PBIX será criado. Esse arquivo contém o relatório. Além disso, se você importar dados, o PBIX conterá o conjunto de dados completo, mas se usar o **DirectQuery**, o PBIX conterá apenas um esquema de conjunto de dados. Implante o PBIX em seu workspace programaticamente usando a [API de importação do Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [!NOTE]
> As **Coleções de Workspaces do Power BI** têm APIs adicionais para alterar o servidor e o banco de dados para onde seu conjunto de dados está apontando e para definir uma credencial da conta de serviço que o conjunto de dados usará a fim de se conectar ao banco de dados. Confira [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) e [Correção de fonte de dados do gateway](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>Criar relatórios e conjuntos de dados do Power BI usando APIs

### <a name="datasets"></a>Conjunto de dados

Você pode criar conjuntos de dados nas Coleções de Workspaces do Power BI usando a API REST. Em seguida, você pode enviar dados por push para seu conjunto de dados. Isso permite que você trabalhe com dados sem a necessidade do Power BI Desktop. Para saber mais, veja [Postar conjuntos de dados](https://msdn.microsoft.com/library/azure/mt778875.aspx).

### <a name="reports"></a>Relatórios

Você pode criar um relatório de um conjunto de dados diretamente em seu aplicativo usando a API de JavaScript. Para saber mais, confira [Criar um novo relatório de um conjunto de dados nas Coleções de Workspaces do Power BI](create-report-from-dataset.md).

## <a name="see-also"></a>Consulte também

[Introdução a exemplos](get-started-sample.md)  
[Autenticando e autorizando em Coleções de Workspaces do Power BI](app-token-flow.md)  
[Inserir um relatório](embed-report.md)  
[Criar um novo relatório de um conjunto de dados em Coleções de Workspaces do Power BI](create-report-from-dataset.md)
[Salvar relatórios](save-reports.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Amostra de inserção de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Mais perguntas? [Experimentar a comunidade do Power BI](https://community.powerbi.com/)

