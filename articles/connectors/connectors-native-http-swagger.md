---
title: Conectar-se aos pontos de extremidade REST pelos Aplicativos Lógicos do Azure
description: Monitore os pontos de extremidade REST em tarefas, processos e fluxos de trabalho automatizados com os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: 810aaae9634a7de8d07b6d49edd0c6c2eda96754
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730874"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Chamar pontos de extremidade REST com os Aplicativos Lógicos do Azure

Com os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e o conector HTTP + Swagger interno, você pode automatizar fluxos de trabalho que chamam regularmente qualquer ponto de extremidade REST por meio de um [arquivo Swagger](https://swagger.io) criando aplicativos lógicos. O gatilho e a ação HTTP + Swagger funcionam da mesma forma que o [gatilho e ação HTTP](connectors-native-http.md), mas fornecem uma experiência melhor no Designer de Aplicativos Lógicos ao expor a estrutura da API e as saídas descritas pelo arquivo Swagger. Para implementar um gatilho de sondagem, siga o padrão de sondagem descrito em [Criar APIs personalizadas para chamar outras APIs, serviços e sistemas de aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* A URL para o arquivo Swagger (não OpenAPI) que descreve o ponto de extremidade REST de destino

  Normalmente, o ponto de extremidade REST deve atender a estes critérios para que o conector funcione:

  * O arquivo Swagger deve ser hospedado em uma URL HTTPS publicamente acessível.
  
  * O arquivo Swagger deve conter um `operationID` para cada operação na definição. Caso contrário, o conector mostrará apenas a última operação no arquivo do Swagger. 

  * O arquivo Swagger deve ter o [CORS (compartilhamento de recursos entre origens)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) habilitado.

  Para fazer referência a um arquivo Swagger que não está hospedado ou que não atende aos requisitos de segurança e entre origens, você pode [carregar o arquivo Swagger em um contêiner de blob em uma conta de armazenamento do Azure](#host-swagger) e habilitar o CORS nessa conta de armazenamento para que seja possível referenciar o arquivo.

  Os exemplos neste tópico usam a [API de Detecção Facial dos Serviços Cognitivos](../cognitive-services/face/overview.md), que requer uma [conta no Serviços Cognitivos e uma chave de acesso](../cognitive-services/cognitive-services-apis-create-account.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

* O aplicativo lógico do qual você deseja chamar o ponto de extremidade de destino. Para começar com o gatilho HTTP + Swagger, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar a ação HTTP + Swagger, inicie seu aplicativo lógico com o gatilho que você quiser. Este exemplo usa o gatilho HTTP + Swagger como primeira etapa.

## <a name="add-an-http--swagger-trigger"></a>Adicionar um gatilho HTTP + Swagger

Este gatilho interno envia uma solicitação HTTP a uma URL de um arquivo Swagger que descreve uma API REST e retorna uma resposta com o conteúdo desse arquivo.

1. Entre no [portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico em branco no Designer de Aplicativos Lógicos.

1. No designer, na caixa de pesquisa, digite "swagger" como filtro. Na lista de **Gatilhos**, selecione **HTTP + Swagger**.

   ![Selecionar o gatilho HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Na caixa **URL DO PONTO DE EXTREMIDADE DO SWAGGER**, insira a URL do arquivo Swagger e selecione **Próximo**.

   Este exemplo usa a URL do Swagger localizada na região Oeste dos EUA para a [API de Detecção Facial dos Serviços Cognitivos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Captura de tela que mostra o designer do aplicativo lógico com o gatilho "H T T P + Swagger" e a propriedade "ponto de extremidade do Swagger U R L" definida como um valor U R L.](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Quando o designer mostrar as operações descritas pelo arquivo Swagger, selecione a operação que você deseja usar.

   ![Captura de tela que mostra o designer do aplicativo lógico com o gatilho "H T T P + Swagger" e uma lista que exibe operações do Swagger.](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Forneça os valores para os parâmetros de gatilho (que variam de acordo com a operação selecionada) que você deseja incluir na chamada do ponto de extremidade. Configure a recorrência para definir a frequência com que você deseja que o gatilho chame o ponto de extremidade.

   Este exemplo renomeia o gatilho para "gatilho HTTP + Swagger: Detecção Facial" para que a etapa tenha um nome mais descritivo.

   ![Captura de tela que mostra o designer do aplicativo lógico com o gatilho "H T T P + Swagger" que exibe a operação "detecção de face".](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Para adicionar outros parâmetros disponíveis, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros desejados.

   Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP + Swagger, confira [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Continue criando o fluxo de trabalho do aplicativo lógico com as ações que são executadas quando o gatilho é acionado.

1. Quando tiver terminado, lembre-se de salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

## <a name="add-an-http--swagger-action"></a>Selecionar uma ação HTTP + Swagger

Esta ação interna faz uma solicitação HTTP para a URL do arquivo Swagger que descreve uma API REST e retorna uma resposta com o conteúdo desse arquivo.

1. Entre no [portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico no Logic App Designer.

1. Na etapa em que você deseja adicionar a ação HTTP + Swagger, selecione **Nova etapa**.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. No designer, na caixa de pesquisa, digite "swagger" como filtro. Na lista **Ações**, selecione a ação **HTTP + Swagger**.

    ![Selecionar ação de HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Na caixa **URL DO PONTO DE EXTREMIDADE DO SWAGGER**, insira a URL do arquivo Swagger e selecione **Próximo**.

   Este exemplo usa a URL do Swagger localizada na região Oeste dos EUA para a [API de Detecção Facial dos Serviços Cognitivos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Inserir a URL para o ponto de extremidade do Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Quando o designer mostrar as operações descritas pelo arquivo Swagger, selecione a operação que você deseja usar.

   ![Operações no arquivo Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Forneça os valores para os parâmetros de ação (que variam conforme a operação selecionada) que você deseja incluir na chamada do ponto de extremidade.

   Este exemplo não tem parâmetros, mas renomeia a ação para "ação HTTP + Swagger: Detecção Facial" para que a etapa tenha um nome mais descritivo.

   ![Detalhes da operação](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Para adicionar outros parâmetros disponíveis, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros desejados.

   Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP + Swagger, confira [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Quando tiver terminado, lembre-se de salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Hospedar o Swagger no Armazenamento do Azure

Você pode fazer referência a um arquivo Swagger que não está hospedado ou que não atende aos requisitos de segurança e entre origens carregando o arquivo no contêiner de blob em uma conta de armazenamento do Azure e habilitando o CORS nessa conta de armazenamento. Para criar, configurar e armazenar arquivos Swagger no Armazenamento do Azure, siga estas etapas:

1. [Criar uma conta de Armazenamento do Azure](../storage/common/storage-account-create.md).

1. Agora, habilite o CORS para o blob. No menu da sua conta de armazenamento, selecione **CORS**. Na guia **Serviço Blob**, especifique estes valores e, em seguida, selecione **Salvar**.

   | Propriedade | Valor |
   |----------|-------|
   | **Origens permitidas** | `*` |
   | **Métodos permitidos** | `GET`, `HEAD`, `PUT` |
   | **Cabeçalhos permitidos** | `*` |
   | **Cabeçalhos expostos** | `*` |
   | **Idade máxima** (em segundos) | `200` |
   |||

   Embora esse exemplo use o [portal do Azure](https://portal.azure.com), você pode usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) ou definir essa configuração automaticamente com este exemplo de [script do PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Criar um contêiner de blob](../storage/blobs/storage-quickstart-blobs-portal.md). No painel **Visão geral** do contêiner, selecione **Alterar nível de acesso**. Na lista **Nível de acesso público**, selecione **Blob (acesso de leitura anônimo somente para blobs)** e selecione **OK**.

1. [Carregue o arquivo Swagger no contêiner de blob](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), por meio do [portal do Azure](https://portal.azure.com) ou do [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/).

1. Para fazer referência ao arquivo no contêiner de blob, obtenha a URL HTTPS que segue este formato, que diferencia maiúsculas de minúsculas, no Gerenciador de Armazenamento do Azure:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<complete-swagger-file-name>?<query-parameters>`

## <a name="connector-reference"></a>Referência de conector

Aqui estão mais informações sobre as saídas de um gatilho ou ação HTTP + Swagger. A chamada HTTP + Swagger retorna estas informações:

| Nome da propriedade | Type | Descrição |
|---------------|------|-------------|
| headers | objeto | Os cabeçalhos da solicitação |
| body | objeto | Objeto JSON | O objeto com o conteúdo do corpo da solicitação |
| código de status | INT | O código de status da solicitação |
|||

| Código de status | Descrição |
|-------------|-------------|
| 200 | OK |
| 202 | Aceita |
| 400 | Solicitação incorreta |
| 401 | Não Autorizado |
| 403 | Proibido |
| 404 | Não encontrado |
| 500 | Erro interno do servidor. Ocorreu um erro desconhecido. |
|||

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
