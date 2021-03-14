---
title: Problemas conhecidos e solução de problemas do Azure Kinect
description: Saiba mais sobre alguns dos problemas conhecidos e dicas de solução de problemas ao usar o SDK do sensor com o Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: solução de problemas, atualização, Bug, Kinect, comentários, recuperação, registro em log, dicas
ms.openlocfilehash: 5f13815b8f8b26f6a08da28181a4a6164b7b89a3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038813"
---
# <a name="azure-kinect-known-issues-and-troubleshooting"></a>Problemas conhecidos e solução de problemas do Azure Kinect

Esta página contém problemas conhecidos e dicas de solução de problemas ao usar o SDK do sensor com o Azure Kinect DK. Consulte também [páginas de suporte do produto](./index.yml) para problemas específicos de hardware do produto.

## <a name="known-issues"></a>Problemas conhecidos

- Problemas de compatibilidade com controladores de host USB do asmedia (por exemplo, chipset ASM1142)
  - Alguns casos que usam o driver USB da Microsoft podem desbloquear
  - Muitos PCs também têm controladores de host alternativos e a alteração da porta USB3 pode ajudar

Para obter mais problemas relacionados ao SDK do sensor, verifique os [problemas do GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/issues)

## <a name="collecting-logs"></a>Coletando logs

O registro em log para K4A.dll é habilitado por meio de variáveis de ambiente. Por padrão, o registro em log é enviado para stdout e somente erros e mensagens críticas são gerados. Essas configurações podem ser alteradas para que o registro em log vá para um arquivo. O detalhamento também pode ser ajustado conforme necessário. Abaixo está um exemplo, para o Windows, de habilitar o registro em log em um arquivo, chamado k4a. log, e capturará mensagens de aviso e de nível superior.

1. `set K4A_ENABLE_LOG_TO_A_FILE=k4a.log`
2. `set K4A_LOG_LEVEL=w`
3. Executar cenário do prompt de comando (por exemplo, iniciar visualizador)
4. Navegue até k4a. log e compartilhe o arquivo.

Para obter mais informações, consulte o clipe abaixo do arquivo de cabeçalho:

