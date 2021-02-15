---
title: Importar informações do dispositivo
description: Os sensores do defender for IoT monitoram e analisam o tráfego espelhado. Nesses casos, talvez você queira importar dados para enriquecer informações sobre os dispositivos já detectados.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 53c342005d2c33e2ee18057fe0e667ebdec5166c
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522233"
---
# <a name="import-device-information-to-a-sensor"></a>Importar informações do dispositivo para um sensor

Um sensor do Azure defender para IoT monitora e analisa o tráfego espelhado. Em alguns casos, devido a políticas de configuração de rede específicas da organização, algumas informações podem não ser transmitidas.

Nesses casos, talvez você queira importar dados para enriquecer informações sobre dispositivos que já foram detectados. Duas opções estão disponíveis para importar informações para sensores:

- **Importar do mapa**: Atualize o nome do dispositivo, o tipo, o grupo ou a camada Purdue para o mapa.

- **Importar das configurações de importação**: importar so do dispositivo, endereço IP, nível de patch ou status de autorização.

## <a name="import-from-the-map"></a>Importar do mapa

Esta seção descreve como importar nomes de dispositivos, tipos, grupos ou camadas Purdue para o mapa do dispositivo. Você faz isso no mapa.

Aqui estão os requisitos de importação:

- **Nomes**: podem ter até 30 caracteres.

- Camada **Type** ou **Purdue**: Use as opções que aparecem na caixa de diálogo **Propriedades do dispositivo** . (Clique com o botão direito do mouse no dispositivo e selecione **Exibir Propriedades**.)

- **Grupo de dispositivos**: Crie um novo grupo com até 30 caracteres. 

> [!NOTE]
> Para evitar conflitos, não importe os dados que você exportou de um sensor para outro sensor.

Para importar:

1. No menu lateral, selecione **dispositivos**.

2. No canto superior direito da janela **dispositivos** , selecione :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Captura de tela da janela do dispositivo.":::

3. Selecione **Exportar dispositivos**. Uma ampla gama de informações aparece no arquivo exportado. Essas informações incluem protocolos que o dispositivo usa e o status de autorização do dispositivo.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="As informações no arquivo exportado.":::

4. No arquivo CSV, altere apenas o nome do dispositivo, o tipo, o grupo e a camada Purdue. Em seguida, salve o arquivo. 

   Use padrões de capitalização mostrados no arquivo exportado. Por exemplo, para a camada Purdue, use todas as letras maiúsculas de primeira letra.

5. No menu suspenso **importar/exportar** na janela do **dispositivo** , selecione **importar dispositivos**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importar dispositivos por meio da janela do dispositivo.":::

6. Selecione **importar dispositivos** e selecione o arquivo CSV que você deseja importar. As mensagens de status de importação aparecem na tela até que a caixa de diálogo **importar dispositivos** seja fechada.

## <a name="import-from-import-settings"></a>Importar das configurações de importação

Esta seção descreve como importar o endereço IP do dispositivo, o sistema operacional, o nível de patch ou o status de autorização para o mapa do dispositivo. Faça isso na caixa de diálogo **Importar configurações** .

Para importar o endereço IP, o sistema operacional e o nível de patch:

