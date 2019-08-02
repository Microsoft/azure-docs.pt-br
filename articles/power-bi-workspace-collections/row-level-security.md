---
title: Segurança em nível de linha com Coleções de Workspaces do Power BI
description: Detalhes sobre a segurança em nível de linha com as Coleções de Workspaces do Power BI
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 2a26cc7573abb970dc58c6f7c327dfbc659cb646
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672490"
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Segurança em nível de linha com Coleções de Workspaces do Power BI

A RLS (segurança em nível de linha) pode ser usada para restringir o acesso do usuário a dados específicos em um relatório ou conjunto de dados, permitindo que vários usuários diferentes usem o mesmo relatório, mas vendo dados diferentes. As Coleções de Workspaces do Power BI dão suporte a conjuntos de dados configurados com RLS.

![Fluxo da segurança em nível de linha com as Coleções de Workspaces do Power BI](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> As Coleções de Workspaces do Power BI foram preteridas e só estarão disponíveis até junho de 2018 ou conforme a indicação do seu contrato. Recomendamos planejar a migração para o Power BI Embedded a fim de evitar interrupções em seu aplicativo. Para saber mais sobre como migrar seus dados para o Power BI Embedded, confira [Como migrar o conteúdo das Coleções de Workspaces do Power BI para o Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Para tirar proveito da RLS, é importante compreender os três conceitos principais: usuários, funções e regras. Vamos examinar cada um com mais detalhes:

**Usuários** – são os usuários finais reais que exibem os relatórios. Nas Coleções de Workspaces do Power BI, os usuários são identificados pela propriedade de nome de usuário em um Token de Aplicativo.

**Funções** – os usuários pertencem a funções. Uma função é um contêiner de regras e pode ter um nome como "Gerente de vendas" ou "Representante de vendas". Nas Coleções de Workspaces do Power BI, os usuários são identificados pela propriedade de funções em um Token de Aplicativo.

**Regras** – funções têm regras e essas regras são os filtros reais aplicados aos dados. Isso pode ser tão simples quanto "País = EUA" ou algo muito mais dinâmico.

### <a name="example"></a>Exemplo

Para o restante deste artigo, fornecemos um exemplo de criação de RLS e o usaremos em um aplicativo inserido. Nosso exemplo usa o arquivo PBIX [exemplo de análise de varejo](https://go.microsoft.com/fwlink/?LinkID=780547) .

![Relatório de vendas de exemplo](media/row-level-security/scenario-2.png)

Nosso exemplo de análise de varejo mostra as vendas para todas as lojas em uma cadeia de varejo específica. Sem RLS, não importa que gerente regional entra e exibe o relatório, eles veem os mesmos dados. A cúpula sênior determinou que cada gerente regional veja apenas as vendas das lojas que eles gerenciam e, para fazer isso, podemos usar RLS.

A RLS é criada no Power BI Desktop. Quando o conjunto de dados e o relatório são abertos, podemos alternar para modo de exibição de diagrama a fim de ver o esquema:

![Diagrama de modelo no Power BI Desktop](media/row-level-security/diagram-view-3.png)

Eis algumas observações em relação a esse esquema:

* Todas as medidas, como **Total de vendas**, são armazenadas na tabela de fatos **Vendas**.
* Há quatro tabelas de dimensões adicionais relacionadas: **Item**, **Tempo**, **Loja** e **Distrito**.
* As setas nas linhas de relação indicam como os filtros podem fluir de uma tabela para outra. Por exemplo, se um filtro é colocado em **Hora[Data]** , no esquema atual ele filtraria somente valores na tabela **Vendas**. Nenhuma outra tabela seria afetada por esse filtro, já que todas as setas nas linhas de relação apontam em direção à tabela de vendas, e não para fora dela.
* A tabela **Região** indica quem é o gerente de cada região:
  
  ![Linhas da tabela de região](media/row-level-security/district-table-4.png)

Com base nesse esquema, se aplicamos um filtro na coluna **Gerente regional** e esse filtro corresponder ao usuário que exibe o relatório, ele também filtra as tabelas **Loja** e **Vendas**, a fim de mostrar somente os dados relacionados ao gerente regional em questão.

Veja como fazer isso:

1. Na guia Modelagem, clique em **Gerenciar Funções**.  
   ![Gerenciar o botão de funções na faixa de opções de modelagem](media/row-level-security/modeling-tab-5.png)
2. Criar uma nova função chamada **Gerente**.  
   ![Criação de funções no Power BI Desktop](media/row-level-security/manager-role-6.png)
3. Na tabela **Região**, insira a seguinte expressão DAX: **[District Manager] = Username()**  
   ![Expressão de filtro DAX para a tabela na função](media/row-level-security/manager-role-7.png)
4. Para verificar se as regras estão funcionando, na guia **Modelagem**, clique em **Exibir como Funções** e digite o seguinte:  
   ![Exibir como funções](media/row-level-security/view-as-roles-8.png)

   Os relatórios agora mostrarão dados como se você estivesse conectado como **Paulo Araújo**.

A aplicação do filtro como fizemos aqui filtra todos os registros nas tabelas **Região**, **Loja** e **Vendas**. No entanto, devido à direção do filtro nas relações entre **Vendas** e **Hora**, as tabelas **Vendas** e **Item** e **Item** e **Hora** não serão filtradas.

![Exibição de diagrama com relações realçadas](media/row-level-security/diagram-view-9.png)

Que podem ser adequadas para esse requisito; no entanto, se não quisermos que os gerentes vejam itens para os quais não têm vendas, poderíamos ativar a filtragem cruzada bidirecional a relação e fluir o filtro de segurança em ambos os sentidos. Isso pode ser feito editando a relação entre **Vendas** e **Item**, assim:

![Direção do filtro cruzado para relação](media/row-level-security/edit-relationship-10.png)

Agora, os filtros também podem fluir da tabela Vendas para a tabela **Item** :

![Ícone de direção de filtro de relação na exibição de diagrama](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> Se você estiver usando o modo DirectQuery para seus dados, precisará habilitar a filtragem bidirecional selecionando estas duas opções:

1. **Arquivo** -> **Opções e Configurações** -> **Recursos de Visualização** -> **Habilitar filtragem cruzada em ambas as direções para DirectQuery**.
2. **Arquivo** -> **Opções e Configurações** -> **DirectQuery** -> **Permitir medidas irrestritas no modo DirectQuery**.

Para saber mais sobre filtragem cruzada bidirecional, baixe o whitepaper [Filtragem cruzada bidirecional no SQL Server Analysis Services 2016 e no Power BI Desktop](https://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional%20cross-filtering%20in%20Analysis%20Services%202016%20and%20Power%20BI.docx).

Isso conclui todo o trabalho que precisa ser feito no Power BI Desktop, mas há um mais trabalho que precisa ser feito para tornar as regras de RLS definidas funcionarem no Power BI Embedded. Os usuários são autenticados e autorizados pelo seu aplicativo e Tokens de Aplicativo são usados para conceder acesso de usuário a um relatório de Power BI Embedded específico. O Power BI Embedded não tem nenhuma informação específica sobre quem é o usuário. Para que a RLS funcione, você precisa passar algum contexto adicional como parte de seu token do aplicativo:

* **nome de usuário** (opcional) – usado com RLS, é uma cadeia de caracteres que pode ser usada para ajudar a identificar o usuário ao aplicar regras RLS. Confira Usando segurança de nível de linha com o Power BI Embedded
* **funções** – uma cadeia de caracteres que contém as funções a selecionar ao aplicar regras de segurança em nível de linha. Ao transmitir mais de uma função, elas deverão ser transmitidas como uma matriz de cadeia de caracteres.

Crie o token usando o método [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN). Se a propriedade de nome de usuário estiver presente, você também deverá passar pelo menos um valor como funções.

Por exemplo, você pode alterar EmbedSample. A linha 55 DashboardController pode ser atualizada de

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

para

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

O token do aplicativo completo é algo parecido com isto:

![Exemplo de Token Web JSON](media/row-level-security/app-token-string-12.png)

Agora, com todas as partes juntas, quando alguém fizer logon em nosso aplicativo para exibir esse relatório, verá os dados que tiver permissão para ver, conforme definido pela nossa segurança em nível de linha.

![Relatório exibido no aplicativo](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>Consulte também

[RLS (segurança no nível da linha) com Power](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Autenticando e autorizando em Coleções de Workspaces do Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Amostra de inserção de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Mais perguntas? [Experimentar a comunidade do Power BI](https://community.powerbi.com/)
