---
title: incluir arquivo
description: incluir arquivo
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 472c1770e2793d8da4e8fc76fafbf3b9073b746d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96763376"
---
## <a name="deploy-and-configure-azure-media-services"></a>Implantar e configurar os Serviços de Mídia do Azure

A solução usa uma conta dos Serviços de Mídia do Azure para armazenar os videoclipes de detecção de objetos feitos pelo dispositivo de gateway do IoT Edge.

Ao criar a conta dos Serviços de Mídia:

- Você precisa fornecer um nome de conta, uma assinatura do Azure, uma localização, um grupo de recursos e uma conta de armazenamento. Crie uma conta de armazenamento usando as configurações padrão durante a criação da conta dos Serviços de Mídia.

- Escolha a região **Leste dos EUA** como a localização.

- Crie um grupo de recursos chamado *lva-rg* na região **Leste dos EUA** para os Serviços de Mídia e as contas de armazenamento. Quando você concluir os tutoriais, será fácil remover todos os recursos excluindo o grupo de recursos *lva-rg*.

Crie a [conta dos Serviços de Mídia no portal do Azure](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> Estes tutoriais usam a região **Leste dos EUA** em todos os exemplos. Você poderá usar a região mais próxima se preferir.

Anote o nome da sua conta dos **Serviços de Mídia** no arquivo *scratchpad.txt*.

Quando a implantação for concluída, abra um Cloud Shell e execute o seguinte comando para recuperar a **ID de Recurso** de sua conta de serviço de mídia:

```azurecli
az resource list --resource-group lva-rg --resource-type microsoft.media/mediaservices --output table --query "[].{ResourceID:id}"
```

:::image type="content" source="media/iot-central-video-analytics-part2/get-resource-id.png" alt-text="Usar o Cloud Shell para obter a ID do recurso":::

Anote a **ID do Recurso** no arquivo *scratchpad.txt*; você usará esse valor posteriormente ao configurar o módulo do IoT Edge.

Em seguida, configure uma entidade de serviço do Azure Active Directory para o recurso dos Serviços de Mídia. Selecione **Acesso à API** e **Autenticação da entidade de serviço**. Crie um aplicativo do Azure Active Directory com o mesmo nome do recurso dos Serviços de Mídia e crie um segredo com a descrição *Acesso do IoT Edge*.

:::image type="content" source="./media/iot-central-video-analytics-part2/media-service-authentication.png" alt-text="Configurar um aplicativo do Azure AD para os Serviços de Mídia do Azure":::

Quando o segredo for gerado, role a página para baixo até a seção **Copiar suas credenciais para conectar seu aplicativo de entidade de serviço**. Em seguida, selecione **JSON**. Você pode copiar todas as informações das credenciais aqui de uma só vez. Anote essas informações no arquivo *scratchpad.txt*; você as usará posteriormente ao configurar o dispositivo do IoT Edge.

> [!WARNING]
> Essa é sua única chance de ver e salvar o segredo. Se você perdê-lo, precisará gerar outro segredo.

## <a name="create-the-azure-iot-central-application"></a>Criar o aplicativo do Azure IoT Central

Nesta seção, você cria um novo aplicativo do Azure IoT Central com base em um modelo. Você usará esse aplicativo em toda a série de tutoriais para criar uma solução completa.

Para criar um novo aplicativo Azure IoT Central:

1. Navegue até o site do [Gerenciador de aplicativos do Azure IoT Central](https://aka.ms/iotcentral).

1. Entre com as credenciais usadas para acessar sua assinatura do Azure.

1. Para começar a criar um aplicativo do Azure IoT Central, selecione **Novo Aplicativo** na página **Criar**.

1. Selecione **Varejo**. A página de varejo exibe vários modelos de aplicativos de varejo.

Para criar um aplicativo de análise de vídeo:

1. Selecione o modelo de aplicativo de **detecção de objetos e movimentos e análise de vídeo**. Esse modelo inclui os modelos de todos os dispositivos usados no tutorial. O modelo inclui painéis de exemplo que os operadores podem usar para executar tarefas como monitoramento e gerenciamento de câmeras.

1. Opcionalmente, escolha um **Nome de aplicativo** amigável. Esse aplicativo se baseia em um loja de varejo fictícia chamada Northwind Traders. O tutorial usa o **Nome do aplicativo** *Análise de vídeo da Northwind Traders*.

    > [!NOTE]
    > Se você usar um **Nome de aplicativo** amigável, ainda precisará usar um valor exclusivo para a **URL** do aplicativo.

1. Caso você tenha uma assinatura do Azure, selecione **Diretório**, **assinatura do Azure** e **Estados Unidos** como a **Localização**. Se você não tiver uma assinatura, poderá habilitar a **avaliação gratuita de sete dias** e preencher as informações de contato necessárias. Este tutorial usa três dispositivos: duas câmeras e um dispositivo do IoT Edge; portanto, se você não usar a avaliação gratuita, será cobrado pelo uso.

    Para obter mais informações sobre diretórios, assinaturas e localizações, confira o [guia de início rápido Criar um aplicativo](../articles/iot-central/core/quick-deploy-iot-central.md).

1. Selecione **Criar**.

    :::image type="content" source="./media/iot-central-video-analytics-part2/new-application.png" alt-text="Página Criar aplicativo do Azure IoT Central":::

### <a name="retrieve-the-configuration-data"></a>Recuperar os dados de configuração

Mais adiante neste tutorial, ao configurar o gateway do IoT Edge, você precisará de alguns dados de configuração do aplicativo do IoT Central. O dispositivo do IoT Edge precisa dessas informações para se registrar no aplicativo e se conectar a ele.

Na seção **Administração**, escolha **Seu aplicativo** e anote a **URL do Aplicativo** e a **ID do Aplicativo** no arquivo *scratchpad.txt*:

:::image type="content" source="./media/iot-central-video-analytics-part2/administration.png" alt-text="A captura de tela mostra o painel Administração de uma página de análise de vídeo com a URL e a ID do Aplicativo realçadas.":::

Selecione **Tokens de API** e gere um novo token chamado **LVAEdgeToken** para a função **Operador**:

:::image type="content" source="./media/iot-central-video-analytics-part2/token.png" alt-text="Gerar Token":::

Anote o token no arquivo *scratchpad.txt* para uso posterior. Depois que a caixa de diálogo for fechada, você não poderá ver o token novamente.

Na seção **Administração**, escolha **Conexão do dispositivo** e selecione **SAS-IoT-Dispositivos**.

Anote a **Chave primária** dos dispositivos no arquivo *scratchpad.txt*. Você usará esse *token de Assinatura de Acesso Compartilhado do grupo primário* mais tarde ao configurar o dispositivo do IoT Edge.
