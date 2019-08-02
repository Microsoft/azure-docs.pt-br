---
title: Tutorial de design do Projeto Acústico do Unreal
titlesuffix: Azure Cognitive Services
description: Este tutorial descreve o fluxo de trabalho de design do Projeto Acústico no Unreal e Wwise.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 1692032b093cd6189cac3ea3f63c563d9accd8ed
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477823"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Tutorial de design do Projeto Acústico do Unreal/Wwise
Este tutorial descreve a configuração de design e fluxo de trabalho do Projeto Acústico no Unreal e Wwise.

Pré-requisitos de software:
* Um projeto do Unreal com os plug-ins do Unreal e Wwise do Projeto Acústico

Para obter um projeto do Unreal com Projeto Acústico, você pode:
* Seguir as instruções de [Integração do Unreal do Projeto Acústico](unreal-integration.md) para adicionar o Projeto Acústico ao projeto do Unreal
* Ou, usar o [projeto de exemplo do Projeto Acústico](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Configurar propriedades do WWE em todo o projeto
O Wwise tem curvas de oclusão e obstrução globais que afetam como o plug-in do Projeto Acústico direciona o DSP de áudio do Wwise.

### <a name="design-wwise-occlusion-curves"></a>Projetar curvas de oclusão do Wwise
Quando o Projeto Acústico estiver ativo, ele responderá às curvas de volume de oclusão, LPF (filtro passa baixa) e HPF (filtro passa alta) definidas no Wwise. É recomendável definir o tipo de curva de volume como linear com um valor de -100 dB para um valor de Oclusão de 100.

Com essa configuração, se a simulação do Projeto Acústico calcular uma oclusão de -18 dB, ela será inserida na curva abaixo em X = 18 e o valor Y correspondente será a atenuação aplicada. Para fazer meia oclusão, defina o ponto de extremidade como -50 dB, em vez de -100 dB, ou como -200 dB para exagerar a oclusão. Você pode adaptar e ajustar qualquer curva que funcione melhor para o seu jogo.
 
![Captura de tela do editor de curva de oclusão do Wwise](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Desabilitar curvas de obstrução do Wwise
As curvas de obstrução do Wwise afetam o nível de dry em isolamento, mas o Projeto Acústico usa controles de design e simulação para impor relações wet/dry. É recomendável desabilitar a curva de volume de obstrução. Para projetar o nível de wetness, utilize o controle Ajuste de Wetness descrito posteriormente.
 
Se você estiver usando curvas LPF/HPF de obstrução para outras finalidades, certifique-se de defini-las para Y=0 em X=0 (ou seja, não haverá LPF ou HPF quando não houver obstrução).

![Captura de tela do editor de curva de obstrução do Wwise](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Projetar parâmetros de mixer do Projeto Acústico
É possível controlar as propriedades de reverberação globais, visitando a guia de plug-in do mixer do barramento do Projeto Acústico. Clique duas vezes em "Mixer do Projeto Acústico (Personalizado)" para abrir o painel de configurações do plug-in do mixer.

Também é possível ver que o plug-in do mixer tem uma opção "Executar Espacialização". Se você preferir usar a espacialização interna do Projeto Acústico, marque a caixa de seleção "Executar Espacialização" e escolha entre HRTF ou Panorâmica. Certifique-se de desabilitar todos os barramentos do Aux Dry que você configurou, caso contrário, ouvirá o caminho direto duas vezes. Use o "Ajuste de Wetness" e "Fator de Escala de Tempo de Reverberação" para exercitar o controle global da combinação de reverberação. Note que você deverá reiniciar o Unreal e, em seguida, regenerar os bancos de sons antes de clicar no play para pegar as alterações de configuração do plug-in do mixer, como a caixa de seleção 'Executar Espacialização'.

![Captura de tela das opções de plug-in do mixer do Wwise do Projeto Acústico](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Definir controles de design do Projeto Acústico na hierarquia actor-mixer do Wwise
Para controlar os parâmetros de um actor-mixer individual, clique duas vezes no Actor-Mixer e, em seguida, clique na aba Plug-in do Mixer. Aqui você poderá alterar qualquer parâmetro no nível por som. Esses valores combinam com os definidos do lado Unreal (descrito abaixo). Por exemplo, se o plug-in do Unreal do Projeto Acústico definir o Ajuste de Outdoorness em um objeto como 0,5, e o Wwise o definir como -0,25, o Ajuste de Outdoorness resultante aplicado a esse som será de 0,25.

![Captura de tela das configurações do mixer por som na hierarquia de mixer de ator do Wwise](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Assegure-se de que o barramento auxiliar tenha envio de dry e que o barramento de saída tenha envio de wet
Lembre-se de que a configuração necessária de actor-mixer realiza intercâmbio de roteamento wet e dry usual no Wwise. Isso produz um sinal de reverberação no barramento de saída do actor-mixer (definido como Barramento do Projeto Acústico) e sinal dry ao longo do barramento auxiliar definido pelo usuário. Esse roteamento é necessário devido aos recursos da API de plug-in do mixer do Wwise que o plug-in do Wwise do Projeto Acústico usa.

![Captura de tela do editor do Wwise mostrando diretrizes de design de voz para Projeto Acústico](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Configurar curvas de atenuação de distância
Assegure-se de que qualquer curva de atenuação usada pelos actor-mixers usando o Projeto Acústico tenha o envio auxiliar definido pelo usuário configurado como "volume de barramento de saída". O Wwise faz isso por padrão para curvas de atenuação recém-criadas. Se você estiver migrando um projeto existente, verifique as configurações de curva.

Por padrão, a simulação do Projeto Acústico tem um raio de 45 metros ao redor do local do player. Em geral, é recomendável definir a curva de atenuação para -200 dB em torno dessa distância. Essa distância não é uma restrição difícil. Para alguns sons como armas, você pode querer um raio maior. Nesses casos, a ressalva é que apenas a geometria dentro de 45 m do local do player participará. Se o player estiver em uma sala e uma fonte de som estiver fora da sala e a 100 m de distância, estará adequadamente oclusa. Se a fonte estiver em uma sala e o player estiver fora e a 100 m de distância, não estará adequadamente oclusa.

![Captura de tela de curvas de atenuação do Wwise](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Equalização pós-mixer ###
 Outra coisa que você talvez queira fazer é adicionar um equalizador pós-mixer. Você pode tratar o barramento do Projeto Acústico como um barramento de reverberação típico (no modo de reverberação padrão) e colocar um filtro nele para realizar a equalização. Você pode ver uma amostra disso no Projeto de Amostra Wwise do Projeto Acústico.

![Captura de tela da EQ pós-mixer do Wwise](media/wwise-post-mixer-eq.png)

Por exemplo, um filtro passa alta pode ajudar a lidar com os graves de gravações de campo próximo que geram reverberação de graves exagerados irreal. Você também pode obter mais controle pós-bake ajustando a EQ por meio de RTPCs, permitindo que você altere a cor da reverberação no momento do jogo.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Configurar propriedades do Projeto Acústico para toda a cena

O ator do Projeto Acústico expõe muitos controles que modificam o comportamento do sistema e são úteis na depuração.

![Captura de tela de Controles de espaço acústico do Unreal](media/acoustics-space-controls.png)

* **Dados acústicos:** Nesse campo deve ser atribuído um ativo de bake da acústica do diretório Conteúdo/Acústico. O plug-in do Projeto Acústico adicionará automaticamente o diretório Conteúdo/Acústico aos diretórios empacotados do projeto.
* **Tamanho do bloco:** As extensões da região ao redor do ouvinte que você deseja que os dados acústicos carreguem na RAM. Enquanto as sondas de escuta imediatamente ao redor do player forem carregadas, os resultados serão os mesmos que o carregamento de dados acústicos para todas as sondas. Blocos maiores usam mais RAM, mas reduzem E/S de disco
* **Stream Automático:** Quando habilitado, carrega automaticamente em novos blocos à medida que o ouvinte alcança a borda de uma região carregada. Quando desabilitado, será necessário carregar novos blocos manualmente por meio de código ou blueprints
* **Dimensionar Cache:** controla o tamanho do cache usado para consultas acústicas. Um cache menor usa menos RAM, mas pode aumentar o uso da CPU para cada consulta.
* **Acústica Habilitada:** Um controle de depuração para habilitar a alternância A/B rápida da simulação de Acústica. Esse controle é ignorado nas configurações de envio. O controle é útil para descobrir se um bug de áudio específico origina-se nos cálculos de acústica ou algum outro problema no projeto do Wwise.
* **Atualizar Distâncias:** Utilize essa opção se você quiser usar as informações acústicas pré-preparadas para consultas de distância. Essas consultas são semelhantes a ray-casts, mas como são pré-calculadas consumem muito menos CPU. Um exemplo de uso é para reflexões discretas da superfície mais próxima do ouvinte. Para ter um aproveitamento total, você deverá usar código ou Blueprints para consultar as distâncias.
* **Desenhar Estatísticas:** Embora o `stat Acoustics` do UE possa fornecer informações sobre a CPU, essa exibição de status mostrará o arquivo ACE atualmente carregado, o uso da RAM e outras informações de status no canto superior esquerdo da tela.
* **Desenhar Voxels:** Sobreposição de voxels próximos ao ouvinte mostrando a grade de voxels usada durante a interpolação de tempo de execução. Se um emissor estiver dentro de um voxel de tempo de execução, ele falhará nas consultas acústicas.
* **Desenhar Sondas:** Mostra todas as sondas dessa cena. Terão cores diferentes, dependendo do estado de carga.
* **Desenhar Distâncias:** Se Atualizar Distâncias estiver habilitado, mostrará uma caixa na superfície mais próxima do ouvinte, em direções quantizadas ao redor do ouvinte.

## <a name="actor-specific-acoustics-design-controls"></a>Controles de design de acústica específicos do ator
Esses controles de design são definidos para um componente de áudio individual no Unreal.

![Captura de tela de Controles de componente de áudio do Unreal](media/audio-component-controls.png)

* **Multiplicador de Oclusão:** Controla o efeito de oclusão. Valores > 1 amplificarão a oclusão. Valores <1 irão minimizá-la.
* **Ajuste de Wetness:** dB de reverberação adicional
* **Multiplicador de Tempo de Decaimento:** Controla o RT60 multiplicativamente, com base na saída da simulação acústica
* **Ajuste de Outdoorness:** Controla o nível da reverberação de ambiente externo. Valores mais próximos de 0 são mais fechados, mais próximos de 1 são mais abertos. Esse ajuste é aditivo, portanto, defini-lo como -1 imporá fechado, defini-lo como +1 imporá aberto.
* **Db de Transmissão:** Renderize um som adicional através da parede com essa intensidade combinada com atenuação da distância baseada no campo visual.
* **Distorção de Distância de Relação Wet:** Ajusta as características de reverberação na fonte como se estivesse mais próximo/mais afastado, sem afetar o caminho direto.
* **Tocar ao Iniciar:** ative/desative para especificar se o som deve ser reproduzido automaticamente na inicialização da cena. Habilitado por padrão.
* **Mostrar Parâmetros Acústicos:** Exibir informações de depuração diretamente na parte superior do componente no jogo. (somente para configurações de não envio)

## <a name="blueprint-functionality"></a>Funcionalidade Blueprint
O ator do Espaço Acústico é acessível via blueprint, fornecendo funcionalidade como carregar um mapa ou modificar configurações via script de nível. Nós fornecemos dois exemplos aqui.

### <a name="add-finer-grained-control-over-streaming-load"></a>Adicionar um controle mais refinado à carga de streaming
Para gerenciar você mesmo o streaming de dados acústicos, em vez de transmitir automaticamente com base na posição do player, é possível usar a função de blueprint do Bloco Forçar Carregamento:

![Captura de tela de opções de Streaming de Blueprint no Unreal](media/blueprint-streaming.png)

* **Destino:** O ator do espaço acústico
* **Posição central:** O centro da região que precisa de dados carregados
* **Descarregar sondas fora do bloco:** Se estiver marcado, todas as sondas que não estiverem na nova região serão descarregadas da RAM. Se estiver desmarcado, a nova região será carregada na memória, deixando as sondas existentes também carregadas na memória
* **Bloquear na conclusão:** Faz com que o bloco carregue uma operação síncrona

O tamanho do bloco já deve ser definido antes de chamar Bloco Forçar Carregamento. Por exemplo, seria possível fazer isto para carregar um arquivo de ACE, definir o tamanho do bloco e transmitir em uma região:

![Captura de tela de opções de configuração de Streaming no Unreal](media/streaming-setup.png)

A função de blueprint Carregar dados acústicos usada neste exemplo tem os seguintes parâmetros:

* **Destino:** O ator do Espaço acústico.
* **Novo bake:** O ativo de dados acústicos que será carregado. Deixar esse espaço em branco/configurá-lo como nulo descarregará o bake atual sem carregar um novo.

### <a name="optionally-query-for-surface-proximity"></a>Consulta opcional por proximidade de superfície
Se você quiser ver como as superfícies próximas estão em uma determinada direção ao redor do ouvinte, poderá usar a função Distância de Consulta. Essa função pode ser útil para conduzir reflexões direcionalmente atrasadas, ou para outras lógicas de jogo impulsionadas pela proximidade da superfície. A consulta é menos dispendiosa do que um ray-cast porque os resultados são extraídos da tabela de pesquisa de acústica.

![Captura de tela do exemplo de consulta de distância do Blueprint](media/distance-query.png)

* **Destino:** O ator do espaço acústico
* **Pesquisar Direção:** A direção da consultar, centralizada no ouvinte
* **Distância:** Se a consulta for com êxito, a distância até a superfície mais próxima
* **Valor de retorno:** Booliano - verdadeiro se a consulta foi com êxito, caso contrário, falso

## <a name="next-steps"></a>Próximas etapas
* Explore os conceitos relacionados a [processo de design](design-process.md)
* [Criar uma conta do Azure](create-azure-account.md) para fazer o bake da sua própria cena


