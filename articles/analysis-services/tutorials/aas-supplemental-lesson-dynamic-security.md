---
title: "Lição suplementar de tutorial do Azure Analysis Services: segurança dinâmica | Microsoft Docs"
description: "Descreve como usar a segurança dinâmica usando filtros de linha no tutorial do Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.openlocfilehash: b258c18fde15014192e8f604a4e8b3842c3e52c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="supplemental-lesson---dynamic-security"></a>Lição suplementar – segurança dinâmica

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição suplementar, você cria uma função adicional que implementa a segurança dinâmica. A segurança dinâmica oferece segurança no nível de linha com base no nome de usuário ou na ID de logon do usuário conectado no momento. 
  
Para implementar a segurança dinâmica, você adiciona uma tabela ao modelo que contém os nomes de usuário dos usuários que podem se conectar ao modelo e procurar dados e objetos de modelo. O modelo que você cria usando este tutorial está no contexto da Adventure Works; no entanto, para concluir esta lição, você deve adicionar uma tabela contendo usuários de seu próprio domínio. Você não precisa das senhas para os nomes de usuário que são adicionados. Para criar uma tabela EmployeeSecurity com uma pequena amostra dos usuários de seu próprio domínio, você usa o recurso Colar, colando dados de funcionários de uma planilha do Excel. Em um cenário do mundo real, a tabela que contém os nomes de usuário normalmente seria uma tabela de banco de dados real como uma fonte de dados; por exemplo, uma tabela DimEmployee real.  
  