```console
/**
* environment variables
* K4A_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4A_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4A_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

O registro em log para o SDK de acompanhamento de corpo K4ABT.dll é semelhante, exceto que os usuários devem modificar um conjunto diferente de nomes de variáveis de ambiente:

```console
/**
* environment variables
* K4ABT_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4ABT_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4ABT_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

## <a name="device-doesnt-enumerate-in-device-manager"></a>O dispositivo não enumera no Gerenciador de dispositivos

- Verifique o LED de status por trás do dispositivo, se ele estiver piscando em âmbar, se você tiver um problema de conectividade USB e se ele não obtiver energia suficiente. O cabo da fonte de alimentação deve ser conectado ao adaptador de energia fornecido. Enquanto o cabo de alimentação tem um tipo USB A conectado, o dispositivo requer mais energia do que uma porta USB de PC pode fornecer. Portanto, não conecte-se a ele a uma porta de PC ou hub USB.
- Verifique se você tem um cabo de alimentação conectado e usando a porta USB3 para dados.
- Tente alterar a porta USB3 para a conexão de dados (recomendação para usar a porta USB próxima à placa-mãe, por exemplo, na parte traseira do PC).
- Verifique seu cabo, cabos danificados ou de baixa qualidade podem causar enumeração não confiável (o dispositivo mantém "piscando" no Gerenciador de dispositivos).
- Se você tiver se conectado ao laptop e estiver em execução na bateria, ele poderá estar limitando o poder da porta.
- Reinicialize o PC host.
- Se o problema persistir, pode haver um problema de compatibilidade.
- Se a falha ocorreu durante a atualização do firmware e o dispositivo não foi recuperado por si só, execute a [redefinição de fábrica](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk).

## <a name="azure-kinect-viewer-fails-to-open"></a>Falha ao abrir o Azure Kinect Viewer

- Verifique primeiro se o dispositivo enumera no Windows Gerenciador de Dispositivos.

    ![Câmeras do Azure Kinect no Gerenciador de dispositivos do Windows](./media/resources/viewer-fails.png)

- Verifique se você tem qualquer outro aplicativo usando o dispositivo (por exemplo, aplicativo de câmera do Windows). Somente um aplicativo por vez pode acessar o dispositivo.
- Verifique o log k4aviewer. err para obter mensagens de erro.
- Abra o aplicativo de câmera do Windows e verifique se ele funciona.
- Dispositivo de ciclo de energia, a espera de transmissão levou a desligar antes de usar o dispositivo.
- Reinicialize o PC host.
- Verifique se você está usando os drivers gráficos mais recentes em seu computador.
- Se você estiver usando sua própria compilação do SDK, tente usar a versão lançada oficialmente se isso corrigir o problema.
- Se o problema persistir, [colete logs](troubleshooting.md#collecting-logs) e comentários de arquivo.

## <a name="cannot-find-microphone"></a>Não é possível localizar o microfone

- Verifique primeiro se a matriz de microfone está enumerada em Gerenciador de Dispositivos.
- Se um dispositivo for enumerado e funcionar de outra forma corretamente no Windows, o problema poderá ser que, após a atualização do firmware, o Windows tenha atribuído uma ID de contêiner diferente à câmera de profundidade.
- Você pode tentar redefini-lo acessando Gerenciador de Dispositivos, clicando com o botão direito do mouse em "matriz de microfone Kinect do Azure" e selecionando "desinstalar dispositivo". Quando isso for concluído, desanexe e anexe novamente o sensor.

    ![Matriz do MIC Kinect do Azure](./media/resources/mic-not-found.png)

- Depois disso, reinicie o Azure Kinect Viewer e tente novamente.

## <a name="device-firmware-update-issues"></a>Problemas de atualização do firmware do dispositivo

- Se o número de versão correto não for relatado após a atualização, talvez seja necessário fazer o ciclo de energia do dispositivo.
- Se a atualização do firmware for interrompida, ela pode entrar em estado inadequado e falhar ao enumerar. Desanexe e anexe novamente o dispositivo e aguarde 60 segundos para ver se ele pode ser recuperado.
Caso contrário, execute uma [redefinição de fábrica](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)

## <a name="image-quality-issues"></a>Problemas de qualidade da imagem

- Inicie o [Azure Kinect Viewer](azure-kinect-viewer.md) e verifique o posicionamento do dispositivo quanto a interferência ou se o sensor está bloqueado ou se as lentes estão sujas.
- Tente modos de operação diferentes para restringir se o problema estiver ocorrendo no modo específico.
- Para compartilhar problemas de qualidade de imagem com a equipe, você pode:

1) Faça uma pausa na exibição do [Azure Kinect Viewer](azure-kinect-viewer.md) e faça uma captura de tela ou
2) Faça a gravação usando o [gravador de Kinect do Azure](azure-kinect-recorder.md), por exemplo, `k4arecorder.exe -l 5 -r 5 output.mkv`

## <a name="inconsistent-or-unexpected-device-timestamps"></a>Carimbos de data/hora de dispositivo inconsistentes ou inesperados

A chamada ```k4a_device_set_color_control``` pode induzir temporariamente as alterações de tempo ao dispositivo que pode levar algumas capturas para estabilizar. Evite chamar a API no loop de captura de imagem para evitar redefinir o cálculo de tempo interno com cada nova imagem. Em vez disso, chame a API antes de iniciar a câmera ou apenas quando precisar alterar o valor dentro do loop de captura de imagem. Em particular, evite chamar ```k4a_device_set_color_control(K4A_COLOR_CONTROL_AUTO_EXPOSURE_PRIORITY)``` .

## <a name="usb3-host-controller-compatibility"></a>Compatibilidade do controlador de host USB3

Se o dispositivo não estiver enumerando no Gerenciador de dispositivos, talvez seja porque ele está conectado a um controlador USB3 sem suporte. 

Para o Azure Kinect DK no **Windows, Intel**, **Texas Instruments (TI)** e **Renesas** são os *únicos controladores de host com suporte*. O SDK do Azure Kinect em plataformas Windows depende de uma ID de contêiner unificada e deve abranger dispositivos USB 2,0 e 3,0 para que o SDK possa encontrar os dispositivos de profundidade, cor e áudio que estão localizados fisicamente no mesmo dispositivo. No Linux, mais controladores de host podem ter suporte, pois essa plataforma depende menos da ID do contêiner e mais em números de série do dispositivo. 

O tópico dos controladores de host USB fica ainda mais complicado quando um PC tem mais de um controlador de host instalado. Quando os controladores de host estão misturados, um usuário pode enfrentar problemas em que algumas portas funcionam bem e outras não funcionam. Dependendo de como as portas são conectadas ao caso, você poderá ver todas as portas frontais com problemas com o Azure Kinect

**Windows:** Para descobrir o controlador de host que você abriu Gerenciador de Dispositivos

1. Exibir-> dispositivos por tipo 
2. Com o Azure Kinect conectado, selecione câmeras->câmera do Azure Kinect 4K
3. Exibir-> dispositivos por conexão

![Solução de problemas de porta USB](./media/resources/usb-troubleshooting.png)

Para entender melhor qual porta USB está conectada em seu computador, repita essas etapas para cada porta USB ao conectar o Azure Kinect DK a diferentes portas USB no PC.

## <a name="depth-camera-auto-powers-down"></a>Reinicialização automática da câmera de profundidade

A laser usada pela câmera de profundidade para calcular dados de profundidade de imagem tem um ciclo de vida limitado. Para maximizar a vida útil dos lasers, a câmera de profundidade será detectada quando os dados de profundidade não estiverem sendo consumidos. A câmera de profundidade desliga quando o dispositivo está transmitindo por vários minutos, mas o PC host não está lendo os dados. Ele também afeta a sincronização de vários dispositivos, em que os dispositivos subordinados são iniciados em um estado em que a câmera de profundidade é a transmissão e os quadros de profundidade estão ativamente ajudando a aguardar que o dispositivo mestre inicie a sincronização de capturas. Para evitar esse problema em cenários de captura de vários dispositivos, verifique se o dispositivo mestre é iniciado em um minuto do primeiro subordinado que está sendo iniciado. 

## <a name="using-body-tracking-sdk-with-unreal"></a>Usando o SDK de acompanhamento de corpo com não real

Para usar o SDK de acompanhamento de corpo com o não real, verifique se você adicionou `<SDK Installation Path>\tools` à variável de ambiente `PATH` e se copiou `dnn_model_2_0.onnx` e `cudnn64_7.dll` para `Program Files/Epic Games/UE_4.23/Engine/Binaries/Win64` .

## <a name="using-azure-kinect-on-headless-linux-system"></a>Usando o Azure Kinect no sistema Linux sem periféricos

O mecanismo de profundidade do Azure Kinect no Linux usa OpenGL. O OpenGL requer uma instância de janela que requer que um monitor esteja conectado ao sistema. Uma solução alternativa para esse problema é:

1. Habilite o logon automático para a conta de usuário que você planeja usar. Consulte [este](https://vitux.com/how-to-enable-disable-automatic-login-in-ubuntu-18-04-lts/) artigo para obter instruções sobre como habilitar o logon automático.
2. Desligue o sistema, desconecte o monitor e ligue o sistema. O logon automático força a criação de uma sessão x-Server.
2. Conecte-se via SSH e defina a variável exibir env `export DISPLAY=:0`
3. Inicie seu aplicativo Kinect do Azure.

O utilitário [xtrlock](http://manpages.ubuntu.com/manpages/xenial/man1/xtrlock.1x.html) pode ser usado para bloquear imediatamente a tela após o logon automático. Adicione o seguinte comando ao aplicativo de inicialização ou ao serviço de sistema:

`bash -c “xtrlock -b”` 

## <a name="missing-c-documentation"></a>Documentação do C# ausente

A documentação do SDK do sensor do C# está localizada [aqui](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/namespace_microsoft_1_1_azure_1_1_kinect_1_1_sensor.html).

A documentação C# do SDK do acompanhamento de corpo está localizada [aqui](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/namespace_microsoft_1_1_azure_1_1_kinect_1_1_body_tracking.html).

## <a name="next-steps"></a>Próximas etapas

[Mais informações de suporte](support.md)