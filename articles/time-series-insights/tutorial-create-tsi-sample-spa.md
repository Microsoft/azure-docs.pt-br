---
title: 'Tutorial: Criar um aplicativo Web de página única do Azure Time Series Insights | Microsoft Docs'
description: Saiba como criar um aplicativo Web de página única que consulta e renderiza dados de um ambiente do Azure Time Series Insights.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: bd50fb4a28aa0ab71c1fb0aeba772a2bd7d1df9d
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677736"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Tutorial: Criar um aplicativo Web de página única do Azure Time Series Insights

Este tutorial orienta você pelo processo de criação de seu próprio SPA (aplicativo Web de página única) para acessar dados do Azure Time Series Insights.

Neste tutorial, você aprenderá:

> [!div class="checklist"]
> * O design do aplicativo
> * Como registrar o aplicativo no Azure AD (Azure Active Directory)
> * Como criar, publicar e testar o aplicativo Web

> [!NOTE]
> * O código-fonte completo deste tutorial está disponível no [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * O [aplicativo de exemplo do cliente](https://insights.timeseries.azure.com/clientsample) do Time Series Insights está hospedado para mostrar o aplicativo concluído usado neste tutorial.

Inscreva-se em uma [assinatura do Azure gratuita](https://azure.microsoft.com/free/) se já não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* Uma cópia gratuita do Visual Studio. Baixe as [versões Community 2017 ou 2019](https://www.visualstudio.com/downloads/) para começar.

* Os componentes das principais ferramentas do IIS Express, da Implantação da Web e dos Serviços de Nuvem do Azure para o Visual Studio. Adicione os componentes modificando sua instalação do Visual Studio.

## <a name="application-design"></a>Design do aplicativo

O SPA de exemplo do Time Series Insights é a base para o design e o código usados neste tutorial. O código usa a biblioteca de clientes JavaScript do Time Series Insights. A biblioteca de clientes do Time Series Insights fornece uma abstração para duas categorias principais de API:

- **Métodos de wrapper para chamar as APIs de consulta do Time Series Insights**: As APIs REST que você pode usar para consultar dados do Time Series Insights utilizando expressões baseadas em JSON. Os métodos estão organizados no namespace TsiClient.server da biblioteca.

- **Métodos para criar e popular os vários tipos de controles de criação de gráficos**: Métodos que você pode usar para visualizar dados do Time Series Insights em uma página da Web. Os métodos estão organizados no namespace TsiClient.ux da biblioteca.

Este tutorial também usa os dados do ambiente do Time Series Insights do aplicativo de exemplo. Para saber detalhes sobre a estrutura do aplicativo de exemplo e como ele usa a biblioteca de clientes do Time Series Insights, confira o tutorial [Explorar a biblioteca de clientes JavaScript do Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Registrar o aplicativo no Azure AD

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="build-and-publish-the-web-application"></a>Compilar e publicar o aplicativo Web

1. Crie um diretório para armazenar os arquivos do projeto de aplicativo. Em seguida, acesse cada uma das URLs a seguir. Clique com o botão direito do mouse no link **Bruto** no canto superior direito da página e selecione **Salvar como** para salvar os arquivos no diretório do projeto.

   - [*index.html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): HTML e JavaScript da página
   - [*sampleStyles.css*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): a folha de estilos CSS

   > [!NOTE]
   > Dependendo do navegador, você precisará alterar as extensões de arquivo para .html ou .css antes de salvá-lo.

1. Verifique se que os componentes necessários estão instalados no Visual Studio. Os componentes das principais ferramentas do IIS Express, da Implantação da Web e dos Serviços de Nuvem do Azure precisam ser instalados.

    [![Visual Studio – Modificar os componentes instalados](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Sua experiência do Visual Studio pode variar um pouco dos exemplos descritos dependendo de suas definições de configuração e versão.

1. Abra o Visual Studio e entre. Para criar um projeto para o aplicativo Web, no menu **Arquivo**, selecione **Abrir** > **Site da Web**.

    [![Visual Studio – Criar uma nova solução](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. No painel **Abrir Site da Web**, selecione o diretório de trabalho em que você armazenou os arquivos HTML e CSS e clique em **Abrir**.

   [![Visual Studio – O menu Arquivo, com as opções de Abrir e Site da Web](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. No menu **Exibição** do Visual Studio, selecione **Gerenciador de Soluções**. A nova solução é aberta. Ela contém um projeto de site (ícone de globo), que contém os arquivos HTML e CSS.

   [![Visual Studio – A nova solução no Gerenciador de Soluções](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Antes de publicar o aplicativo, altere as definições de configuração em *index. HTML*.

   1. Remova a marca de comentário das três linhas abaixo do comentário `"PROD RESOURCE LINKS"` para alternar as dependências de DESENVOLVIMENTO para PRODUÇÃO. Comente as três linhas abaixo do comentário `"DEV RESOURCE LINKS"`.

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      As dependências devem ser comentadas como no exemplo a seguir:

      ```HTML
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css">

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="../../dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="../../dist/tsiclient.css"> -->
      ```

   1. Para configurar o aplicativo a fim de usar a ID de registro do aplicativo do Azure AD, altere o valor de `clientID` para usar a **ID do aplicativo** copiada na **etapa 3** ao [registrar o aplicativo para usar o Azure AD](#register-the-application-with-azure-ad). Se você tiver criado uma **URL de logoff** no Azure Active Directory, defina esse valor como `postLogoutRedirectUri`.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Por exemplo:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. Quando você terminar de fazer modificações, salve *index.html*.

1. Publique o aplicativo Web em sua assinatura do Azure como um Serviço de Aplicativo do Azure.  

   > [!NOTE]
   > Várias opções nas capturas de tela que são mostradas nas etapas a seguir são preenchidas automaticamente com os dados de sua assinatura do Azure. Pode levar alguns segundos para cada painel carregar completamente.  

   1. No Gerenciador de Soluções, clique com o botão direito do mouse no nó do projeto de site da Web e selecione **Publicar Aplicativo Web**.  

      [![Visual Studio – Selecionar a opção Publicar Aplicativo Web no Gerenciador de Soluções](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Selecione **Iniciar** para começar a publicar o aplicativo.

      [![Visual Studio – o painel Perfil de publicação](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Selecione a assinatura que você quer usar para publicar o aplicativo. Selecione o projeto **TsiSpaApp**. Depois, selecione **OK**.

      [![Visual Studio – o painel do Serviço de Aplicativo Perfil de publicação](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Clique em **Publicar** para implantar o aplicativo Web.

      [![Visual Studio – A opção Publicar e a saída de log de publicação](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. Você deve ver um log de publicação bem-sucedido no painel **Saída** do Visual Studio. Após a implantação, o Visual Studio abrirá o aplicativo Web em uma guia do navegador e solicitará a entrada. Depois de entrar, os controles do Time Series Insights serão preenchidos com os dados.

## <a name="troubleshoot"></a>Solucionar problemas  

Condição/código de erro | DESCRIÇÃO
---------------------| -----------
*AADSTS50011: Nenhum endereço de resposta está registrado para o aplicativo.* | O registro do Azure AD não tem a propriedade **URL de resposta**. Acesse **Configurações** > **URLs de Resposta** do registro de aplicativo do Azure AD. Verifique se no **URI de redirecionamento** havia a opção de especificar na **etapa 2** ao [registrar o aplicativo para usar o Azure Active Directory](#register-the-application-with-azure-ad).
*AADSTS50011: A URL de resposta especificada na solicitação não corresponde às URLs de resposta configuradas para o aplicativo: '\<GUID da ID de Aplicativo>'.* | O `postLogoutRedirectUri` especificado na **etapa 6** de [Compilar e publicar o aplicativo Web](#build-and-publish-the-web-application) deve corresponder ao valor especificado em **Configurações** > **URLs de Resposta** no registro de aplicativo do Azure AD. Também não se esqueça de alterar o valor da **URL de destino** para usar *https* de acordo com a **etapa 5** em [Compilar e publicar o aplicativo Web](#build-and-publish-the-web-application).
O aplicativo Web é carregado, mas tem uma página de entrada somente texto e sem estilo, com uma tela de fundo branca. | Verifique se os caminhos discutidos na **etapa 4** de [Compilar e publicar o aplicativo Web](#build-and-publish-the-web-application) estão corretos. Se o aplicativo Web não puder localizar os arquivos .css, a página não será estilizada corretamente.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial cria vários serviços do Azure em execução. Se você não planeja concluir esta série de tutoriais, recomendamos a exclusão de todos os recursos para evitar incorrer em custos desnecessários.

No menu esquerdo do portal do Azure:

1. Clique em **Grupos de recursos**, depois selecione o grupo de recursos que você criou para o ambiente do Time Series Insights. Na parte superior da página, clique em **Excluir grupo de recursos**, insira o nome do grupo de recursos e, em seguida, clique em **Excluir**.
1. Clique em **Grupos de recursos**, depois selecione o grupo de recursos que foi criado com o acelerador de solução de simulação do dispositivo. Na parte superior da página, clique em **Excluir grupo de recursos**, insira o nome do grupo de recursos e, em seguida, clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * O design do aplicativo
> * Como registrar o aplicativo no Azure AD
> * Como criar, publicar e testar o aplicativo Web

Este tutorial se integra ao Azure AD e usa a identidade do usuário conectado para adquirir um token de acesso. Para saber como acessar a API do Time Series Insights usando a identidade de um aplicativo de serviço ou daemon, confira este artigo:

> [!div class="nextstepaction"]
> [Autenticação e autorização para API do Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
