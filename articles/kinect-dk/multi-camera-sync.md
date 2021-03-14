---
title: Sincronizar vários dispositivos do Azure Kinect DK
description: Este artigo explora os benefícios da sincronização de vários dispositivos, bem como configurar os dispositivos a serem sincronizados.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: Azure, Kinect, specs, hardware, DK, funcionalidades, profundidade, cor, RGB, IMU, matriz, profundidade, várias, sincronização
ms.openlocfilehash: eabf77896777f39efcfd61adb3040bca8642716e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039947"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>Sincronizar vários dispositivos do Azure Kinect DK

Cada dispositivo Azure Kinect DK inclui portas de sincronização de 3,5-mm (**sincronização** e **sincronização**) que você pode usar para vincular vários dispositivos juntos. Depois de conectar os dispositivos, o software pode coordenar o tempo de gatilho entre eles. 

Este artigo como conectar e sincronizar os dispositivos.

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>Benefícios do uso de vários dispositivos do Azure Kinect DK

Há vários motivos para usar vários dispositivos do Azure Kinect DK, incluindo o seguinte:

- Preencha occlusions. Embora as transformações de dados do Azure Kinect DK produzam uma única imagem, as duas câmeras (profundidade e RGB) são, na verdade, uma pequena distância. O deslocamento torna o occlusions possível. Um oclusão ocorre quando um objeto de primeiro plano bloqueia a exibição de parte de um objeto de plano de fundo para uma das duas câmeras em um dispositivo. Na imagem de cor resultante, o objeto de primeiro plano parece converter uma sombra no objeto de plano de fundo.  
   Por exemplo, no diagrama a seguir, a câmera do lado esquerdo vê o pixel cinza "P2". No entanto, o objeto de primeiro plano branco bloqueia o feixe de IR da câmera do lado direito. A câmera do lado direito não tem dados para "P2".  
   ![O diagrama mostra duas câmeras direcionadas no mesmo ponto com uma delas bloqueadas.](./media/occlusion.png)  
   Dispositivos sincronizados adicionais podem fornecer os dados de obstruído.
- Verificar objetos em três dimensões.
- Aumente a taxa de quadros efetivas para um valor maior que 30 quadros por segundo (FPS).
- Capture várias imagens coloridas de 4K da mesma cena, todas alinhadas em 100 microssegundos ( &mu; s) do centro de exposição.
- Aumente a cobertura da câmera dentro do espaço.

## <a name="plan-your-multi-device-configuration"></a>Planejar a configuração de vários dispositivos

Antes de começar, verifique se você revisar as [especificações de hardware do Azure Kinect DK](hardware-specification.md) e a [câmera de profundidade do Azure Kinect DK](depth-camera.md).

> [!NOTE]  
> Remova a tampa de plástico externa para expor a sincronização no e sincronizar os conectores.

### <a name="select-a-device-configuration"></a>Selecionar uma configuração de dispositivo

Você pode usar qualquer uma das seguintes abordagens para a configuração do dispositivo:

- **Configuração da cadeia de Margarida**. Sincronize um dispositivo mestre e até oito dispositivos subordinados.  
   ![Diagrama que mostra como conectar dispositivos do Azure Kinect DK em uma configuração de cadeia de Margarida.](./media/multicam-sync-daisychain.png)
