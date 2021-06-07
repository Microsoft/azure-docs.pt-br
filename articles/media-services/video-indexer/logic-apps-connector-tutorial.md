---
title: Tutorial dos conectores do Video Indexer com o Aplicativo Lógico e o Power Automate.
description: Este tutorial mostra como aproveitar novas experiências e oportunidades de monetização dos conectores do Video Indexer com o Aplicativo Lógico e o Power Automate.
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: caff6a2496e907da1bdc140860c47476d1842df4
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600668"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Tutorial: usar o Video Indexer com o Aplicativo Lógico e o Power Automate

A [API REST do Video Indexer v2](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Video) dos Serviços de Mídia do Azure dá suporte à comunicação de servidor para servidor e de cliente para servidor, além de permitir que os usuários do Video Indexer integrem insights de áudio e vídeo facilmente à lógica do aplicativo, proporcionando novas experiências e oportunidades de monetização.

Para facilitar ainda mais a integração, temos suporte para os conectores dos  [Aplicativos Lógicos](https://azure.microsoft.com/services/logic-apps/) e do [Power Automate](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/) , que são compatíveis com nossa API. Você pode usar os conectores para configurar fluxos de trabalho personalizados a fim de indexar e extrair insights de uma grande quantidade de arquivos de vídeo e áudio, sem escrever uma só linha de código. Além disso, o uso dos conectores para sua integração proporciona melhor visibilidade da integridade do fluxo de trabalho e uma forma fácil de depurá-lo.  

Para ajudar você a se familiarizar rapidamente com os conectores do Video Indexer, percorreremos um exemplo de uma solução do Aplicativo Lógico e do Power Automate que você pode configurar. Este tutorial mostra como configurar fluxos usando os Aplicativos Lógicos. No entanto, os editores e as funcionalidades são quase idênticos em ambas as soluções, portanto, os diagramas e as explicações são aplicáveis aos Aplicativos Lógicos e ao Power Automate.

O cenário "carregar e indexar o vídeo automaticamente", abordado neste tutorial, é composto por dois fluxos diferentes que funcionam em conjunto. 
* O primeiro fluxo é disparado quando um blob é adicionado ou modificado em uma conta de Armazenamento do Azure. Ele carrega o novo arquivo no Video Indexer com uma URL de retorno de chamada para enviar uma notificação após a conclusão da operação de indexação. 
* O segundo fluxo é disparado com base na URL de retorno de chamada e salva os insights extraídos em um arquivo JSON no Armazenamento do Azure. Essa abordagem de dois fluxos é usada para dar suporte ao upload e à indexação assíncronos de arquivos maiores com eficiência. 

Este tutorial está usando os Aplicativos Lógicos para mostrar como:

> [!div class="checklist"]
> * Configurar o fluxo de upload do arquivo
> * Configurar o fluxo de extração JSON

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Para começar, você precisará de uma conta do Video Indexer e de [acesso às APIs por meio da chave de API](video-indexer-use-apis.md). 
* Você também precisará de uma conta de armazenamento do Azure. Anote a chave de acesso de sua conta de armazenamento. Crie dois contêineres – um para armazenar vídeos e outro para armazenar os insights gerados pelo Video Indexer.  
* Em seguida, você precisará abrir dois fluxos separados nos Aplicativos Lógicos ou no Power Automate (dependendo do que estiver usando). 

## <a name="set-up-the-first-flow---file-upload"></a>Configurar o primeiro fluxo – upload do arquivo   

O primeiro fluxo é disparado sempre que um blob é adicionado ao contêiner do Armazenamento do Azure. Uma vez disparado, ele cria um URI de SAS que você pode usar para carregar e indexar o vídeo no Video Indexer. Nesta seção, você criará o fluxo a seguir. 

![Fluxo de upload de arquivo](./media/logic-apps-connector-tutorial/file-upload-flow.png)

Para configurar o primeiro fluxo, você precisará fornecer sua chave de API do Video Indexer e as credenciais do Armazenamento do Azure. 

![Armazenamento do blob do Azure](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Nome de conexão e chave da API](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

> [!TIP]
> Se você tiver conectado anteriormente uma conta de Armazenamento do Azure ou uma conta do Video Indexer a um Aplicativo Lógico, os detalhes de conexão serão armazenados e você será conectado automaticamente. <br/>Você pode editar a conexão clicando em **Alterar conexão** na parte inferior de um Armazenamento do Azure (a janela de armazenamento) ou da ação do Video Indexer (a janela do player).

Quando você puder se conectar ao Armazenamento do Azure e às contas do Video Indexer, localize e escolha o gatilho "Quando um blob é adicionado ou modificado", no **Designer dos Aplicativos Lógicos**.

Selecione o contêiner em que você colocará os arquivos de vídeo. 

![Captura de tela mostrando a caixa de diálogo Quando um blob é adicionado ou modificado, em que você pode selecionar um contêiner.](./media/logic-apps-connector-tutorial/container.png)

Em seguida, localize e selecione a ação "Criar URI de SAS por caminho". Na caixa de diálogo da ação, selecione Lista de caminhos de arquivos nas opções de Conteúdo dinâmico.  

Além disso, adicione um novo parâmetro "Protocolo de acesso compartilhado". Escolha HttpsOnly como valor do parâmetro.

![URI do SAS pelo caminho](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Preencha a [localização da conta](regions.md) e a [ID da conta](./video-indexer-use-apis.md#account-id) para obter o token da conta do Video Indexer.

![Obter token de acesso da conta](./media/logic-apps-connector-tutorial/account-access-token.png)

Para "Carregar vídeo e índice", preencha os parâmetros necessários e a URL do vídeo. Selecione “Adicionar novo parâmetro” e selecione URL de Retorno de chamada. 

![Carregar e indexar](./media/logic-apps-connector-tutorial/upload-and-index.png)

Você deixará a URL de retorno de chamada vazia por enquanto. Você a adicionará somente após concluir o segundo fluxo, em que a URL de retorno de chamada é criada. 

Use o valor padrão para os outros parâmetros ou defina-os de acordo com suas necessidades. 

Clique em **Salvar** e vamos configurar o segundo fluxo, a fim de extrair os insights depois que o upload e a indexação forem concluídos. 

## <a name="set-up-the-second-flow---json-extraction"></a>Configurar o segundo fluxo – extração do JSON  

A conclusão do upload e da indexação no primeiro fluxo envia uma solicitação HTTP com a URL de retorno de chamada correta para disparar o segundo fluxo. Em seguida, ele recupera os insights gerados pelo Video Indexer. Neste exemplo, ele armazena a saída de seu trabalho de indexação no Armazenamento do Azure.  No entanto, você decide o que deseja fazer com a saída.  

Crie o segundo fluxo separado do primeiro. 

![Fluxo de extração JSON](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

Para configurar esse fluxo, você precisará fornecer sua chave de API do Video Indexer e as credenciais do Armazenamento do Azure novamente. Você precisará atualizar os mesmos parâmetros que atualizou para o primeiro fluxo. 

Para o gatilho, você verá um campo URL HTTP POST. A URL não será gerada até que você salve o fluxo. No entanto, você precisará dela em algum momento. Voltaremos a isso mais tarde. 

Preencha a [localização da conta](regions.md) e a [ID da conta](./video-indexer-use-apis.md#account-id) para obter o token da conta do Video Indexer.  

Vá para a ação "Obter Índice de Vídeo" e preencha os parâmetros necessários. Para a ID de Vídeo, insira a seguinte expressão: triggerOutputs()['queries']['id'] 

![informações de ação do Video Indexer](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

Essa expressão instrui o conector a obter a ID de Vídeo da saída do gatilho. Nesse caso, a saída do gatilho será a saída de "Carregar vídeo e índice" no primeiro gatilho. 

Vá para a ação "Criar blob" e selecione o caminho para a pasta na qual você salvará os insights. Defina o nome do blob que você está criando. Para o conteúdo do Blob, insira a seguinte expressão: body(‘Get_Video_Index’) 

![Ação Criar blob](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

Essa expressão usa a saída da ação "Obter Índice de Vídeo" deste fluxo. 

Clique em **Salvar fluxo**. 

Após o fluxo ser salvo, uma URL HTTP POST será criada no gatilho. Copie a URL do gatilho. 

![Salvar gatilho de URL](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Agora, volte para o primeiro fluxo e cole a URL na ação "Carregar vídeo e índice" do parâmetro de URL de retorno de chamada. 

Verifique se os dois fluxos foram salvos e você está pronto! 

Experimente nossa nova solução do Aplicativo Lógico ou do Power Automate adicionando um vídeo ao seu contêiner de BLOBS do Azure e volte alguns minutos mais tarde para ver se os insights aparecem na pasta de destino. 

## <a name="generate-captions"></a>Gerar legendas

Confira o blog a seguir para ver as etapas que mostram [como gerar legendas com o Video Indexer e os Aplicativos Lógicos](https://techcommunity.microsoft.com/t5/azure-media-services/generating-captions-with-video-indexer-and-logic-apps/ba-p/1672198). 

O artigo também mostra como indexar um vídeo automaticamente, copiando-o no OneDrive, e como armazenar as legendas geradas pelo Video Indexer no OneDrive.
 
## <a name="clean-up-resources"></a>Limpar os recursos

Depois de concluir este tutorial, fique à vontade para manter essa solução do Aplicativo Lógico ou do Power Automate em execução se necessário. No entanto, se não quiser mantê-la em execução e não quiser ser cobrado, desligue os dois fluxos se estiver usando o Power Automate. Desabilite os dois fluxos se estiver usando os Aplicativos Lógicos. 

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou apenas um exemplo de conectores do Video Indexer. Você pode usar conectores do Video Indexer para qualquer chamada à API fornecida por ele. Por exemplo: carregar e recuperar insights, converter os resultados, obter widgets incorporáveis e até mesmo personalizar seus modelos. Além disso, você pode optar por disparar essas ações com base em fontes diferentes, como atualizações de repositórios de arquivos ou emails enviados. Você pode, então, optar por fazer com que os resultados sejam atualizados em nossa infraestrutura ou aplicativo relevante ou com que gerem qualquer número de itens de ação.  

> [!div class="nextstepaction"]
> [Usar a API do Video Indexer](video-indexer-use-apis.md)

Para obter recursos adicionais, veja este documento sobre o [Video Indexer.](/connectors/videoindexer-v2/)
