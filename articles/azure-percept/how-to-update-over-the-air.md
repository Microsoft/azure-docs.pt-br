---
title: Atualizar seu Percept do Azure DK no ar
description: Saiba como receber as atualizações do ar para o Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 2e627e582b47c5174e70f5d21d758148cde8dbdd
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022835"
---
# <a name="update-your-azure-percept-dk-over-the-air"></a>Atualizar seu Percept do Azure DK no ar

Siga este guia para saber como atualizar a placa transportadora de seu Percept do Azure com o over-the-Air com a atualização do dispositivo para o Hub IoT.

## <a name="import-your-update-file-and-manifest-file"></a>Importe o arquivo de atualização e o arquivo de manifesto.

> [!NOTE]
> Se você já importou a atualização, poderá pular diretamente para **criar um grupo de dispositivos atualizados**.

1. Navegue até o Hub IoT do Azure que você está usando para o dispositivo Percept do Azure. No painel de menu à esquerda, selecione **atualizações de dispositivo** em **Gerenciamento de dispositivo automático**.
 
1. Você verá várias guias na parte superior da tela. Selecione a guia **atualizações** .
 
1. Selecione **+ importar nova atualização** abaixo do cabeçalho **pronto para implantar** .
 
1. Clique nas caixas em **selecionar Importar arquivo de manifesto** e **selecione Atualizar arquivos** para selecionar o arquivo de manifesto apropriado (. JSON) e um arquivo de atualização (. SWU). Você pode encontrar esses arquivos de atualização para seu dispositivo Percept do Azure [aqui](https://go.microsoft.com/fwlink/?linkid=2155625).
 
1. Selecione o ícone de pasta ou a caixa de texto em **selecionar um contêiner de armazenamento** e, em seguida, selecione a conta de armazenamento apropriada.
 
1. Se você já tiver criado um contêiner de armazenamento, poderá usá-lo novamente. Caso contrário, selecione **+ contêiner** para criar um novo contêiner de armazenamento para atualizações OTA. Selecione o contêiner que você deseja usar e clique em **selecionar**.
 
    >[!Note]
    >Se você não tiver um contêiner, será solicitado a criar um.
 
1. Selecione **Enviar** para iniciar o processo de importação. O processo de envio levará cerca de 4 minutos.
 
    >[!Note]
    >Talvez seja solicitado que você adicione uma regra CORS (solicitação de cross-Origin) para acessar o contêiner de armazenamento selecionado. Selecione **Adicionar regra e tente continuar novamente** .
 
    >[!Note]
    >Devido ao tamanho da imagem, você pode ver a página **enviando...** Para até 5 minutos antes de ver a próxima etapa.
    
1. O processo de importação é iniciado e você é redirecionado para a guia **histórico de importação** da página **atualizações do dispositivo** . Clique em **Atualizar** para monitorar o progresso enquanto o processo de importação estiver concluído. Dependendo do tamanho da atualização, isso pode levar alguns minutos ou mais (durante horários de pico, espere que o serviço de importação retenha até 1h).

1. Quando a coluna status indicar que a importação foi bem-sucedida, selecione a guia **pronto para implantar** e clique em **Atualizar**. Agora você deve ver a atualização importada na lista.
 
## <a name="create-a-device-update-group"></a>Criar um grupo de atualização de dispositivo
A atualização de dispositivo para o Hub IoT permite que você direcione uma atualização para grupos específicos do Azure Percept DKs. Para criar um grupo, você deve adicionar uma marca ao conjunto de dispositivos de destino no Hub IoT do Azure.

> [!NOTE]
> Se você já tiver criado um grupo, poderá pular diretamente para a próxima etapa.

Requisitos de marca de Grupo:
- Você pode adicionar qualquer valor à sua marca, exceto para não **Categorizado**, que é um valor reservado.
- O valor da marca não pode exceder 255 caracteres.
- O valor da marca só pode conter estes caracteres especiais: ".", "-", "_", "~".
- Nomes de marca e de grupo diferenciam maiúsculas de minúsculas.
- Um dispositivo só pode ter uma marca. Qualquer marca subsequente adicionada ao dispositivo substituirá a marca anterior.
- Um dispositivo só pode pertencer a um grupo.

1. Adicione uma marca a seus dispositivos.
    1. Em **IOT Edge** no painel de navegação esquerdo, localize o Azure Percept DK e navegue até seu **dispositivo**.
    1. Adicione uma nova **atualização de dispositivo para o valor da marca do Hub IOT** , conforme mostrado abaixo (altere ```<CustomTagValue>``` para seu valor, ou seja, AzurePerceptGroup1). Saiba mais sobre marcas de [documento JSON](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)de dispositivo.

    ```
    "tags": {
    "ADUGroup": "<CustomTagValue>"
    },
    ```

 
1. Clique em **salvar** e resolva quaisquer problemas de formatação.
 
1. Crie um grupo selecionando uma marca de Hub IoT do Azure existente.
    1. Navegue de volta para a página do Hub IoT do Azure.
    1. Selecione **atualizações de dispositivo** em **Gerenciamento de dispositivo automático** no painel de menu à esquerda.
    1. Selecione a guia **grupos** . Esta página exibirá o número de dispositivos desagrupados conectados à atualização do dispositivo.
    1. Selecione **+ Adicionar** para criar um novo grupo.
    1. Selecione uma marca do Hub IoT na lista e clique em **Enviar**.
    1. Depois que o grupo for criado, a lista de gráficos e grupos de conformidade de atualização será atualizada. O gráfico mostra o número de dispositivos em vários Estados de conformidade: **na atualização mais recente**, **novas atualizações disponíveis**, **atualizações em andamento** e **ainda não agrupadas**.
 

## <a name="deploy-an-update"></a>Implantar uma atualização
1. Você deve ver seu grupo recém-criado com uma nova atualização listada em **atualizações disponíveis** (talvez seja necessário atualizar uma vez). Selecione a atualização.
 
1. Confirme se o grupo de dispositivos correto está selecionado como o grupo de dispositivos de destino. Selecione uma **data de início** e uma **hora de início** para sua implantação e clique em **criar implantação**. 

    >[!CAUTION]
    >Definir a hora de início no passado irá disparar a implantação imediatamente.
 
1. Verifique o gráfico de conformidade. Será possível ver que a atualização está em andamento.
 
1. Depois que a atualização for concluída, seu gráfico de conformidade refletirá o novo status de atualização.
 
1. Selecione a guia **implantações** na parte superior da página **atualizações do dispositivo** .
 
1. Selecione sua implantação para exibir os detalhes da implantação. Talvez seja necessário clicar em **Atualizar** até que o **status** seja alterado para **êxito**.

## <a name="next-steps"></a>Próximas etapas

Seu kit de desenvolvimento agora foi atualizado com êxito. Você pode continuar o desenvolvimento e a operação com seu devkit.