---
title: Se conectar a pontos de extremidade REST de aplicativos lógicos do Azure
description: Monitorar pontos de extremidade REST em fluxos de trabalho, processos e tarefas automatizadas por meio de aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: f0410ed7a98e4838e41407868cf26b5254811ae3
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541713"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Chamar pontos de extremidade REST por meio de aplicativos lógicos do Azure

Com o [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e internos HTTP + Swagger conector, você pode automatizar fluxos de trabalho que regularmente chamar qualquer ponto de extremidade REST por meio de uma [arquivo do Swagger](https://swagger.io) criando aplicativos lógicos. O HTTP + Swagger gatilho e ação funcionam da mesma forma a [gatilho HTTP e a ação](connectors-native-http.md) , mas fornecem uma melhor experiência no Designer do aplicativo lógico, expondo a estrutura de API e as saídas descritas pelo arquivo Swagger. Para implementar um gatilho de sondagem, siga o padrão de sondagem descrito [criar APIs personalizadas para chamar outras APIs, serviços e sistemas de aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* A URL para o arquivo do Swagger que descreve o ponto de extremidade REST de destino

  Normalmente, o ponto de extremidade REST deve atender esses critérios para o conector funcione:

  * O arquivo do Swagger deve ser hospedado em uma URL HTTPS que esteja acessível publicamente.

  * O arquivo do Swagger deve ter [Cross-Origin Resource Sharing (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) habilitado.

  Para fazer referência a um arquivo do Swagger que não esteja hospedado ou que não atenda a requisitos de segurança e entre origens, você pode [carregar o arquivo do Swagger em um contêiner de blob em uma conta de armazenamento do Azure](#host-swagger)e então habilitar o CORS na conta de armazenamento Você pode referenciar o arquivo.

  Os exemplos neste tópico usam o [API de detecção facial dos serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/face/overview), que requer uma [chave de acesso e conta de serviços Cognitivos](../cognitive-services/cognitive-services-apis-create-account.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

* O aplicativo lógico de onde você deseja chamar o ponto de extremidade de destino. Começar com HTTP + Swagger disparar, [criar um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar HTTP + Swagger ação, inicie seu aplicativo lógico com qualquer gatilho que você deseja. Este exemplo usa o HTTP + Swagger gatilho como a primeira etapa.

## <a name="add-an-http--swagger-trigger"></a>Adicionar um HTTP + Swagger gatilho

Esse gatilho interno envia uma solicitação HTTP para uma URL para um arquivo do Swagger que descreve uma API REST e retorna uma resposta que contém o conteúdo do arquivo.

1. Entre no [Portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico em branco no Designer de aplicativo lógico.

1. No designer, na caixa de pesquisa, digite "swagger" como filtro. Dos **disparadores** lista, selecione o **HTTP + Swagger** gatilho.

   ![Selecione HTTP + Swagger disparar](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. No **URL de ponto de EXTREMIDADE do SWAGGER** caixa, digite a URL para o arquivo do Swagger e selecione **próxima**.

   Este exemplo usa a URL de Swagger está localizado na região Oeste dos EUA para o [API de detecção facial dos serviços Cognitivos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Insira a URL para o ponto de extremidade do Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Quando o designer mostra as operações descritas pelo arquivo Swagger, selecione a operação que você deseja usar.

   ![Operações no arquivo do Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Forneça os valores para os parâmetros de gatilho, que variam de acordo com a operação selecionada, o que você deseja incluir na chamada de ponto de extremidade. Configure a recorrência para a frequência com que você deseja que o gatilho para chamar o ponto de extremidade.

   Este exemplo renomeia o gatilho "HTTP + Swagger disparar: Enfrentam - detectar"para que a etapa que tenha um nome mais descritivo.

   ![Detalhes da operação](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Para adicionar outros parâmetros disponíveis, abra o **adicionar novo parâmetro** lista e, em seguida, selecione os parâmetros que você deseja.

   Para obter mais informações sobre tipos de autenticação disponíveis para HTTP + Swagger, consulte [autenticar HTTP gatilhos e ações](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Continue criando o fluxo de trabalho do aplicativo lógico com as ações que são executadas quando o gatilho é acionado.

1. Quando tiver terminado, lembre-se de salvar seu aplicativo lógico. Na barra de ferramentas designer, selecione **salvar**.

## <a name="add-an-http--swagger-action"></a>Adicionar um HTTP + Swagger ação

Essa ação interna faz uma solicitação HTTP para a URL para o arquivo do Swagger que descreve uma API REST e retorna uma resposta que contém o conteúdo do arquivo.

1. Entre no [Portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico no Logic App Designer.

1. Na etapa onde você deseja adicionar o HTTP + Swagger ação, selecione **nova etapa**.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **adicionar uma ação**.

1. No designer, na caixa de pesquisa, digite "swagger" como filtro. Dos **ações** lista, selecione o **HTTP + Swagger** ação.

    ![Selecionar ação de HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. No **URL de ponto de EXTREMIDADE do SWAGGER** caixa, digite a URL para o arquivo do Swagger e selecione **próxima**.

   Este exemplo usa a URL de Swagger está localizado na região Oeste dos EUA para o [API de detecção facial dos serviços Cognitivos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Insira a URL para o ponto de extremidade do Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Quando o designer mostra as operações descritas pelo arquivo Swagger, selecione a operação que você deseja usar.

   ![Operações no arquivo do Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Forneça os valores para os parâmetros de ação, que variam de acordo com a operação selecionada, o que você deseja incluir na chamada de ponto de extremidade.

   Este exemplo não tem parâmetros, mas renomeia a ação a ser "HTTP + Swagger ação: Enfrentam – identificar"para que a etapa que tenha um nome mais descritivo.

   ![Detalhes da operação](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Para adicionar outros parâmetros disponíveis, abra o **adicionar novo parâmetro** lista e, em seguida, selecione os parâmetros que você deseja.

   Para obter mais informações sobre tipos de autenticação disponíveis para HTTP + Swagger, consulte [autenticar HTTP gatilhos e ações](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Quando tiver terminado, lembre-se de salvar seu aplicativo lógico. Na barra de ferramentas designer, selecione **salvar**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Hospedar o Swagger no armazenamento do Azure

Você pode fazer referência a um arquivo do Swagger que não esteja hospedado ou que não atenda a requisitos de segurança e entre origens, carregar esse arquivo no contêiner de blob em uma conta de armazenamento do Azure e habilitando o CORS na conta de armazenamento. Para criar, configurar e armazenar arquivos do Swagger no armazenamento do Azure, siga estas etapas:

1. [Criar uma conta de Armazenamento do Azure](../storage/common/storage-create-storage-account.md).

1. Agora habilite o CORS para o blob. No menu da sua conta de armazenamento, selecione **CORS**. Sobre o **serviço Blob** guia, especifique esses valores e, em seguida, selecione **salvar**.

   | Propriedade | Value |
   |----------|-------|
   | **Origens permitidas** | `*` |
   | **Métodos permitidos** | `GET`, `HEAD`, `PUT` |
   | **Cabeçalhos permitidos** | `*` |
   | **Cabeçalhos expostos** | `*` |
   | **Idade máxima** (em segundos) | `200` |
   |||

   Embora este exemplo usa o [portal do Azure](https://portal.azure.com), você pode usar uma ferramenta como [Azure Storage Explorer](https://storageexplorer.com/), ou definir automaticamente essa configuração usando este exemplo [descriptdoPowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Criar um contêiner de blob](../storage/blobs/storage-quickstart-blobs-portal.md). O contêiner **visão geral** painel, selecione **nível de acesso de alteração**. Dos **nível de acesso público** lista, selecione **Blob (acesso de leitura anônimo somente para blobs)** e selecione **Okey**.

1. [Carregue o arquivo do Swagger para o contêiner de blob](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), seja por meio de [portal do Azure](https://portal.azure.com) ou [Azure Storage Explorer](https://storageexplorer.com/).

1. Para referenciar o arquivo no contêiner de blob, use um link HTTPS que segue este formato, que diferencia maiusculas de minúsculas:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Referência de conector

Eis aqui mais informações sobre as saídas de um HTTP + Swagger gatilho ou ação. HTTP + Swagger chamada retorna essas informações:

| Nome da propriedade | Type | DESCRIÇÃO |
|---------------|------|-------------|
| headers | object | Os cabeçalhos da solicitação |
| body | object | Objeto JSON | O objeto com o conteúdo do corpo da solicitação |
| código de status | int | O código de status da solicitação |
|||

| Código de status | DESCRIÇÃO |
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