- **Configuração de estrela**. Sincronizar um dispositivo mestre e até dois dispositivos subordinados.  
   ![Diagrama que mostra como configurar vários dispositivos do Azure DK em uma configuração em estrela.](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>Usando um gatilho de sincronização externo

Em ambas as configurações, o dispositivo mestre fornece o sinal de disparo para os dispositivos subordinados. No entanto, você pode usar uma fonte externa personalizada para o gatilho de sincronização. Por exemplo, você pode usar essa opção para sincronizar capturas de imagem com outros equipamentos. Na configuração da cadeia de Margarida ou na configuração de estrela, a origem do gatilho externo se conecta ao dispositivo mestre.

A origem do gatilho externo deve funcionar da mesma maneira que o dispositivo mestre. Ele deve fornecer um sinal de sincronização que tenha as seguintes características:

- Ativo alto
- Largura do pulso: maior que 8 &mu; s
- 5 V TTL/CMOS
- Capacidade máxima de condução: sem menos de 8 milliamps (mA)
- Suporte a frequência: exatamente 30 FPS, 15 FPS e 5 FPS (a frequência do sinal de mestre VSYNC da câmera de cores)

A origem do gatilho deve entregar o sinal à sincronização do dispositivo mestre **na** porta usando um cabo de áudio 3,5-mm. Você pode usar um cabo estéreo ou mono. O Azure Kinect DK shortu todas as mangas e anéis do conector de cabo de áudio juntos e os aterra. Conforme mostrado no diagrama a seguir, o dispositivo recebe o sinal de sincronização somente da gorjeta do conector.

![Configurações de cabo para um sinal de gatilho externo](./media/resources/camera-trigger-signal.jpg)

Para obter mais informações sobre como trabalhar com equipamentos externos, consulte [usar o gravador de Kinect do Azure com dispositivos sincronizados externos](record-external-synchronized-units.md)

> [!NOTE]  
> Sincronizar é o VSync para a câmera RGB. Os carimbos de data/hora de todos os dispositivos são definidos como zero e contam. A Microsoft não caracteriza a largura mínima e máxima do pulso de sincronização e recomenda a imitando do pulso gerado pela sincronização de um Kinect do Azure DK.

### <a name="plan-your-camera-settings-and-software-configuration"></a>Planejar as configurações da câmera e a configuração do software

Para obter informações sobre como configurar seu software para controlar as câmeras e usar os dados da imagem, consulte o [SDK do sensor de Kinect do Azure](about-sensor-sdk.md).

Esta seção aborda vários fatores que afetam dispositivos sincronizados (mas não dispositivos únicos). Seu software deve considerar esses fatores.

#### <a name="exposure-considerations"></a>Considerações sobre exposição
Se você quiser controlar o tempo preciso de cada dispositivo, recomendamos que use uma configuração de exposição manual. Na configuração de exposição automática, cada câmera colorida pode alterar dinamicamente a exposição real. Como a exposição afeta o tempo, essas alterações enviam rapidamente as câmeras para fora da sincronização.

No loop de captura de imagem, evite definir repetidamente a mesma configuração de exposição. Chame a API apenas uma vez quando for necessário.

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>Evitando interferência entre câmeras com várias camadas

Quando várias câmeras de profundidade são campos de exibição sobrepostos de imagens, cada câmera deve fazer a imagem de seu próprio laser associado. Para evitar que os lasers interfiram um ao outro, as capturas da câmera devem ser deslocadas de uma outra por 160 μs ou mais.

Para cada captura de câmera de profundidade, a laser ativa nove vezes e fica ativa por apenas 125 &mu; s a cada vez. A laser fica ociosa por 14505 &mu; s ou 23905 &mu; s, dependendo do modo de operação. Esse comportamento significa que o ponto de partida para o cálculo de deslocamento é de 125 &mu; s.

Além disso, as diferenças entre o relógio da câmera e o relógio do firmware do dispositivo aumentam o deslocamento mínimo para 160 &mu; s. Para calcular um deslocamento mais preciso para sua configuração, observe o modo de profundidade que você está usando e consulte a [tabela de tempo bruta do sensor de profundidade](hardware-specification.md#depth-sensor-raw-timing). Usando os dados desta tabela, você pode calcular o deslocamento mínimo (o tempo de exposição de cada câmera) usando a seguinte equação:

> *Tempo de exposição* = (largura de pulso de *pulsos de ir* &times; ) + (tempo ocioso de *períodos ociosos* &times; )

Ao usar um deslocamento de 160 &mu; s, você pode configurar até nove câmeras de profundidade adicionais para que cada uma das lasers seja ativada enquanto os outros lasers estiverem ociosos.

Em seu software, use ```depth_delay_off_color_usec``` ou ```subordinate_delay_off_master_usec``` para certificar-se de que cada laser de ir seja acionado em sua própria &mu; janela de 160 s ou tenha um campo de exibição diferente.

> [!NOTE]  
> A largura real do pulso é 125us, mas podemos declarar 160us para fornecer algumas reserva. Tomando NFOV UNBINNED como exemplo, cada pulso 125us é seguido por 1450us ocioso. Totalizando esses backups-(9 x 125) + (8 x 1450) – gera o tempo de exposição de 12,8 ms. O armário que você pode intercalar a exposição de dois dispositivos é fazer com que o primeiro pulso da segunda câmera caia no primeiro período ocioso da primeira câmera. O atraso entre a primeira e a segunda câmeras poderia ser tão pequeno quanto 125us (a largura de um pulso) no entanto, recomendamos alguns reserva, portanto, o 160us. Determinado 160us você pode intercalar os períodos de exposição de um máximo de 10 câmeras.

## <a name="prepare-your-devices-and-other-hardware"></a>Preparar seus dispositivos e outros Hardwares

Além de vários dispositivos do Azure Kinect DK, talvez seja necessário obter computadores host adicionais e outros hardwares para dar suporte à configuração que você deseja criar. Use as informações desta seção para verificar se todos os dispositivos e hardware estão prontos antes de começar a configurar o.

### <a name="azure-kinect-dk-devices"></a>Dispositivos do Azure Kinect DK

Para cada dispositivo do Azure Kinect DK que você deseja sincronizar, faça o seguinte:

- Verifique se o firmware mais recente está instalado no dispositivo. Para obter mais informações sobre como atualizar seus dispositivos, vá para [atualizar o firmware do Azure Kinect DK](update-device-firmware.md). 
- Remova a tampa do dispositivo para revelar as portas de sincronização.
- Anote o número de série para cada dispositivo. Você usará esse número posteriormente no processo de instalação.

### <a name="host-computers"></a>Computadores host

Normalmente, cada Azure Kinect DK usa seu próprio computador host. Você pode usar um controlador de host dedicado, dependendo de como você usa o dispositivo e a quantidade de dados transferidos pela conexão USB. 

Verifique se o SDK do sensor de Kinect do Azure está instalado em cada computador host. Para obter mais informações sobre como instalar o SDK do sensor, acesse [início rápido: configurar o Azure Kinect DK](set-up-azure-kinect-dk.md). 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Computadores Linux: memória USB no Ubuntu

Por padrão, os computadores host baseados em Linux alocam o controlador USB somente 16 MB de memória do kernel para lidar com as transferências USB. Esse valor é geralmente suficiente para dar suporte a um único Azure Kinect DK. No entanto, para dar suporte a vários dispositivos, o controlador USB precisa ter mais memória. Para aumentar a memória, siga estas etapas:

1. Edit/**etc/default/grub**.
1. Localize a seguinte linha:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   Substitua-o usando esta linha:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > Esses comandos definem a memória USB como 32 MB. Essa é uma configuração de exemplo em duas vezes o valor padrão. Você pode definir um valor muito maior, conforme apropriado para sua solução.
1. Execute **Update sudo-grub**.
1. Reinicie o computador.

### <a name="cables"></a>Cabos

Para conectar os dispositivos uns aos outros e aos computadores host, você precisa usar cabos macho para macho de 3,5-mm (também conhecido como cabo de áudio de 3,5 mm). Os cabos devem ter menos de 10 metros de comprimento e podem ser estéreo ou mono.

O número de cabos que você deve ter depende do número de dispositivos que você usa e também da configuração de dispositivo específica. A caixa do Azure Kinect DK não inclui cabos. Você deve comprá-los separadamente.

Se você conectar os dispositivos na configuração de estrela, também deverá ter um divisor de fone de ouvido.

## <a name="connect-your-devices"></a>Conecte seus dispositivos

**Para conectar dispositivos do Azure Kinect DK em uma configuração de cadeia de Margarida**

1. Conecte cada Azure Kinect DK ao Power.
1. Conecte cada dispositivo ao seu PC host. 
1. Selecione um dispositivo para ser o dispositivo mestre e conecte um cabo de áudio 3,5-mm à sua porta de **sincronização** .
1. Conecte a outra extremidade do cabo à **sincronização na** porta do primeiro dispositivo subordinado.
1. Para conectar outro dispositivo, conecte outro cabo à porta de **sincronização** do primeiro dispositivo subordinado e à **sincronização na** porta do próximo dispositivo.
1. Repita a etapa anterior até que todos os dispositivos estejam conectados. O último dispositivo deve ter apenas uma conexão de cabo. Sua porta de **sincronização** deve estar vazia.

**Para conectar dispositivos do Azure Kinect DK em uma configuração de estrela**

1. Conecte cada Azure Kinect DK ao Power.
1. Conecte cada dispositivo ao seu PC host. 
1. Selecione um dispositivo para ser o dispositivo mestre e conecte a extremidade única do divisor de fone de ouvido em sua porta de **sincronização** .
1. Conecte os cabos de áudio 3,5-mm às extremidades de "divisão" do divisor de fone de ouvido.
1. Conecte a outra extremidade de cada cabo à **sincronização na** porta de um dos dispositivos subordinados.

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>Verifique se os dispositivos estão conectados e se comunicando

Para verificar se os dispositivos estão conectados corretamente, use o [Azure Kinect Viewer](azure-kinect-viewer.md). Repita esse procedimento conforme necessário para testar cada dispositivo subordinado em combinação com o dispositivo mestre

> [!IMPORTANT]  
> Para este procedimento, você deve saber o número de série de cada Azure Kinect DK.

1. Abra duas instâncias do Azure Kinect Viewer.
1. Em **abrir dispositivo**, selecione o número de série do dispositivo subordinado que você deseja testar.  
   ![Abrir dispositivo](./media/open-devices.png)
   > [!IMPORTANT]  
   > Para obter o alinhamento preciso da captura de imagem entre todos os dispositivos, você precisa iniciar o dispositivo mestre por último.  
1. Em **sincronização externa**, selecione **sub**.  
   ![Início da câmera subordinada](./media/sub-device-start.png)
1.  Selecione **Iniciar**.  
    > [!NOTE]  
    > Como esse é um dispositivo subordinado, o Azure Kinect Viewer não exibe uma imagem depois que o dispositivo é iniciado. Nenhuma imagem é exibida até que o dispositivo subordinado receba um sinal de sincronização do dispositivo mestre.
1. Depois que o dispositivo subordinado for iniciado, use a outra instância do Azure Kinect Viewer para abrir o dispositivo mestre.
1. Em **sincronização externa**, selecione **mestre**.
1. Selecione **Iniciar**.

Quando o dispositivo mestre do Azure Kinect é iniciado, as duas instâncias do Visualizador de Kinect do Azure devem exibir imagens.

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>Calibrar os dispositivos como um conjunto sincronizado

Depois de verificar se os dispositivos estão se comunicando corretamente, você está pronto para calibre-los para produzir imagens em um único domínio.

Em um único dispositivo, as câmeras de profundidade e RGB são calibradas de fábrica para trabalharem juntas. No entanto, quando vários dispositivos precisam trabalhar juntos, eles precisam ser calibrados para determinar como transformar uma imagem do domínio da câmera que a capturou no domínio da câmera que você deseja usar para processar imagens.

Há várias opções para dispositivos de calibragem cruzada. A Microsoft fornece o [exemplo de código de tela verde do GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen), que usa o método OpenCV. O arquivo Leiame para este exemplo de código fornece mais detalhes e instruções para a calibragem dos dispositivos.

Para obter mais informações sobre calibragem, consulte [usar funções de calibragem Kinect do Azure](use-calibration-functions.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar dispositivos sincronizados, você também pode aprender a usar o
> [!div class="nextstepaction"]
> [API de registro e reprodução do SDK do sensor do Azure Kinect](record-playback-api.md)

## <a name="related-topics"></a>Tópicos relacionados

- [Sobre o SDK do sensor de Kinect do Azure](about-sensor-sdk.md)
- [Especificações de hardware do Azure Kinect DK](hardware-specification.md) 
- [Início rápido: configurar seu Kinect do Azure DK](set-up-azure-kinect-dk.md) 
- [Atualizar o firmware do Azure Kinect DK](update-device-firmware.md) 
- [Restaurar o Azure Kinect DK](reset-azure-kinect-dk.md) 
- [Visualizador de Kinect do Azure](azure-kinect-viewer.md) 