Para implementar a segurança dinâmica, você usa duas funções DAX: [Função USERNAME (DAX)](http://msdn.microsoft.com/22dddc4b-1648-4c89-8c93-f1151162b93f) e [Função LOOKUPVALUE (DAX)](http://msdn.microsoft.com/73a51c4d-131c-4c33-a139-b1342d10caab). Essas funções, aplicadas em uma fórmula de filtro de linha, são definidas em uma nova função. Usando a função LOOKUPVALUE, a fórmula especifica um valor da tabela EmployeeSecurity. A fórmula então passa esse valor para a função USERNAME, que especifica o nome de usuário do usuário conectado que pertence a essa função. O usuário pode procurar apenas os dados especificados pelos filtros de linha da função. Nesse cenário, você especifica que os funcionários de vendas só podem procurar dados de vendas pela Internet para as regiões de vendas de que eles são membros.  
  
Aquelas tarefas que são exclusivas deste cenário de modelo tabular da Adventure Works, mas que não seriam aplicariam necessariamente a um cenário do mundo real, são identificadas como tal. Cada tarefa inclui informações adicionais que descrevem o propósito da tarefa.  
  
Tempo estimado para conclusão desta lição: **30 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico de lição suplementar faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição suplementar, você deve ter concluído todas as lições anteriores.  
  
## <a name="add-the-dimsalesterritory-table-to-the-aw-internet-sales-tabular-model-project"></a>Adicionar a tabela DimSalesTerritory ao projeto de modelo tabular de Vendas pela Internet da AW  
Para implementar a segurança dinâmica para este cenário da Adventure Works, você deve adicionar duas tabelas adicionais ao seu modelo. A primeira tabela adicionada é DimSalesTerritory (como Região de Vendas) do mesmo banco de dados AdventureWorksDW. Posteriormente, você aplica um filtro de linha à tabela SalesTerritory que define os dados específicos pelos quais o usuário conectado pode navegar.  
  
#### <a name="to-add-the-dimsalesterritory-table"></a>Para adicionar a tabela DimSalesTerritory  
  
1.  Em Gerenciador de Modelos tabulares > **Fontes de Dados**, clique com o botão direito do mouse em sua conexão e clique em **Importar Novas Tabelas**.  

    Se a caixa de diálogo Credenciais de Representação aparecer, digite as credenciais de representação usadas na Lição 2: adicionar dados.
  
2.  No navegador, selecione a tabela **DimSalesTerritory** e, em seguida, clique em **OK**.    
  
3.  No Editor de Consultas, clique na consulta **DimSalesTerritory** e, em seguida, remova a coluna **SalesTerritoryAlternateKey**.  
  
7.  Clique em **Importar**.  
  
    A nova tabela é adicionada ao espaço de trabalho de modelo. Objetos e dados da tabela de origem DimSalesTerritory são importados para o modelo tabular de vendas pela Internet da AW.  
  
9. Depois que a tabela tiver sido importada com êxito, clique em **Fechar**.  

## <a name="add-a-table-with-user-name-data"></a>Adicionar uma tabela com os dados de nome de usuário  
A tabela DimEmployee no banco de dados de exemplo AdventureWorksDW contém usuários do domínio AdventureWorks. Esses nomes de usuários não existem em seu ambiente. Você deve criar uma tabela em seu modelo que contenha um exemplo pequeno (pelo menos três) de usuários reais de sua organização. Em seguida, adicione esses usuários como membros à nova função. Você não precisa de senhas para os exemplos de nomes de usuário, mas você precisa de nomes de usuário do Windows reais de seu próprio domínio.  
  
#### <a name="to-add-an-employeesecurity-table"></a>Para adicionar uma tabela EmployeeSecurity  
  
1.  Abra o Microsoft Excel e crie uma planilha.  
  
2.  Copie a tabela a seguir incluindo a linha de cabeçalho e cole-a na planilha.  

    ```
      |EmployeeId|SalesTerritoryId|FirstName|LastName|LoginId|  
      |---------------|----------------------|--------------|-------------|------------|  
      |1|2|<user first name>|<user last name>|\<domain\username>|  
      |1|3|<user first name>|<user last name>|\<domain\username>|  
      |2|4|<user first name>|<user last name>|\<domain\username>|  
      |3|5|<user first name>|<user last name>|\<domain\username>|  
    ```

3.  Substitua o nome, sobrenome e domínio\nomedeusuário com os nomes e IDs de logon de três usuários em sua organização. Coloque o mesmo usuário nas duas primeiras linhas, para a EmployeeId1, mostrando que esse usuário pertence a mais de uma região de vendas. Deixe os campos EmployeeId e SalesTerritoryId como estão.  
  
4.  Salve a planilha como **SampleEmployee**.  
  
5.  Na planilha, selecione todas as células com os dados do funcionário, incluindo os cabeçalhos; em seguida, clique nos dados selecionados e depois em **Copiar**.  
  
6.  No SSDT, clique no menu **Editar** e depois em **Colar**.  
  
    Se Colar estiver esmaecido, clique em qualquer coluna em qualquer tabela na janela do designer de modelos e tente novamente.  
  
7.  Na caixa de diálogo **Versão Prévia de Colagem**, em **Nome da Tabela**, digite **EmployeeSecurity**.  
  
8.  Em **Dados a serem colados**, verifique se os dados incluem todos os dados de usuário e os cabeçalhos da planilha SampleEmployee.  
  
9. Verifique se a opção **Usar primeira linha como cabeçalhos de coluna** está marcada e clique em **Ok**.  
  
    Uma nova tabela denominada EmployeeSecurity com os dados de funcionário copiados da planilha SampleEmployee é criada.  
  
## <a name="create-relationships-between-factinternetsales-dimgeography-and-dimsalesterritory-table"></a>Criar relações entre as tabelas FactInternetSales, DimGeography e DimSalesTerritory  
As tabelas DimSalesTerritory, FactInternetSales e DimGeography contêm uma coluna em comum, SalesTerritoryId. A coluna SalesTerritoryId na tabela DimSalesTerritory contém valores com uma ID diferente para cada região de vendas.  
  
#### <a name="to-create-relationships-between-the-factinternetsales-dimgeography-and-the-dimsalesterritory-table"></a>Para criar relações entre as tabelas FactInternetSales, DimGeography e DimSalesTerritory  
  
1.  Na Exibição de Diagrama na tabela **DimGeography**, clique na coluna **SalesTerritoryId** e mantenha o botão do mouse pressionado; em seguida, arraste o cursor para a coluna **SalesTerritoryId** na tabela **DimSalesTerritory** e solte.  
  
2.  Na tabela **FactInternetSales**, clique na coluna **SalesTerritoryId** e mantenha o botão do mouse pressionado sobre ela; em seguida, arraste o cursor para a coluna **SalesTerritoryId** na tabela **DimSalesTerritory** e solte.  
  
    Observe que a propriedade ativa desta relação é False, o que significa que ela está inativa. A tabela FactInternetSales já tem outra relação ativa.  
  
## <a name="hide-the-employeesecurity-table-from-client-applications"></a>Ocultar a tabela EmployeeSecurity de aplicativos cliente  
Nesta tarefa, você oculta a tabela EmployeeSecurity, impedindo que ela apareça na lista de campos de um aplicativo cliente. Lembre-se de que a ocultação de uma tabela não a protege. Se souberem como, os usuários ainda poderão consultar os dados da tabela EmployeeSecurity. Para proteger os dados da tabela EmployeeSecurity, impedindo que os usuários sejam capazes de consultar qualquer um dos seus dados, você deve aplicar um filtro em uma tarefa posterior.  
  
#### <a name="to-hide-the-employeesecurity-table-from-client-applications"></a>Para ocultar a tabela EmployeeSecurity de aplicativos cliente  
  
-   No designer de modelos, na Exibição de Diagrama, clique com o botão direito do mouse no título de tabela **Funcionário** e, em seguida, clique em **Ocultar das Ferramentas de Cliente**.  
  
## <a name="create-a-sales-employees-by-territory-user-role"></a>Criar uma função de usuário de Funcionários de Vendas por Região  
Nesta tarefa, você deve criar uma função de usuário. Essa função inclui um filtro de linha definindo quais linhas da tabela DimSalesTerritory estão visíveis aos usuários. O filtro é então aplicado na direção da relação um-para-muitos a todas as outras tabelas relacionadas a DimSalesTerritory. Você também pode aplicar um filtro que protege toda a tabela EmployeeSecurity de ser consultável por qualquer usuário que seja um membro da função.  
  
> [!NOTE]  
> A função Funcionários de Vendas por Região criada nesta lição restringe os membros para procurar (ou consultar) apenas dados de vendas da região de vendas à qual pertencem. Se você adicionar um usuário como um membro para a função Funcionários de Vendas por Região que também existe como um membro em uma função criada na [Lição 11: criar funções](../tutorials/aas-lesson-11-create-roles.md), você obterá uma combinação de permissões. Quando um usuário é um membro de várias funções, permissões e filtros de linha definidos para cada função são cumulativos. Ou seja, o usuário tem as maiores permissões determinadas pela combinação de funções.  
  
#### <a name="to-create-a-sales-employees-by-territory-user-role"></a>Para criar uma função de usuário de Funcionários de Vendas por Região  
  
1.  No SSDT, clique no menu **Modelo** e depois em **Funções**.  
  
2.  No **Gerenciador de Funções**, clique em **Novo**.  
  
    Uma nova função com a permissão Nenhum é adicionada à lista.  
  
3.  Clique na nova função e, na coluna **Nome**, renomeie a função para **Funcionários de Vendas por Região**.  
  
4.  Na coluna **Permissões**, clique na lista suspensa e, em seguida, selecione a permissão **Leitura**.  
  
5.  Clique na guia **Membros** e, em seguida, clique em **Adicionar**.  
  
6.  Na caixa de diálogo **Selecionar Usuário ou Grupo**, em **Insira o nome de objeto para selecionar**, digite o primeiro nome de usuário de exemplo usado ao criar a tabela EmployeeSecurity. Clique em **Verificar Nomes** para verificar se o nome de usuário é válido e, em seguida, clique em **Ok**.  
  
    Repita essa etapa, adicionando os outros nomes de usuário de exemplo usados ao criar a tabela EmployeeSecurity.  
  
7.  Clique na guia **Filtros de Linha**.  
  
8.  Para a tabela **EmployeeSecurity**, na coluna **filtro DAX**, digite a seguinte fórmula:  
  
    ```
      =FALSE()  
    ```
  
    Esta fórmula especifica que todas as colunas são resolvidas para a condição Booliana false. Nenhuma coluna da tabela EmployeeSecurity pode ser consultada por um membro dos Funcionários de Vendas por função de usuário Região.  
  
9. Para a tabela **DimSalesTerritory**, digite a seguinte fórmula:  

    ```  
    ='Sales Territory'[Sales Territory Id]=LOOKUPVALUE('Employee Security'[Sales Territory Id], 
      'Employee Security'[Login Id], USERNAME(), 
      'Employee Security'[Sales Territory Id], 
      'Sales Territory'[Sales Territory Id]) 
    ```
  
    Nesta fórmula, a função LOOKUPVALUE retorna todos os valores para a coluna DimEmployeeSecurity[SalesTerritoryId], em que o EmployeeSecurity[LoginId] é o mesmo que o nome de usuário do Windows conectado atualmente e EmployeeSecurity[SalesTerritoryId] é igual a DimSalesTerritory[SalesTerritoryId].  
  
    O conjunto de IDs de região de vendas retornadas por LOOKUPVALUE é então usado para restringir as linhas mostradas na tabela DimSalesTerritory. São exibidas somente as linhas cuja SalesTerritoryID está no conjunto de IDs retornadas pela função LOOKUPVALUE.  
  
10. Em Gerenciador de Funções, clique em **Ok**.  
  
## <a name="test-the-sales-employees-by-territory-user-role"></a>Testar a função de usuário de Funcionários de Vendas por Região  
Nesta tarefa, você usa o recurso Analisar no Excel no SSDT para testar a eficácia da função de usuário Funcionários de Vendas por Região. Você especifica um dos nomes de usuário que você adicionou à tabela EmployeeSecurity e como um membro da função. Esse nome de usuário é então usado como o nome de usuário efetivo na conexão criada entre o Excel e o modelo.  
  
#### <a name="to-test-the-sales-employees-by-territory-user-role"></a>Para testar a função de usuário de Funcionários de Vendas por Região  
  
1.  No SSDT, clique no menu **Modelo** e depois em **Analisar no Excel**.  
  
2.  Na caixa de diálogo **Analisar no Excel**, **especifique o nome de usuário ou função a ser usada para se conectar ao modelo**, selecione **Outro Usuário do Windows** e, em seguida, clique em **Procurar**.  
  
3.  Na caixa de diálogo **Selecionar Usuário ou Grupo**, em **Insira o nome de objeto para selecionar**, digite um nome de usuário que você incluiu na tabela EmployeeSecurity e clique em **Verificar Nomes**.  
  
4.  Clique em **Ok** para fechar a caixa de diálogo **Selecionar Usuário ou Grupo** e clique em **Ok** para fechar a caixa de diálogo **Analisar no Excel**.  
  
    O Excel abre com uma nova pasta de trabalho. Uma tabela dinâmica é criada automaticamente. A lista de campos de tabela dinâmica inclui a maioria dos campos de dados disponíveis no novo modelo.  
  
    Observe que a tabela EmployeeSecurity não está visível na lista Campos de Tabela Dinâmica. Você ocultou essa tabela das ferramentas de cliente em uma tarefa anterior.  
  
5.  Na lista **Campos**, **∑ Vendas pela Internet** (medidas), selecione a medida **InternetTotalSales**. A medida é inserida nos campos **Valores**.  
  
6.  Selecione a coluna **SalesTerritoryId** da tabela **DimSalesTerritory**. A coluna é inserida nos campos **Rótulos de Linha**.  
  
    Observe que os valores de vendas pela Internet aparecem somente para a região à qual o nome de usuário efetivo usado pertence. Se você selecionar outra coluna, como City em DimGeography como o campo Rótulo de Linha, apenas as cidades da região de vendas à qual o usuário efetivo pertence serão exibidas.  
  
    Esse usuário não pode procurar nem consultar nenhum dado de vendas pela Internet de outras regiões que não aquela a que ele pertence. Essa restrição existe porque o filtro de linha definido para a tabela DimSalesTerritory, na função de usuário Funcionários de Vendas por Região, protege dados para todos os dados relacionados a outras regiões de vendas.  
  
## <a name="see-also"></a>Consulte também  
[Função USERNAME (DAX)](https://msdn.microsoft.com/library/hh230954.aspx)  
[Função LOOKUPVALUE (DAX)](https://msdn.microsoft.com/library/gg492170.aspx)  
[Função CUSTOMDATA (DAX)](https://msdn.microsoft.com/library/hh213140.aspx)  