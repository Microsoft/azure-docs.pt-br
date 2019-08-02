---
title: 'Início rápido do Node. js: Criar, carregar e consultar índices usando APIs de REST de pesquisa do Azure - Azure Search'
description: Explica como criar um índice, carregar os dados e executar consultas usando Node. js e as APIs de REST de pesquisa do Azure.
author: jj09
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/26/2017
ms.author: jjed
ms.custom: seodec2018
ms.openlocfilehash: 44b7f1f49d6764418dcc0e72cb667e17a2b920c6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450035"
---
# <a name="quickstart-create-an-azure-search-index-in-nodejs"></a>Início Rápido: Criar um índice de Azure Search no Node. js
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Aprenda a criar um aplicativo de pesquisa Node.js personalizado que usa o Azure Search para a sua experiência de pesquisa. O tutorial usa a [API REST do Serviço de Azure Search](https://msdn.microsoft.com/library/dn798935.aspx) para construir os objetos e as operações usados neste exercício.

Usamos o [Node.js](https://Nodejs.org) e o NPM, o [Sublime Text 3](https://www.sublimetext.com/3) e o Windows PowerShell no Windows 8.1 para desenvolver e testar esse código.

Para executar este exemplo, você deve ter um serviço do Azure Search, no qual pode se inscrever no [portal do Azure](https://portal.azure.com). Consulte [Criar um serviço de Azure Search no portal](search-create-service-portal.md) para encontrar instruções passo a passo.

## <a name="about-the-data"></a>Sobre os dados
Este exemplo de aplicativo usa dados do [Serviço Geológico dos Estados Unidos (USGS)](https://geonames.usgs.gov/domestic/download_data.htm), filtrados no estado de Rhode Island, para reduzir o tamanho do conjunto de dados. Vamos usar esses dados para criar um aplicativo de pesquisa que retorna prédios de referência, como hospitais e escolas, bem como características geológicas como rios, lagos e picos.

Neste aplicativo, o **DataIndexer** programa cria e carrega o índice usando uma [indexador](https://msdn.microsoft.com/library/azure/dn798918.aspx) constructo, recuperando o conjunto de dados filtrado do USGS de um banco de dados do SQL Azure. As informações de credenciais e de conexão para a fonte de dados online são fornecidas no código do programa. Nenhuma configuração adicional é necessária.

> [!NOTE]
> Aplicamos um filtro a esse conjunto de dados para permanecer abaixo do limite de 10.000 documentos da camada de preços gratuita. Se você usar a camada padrão, esse limite não se aplicará. Para obter detalhes sobre a capacidade de cada tipo de preço, consulte [Limites do serviço Search](search-limits-quotas-capacity.md).
> 
> 

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Localizar o nome do serviço e a chave de api do serviço de Azure Search
Depois de criar o serviço, retorne ao portal para obter a URL ou `api-key`. Conexões com seu serviço Search requerem que você tenha tanto uma URL quanto um `api-key` para autenticar a chamada.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na barra de atalhos, clique em **Serviço de Pesquisa** para listar todos os serviços do Azure Search provisionados para a sua assinatura.
3. Selecione o serviço que você deseja usar.
4. No painel de serviço, você deverá ver blocos com as informações essenciais, como o ícone de chave para acessar as chaves de administrador.
5. Copie a URL do serviço, uma chave de administrador e uma chave de consulta. Você precisa de todos os três mais tarde, ao adicioná-los ao arquivo config.js.

## <a name="download-the-sample-files"></a>Baixe os arquivos do exemplo.
Use qualquer um dos procedimentos a seguir para baixar o exemplo.

1. Vá para [search-node-indexer-demo](https://github.com/Azure-Samples/search-node-indexer-demo).
2. Clique em **Baixar ZIP**, salve o arquivo .zip e, em seguida, extraia todos os arquivos que ele contém.

Todas as modificações de arquivos posteriores e instruções de execução serão feitas nos arquivos nessa pasta.

## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Atualize o config.js. com a URL e a chave de api do serviço Search
Usando a URL e a chave de api que você copiou anteriormente, especifique a URL, a chave de administrador e a chave de consulta no arquivo de configuração.

As chaves de administrador oferecem controle total sobre as operações do serviço, incluindo a criação ou exclusão de um índice e carregamento de documentos. Em contraste, as chaves de consulta servem para operações somente leitura, normalmente usadas por aplicativos cliente que se conectam ao Azure Search.

Neste exemplo, incluímos a chave de consulta para ajudar a reforçar a prática recomendada de usar a chave de consulta em aplicativos cliente.

A captura de tela a seguir mostra **config.js** aberto em um editor de texto, com as entradas relevantes demarcadas para que você possa ver onde deve atualizá-lo com os valores válidos para o serviço de pesquisa.

![][5]

## <a name="host-a-runtime-environment-for-the-sample"></a>Hospedar um ambiente de tempo de execução para o exemplo
O exemplo exige um servidor HTTP, que pode ser instalado globalmente usando npm.

Use uma janela do PowerShell para os comandos a seguir.

1. Navegue até a pasta que contém o arquivo **package.json** .
2. Digite `npm install`.
3. Digite `npm install -g http-server`.

## <a name="build-the-index-and-run-the-application"></a>Crie o índice e executar o aplicativo.
1. Digite `npm run indexDocuments`.
2. Digite `npm run build`.
3. Digite `npm run start_server`.
4. Direcione seu navegador para `http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>Pesquisar em dados USGS
O conjunto de dados do USGS inclui registros relevantes para o estado de Rhode Island. Se você clicar em **Pesquisar** em uma caixa de pesquisa vazia, obterá as 50 entradas principais, que é o valor padrão.

A inserção de um termo de pesquisa fornece ao mecanismo de pesquisa algo para seguir. Tente inserir um nome regional. "Roger Williams" foi o primeiro governador de Rhode Island. Vários parques, edifícios e escolas receberam seus nomes em homenagem a ele.

![][9]

Você também pode tentar qualquer um destes termos:

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Próximas etapas
Este é o primeiro tutorial do Azure Search com base no Node.js e no conjunto de dados do USGS. Ao longo do tempo, ampliaremos este tutorial para demonstrar outros recursos de pesquisa que talvez você queira usar em suas soluções personalizadas.

Se você já tiver alguma experiência com o Azure Search, use este exemplo como um trampolim para experimentar sugestões (consultas de preenchimento automático ou de digitação antecipada), filtros e navegação facetada. Você também pode melhorar a página de resultados da pesquisa adicionando contagens e documentos em lote para que os usuários possam percorrer os resultados.

Ainda não conhece o Azure Search? Recomendamos os outros tutoriais para que você compreenda o que pode criar. Visite nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) para encontrar mais recursos. 

<!--Image references-->
[1]: ./media/search-get-started-Nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-Nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-Nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-Nodejs/AzSearch-Nodejs-configjs.png
[9]: ./media/search-get-started-Nodejs/rogerwilliamsschool.png
