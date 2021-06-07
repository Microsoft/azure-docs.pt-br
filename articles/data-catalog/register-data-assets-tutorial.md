---
title: 'Tutorial: Gerenciar ativos de dados no Catálogo de Dados do Azure'
description: Este tutorial descreve como registrar ativos de dados no seu Catálogo de Dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: de85cad8232d76706358c36806ce19a7c1df893f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674998"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Tutorial: Gerenciar ativos de dados no Catálogo de Dados do Azure

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Neste tutorial, você usa a ferramenta de registro para registrar ativos de dados da amostra de banco de dados com o catálogo. O registro é o processo de extração de metadados estruturais chave, como nomes, tipos e locais, da fonte de dados e dos ativos que ela contém, copiando os metadados para o catálogo. As fontes de dados e seus dados permanecem onde estão, porém os metadados são usados pelo catálogo para torná-los mais facilmente identificáveis e compreensíveis.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Registrar ativos de dados 
> * Pesquisar ativos de dados
> * Anotar ativos de dados
> * Conectar aos ativos de dados
> * Gerenciar ativos de dados
> * Excluir ativos de dados

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você deve concluir o [início rápido](register-data-assets-tutorial.md).

* Uma assinatura do [Microsoft Azure](https://azure.microsoft.com/).
* Você precisa ter seu próprio [locatário do Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Para configurar o Catálogo de Dados, você deve ser o proprietário ou o coproprietário de uma assinatura do Azure.

## <a name="register-data-assets"></a>Registrar ativos de dados

### <a name="register-a-data-source"></a>Registrar uma fonte de dados

Você registra ativos de dados (tabelas) de uma [amostra de banco de dados](../azure-sql/database/single-database-create-quickstart.md) para o Banco de Dados SQL do Azure, mas pode usar qualquer fonte de dados compatível caso prefira trabalhar usando dados familiares e relevantes para sua função. Para obter uma lista das fontes de dados com suporte, confira [Fontes de dados com suporte](data-catalog-dsr.md).

O nome do banco de dados que estamos usando neste tutorial é *RLSTest*.

Agora, registre ativos de dados da amostra de banco de dados usando o Catálogo de Dados do Azure.

1. Vá para a [página inicial do Catálogo de Dados do Azure](http://azuredatacatalog.com) e clique em **Publicar Dados**.

   ![Catálogo de Dados do Azure – botão Publicar Dados](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. Selecione **Iniciar Aplicativo** para baixar, instalar e executar a ferramenta de registro no computador.

   ![Catálogo de Dados do Azure – botão Iniciar](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Na página **Boas-vindas**, clique em **Entrar** e insira suas credenciais.

    ![Catálogo de Dados do Azure – página inicial](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. Na página **Catálogo de Dados do Microsoft Azure**, clique em **SQL Server** e em **Avançar**.

    ![Catálogo de Dados do Azure – fontes de dados](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Insira as propriedades de conexão do SQL Server para sua amostra de banco de dados no Banco de Dados SQL do Azure e selecione **CONECTAR**.

   ![Catálogo de Dados do Azure – configurações de conexão do SQL Server](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Registre os metadados do seu ativo de dados. Neste exemplo, você registra objetos de **Produto** do namespace de amostra:

   1. Na árvore **Hierarquia de Servidores**, expanda sua amostra de banco de dados e selecione **SalesLT**.

   2. Selecione **Product**, **ProductCategory**, **ProductDescription** e **ProductModel** usando Ctrl+selecionar.

   3. Selecione a **seta de movimentação selecionada** ( **>** ). Esta ação move todos os objetos selecionados para a lista **Objetos para registrar** .

      ![Tutorial do Catálogo de Dados do Azure – procurar e selecionar objetos](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

   4. Selecione **Incluir uma Visualização** para incluir uma visualização dos dados como instantâneo. O instantâneo inclui até 20 registros de cada tabela e é copiado no catálogo.

   5. Selecione **Incluir Perfil de Dados** para incluir um instantâneo das estatísticas de objeto no perfil de dados (por exemplo, valores mínimo, máximo e médio de uma coluna, número de linhas).

   6. No campo **Adicionar marcas**, insira **vendas, produto, sql azure**. Essa ação adiciona marcas de pesquisa para esses ativos de dados. Marcas são uma ótima maneira de ajudar os usuários a localizar uma fonte de dados registrada.

   7. Especifique o nome de um **especialista** nesses dados (opcional).

      ![Tutorial do Catálogo de Dados do Azure – objetos para registrar](media/register-data-assets-tutorial/data-catalog-objects-register.png)

   8. Selecione **REGISTRAR**. O Catálogo de Dados do Azure registra os objetos selecionados. Neste exercício, os objetos selecionados da sua amostra de banco de dados são registrados. A ferramenta de registro extrai metadados de ativo de dados e copia esses dados no serviço Catálogo de Dados do Azure. Os dados permanecem no local em que estão no momento. Os dados permanecem sob o controle dos administradores e das políticas do sistema de origem.

      ![Catálogo de Dados do Azure – objetos registrados](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. Para ver seus objetos de fonte de dados registrados, selecione **Exibir Portal**. No portal do Catálogo de Dados do Azure, confirme que você vê todas as quatro tabelas e o banco de dados na exibição de grade (verifique se a barra de pesquisa está clara).

       ![Objetos no portal do Catálogo de Dados do Azure](media/register-data-assets-tutorial/data-catalog-view-portal.png)

Neste exercício, você registrou objetos da amostra de banco de dados para Banco de Dados SQL do Azure para que eles pudessem ser facilmente descobertos por usuários na sua organização.

No próximo exercício, você aprenderá como descobrir ativos de dados registrados.

## <a name="discover-data-assets"></a>Descobrir ativos de dados

A descoberta no Catálogo de Dados do Azure usa dois mecanismos principais: pesquisa e filtragem.

A pesquisa foi projetada para ser intuitiva e eficiente. Por padrão, os termos de pesquisa são correspondidos com alguma propriedade no catálogo, incluindo anotações fornecidas pelo usuário.

A filtragem foi desenvolvida para complementar a pesquisa. Você pode selecionar características específicas como especialistas, tipo de fonte de dados, tipo de objeto e marcas, para exibir ativos de dados correspondentes e para restringir os resultados da pesquisa a ativos correspondentes.

Usando uma combinação de pesquisa e filtragem, você pode navegar rapidamente nas fontes de dados registradas no Catálogo de Dados do Azure.

Neste exercício, você usará o portal do Catálogo de Dados do Azure para descobrir os ativos de dados registrados no exercício anterior. Confira [Referência de sintaxe de pesquisa do catálogo de dados](/rest/api/datacatalog/#search-syntax-reference) para obter detalhes sobre a sintaxe de pesquisa.

Eis alguns exemplos para descobrir ativos de dados no catálogo.  

### <a name="discover-data-assets-with-basic-search"></a>Descobrir ativos de dados com a pesquisa básica

A pesquisa básica ajuda você a procurar um catálogo usando um ou mais termos de pesquisa. Os resultados são quaisquer ativos que correspondam a qualquer propriedade com um ou mais dos termos especificados.

1. Selecione **Página Inicial** no portal do Catálogo de Dados do Azure. Se você tiver fechado o navegador da Web, vá até a [página inicial do Catálogo de Dados do Azure](https://www.azuredatacatalog.com).

2. Na caixa de pesquisa, insira `product` e pressione **ENTER**.

   ![Catálogo de Dados do Azure – pesquisa de texto básica](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Confirme que você vê todas as quatro tabelas e o banco de dados nos resultados. Você pode alternar entre **exibição de grade** e **exibição de lista** selecionando os botões na barra de ferramentas como mostrado na imagem a seguir. Observe que a palavra-chave de pesquisa fica realçada nos resultados da pesquisa, já que a opção **Realçar** está **ATIVADA**. Você também pode especificar o número de **resultados por página** nos resultados da pesquisa.

   ![Catálogo de Dados do Azure – resultados da pesquisa de texto básica](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

   O painel **Pesquisas** fica à esquerda e o painel **Propriedades**, à direita. No painel **Pesquisas** , você pode alterar os critérios de pesquisa e filtrar os resultados. O painel **Propriedades** exibe as propriedades de um objeto selecionado na grade ou lista.

4. Selecione **Produto** nos resultados da pesquisa. Selecione as guias **Visualização**, **Colunas**, **Perfil de Dados** e **Documentação** ou selecione a seta para expandir o painel inferior.  

   ![Catálogo de Dados do Azure – painel inferior](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

   Na guia **Visualização**, você vê a visualização dos dados na tabela **Product**.

5. Selecione a guia **Colunas** para encontrar detalhes sobre as colunas (como o **nome** e o **tipo de dados**) no ativo de dados.

6. Selecione a guia **Perfil de Dados** para ver a criação de perfil de dados (por exemplo, número de linhas, tamanho dos dados ou valor mínimo em uma coluna) no ativo de dados.

### <a name="discover-data-assets-with-property-scoping"></a>Descobrir ativos de dados com escopo de propriedade

O escopo de propriedade ajuda você a descobrir os ativos de dados onde o termo de pesquisa corresponde à propriedade especificada.

1. Limpe o filtro **Tabela** em **Tipo de Objeto**, em **Filtros**.  

2. Na caixa de pesquisa, insira `tags:product` e pressione **ENTER**. Confira [Referência de sintaxe de pesquisa do Catálogo de Dados](/rest/api/datacatalog/#search-syntax-reference) para ver todas as propriedades que você pode usar para pesquisar o catálogo de dados.

3. Confirme que você vê as tabelas e o banco de dados nos resultados.  

   ![Catálogo de Dados – resultados da pesquisa de escopo de propriedade](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Salvar a pesquisa

1. No painel **Pesquisas**, na seção **Pesquisa Atual**, digite um nome para a pesquisa e selecione **Salvar**.

   ![Catálogo de dados do Azure – salvar pesquisa](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. Confirme se a pesquisa salva aparece em **Pesquisas Salvas**.

3. Selecione uma das ações que você pode realizar na pesquisa salva (**Renomear**, **Excluir**, **Salvar como Padrão**).

### <a name="grouping-with-parentheses"></a>Agrupando com parênteses

Ao usar parênteses, você pode agrupar partes da consulta para obter o isolamento lógico, principalmente junto com operadores boolianos.

1. Na caixa de pesquisa, insira `name:product AND (tags:product AND objectType:table)` e pressione **ENTER**.

2. Verifique se você vê apenas a tabela **Product** nos resultados da pesquisa.

   ![Catálogo de dados do Azure – agrupando pesquisa](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>Operadores de comparação

Com operadores de comparação, você pode usar comparações que não sejam de igualdade para propriedades que tenham tipos de dados numéricos e de data.

1. Na caixa de pesquisa, insira `lastRegisteredTime:>"06/09/2016"`.

2. Limpe o filtro **Tabela** em **Tipo de Objeto**.

3. Pressione **ENTER**.

4. Confirme que você vê as tabelas **Product**, **ProductCategory** e **ProductDescription** e o Banco de Dados SQL que você registrou nos resultados da pesquisa.

   ![Catálogo de Dados do Azure – resultados da pequisa de comparação](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Veja [Como descobrir ativos de dados](data-catalog-how-to-discover.md) para obter informações detalhadas sobre a descoberta de ativos de dados. Para obter mais informações sobre sintaxe de pesquisa, veja [Referência de sintaxe de Pesquisa do Catálogo de Dados](/rest/api/datacatalog/#search-syntax-reference).

## <a name="annotate-data-assets"></a>Anotar ativos de dados

Neste exercício, você deve usar o portal do Catálogo de Dados do Azure para anotar (adicionar informações como descrições, marcas, especialistas) em ativos de dados existentes no catálogo. As anotações complementam os metadados estruturais extraídos da fonte de dados durante o registro. A anotação torna os ativos de dados muito mais fáceis de descobrir e entender.

Neste exercício, você anota um ativo de dados único (ProductPhoto). Você adiciona um nome amigável e uma descrição ao ativo de dados ProductPhoto.  

1. Vá para a [página inicial do Catálogo de Dados do Azure](https://www.azuredatacatalog.com) e pesquise com `tags:product` para encontrar os ativos de dados registrados.

2. Selecione **ProductModel** nos resultados da pesquisa.  

3. Insira **Imagens do produto** como **Nome Amigável** e **Fotos de produto para materiais de marketing** na **Descrição**.

    ![Catálogo de Dados do Azure – descrição de ProductPhoto](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    A **Descrição** ajuda outros usuários a descobrir e a entender por que e como usar o ativo de dados selecionado. Você também pode adicionar mais marcas e exibir colunas. Você pode pesquisar e filtrar as fontes de dados usando os metadados descritivos que você adicionou ao catálogo.

Você também pode executar as seguintes etapas nesta página:

* Adicione especialistas para o ativo de dados. selecionar **Adicionar** na área **Especialistas**.

* Adicione marcas no nível do conjunto de dados. selecionar **Adicionar** na área **Marcas**. Uma marca pode ser uma marca de usuário ou de glossário. A edição Standard do catálogo de dados inclui um glossário de negócios que ajuda os administradores de catálogo a definir uma taxonomia de negócios central. Os usuários do catálogo podem anotar os ativos de dados com os termos do glossário. Para saber mais, confira [Como configurar o glossário de negócios para Marcação Governada](data-catalog-how-to-business-glossary.md)

* Adicione marcas no nível da coluna. selecionar **Adicionar** em **Marcas** na coluna que você deseja anotar.

* Adicione uma descrição no nível da coluna. Digite a **Descrição** para uma coluna. Você também pode exibir os metadados de descrição extraídos da fonte de dados.

* Adicione informações de **Solicitar acesso** que mostram aos usuários como solicitar acesso ao ativo de dados.
  
* Escolha a guia **Documentação** e forneça a documentação para o ativo de dados. Com a documentação do Catálogo de Dados do Azure, você pode usar seu Catálogo de Dados como um repositório de conteúdo para criar uma narrativa completa de seus ativos de dados.
  
Você também pode adicionar uma anotação a vários ativos de dados. Por exemplo, você pode selecionar todos os ativos de dados que registrou e especificar um especialista para eles.

![Catálogo de Dados do Azure – anotar vários ativos de dados](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

O Catálogo de Dados do Azure dá suporte a uma abordagem de crowdsourcing para anotações. Qualquer usuário do Catálogo de Dados pode adicionar marcas (do usuário ou do glossário), descrições e outros metadados. Ao fazer isso, os usuários adicionam perspectiva em um ativo de dados e seu uso e compartilham essa perspectiva com outros usuários.

Confira [Como anotar os ativos de dados](data-catalog-how-to-annotate.md) para obter informações detalhadas sobre como anotar ativos de dados.

## <a name="connect-to-data-assets"></a>Conectar aos ativos de dados

Neste exercício, você abre os ativos de dados em uma ferramenta de cliente integrada (Excel) e em uma ferramenta não integrada (SQL Server Management Studio) usando informações de conexão.

> [!NOTE]
> É importante lembrar que o Catálogo de Dados do Azure não concede a você acesso à fonte de dados real; ele simplesmente facilita sua descoberta e compreensão. Quando você se conectar a uma fonte de dados, o aplicativo cliente escolhido usará as credenciais do Windows ou solicitará as credenciais conforme necessário. Se você não tiver recebido acesso à fonte de dados previamente, precisará receber esse acesso para poder se conectar.

### <a name="connect-to-a-data-asset-from-excel"></a>Conectar-se a um ativo de dados do Excel

1. Selecione **Produto** nos resultados da pesquisa. selecione **Abrir em** na barra de ferramentas e selecione **Excel**.

   ![Catálogo de Dados do Azure – conectar-se a ativos de dados](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. Selecione **Abrir** na janela pop-up de download. Essa experiência pode variar dependendo do navegador.

3. Na janela **Aviso de Segurança do Microsoft Excel**, selecione **Habilitar**.

   ![Catálogo de Dados do Azure – pop-up de segurança do Excel](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. Mantenha os padrões na caixa de diálogo **Importar Dados** e selecione **OK**.

   ![Catálogo de Dados do Azure – importar dados do Excel](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Exiba a fonte de dados no Excel.

   ![Catálogo de Dados do Azure – tabela de produto no Excel](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Neste exercício, você se conectou aos ativos de dados descobertos usando o Catálogo de Dados do Azure. Com o portal do Catálogo de Dados do Azure, você pode se conectar diretamente usando os aplicativos clientes integrados ao menu **Abrir no** . Você também pode se conectar a qualquer aplicativo escolhido usando as informações de conexão local incluídas nos metadados do ativo. Por exemplo, é possível usar o SQL Server Management Studio para se conectar ao Banco de Dados SQL do Azure para acessar os dados nos ativos de dados registrados neste tutorial.

1. Abra o **SQL Server Management Studio**.

2. Na caixa de diálogo **Conectar ao Servidor**, digite o nome do servidor no painel **Propriedades** no portal do Catálogo de Dados do Azure.

3. Use credenciais e autenticação apropriadas para acessar o ativo de dados. Se você não tem acesso, use as informações no campo **Solicitar Acesso** para obtê-lo.

   ![Catálogo de Dados do Azure – solicitar acesso](media/register-data-assets-tutorial/data-catalog-request-access.png)

Selecione **Exibir Cadeias de Conexão** para exibir e copiar as cadeias de conexão ADO.NET, ODBC e OLEDB na área de transferência para usar em seu aplicativo.

## <a name="manage-data-assets"></a>Gerenciar ativos de dados

Nesta etapa, você vê como configurar a segurança para seus ativos de dados. O Catálogo de Dados não dá aos usuários acesso aos dados em si. O proprietário da fonte de dados controla o acesso aos dados.

Você pode usar o Catálogo de Dados para descobrir fontes de dados e exibir os metadados relacionados às fontes registradas no catálogo. Porém, pode haver situações em que as fontes de dados só devam estar visíveis para usuários específicos ou membros de grupos. Para esses cenários, você pode usar o Catálogo de Dados para se apropriar de ativos de dados registrados e controlar a visibilidade dos ativos que pertencem a você.

> [!NOTE]
> Os recursos de gerenciamento descritos neste exercício estão disponíveis apenas na Edição Standard do Catálogo de Dados do Azure, e não na Edição Gratuita.
> No Catálogo de Dados do Azure, você pode apropriar-se dos ativos de dados, adicionar coproprietários e definir a visibilidade dos ativos de dados.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Apropriar-se dos ativos de dados e restringir a visibilidade

1. Vá para a [home page do catálogo de dados do Azure](https://www.azuredatacatalog.com). Na caixa de texto **Pesquisar**, insira `tags:cycles` e pressione **ENTER**.

2. Selecione um item na lista de resultados e selecione **Apropriar-se** na barra de ferramentas.

3. Na seção **Gerenciamento** do painel **Propriedades**, selecione **Apropriar-se**.

    ![Catálogo de Dados do Azure – apropriar-se](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Para restringir a visibilidade, escolha **Proprietários e Esses Usuários** na seção **Visibilidade** e selecione **Adicionar**. Insira os endereços de email do usuário na caixa de texto e pressione **ENTER**.

    ![Catálogo de Dados do Azure – restringir acesso](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Remover ativos de dados

Neste exercício, você usa o portal do Catálogo de Dados do Azure para remover dados de visualização de ativos de dados registrados e excluir ativos de dados do catálogo.

No Catálogo de Dados do Azure, você pode eliminar um ativo individual ou excluir vários ativos.

1. Vá para a [home page do catálogo de dados do Azure](https://www.azuredatacatalog.com).

2. Na caixa de texto **Pesquisa**, insira `tags:cycles` e selecione **ENTER**.

3. Selecione um item na lista de resultados e selecione **Excluir** na barra de ferramentas, conforme mostrado na imagem a seguir:

   ![Catálogo de Dados do Azure – excluir o item de grade](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

   Se você estiver usando o modo de exibição de lista, a caixa de seleção estará à esquerda do item, conforme mostrado na imagem a seguir:

   ![Catálogo de Dados do Azure – excluir o item de lista](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

   Você também pode selecionar vários ativos de dados e excluí-los, conforme mostrado na imagem abaixo:

   ![Catálogo de Dados do Azure – excluir vários ativos de dados](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> O comportamento padrão do catálogo é permitir que qualquer usuário registre qualquer fonte de dados, bem como permitir que qualquer usuário exclua qualquer ativo de dados que foi registrado. Os recursos de gerenciamento incluídos na Edição Standard do Catálogo de Dados do Azure fornecem opções adicionais para a apropriação dos ativos, a restrição de quem pode descobri-los e a restrição de quem pode excluí-los.

## <a name="clean-up-resources"></a>Limpar os recursos

Siga as etapas em [Remover ativos de dados](#remove-data-assets) para limpar os ativos que você usou ao seguir este tutorial.

## <a name="summary"></a>Resumo

Neste tutorial, você explorou recursos essenciais do Catálogo de Dados do Azure, incluindo o registro, a anotação, a descoberta e o gerenciamento de ativos de dados corporativos. Agora que você concluiu o tutorial, é hora de começar. Você pode começar hoje mesmo registrando as fontes de dados que você e sua equipe precisam e convidando colegas para usar o catálogo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Fontes de dados com suporte](data-catalog-dsr.md)