1. Baixe o arquivo [devices_info_2.2.8 e up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) no [centro de ajuda](https://cyberx-labs.zendesk.com/hc/en-us) e insira as informações da seguinte maneira:

   - **Endereço IP**: Insira o endereço IP do dispositivo.

   - **Sistema operacional**: selecione na lista suspensa.

   - **Última atualização**: Use o formato aaaa-mm-dd.

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="A tela de opções.":::

2. No menu lateral, selecione **Importar configurações**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Importe suas configurações.":::

3. Para carregar a configuração necessária, na seção **informações do dispositivo** , selecione **Adicionar** e carregue o arquivo CSV que você preparou.

Para importar o status de autorização:

1. Baixe e salve o arquivo de [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) no centro de ajuda do defender para IOT. Verifique se você salvou o arquivo como um CSV.

2. Insira as informações como:

   - **Endereço IP**: o endereço IP do dispositivo.

   - **Nome**: o nome do dispositivo autorizado. Certifique-se de que os nomes são precisos. Os nomes fornecidos aos dispositivos na lista importada substituem os nomes mostrados no mapa do dispositivo.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Arquivos do Excel com a lista de dispositivos importados.":::

3. No menu lateral, selecione **Importar configurações**.

4. Na seção **dispositivos autorizados** , selecione **Adicionar** e carregue o arquivo CSV que você salvou.

Quando as informações são importadas, você recebe alertas sobre dispositivos não autorizados para todos os dispositivos que não aparecem nessa lista.

## <a name="import-device-information-to-the-sensor"></a>Importar informações do dispositivo para o sensor

O sensor monitora e analisa o tráfego espelhado. Em alguns casos, devido a políticas de configuração de rede específicas da organização, algumas informações podem não ser transmitidas.

Nesses casos, talvez você queira importar dados para enriquecer as informações do dispositivo em dispositivos que já foram detectados. Duas opções estão disponíveis para importar informações para sensores:

- **Importar do mapa**: Atualize o nome do dispositivo, o tipo, o grupo ou a camada Purdue para o mapa.

- **Importar das configurações de importação**: importar so do dispositivo, endereço IP, nível de patch ou status de autorização.

### <a name="import-from-the-map"></a>Importar do mapa

Esta seção descreve como importar nomes de dispositivos, tipos, grupos ou camadas Purdue para o mapa do dispositivo. Você faz isso no mapa.

Aqui estão os requisitos de importação:

- **Nomes**: podem ter até 30 caracteres.

- Camada **Type** ou **Purdue**: Use as opções que aparecem na caixa de diálogo **Propriedades do dispositivo** . (Clique com o botão direito do mouse no dispositivo e selecione **Exibir Propriedades**.)

- **Grupo de dispositivos**: Crie um novo grupo com até 30 caracteres. 

> [!NOTE]
> Para evitar conflitos, não importe os dados que você exportou de um sensor para outro sensor.

Para importar:

1. No menu lateral, selecione **dispositivos**.

2. No canto superior direito da janela **dispositivos** , selecione :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="A janela da qual escolher seu dispositivo.":::

3. Selecione **Exportar dispositivos**. Uma ampla gama de informações aparece no arquivo exportado. Os exemplos incluem protocolos que o dispositivo usa e o status de autorização do dispositivo.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="As informações no arquivo exportado.":::

4. No arquivo CSV, altere apenas o nome do dispositivo, o tipo, o grupo e a camada Purdue. Em seguida, salve o arquivo. 

   Use padrões de capitalização mostrados no arquivo exportado. Por exemplo, para a camada Purdue, use todas as letras maiúsculas de primeira letra.

5. No menu suspenso **importar/exportar** na janela do **dispositivo** , selecione **importar dispositivos**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importe seus dispositivos.":::

6. Selecione **importar dispositivos** e selecione o arquivo CSV que você deseja importar. As mensagens de status de importação aparecem na tela até que a caixa de diálogo **importar dispositivos** seja fechada.

### <a name="import-from-import-settings"></a>Importar das configurações de importação 

Esta seção descreve como importar o endereço IP do dispositivo, o sistema operacional, o nível de patch ou o status de autorização para o mapa do dispositivo. Faça isso na caixa de diálogo **Importar configurações** .

Para importar o endereço IP, o sistema operacional e o nível de patch:

1. Baixe o arquivo [devices_info_2.2.8 e up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) no [centro de ajuda](https://cyberx-labs.zendesk.com/hc/en-us) e insira as informações da seguinte maneira:

   - **Endereço IP**: o endereço IP do dispositivo.

   - **Sistema operacional**: selecione na lista suspensa.

   - **Data da última atualização**: Use o formato aaaa-mm-dd.

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="O conteúdo na tela.":::

2. No menu lateral, selecione **Importar configurações**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Preencha a tela de configurações de importação.":::

3. Para carregar a configuração necessária, na seção **informações do dispositivo** , selecione **Adicionar** e carregue o arquivo CSV que você preparou.

Para importar o status de autorização:

1. Baixe e salve o arquivo de [authorized_devices examples.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) do centro de ajuda do defender para IOT. Verifique se você salvou o arquivo como um CSV.

2. Insira as informações como:

   - **Endereço IP**: o endereço IP do dispositivo.

   - **Nome**: o nome do dispositivo autorizado. Verifique se os nomes são precisos. Os nomes fornecidos aos dispositivos na lista importada substituem os nomes mostrados no mapa do dispositivo.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="A lista de importação para o mapa do dispositivo.":::

3. No menu lateral, selecione **Importar configurações**.

4. Na seção **dispositivos autorizados** , selecione **Adicionar** e carregue o arquivo CSV que você salvou.

Quando as informações são importadas, você recebe alertas sobre dispositivos não autorizados para todos os dispositivos que não aparecem nessa lista.

## <a name="next-steps"></a>Próximas etapas

[Controlar qual tráfego é monitorado](how-to-control-what-traffic-is-monitored.md)

[Investigar as detecções do sensor em um inventário de dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md)
