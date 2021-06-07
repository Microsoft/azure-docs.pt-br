---
title: Solucionar problemas
description: Informações de solução de problemas do Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4990f0d0a10709f2c1c5a17806020cd685f999fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593326"
---
# <a name="troubleshoot"></a>Solucionar problemas

Esta página lista os problemas comuns que interferem no Azure Remote Rendering e maneiras de resolvê-los.

## <a name="cant-link-storage-account-to-arr-account"></a>Não é possível vincular a conta de armazenamento à conta do ARR

Às vezes, durante a [vinculação de uma conta de armazenamento](../how-tos/create-an-account.md#link-storage-accounts), a conta do Remote Rendering não é listada. Para corrigir esse problema, vá para a conta do ARR no portal do Azure e selecione **Identidade** no grupo **Configurações** à esquerda. Verifique se **Status** está definido como **Ativo**.
![Depurador de quadros do Unity](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>O cliente não pode se conectar ao servidor

Verifique se os firewalls (no dispositivo, dentro de roteadores, etc.) não bloqueiam as portas mencionadas nos [requisitos do sistema](../overview/system-requirements.md#network-firewall).

## <a name="error-disconnected-videoformatnotavailable"></a>Erro ' `Disconnected: VideoFormatNotAvailable` '

Verifique se sua GPU é compatível com a decodificação de vídeo por hardware. Confira [PC de Desenvolvimento](../overview/system-requirements.md#development-pc).

Se você estiver trabalhando em um laptop com duas GPUs, é possível que a GPU em que você está executando por padrão não forneça a funcionalidade de decodificação de vídeo por hardware. Se esse for o caso, tente forçar seu aplicativo a usar a outra GPU. Isso geralmente é possível nas configurações do driver da GPU.

## <a name="retrieve-sessionconversion-status-fails"></a>Falha ao recuperar sessão/status de conversão

O envio de comandos da API REST com muita frequência fará com que o servidor seja limitado e retorne a falha eventualmente. O código de status HTTP no caso de limitação é 429 ("muitas solicitações"). Como regra geral, deve haver um atraso de **5 a 10 segundos entre as chamadas subsequentes**.

Observe que esse limite não afeta apenas as chamadas da API REST quando chamado diretamente, mas também suas contrapartes do C#/C + +, como `Session.GetPropertiesAsync` , `Session.RenewAsync` ou `Frontend.GetAssetConversionStatusAsync` .

Se você tiver uma limitação do lado do servidor, altere o código para fazer as chamadas com menos frequência. O servidor redefinirá o estado de limitação a cada minuto, portanto, é seguro executar novamente o código após um minuto.

## <a name="h265-codec-not-available"></a>Codec H265 não disponível

Há dois motivos pelos quais o servidor pode recusar a conexão com um `codec not available` erro.

**O codec H265 não está instalado:**

Primeiro, instale as **Extensões de Vídeo HEVC** conforme mencionado na seção [Software](../overview/system-requirements.md#software) dos requisitos do sistema.

Se você ainda enfrentar problemas, verifique se a placa de vídeo dá suporte a H265 e se você tem o driver de vídeo mais recente instalado. Confira a seção [PC de desenvolvimento](../overview/system-requirements.md#development-pc) dos requisitos do sistema para obter informações específicas do fornecedor.

**O codec está instalado, mas não pode ser usado:**

O motivo para esse problema é uma configuração de segurança incorreta nas DLLs. Esse problema não se manifesta ao tentar assistir vídeos codificados com H265. A reinstalação do codec também não corrige o problema. Em vez disso, execute as seguintes etapas:

1. Abra um **PowerShell com direitos de administrador** e execute

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Esse comando deve gerar o `InstallLocation` do codec, algo como:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Abra essa pasta no Windows Explorer
1. Deve haver uma subpasta **x86** e outra **x64**. Clique com o botão direito do mouse em uma das pastas e escolha **Propriedades**
    1. Selecione a guia **Segurança** e clique no botão de configurações **Avançado**
    1. Clique em **Alterar** para o **Proprietário**
    1. Digite **Administradores** no campo de texto
    1. Clique em **Verificar Nomes** e em **OK**
1. Repita as etapas acima para a outra pasta
1. Além disso, repita as etapas acima em cada arquivo DLL dentro de ambas as pastas. Deve haver ao todo quatro DLLs.

Para verificar se as configurações agora estão corretas, faça isso para cada uma das quatro DLLs:

1. Selecione **Propriedades > Segurança > Editar**
1. Percorra a lista de todos os **Grupos/Usuários** e verifique se cada um deles tem o direito de **Leitura e Execução** definido (a marca de seleção na coluna **Permitir** precisa estar marcada)

## <a name="low-video-quality"></a>Qualidade do vídeo baixa

A qualidade do vídeo pode ser comprometida pela qualidade da rede ou pela ausência do codec de vídeo H265.

* Confira as etapas para [identificar problemas de rede](#unstable-holograms).
* Confira os [requisitos do sistema](../overview/system-requirements.md#development-pc) para instalar o driver de vídeo mais recente.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>O vídeo gravado com a MRC não reflete a qualidade da experiência ao vivo

Um vídeo pode ser registrado no HoloLens por meio da [MRC (captura de realidade misturada)](/windows/mixed-reality/mixed-reality-capture-for-developers). No entanto, o vídeo resultante tem uma qualidade pior do que a experiência ao vivo por dois motivos:
* A taxa de quadros de vídeo é limitada a 30 Hz em vez de 60 Hz.
* As imagens de vídeo não passam pela etapa de processamento de [reprojeção de fase tardia](../overview/features/late-stage-reprojection.md), portanto, o vídeo tem menos fluidez.

Ambas são limitações inerentes da técnica de gravação.

## <a name="black-screen-after-successful-model-loading"></a>Tela preta após o carregamento bem-sucedido do modelo

Se você estiver conectado ao runtime de processamento e tiver carregado um modelo com êxito, mas vir apenas uma tela preta posteriormente, isso poderá ser devido a algumas causas distintas.

É recomendável testar as seguintes possibilidades antes de fazer uma análise mais detalhada:

* O codec H265 está instalado? Embora deva haver um fallback para o codec H264, vimos casos em que esse fallback não funcionou corretamente. Confira os [requisitos do sistema](../overview/system-requirements.md#development-pc) para instalar o driver de vídeo mais recente.
* Ao usar um projeto do Unity, feche o Unity, exclua as pastas temporárias *library* e *obj* no diretório do projeto e carregue/compile o projeto novamente. Em alguns casos, os dados armazenados em cache fizeram com que o exemplo não funcionasse corretamente, sem nenhum motivo óbvio para isso.

Se essas duas etapas não ajudarem, será necessário descobrir se os quadros de vídeo são recebidos pelo cliente ou não. Isso pode ser consultado programaticamente, conforme explicado no capítulo [Consultas de desempenho do lado do servidor](../overview/features/performance-queries.md). O `FrameStatistics struct` tem um membro que indica quantos quadros de vídeo foram recebidos. Se esse número for maior que 0 e estiver aumentando ao longo do tempo, o cliente receberá quadros de vídeo reais do servidor. Consequentemente, é com certeza um problema no lado do cliente.

### <a name="common-client-side-issues"></a>Problemas comuns do lado do cliente

**O modelo excede os limites da VM selecionada, especificamente o número máximo de polígonos:**

Consulte [limites de tamanho de servidor](../reference/limits.md#overall-number-of-polygons)específicos.

**O modelo não está dentro do frustum da câmera:**

Em muitos casos, o modelo é exibido corretamente, mas localizado fora do volume da câmera. Um motivo comum é que o modelo foi exportado com um eixo afastado do centro, de modo que ele é recortado pelo plano de recorte distante da câmera. É útil consultar programaticamente a caixa delimitadora do modelo e visualizar a caixa com o Unity como uma caixa de linha ou, ainda, imprimir os valores dela no log de depuração.

Além disso, o processo de conversão gera um [arquivo JSON de saída](../how-tos/conversion/get-information.md) junto com o modelo convertido. Para depurar problemas de posicionamento de modelo, vale a pena examinar a entrada `boundingBox` na [seção outputStatistics](../how-tos/conversion/get-information.md#the-outputstatistics-section):

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

A caixa delimitadora é descrita como uma posição `min` e `max` no espaço 3D, em metros. Portanto, uma coordenada de 1000,0 significa que ela está a um quilômetro de distância da origem.

Pode haver dois problemas com essa caixa delimitadora que resultam em uma geometria invisível:
* **A caixa pode ser muito afastada do centro**, portanto, o objeto é completamente recortado devido ao recorte de plano distante. Os valores de `boundingBox`, nesse caso, teriam a seguinte aparência: `min = [-2000, -5,-5], max = [-1990, 5,5]`, usando um deslocamento grande no eixo x como um exemplo aqui. Para resolver esse tipo de problema, habilite a opção `recenterToOrigin` na [configuração de conversão de modelo](../how-tos/conversion/configure-model-conversion.md).
* **A caixa pode estar centralizada, ser grande demais com uma diferença de várias ordens de magnitude**. Isso significa que, embora a câmera seja iniciada no centro do modelo, a geometria dele é recortada em todas as direções. Os valores de `boundingBox` típicos, nesse caso, seriam semelhantes a estes: `min = [-1000,-1000,-1000], max = [1000,1000,1000]`. O motivo para esse tipo de problema geralmente é uma incompatibilidade de escala de unidade. Para compensar, especifique um [valor de dimensionamento durante a conversão](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) ou marque o modelo de origem com as unidades corretas. O dimensionamento também pode ser aplicado ao nó raiz ao carregar o modelo em runtime.

**O pipeline de renderização do Unity não inclui os ganchos de renderização:**

O Azure Remote Rendering conecta-se ao pipeline de renderização do Unity para fazer a composição do quadro com o vídeo e para fazer a reprojeção. Para verificar se esses ganchos existem, abra o menu *:::no-loc text="Window > Analysis > Frame debugger":::* . Habilite-o e verifique se há duas entradas para o `HolographicRemotingCallbackPass` no pipeline:

![Pipeline de renderização do Unity](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>O padrão quadriculado é renderizado após o carregamento do modelo

Se a imagem renderizada for parecida com esta: a ![ captura de tela mostra uma grade de quadrados pretos e brancos com um menu ferramentas.](../reference/media/checkerboard.png)
em seguida, o renderizador atinge os [limites do polígono para o tamanho da configuração padrão](../reference/vm-sizes.md). Para mitigar, alterne para o tamanho da configuração **Premium** ou reduza o número de polígonos visíveis.

## <a name="the-rendered-image-in-unity-is-upside-down"></a>A imagem renderizada no Unity está de cabeça para baixo

Certifique-se de seguir o [tutorial do Unity: exibir modelos remotos](../tutorials/unity/view-remote-models/view-remote-models.md) exatamente. Uma imagem de cabeça para baixo indica que o Unity é necessário para criar um destino de renderização fora da tela. Esse comportamento não tem suporte no momento e cria um enorme impacto no desempenho no HoloLens 2.

Os motivos para esse problema podem ser MSAA, HDR ou habilitar o pós-processamento. Verifique se o perfil de baixa qualidade está selecionado e definido como padrão no Unity. Para fazer isso, vá para *editar > configurações de projeto... > qualidade*.

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>O código do Unity usando a API do Remote Rendering não é compilado

### <a name="use-debug-when-compiling-for-unity-editor"></a>Usar Depurar ao compilar para o Editor do Unity

Alterne o *tipo de build* da solução do Unity para **Depuração**. Ao testar ARR no editor do Unity, a definição `UNITY_EDITOR` só está disponível em builds de 'Depuração'. Observe que isso não está relacionado ao tipo de build usado para [aplicativos implantados](../quickstarts/deploy-to-hololens.md), em que você deve preferir os builds de "Versão".

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Falhas de compilação ao compilar amostras do Unity para o HoloLens 2

Vimos falhas falsas ao tentar compilar exemplos do Unity (início rápido, ShowCaseApp etc.) para o HoloLens 2. O Visual Studio reclama sobre não conseguir copiar alguns arquivos, embora eles estejam lá. Se você encontrar esse problema:
* Remova todos os arquivos temporários do Unity do projeto e tente novamente. Ou seja, feche o Unity, exclua as pastas temporárias *library* e *obj* no diretório do projeto e carregue/compile o projeto novamente.
* Verifique se os projetos estão localizados em um diretório no disco com caminho razoavelmente curto, pois a etapa de cópia às vezes parece ter problemas com nomes de arquivo longos.
* Se isso não ajudar, pode ser que o MS Sense interfira na etapa de cópia. Para configurar uma exceção, execute este comando do Registro por meio da linha de comando (requer direitos de administrador):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>As compilações do Arm64 para projetos do Unity falham porque AudioPluginMsHRTF.dll está ausente

O `AudioPluginMsHRTF.dll` para Arm64 foi adicionado ao pacote do *Windows Mixed Reality* *(com. Unity. XR. windowsmr. metro)* na versão 3.0.1. Verifique se você tem a versão 3.0.1 ou posterior instalada por meio do Gerenciador de pacotes do Unity. Na barra de menus do Unity, navegue até *janela > Gerenciador de pacotes* e procure o pacote do *Windows Mixed Reality* .

## <a name="native-c-based-application-does-not-compile"></a>O aplicativo nativo baseado em C++ não compila

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>Erro de ' biblioteca não encontrada ' para o aplicativo UWP ou dll

Dentro do pacote NuGet do C++, há um `microsoft.azure.remoterendering.Cpp.targets` arquivo de arquivo que define qual é o tipo binário a ser usado. Para identificar `UWP` , as condições na verificação de arquivo para `ApplicationType == 'Windows Store'` . Portanto, é preciso garantir que esse tipo seja definido no projeto. Esse deve ser o caso ao criar um aplicativo UWP ou DLL por meio do assistente de projeto do Visual Studio.

## <a name="unstable-holograms"></a>Hologramas instáveis

Caso os objetos renderizados pareçam estar se movendo em conjunto com os movimentos de cabeça, você talvez esteja enfrentando problemas com LSR (*reprojeção de fase tardia*). Veja a seção sobre [Reprojeção de fase tardia](../overview/features/late-stage-reprojection.md) para obter diretrizes sobre como abordar essa situação.

Outro motivo para hologramas instáveis (com oscilações, distorções, tremulações ou saltos) pode ser uma conectividade de rede ruim, em particular uma largura de banda de rede insuficiente ou uma latência muito alta. Um bom indicador para a qualidade da sua conexão de rede é o valor `ServiceStatistics.VideoFramesReused` das [estatísticas de desempenho](../overview/features/performance-queries.md). Quadros reutilizados indicam situações em que um quadro de vídeo antigo precisou ser reutilizado no lado do cliente porque nenhum novo quadro de vídeo estava disponível, por exemplo, devido à perda de pacotes ou devido a variações na latência de rede. Se `ServiceStatistics.VideoFramesReused` é frequentemente maior que zero, isso indica um problema de rede.

Outro valor a ser examinado é `ServiceStatistics.LatencyPoseToReceiveAvg`. Ele deve estar consistentemente abaixo de 100 ms. Ver valores mais altos pode indicar que você está conectado a um data center que está muito longe.

Para obter uma lista de possíveis mitigações, confira as [diretrizes para conectividade de rede](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="z-fighting"></a>Z-fighting

Embora o ARR ofereça [funcionalidade de mitigação de combate z](../overview/features/z-fighting-mitigation.md), o combate a z ainda pode aparecer na cena. Este guia tem como objetivo solucionar esses problemas restantes.

### <a name="recommended-steps"></a>Etapas recomendadas

Use o fluxo de trabalho a seguir para atenuar o combate a z:

1. Testar a cena com as configurações padrão de ARR (mitigação de combate z em)

1. Desabilitar a mitigação de combate ao z por meio de sua [API](../overview/features/z-fighting-mitigation.md) 

1. Alterar a câmera perto e longe do plano para um intervalo mais próximo

1. Solucionar problemas da cena por meio da próxima seção

### <a name="investigating-remaining-z-fighting"></a>Investigando o combate ao z restante

Se as etapas acima tiverem sido esgotadas e o combate a z restante for inaceitável, a causa subjacente do combate a z precisará ser investigada. Conforme mencionado na [página de recursos de mitigação de combate ao z](../overview/features/z-fighting-mitigation.md), há dois motivos principais para combater o z: perda de precisão de profundidade na extremidade extrema do intervalo de profundidade e superfícies que se cruzam ao serem coplanar. A perda de precisão de profundidade é uma eventualidade matemática e só pode ser atenuada seguindo a etapa 3 acima. As superfícies coplanar indicam uma falha de ativo de origem e são mais bem corrigidas nos dados de origem.

O ARR tem um recurso para determinar se as superfícies poderiam ser z-luta: [realce de xadrez](../overview/features/z-fighting-mitigation.md). Você também pode determinar visualmente o que causa o combate ao z. A primeira animação a seguir mostra um exemplo de perda de precisão de profundidade na distância e a segunda mostra um exemplo de superfícies de quase coplanar:

![Animação mostra um exemplo de perda de precisão de profundidade na distância.](./media/depth-precision-z-fighting.gif)  ![Animação mostra um exemplo de superfícies de quase coplanar.](./media/coplanar-z-fighting.gif)

Compare esses exemplos com o combate ao z para determinar a causa ou, opcionalmente, seguir este fluxo de trabalho passo a passo:

1. Posicione a câmera acima das superfícies de combate ao z para olhar diretamente na superfície.
1. Mova lentamente a câmera para trás, afastando-as das superfícies.
1. Se o combate a z estiver visível o tempo todo, as superfícies serão perfeitamente coplanars. 
1. Se o combate a z for visível na maioria das vezes, as superfícies serão quase coplanars.
1. Se o combate ao z estiver visível apenas de longe, a causa será a falta de precisão de profundidade.

As superfícies de coplanar podem ter várias causas diferentes:

* Um objeto foi duplicado pelo aplicativo de exportação devido a um erro ou abordagens de fluxo de trabalho diferentes.

    Verifique esses problemas com o respectivo suporte a aplicativos e aplicativos.

* As superfícies são duplicadas e invertidas para aparecer de lado duplo em renderizadores que usam a remoção de face frontal ou de verso.

    A importação por meio da [conversão de modelo](../how-tos/conversion/model-conversion.md) determina a sidedness principal do modelo. O sidedness duplo é assumido como o padrão. A superfície será renderizada como uma parede fina com iluminação correta de ambos os lados. Single-sidedness pode ser implícito por sinalizadores no ativo de origem ou explicitamente forçado durante a conversão do [modelo](../how-tos/conversion/model-conversion.md). Além disso, opcionalmente, o [modo único](../overview/features/single-sided-rendering.md) pode ser definido como "normal".

* Objetos interseccionam nos ativos de origem.

     Os objetos transformados de forma que algumas de suas superfícies se sobrepõem também criam um combate ao z. A transformação de partes da árvore de cena na cena importada no ARR também pode criar esse problema.

* As superfícies são intencionalmente criadas para toque, como decals ou texto em paredes.

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>Artefatos gráficos usando a renderização de estéreo de várias passagens em aplicativos C++ nativos

Em alguns casos, os aplicativos C++ nativos personalizados que usam um modo de renderização estéreo de várias passagens para conteúdo local (renderização para os olhos esquerdo e direito em passagens separadas) depois de chamar [**BlitRemoteFrame**](../concepts/graphics-bindings.md#render-remote-image) podem disparar um bug de driver. O bug resulta em falhas de rasterização não determinísticas, fazendo com que triângulos individuais ou partes de triângulos do conteúdo local desapareçam aleatoriamente. Por motivos de desempenho, é recomendável, de qualquer forma, renderizar o conteúdo local com uma técnica de renderização estéreo de passagem única mais moderna, por exemplo, usando **SV_RenderTargetArrayIndex**.

## <a name="next-steps"></a>Próximas etapas

* [Requisitos do sistema](../overview/system-requirements.md)
* [Requisitos de rede](../reference/network-requirements.md)