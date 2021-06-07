---
title: Usar o modelo do ARM para publicar o Hub IoT do Azure, a conta de armazenamento, rotear mensagens
description: Usar o modelo do ARM para publicar o Hub IoT do Azure, a conta de armazenamento, rotear mensagens
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: fc8ddba2ec9b7bc9f1c2db8673ab805810afe17e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99981282"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>Início Rápido: Implantar um Hub IoT do Azure e uma conta de armazenamento usando um modelo do ARM

Neste guia de início rápido, você usará um modelo do ARM (Azure Resource Manager) para criar um Hub IoT que roteará mensagens para o Armazenamento do Azure e uma conta de armazenamento que armazenará as mensagens. Depois de adicionar manualmente um dispositivo IoT virtual ao hub para enviar as mensagens, configure essas informações de conexão em um aplicativo chamado *arm-read-write* para enviar mensagens do dispositivo para o hub. O hub é configurado para que as mensagens enviadas ao hub sejam automaticamente roteadas para a conta de armazenamento. Ao final deste guia de início rápido, você poderá abrir a conta de armazenamento e ver as mensagens enviadas.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é chamado `101-iothub-auto-route-messages` dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages).

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json":::

Há dois recursos do Azure definidos no modelo:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Devices/IotHubs](/azure/templates/microsoft.devices/iothubs)

## <a name="deploy-the-template-and-run-the-sample-app"></a>Implantar o modelo e executar o aplicativo de exemplo

Esta seção fornece as etapas para implantar o modelo, criar um dispositivo virtual e executar o aplicativo arm-read-write para enviar as mensagens.

1. Crie os recursos implantando o modelo do ARM.

    > [!TIP]
    > Selecione o botão abaixo para iniciar a implantação do modelo. Enquanto estiver em execução, configure o aplicativo arm-read-write a ser executado.

    [![Implantar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

1. Baixe e descompacte os [exemplos de C# IoT](/samples/azure-samples/azure-iot-samples-csharp/azure-iot-samples-for-csharp-net/).

1. Abra uma janela Comando e vá para a pasta na qual você descompactou os exemplos de C# IoT. Localize a pasta com o arquivo arm-read-write.csproj. Você cria as variáveis de ambiente nesta janela Comando. Faça logon no [portal do Azure](https://portal.azure.com) para obter as chaves. Selecione **Grupos de Recursos** e selecione o grupo de recursos usado para este guia de início rápido.

   ![Selecionar o grupo de recursos](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. Você vê o Hub IoT e a conta de armazenamento que foram criados quando você implantou o modelo do ARM. Aguarde até que o modelo seja totalmente implantado antes de continuar. Em seguida, selecione seu grupo de recursos para ver seus recursos.

   ![Exibir os recursos no grupo de recursos](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. Você precisa do **nome do hub**. Selecione o hub na lista de recursos. Copie o nome do hub da parte superior da seção Hub IoT para a área de transferência do Windows.

   ![Copiar o nome do hub](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

    Substitua o nome do hub neste comando, no local indicado, e execute este comando na janela Comando:

    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   que terá a aparência deste exemplo:

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. A próxima variável de ambiente é a Chave do Dispositivo IoT. Adicione um novo dispositivo ao hub selecionando **Dispositivos IoT** no menu do Hub IoT para o hub.

   ![Selecionar Dispositivos IoT](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. No lado direito da tela, selecione **+ NOVO** para adicionar um novo dispositivo.

   Preencha o nome do novo dispositivo. Este guia de início rápido usa um nome que começa com **Contoso-Test-Device**. Salve o dispositivo e abra essa tela novamente para recuperar a chave do dispositivo. (A chave é gerada para você quando você fecha o painel.) Selecione a chave primária ou secundária e copie-a para a área de transferência do Windows. Na janela Comando, defina o comando a ser executado e pressione **Enter**. O comando deve ter uma aparência semelhante a esta, mas com a chave do dispositivo colada:

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. A última variável de ambiente é a **ID do Dispositivo**. Na janela Comando, configure o comando e execute-o.

   ```cmd
   SET IOT_DEVICE_ID=<device-id-goes-here>
   ```

   que terá a aparência deste exemplo:

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. Para ver as variáveis de ambiente que você definiu, digite SET na linha de comando e pressione **Enter** e procure aqueles que começam com **IoT**.

   ![Confira as variáveis de ambiente](./media/horizontal-arm-route-messages/06-environment-variables.png)

    Agora que as variáveis de ambiente estão definidas, execute o aplicativo na mesma janela Comando. Como você está usando a mesma janela, as variáveis estarão acessíveis na memória quando você executar o aplicativo.

1. Para executar o aplicativo, digite o comando a seguir na janela Comando e pressione **Enter**.

    `dotnet run arm-read-write`

   O aplicativo gera e exibe mensagens no console à medida que envia cada mensagem ao hub IoT. O hub foi configurado no modelo do ARM para ter o roteamento automatizado. As mensagens que contêm o texto `level = storage` são automaticamente roteadas para a conta de armazenamento. Deixe que o aplicativo seja executado por 10 a 15 minutos e pressione **Enter** uma ou duas vezes até que ele pare de ser executado.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

1. Faça logon no [portal do Azure](https://portal.azure.com), selecione o grupo de recursos e escolha a conta de armazenamento.

1. Faça uma busca detalhada na conta de armazenamento até encontrar arquivos.

   ![Examinar os arquivos da conta de armazenamento](./media/horizontal-arm-route-messages/07-see-storage.png)

1. Selecione um dos arquivos e selecione **Baixar** e baixe o arquivo para uma localização que você possa encontrar posteriormente. Ele terá um nome numérico, como 47. Adicione _.txt_ ao final e clique duas vezes no arquivo para abri-lo.

1. Quando você abre o arquivo, cada linha se destina a uma mensagem diferente; o corpo de cada mensagem também é criptografado. Ele deve estar em ordem que você possa executar consultas no corpo da mensagem.

   ![Exibir as mensagens enviadas](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > Essas mensagens são codificadas em UTF-32 e base64. Se você ler a mensagem de volta, precisará decodificá-la de base64 e UTF-32 para lê-la como ASCII. Se você tiver interesse, poderá usar o método ReadOneRowFromFile no Tutorial de Roteamento para ler um para um desses arquivos de mensagem e decodificá-lo em ASCII. ReadOneRowFromFile está no repositório de exemplos C# IoT que você descompactou para este guia de início rápido. Este é o caminho da parte superior da pasta: *./iot-hub/Tutorials/Routing/SimulatedDevice/Program.cs.* Defina o booliano `readTheFile` como true e faça hard-coding do caminho para o arquivo no disco. Ele abrirá e traduzirá a primeira linha no arquivo.

Você implantou um modelo do ARM para criar um Hub IoT e uma conta de armazenamento e executar um programa para enviar mensagens para o hub. As mensagens são armazenadas automaticamente na conta de armazenamento em que podem ser exibidas.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover os recursos adicionados durante este guia de início rápido, faça logon no [portal do Azure](https://portal.azure.com). Selecione **Grupos de Recursos** e localize o grupo de recursos usado para este guia de início rápido. Selecione o grupo de recursos e escolha *Excluir*. Isso excluirá todos os recursos no grupo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Criar e implantar seu primeiro modelo do Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
