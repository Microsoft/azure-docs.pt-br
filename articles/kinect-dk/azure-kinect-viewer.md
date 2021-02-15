---
title: Visualizador de Kinect do Azure
description: Entenda como Visualizar todos os fluxos de dados do dispositivo usando o Azure Kinect Viewer.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, sensor, visualizador, visualização, profundidade, RGB, cor, IMU, áudio, microfone, nuvem de ponto
ms.openlocfilehash: f411de5854d516586dc64701cca729c692ce40e0
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166098"
---
# <a name="azure-kinect-viewer"></a>Visualizador de Kinect do Azure

O Azure Kinect Viewer, encontrado no diretório de ferramentas instaladas como `k4aviewer.exe` (por exemplo, `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe` , em que `X.Y.Z` é a versão instalada do SDK), pode ser usado para Visualizar todos os fluxos de dados do dispositivo para:

* Verifique se os sensores estão funcionando corretamente.
* Ajude a posicionar o dispositivo.
* Experimente as configurações da câmera.
* Ler a configuração do dispositivo.
* Reprodução de gravações feitas com o [gravador de Kinect do Azure](azure-kinect-recorder.md).

Para obter mais informações sobre o Azure Kinect Viewer, Assista [a como usar o vídeo do Azure Kinect](https://www.microsoft.com/videoplayer/embed/RE3hNwG).

O Azure Kinect Viewer [é de](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/tools/k4aviewer) software livre e pode ser usado como um exemplo de como usar as APIs.

## <a name="use-viewer"></a>Usar Visualizador

O Visualizador pode operar em dois modos: com dados dinâmicos do sensor ou de dados registrados ([gravador de Kinect do Azure](azure-kinect-recorder.md)).

### <a name="start-application"></a>Iniciar aplicativo

Inicie o aplicativo executando `k4aviewer.exe` .

![Captura de tela que mostra o aplicativo de visualizador iniciado.](./media/how-to-guides/open-viewer.png)

### <a name="use-the-viewer-with-live-data"></a>Usar o visualizador com dados dinâmicos

1. Na seção **abrir dispositivo** , selecione o **número de série** do dispositivo a ser aberto. Em seguida, selecione **Atualizar**, se o dispositivo estiver ausente.
2. Selecione o botão **abrir dispositivo** .
3. Selecione **Iniciar** para começar a transmitir dados com as configurações padrão.

### <a name="use-the-viewer-with-recorded-data"></a>Usar o visualizador com dados gravados

Na seção **abrir gravação** , navegue até o arquivo gravado e selecione-o.

## <a name="check-device-firmware-version"></a>Verificar a versão do firmware do dispositivo

Acesse a versão do firmware do dispositivo na janela configuração, conforme mostrado na imagem a seguir.

![Verificando a versão do firmware do dispositivo usando o visualizador](./media/how-to-guides/check-firmware-update.png)

Por exemplo, nesse caso, o ISP da câmera de profundidade está executando o FW 1.5.63.

## <a name="depth-camera"></a>Câmera de profundidade

O Visualizador de câmera de profundidade mostrará duas janelas:

* Um é chamado de *brilho ativo* que é uma imagem em tons de cinza mostrando o brilho do ir.
* O segundo é chamado *profundidade*, que tem uma representação colorida dos dados de profundidade.

Focalize o cursor, no pixel na janela profundidade, para ver o valor do sensor de profundidade, conforme mostrado abaixo.

![Exibição de profundidade](./media/how-to-guides/depth-camera.png)

## <a name="rgb-camera"></a>Câmera RGB

A imagem abaixo mostra a exibição de câmera colorida.

![Exibição RGB](./media/how-to-guides/viewer-rgb-camera.png)

Você pode controlar as configurações da câmera RGB na janela de configuração durante o streaming.

![Controles de câmera RGB](./media/how-to-guides/rgb-camera-settings.png)

## <a name="inertial-measurement-unit-imu"></a>Inércia (unidade de medida de IMU)

A janela IMU tem dois componentes, um acelerômetro e um giroscópio.

A metade superior é o acelerômetro e mostra a aceleração linear em metros/segundo<sup>2</sup>.  Ela inclui a aceleração da gravidade, portanto, se ela for uniformemente plana em uma tabela, o eixo Z provavelmente mostrará-9,8 m/s<sup>2</sup>.

A metade inferior é a parte giroscópio e mostra a movimentação rotacional em radianos/segundo

![Exibição do sensor de movimento](./media/how-to-guides/viewer-mu-settings.png)

## <a name="microphone-input"></a>Entrada do microfone

A exibição de microfone mostra uma representação do som ouvido em cada microfone. Se não houver som, o grafo será mostrado como vazio, caso contrário, você verá uma forma de onda azul escura com uma forma de onda azul clara sobreposta sobre ela.

A onda escura representa os valores mínimo e máximo observados pelo microfone nessa fatia de tempo. A onda de luz representa o quadrado médio raiz dos valores observados pelo microfone nessa fatia de tempo.

![Exibição de entrada do microfone](./media/how-to-guides/microphone-data.png)

## <a name="point-cloud-visualization"></a>Visualização de nuvem Point

A profundidade visualizada em 3D permite que você mova a imagem usando as chaves instruidas.

![Nuvem de ponto de profundidade](./media/how-to-guides/depth-point-cloud.png)

## <a name="synchronization-control"></a>Controle de sincronização

Você pode usar o visualizador para configurar o dispositivo como modo autônomo (padrão), mestre ou subordinado ao configurar a sincronização de vários dispositivos.
Ao alterar a configuração ou inserir/remover o cabo de sincronização, selecione **Atualizar** para atualizar.

![Controle de sincronização externa](./media/how-to-guides/sync-control.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Guia de configuração de sincronização externa](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)
