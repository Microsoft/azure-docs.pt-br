---
title: "Introdução ao Azure Search no Java | Microsoft Docs"
description: "Como criar um aplicativo de pesquisa hospedado na nuvem no Azure usando Java como linguagem de programação."
services: search
documentationcenter: 
author: EvanBoyle
manager: pablocas
editor: v-lincan
ms.assetid: 8b4df3c9-3ae5-4e3a-b4bb-74b516a91c8e
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 07/14/2016
ms.author: evboyle
ms.openlocfilehash: f6ca06a0349def97b38a1bf6d0d8f36236077e92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-search-in-java"></a>Introdução à Pesquisa do Azure em Java
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Aprenda a criar um aplicativo de pesquisa Java personalizado que usa a Pesquisa do Azure para sua experiência de pesquisa. O tutorial usa a [API REST do Serviço de Pesquisa do Azure](https://msdn.microsoft.com/library/dn798935.aspx) para construir os objetos e as operações usados neste exercício.

Para executar este exemplo, você deve ter um serviço de Pesquisa do Azure, no qual pode se inscrever no [Portal do Azure](https://portal.azure.com). Consulte [Criar um serviço de Pesquisa do Azure no portal](search-create-service-portal.md) para encontrar instruções passo a passo.

Para compilar e testar este exemplo, usamos o seguinte software:

* [Eclipse IDE para desenvolvedores de Java EE](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Certifique-se de baixar a versão EE. Uma das etapas de verificação requer um recurso que está apenas nesta edição.
* [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## <a name="about-the-data"></a>Sobre os dados
Este exemplo de aplicativo usa dados do [Serviço Geológico dos Estados Unidos (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), filtrados no estado de Rhode Island, para reduzir o tamanho do conjunto de dados. Vamos usar esses dados para criar um aplicativo de pesquisa que retorna prédios de referência, como hospitais e escolas, bem como características geológicas como rios, lagos e picos.

Neste aplicativo, o programa **SearchServlet.java** cria e carrega o índice usando uma construção [Indexador](https://msdn.microsoft.com/library/azure/dn798918.aspx) , recuperando o conjunto de dados filtrado do USGS por meio de um Banco de dados SQL do Azure público. Credenciais predefinidas e informações de conexão para a fonte de dados online são fornecidas no código do programa. Em termos de acesso a dados, nenhuma configuração adicional é necessária.

> [!NOTE]
> Aplicamos um filtro a esse conjunto de dados para permanecer abaixo do limite de 10.000 documentos da camada de preços gratuita. Se você usar a camada padrão, esse limite não se aplica e você pode modificar este código para usar um conjunto de dados maior. Para obter detalhes sobre a capacidade de cada camada de preços, consulte [Limites e restrições](search-limits-quotas-capacity.md).
> 
> 

## <a name="about-the-program-files"></a>Sobre os arquivos de programa
A lista a seguir descreve os arquivos que são relevantes para este exemplo.

* Search.jsp: fornece a interface do usuário
* SearchServlet.java: fornece métodos (semelhantes a um controlador em MVC)
* SearchServiceClient.java: lida com solicitações HTTP
* SearchServiceHelper.java: uma classe auxiliar que fornece métodos estáticos
* Document.Java: fornece o modelo de dados
* config.properties: define a URL do serviço de Pesquisa e a chave de Api
* Pom.xml: uma dependência do Maven

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Localizar o nome do serviço e a chave de api do serviço de Pesquisa do Azure
Todas as chamadas da API REST na Pesquisa do Azure exigem que você forneça a URL do serviço e uma chave de api. 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na barra de atalhos, clique em **Serviço de Pesquisa** para listar todos os serviços da Pesquisa do Azure provisionados para sua assinatura.
3. Selecione o serviço que você deseja usar.
4. No painel de serviço, você verá blocos com as informações essenciais e o ícone de chave para acessar as chaves de administrador.
   
      ![][3]
5. Copie a URL do serviço e uma chave de administrador. Você precisará dos três posteriormente, ao adicioná-los ao arquivo **config.properties** .

## <a name="download-the-sample-files"></a>Baixe os arquivos do exemplo.
1. Vá para [AzureSearchJavaDemo](https://github.com/AzureSearch/AzureSearchJavaIndexerDemo) no GitHub.
2. Clique em **Baixar ZIP**, salve o arquivo .zip no disco e, em seguida, extraia todos os arquivos que ele contém. Considere a possibilidade de extrair os arquivos no espaço de trabalho Java para facilitar a localização do projeto posteriormente.
3. Os arquivos de exemplo são somente leitura. Clique com o botão direito em propriedades da pasta e limpe o atributo somente leitura.

Todas as modificações de arquivos subsequentes e instruções de execução serão feitas nos arquivos nessa pasta.  

## <a name="import-project"></a>Projeto de importação
1. No Eclipse, escolha **Arquivo** > **Importar** > **Geral** > **Projetos Existentes no Espaço de Trabalho**.
   
    ![][4]
2. Em **Selecionar diretório raiz**, navegue até a pasta que contém os arquivos de exemplo. Selecione a pasta que contém a pasta .project. O projeto deve aparecer na lista **Projetos** como um item selecionado.
   
    ![][12]
3. Clique em **Concluir**.
4. Use o **Gerenciador de Projetos** para exibir e editar os arquivos. Se não ainda estiver aberto, clique em **Janela** > **Exibição** > **Gerenciador de Projetos** ou use o atalho para abri-lo.

## <a name="configure-the-service-url-and-api-key"></a>Configurar a URL de serviço e a chave de API
1. Em **Gerenciador de Projetos**, clique duas vezes em **config.properties** para editar as definições de configuração que contêm o nome do servidor e a chave de Api.
2. Consulte as etapas explicadas anteriormente neste artigo, em que você encontrou a URL de serviço e a chave de API no [Portal do Azure](https://portal.azure.com), para obter os valores que você vai inserir agora em **config.properties**.
3. Em **config.properties**, substitua "Chave Api" com a chave de Api para o serviço. Em seguida, o nome do serviço (o primeiro componente da URL http://servicename.search.windows.net) substitui o "nome do serviço" no mesmo arquivo.
   
    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Configurar ambientes de projeto, compilação e tempo de execução
1. No Eclipse, no Gerenciador de Projetos, clique com o botão direito do mouse no projeto > **Propriedades** > **Facetas do Projeto**.
2. Selecione **Módulo da Web dinâmico**, **Java** e **JavaScript**.
   
    ![][6]
3. Clique em **Aplicar**.
4. Selecione **Janela** > **Preferências** > **Servidor** > **Ambientes de tempo de execução** > **Adicionar..**.
5. Expanda o Apache e selecione a versão do servidor Apache Tomcat instalado anteriormente. Em nosso sistema, instalamos a versão 8.
   
    ![][7]
6. Na próxima página, especifique o diretório de instalação do Tomcat. Em um computador Windows, isso provavelmente será C:\Arquivos de Programas\Apache Software Foundation\Tomcat *versão*.
7. Clique em **Concluir**.
8. Selecione **Janela** > **Preferências** > **Java** > **JREs Instalados** > **Adicionar**.
9. Em **Adicionar JRE**, selecione **VM padrão**.
10. Clique em **Avançar**.
11. Na Definição do JRE, na página inicial do JRE, clique em **Diretório**.
12. Navegue até **Arquivos de Programas** > **Java** e selecione o JDK instalado anteriormente. É importante selecionar o JDK como o JRE.
13. Em JREs instalados, escolha o **JDK**. Suas configurações devem ter aparência semelhante à captura de tela a seguir.
    
    ![][9]
14. Opcionalmente, selecione **Janela** > **Navegador da Web** > **Internet Explorer** para abrir o aplicativo em uma janela do navegador externo. Usar um navegador externo oferece uma melhor experiência de aplicativo Web.
    
    ![][8]

Agora, você concluiu as tarefas de configuração. Em seguida, você compilará e executará o projeto.

## <a name="build-the-project"></a>Compilar o projeto
1. No Gerenciador de Projetos, clique com o botão direito do mouse no nome do projeto e escolha **Executar como** > **Compilação Maven...** para configurar o projeto.
   
    ![][10]
2. Em Editar configurações, em Metas, digite "instalação limpa" e, em seguida, clique em **Executar**.

Mensagens de status são passadas para a janela do console. Você deve ver a mensagem COMPILAÇÃO BEM-SUCEDIDA, indicando que o foi projeto compilado sem erros.

## <a name="run-the-app"></a>Executar o aplicativo
Nesta última etapa, você executará o aplicativo em um ambiente de tempo de execução do servidor local.

Se você ainda não especificou um ambiente de tempo de execução do servidor no Eclipse, você precisará fazer isso primeiro.

1. No Gerenciador de Projetos, expanda **WebContent**.
2. Clique com o botão direito do mouse em **Search.jsp** > **Executar como** > **Executar no Servidor**. Selecione o servidor Apache Tomcat e, em seguida, clique em **Executar**.

> [!TIP]
> Se você tiver usado um espaço de trabalho não padrão para armazenar seu projeto, precisará modificar a **Configuração de Execução** para indicar o local do projeto para evitar um erro de inicialização do servidor. No Gerenciador de Projetos, clique com o botão direito do mouse em **Search.jsp** > **Executar como** > **Configurações de Execução**. Selecione o servidor Apache Tomcat. Clique em **Argumentos**. Clique em **Espaço de Trabalho** ou **Sistema de Arquivos** para definir a pasta que contém o projeto.
> 
> 

Quando você executar o aplicativo, verá uma janela do navegador, fornecendo uma caixa de pesquisa para inserir termos.

Aguarde aproximadamente um minuto antes de clicar em **Pesquisa** para dar ao serviço tempo de criar e carregar o índice. Se você receber um erro HTTP 404, basta esperar um pouco mais antes de tentar novamente.

## <a name="search-on-usgs-data"></a>Pesquisar em dados USGS
O conjunto de dados do USGS inclui registros relevantes para o estado de Rhode Island. Se você clicar em **Pesquisar** em uma caixa de pesquisa vazia, obterá as 50 entradas principais, que é o valor padrão.

A inserção de um termo de pesquisa fornecerá ao mecanismo de pesquisa algo para seguir. Tente inserir um nome regional. "Roger Williams" foi o primeiro governador de Rhode Island. Vários parques, edifícios e escolas receberam seus nomes em homenagem a ele.

![][11]

Você também pode tentar qualquer um destes termos:

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Próximas etapas
Este é o primeiro tutorial da Pesquisa do Azure com base em Java e no conjunto de dados do USGS. Ao longo do tempo, ampliaremos este tutorial para demonstrar outros recursos de pesquisa que talvez você queira usar em suas soluções personalizadas.

Se você já tiver algum conhecimento sobre o Azure Search, use este exemplo como um trampolim para experimentos adicionais, talvez aumentando a [página de pesquisa](search-pagination-page-layout.md) ou implementando [navegação facetada](search-faceted-navigation.md). Você também pode melhorar a página de resultados da pesquisa adicionando contagens e documentos em lote para que os usuários possam percorrer os resultados.

Ainda não conhece a Pesquisa do Azure? Recomendamos os outros tutoriais para que você compreenda o que pode criar. Visite nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) para encontrar mais recursos. Você também pode exibir os links em nossa [Lista de vídeos e Tutorial](search-video-demo-tutorial-list.md) para acessar mais informações.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
