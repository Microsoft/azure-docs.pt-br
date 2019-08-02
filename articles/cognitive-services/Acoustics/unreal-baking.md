---
title: Tutorial de bake do Projeto Acústico do Unreal
titlesuffix: Azure Cognitive Services
description: Este documento descreve o processo de envio de um bake da acústica usando a extensão do editor do Unreal.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: michem
ms.openlocfilehash: 6b49a6b9e235414cd63eacdbad523bbda8646963
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304304"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Tutorial de bake do Projeto Acústico do Unreal
Este documento descreve o processo de envio de um bake da acústica usando a extensão do editor do Unreal.

Há cinco etapas para fazer um assar:

1. Criar ou marcar a malha de navegação do player
2. Marcar geometria acústica
3. Atribuir propriedades de materiais acústicos à geometria
4. Visualizar posicionamento do probe
5. Tortas

## <a name="open-the-project-acoustics-editor-mode"></a>Abra o modo do editor do Projeto Acústico

Importe o pacote de plug-ins do Projeto Acústico para o projeto. Para obter ajuda, consulte o tópico [Integração do Unreal](unreal-integration.md). Quando o plug-in estiver integrado, abra a IU da Acústica, clicando no ícone do novo Modo de Acústica.

![Captura de tela da opção Modo Acústico do Editor do Unreal](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Marcar atores para acústica

A guia de objetos é a primeira guia a ser exibida ao abrir o Modo de Acústica. Use essa guia para marcar atores no seu nível, que adiciona as marcas **AcousticsGeometry** ou **AcousticsNavigation** aos atores.

Selecione um ou mais objetos no World Outliner ou use a seção **Seleção em Massa** para ajudar a selecionar todos os objetos de uma categoria específica. Depois que os objetos forem selecionados, use a seção **Marcação** para aplicar a marca desejada aos objetos selecionados.

Se um elemento não tiver a marca **AcousticsGeometry** nem **AcousticsNavigation**, ele será ignorado na simulação. Há suporte somente para malhas estáticas, malhas de navegação e paisagens. Se você marcar outro qualquer elemento, ele será ignorado.

### <a name="for-reference-the-objects-tab-parts"></a>Para referência: As partes da guia Objetos

![Captura de tela da guia Objetos Acústicos no Unreal](media/unreal-objects-tab-details.png)

1. Botões de seleção da guia (**objetos** guia selecionada). Use esses botões para percorrer as várias etapas de execução de bake da acústica, de cima para baixo.
2. Uma breve descrição do que você precisa fazer usando esta página.
3. Seletores disponíveis para atores no nível.
4. Ao clicar em **Selecionar**, selecionará todos os objetos no nível que correspondam a pelo menos um dos tipos de atores verificados.
5. Ao clicar em **Desmarcar tudo**, apagará a seleção atual. Isso é o mesmo que pressionar a tecla Escape.
6. Use esses botões de opção para escolher se deseja aplicar a marca de Geometria ou Navegação aos atores selecionados.
7. Ao clicar em **Marcar**, adicionará a marca selecionada a todos os atores atualmente selecionados.
8. Ao clicar em **Desmarcar**, removerá a marca selecionada de todos os atores atualmente selecionados.
9. Ao clicar em **Selecionar Marcados**, apagará a seleção atual e selecionará todos os atores com a marcação atualmente selecionada.
10. Essas estatísticas mostram quantos atores estão marcados com cada tipo de marca.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Marcar oclusão acústica e geometria de reflexão

Abra a guia Objetos da janela Acústica. Marque todos os objetos como Geometria Acústica, se eles devem ocluir, refletir ou absorver som. Geometria acústica pode incluir elementos como piso, paredes, tetos, janelas e vidro de janela, tapetes e móveis grandes. Você pode usar qualquer nível arbitrário de complexidade para esses objetos. Como a cena é voxelizada antes da simulação, malhas altamente detalhadas, como árvores com muitas folhas pequenas, não são mais onerosas para bake do que objetos simplificados.

Não inclua elementos que não devem afetar a acústica, como malhas de colisão invisíveis.

A transformação de um objeto no momento do cálculo da sonda (através da guia Sondas, abaixo) é fixada nos resultados de bake. Mover qualquer um dos objetos marcados na cena exigirá refazer o cálculo da sonda e reciclar a cena.

### <a name="create-or-tag-a-navigation-mesh"></a>Criar ou marcar uma malha de navegação

Uma malha de navegação é usada para colocar pontos de teste para simulação. Você pode usar o [Volume de Limites de Malha de Navegação](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html) ou especificar sua própria malha de navegação. É necessário marcar pelo menos um objeto como **Navegação Acústica**. Se você usar a malha de Navegação do Unreal, verifique se você a criou primeiro.

### <a name="acoustics-volumes"></a>Volumes de Acústica ###

Há mais personalização avançada que você pode fazer em suas áreas de navegação com **Volumes de Acústica**. **Volumes de Acústica** são atores que você pode adicionar à sua cena e que permitem selecionar áreas para incluir e ignorar na malha de navegação. O ator expõe uma propriedade que pode ser alternada entre "Incluir" e "Excluir". Volumes de "Incluir" garantem que apenas áreas da malha de navegação dentro delas sejam consideradas e volumes de "Excluir" marcam essas áreas para serem ignoradas. Volumes de "Excluir" são sempre aplicados após volumes "Incluir". Marque **Volumes de Acústica** como **Navegação Acústica** durante o processo normal na guia Objetos. Esses atores ***não*** são marcados automaticamente.

![Captura de tela das propriedades de Volume de Acústica no Unreal](media/unreal-acoustics-volume-properties.png)

Volumes de "Excluir" destinam-se principalmente a fornecer controle refinado quanto a em que local não colocar sondas para reforçar o uso de recursos.

![Captura de tela de Excluir Volume de Acústica no Unreal](media/unreal-acoustics-volume-exclude.png)

Volumes de "Incluir" são úteis para criar seções manuais de uma cena, por exemplo, quando você deseja dividir sua cena em várias zonas acústicas. Por exemplo, se você tem uma cena grande, de muitos quilômetros quadrados, e duas áreas de interesse em que deseja fazer o bake da acústica. Você pode desenhar dois grandes volumes de "Incluir" na cena e produzir arquivos ACE para cada um deles, um por vez. Em seguida, no jogo, você pode usar volumes de gatilho combinados a chamadas de blueprint para carregar o arquivo ACE apropriado quando o player se aproximar de cada bloco.

**Volumes de Acústica** restringem apenas a navegação, e ***não*** a geometria. Cada sonda dentro de um **Volume de Acústica** de "Incluir" ainda receberá toda a geometria necessária fora do volume ao executar simulações de onda. Portanto, não deve haver descontinuidades na oclusão nem outras acústica resultante de o player cruzar de uma seção para outra.

## <a name="select-acoustic-materials"></a>Selecionar materiais acústicos

Quando os objetos estiverem marcados, clique no botão **Materiais** para ir para a guia Materiais. Essa guia será usada para especificar as propriedades do material para cada material no nível. Antes de qualquer ator ser marcado, ele ficará em branco.

Os materiais acústicos controlam a quantidade de energia sonora refletida de volta de cada superfície. O material acústico padrão tem absorção semelhante ao concreto. O Projeto Acústico sugere materiais com base no nome do material da cena.

O tempo de reverberação de um determinado material em uma sala é inversamente relacionado ao seu coeficiente de absorção, com a maioria dos materiais tendo valores de absorção na faixa de 0,01 a 0,20. Materiais com coeficientes de absorção acima dessa faixa são muito absorventes. Por exemplo, se uma sala parece muito reverberante, mude o material acústico das paredes, do piso ou do teto para algo de maior capacidade de absorção. A atribuição de material acústico aplica-se a todos os atores que usam esse material de cena.

![Grafo mostrando a correlação negativa do tempo de reverberação com coeficiente de absorção](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Para referência: Partes do guia de materiais

![Captura de tela da guia Objetos Acústicos no Unreal](media/unreal-materials-tab-details.png)

1. O botão da guia **Materiais**, usado para abrir esta página.
2. Uma breve descrição do que você precisa fazer usando esta página.
3. A lista de materiais usados no nível, tirada dos atores marcados como **AcousticsGeometry**. Clicar em um material aqui selecionará todos os objetos na cena que usam esse material.
4. Mostra o material acústico ao qual o material da cena foi atribuído. Clique em um menu suspenso para reatribuir um material de cena a um material acústico diferente.
5. Mostra o coeficiente de absorção acústica do material selecionado na coluna anterior. Um valor de zero significa perfeitamente reflexivo (sem absorção), enquanto um valor de 1 significa perfeitamente absorvente (sem reflexão). A alteração desse valor atualizará o Material Acústico (etapa nº 4) para **Personalizado**.

Se você fizer alterações aos materiais na sua cena, precisará alternar as guias no plug-in Projeto Acústico para que essas alterações sejam aplicadas na guia **Materiais**.

## <a name="calculate-and-review-listener-probe-locations"></a>Calcular e revisar os locais de investigação do ouvinte

Depois de atribuir os materiais, alterne para o **investigações** guia.

### <a name="for-reference-parts-of-the-probes-tab"></a>Para referência: Partes do guia de testes

![Captura de tela da guia Investigações Acústicas no Unreal](media/unreal-probes-tab-details.png)

1. O **investigações** botão guia usada para abrir essa página
2. Uma breve descrição do que você precisa fazer usando essa página
3. Utilize para escolher uma resolução de simulação grossa ou fina. Grande é mais rápido, mas tem determinadas vantagens e desvantagens. Veja [Fazer o Bake da Resolução](bake-resolution.md) abaixo para obter detalhes.
4. Escolha o local onde os arquivos de dados acústicos devem ser colocados usando este campo. Clique no botão "..." para usar um seletor de pastas. Para obter mais informações sobre arquivos de dados, consulte [Arquivo de Dados](#Data-Files), abaixo.
5. Os arquivos de dados para esta cena serão nomeados usando o prefixo fornecido aqui. O padrão é "[Nome do Nível] _AcousticsData".
6. Clique no botão **Calcular** para voxelizar a cena e calcular os locais dos pontos de sonda. Isso é feito localmente em sua máquina e deve ser feito antes de fazer um cozimento. Após o cálculo das sondas, os controles acima serão desabilitados e esse botão mudará para **Limpar**. Clique no botão **Limpar** para apagar os cálculos e habilitar os controles para que você possa recalcular usando as novas configurações.

As sondas devem ser colocados através do processo automatizado fornecido na guia **Sondas**.


### <a name="what-the-calculate-button-calculates"></a>O que o botão "Calcular" calcula

O botão **Calcular** coleta todos os dados que você forneceu até agora (geometria, navegação, materiais e configuração grossa/fina) e passa por várias etapas:

1. Ele pega a geometria das malhas da cena e calcula um volume de voxel. O volume do voxel é um volume tridimensional que envolve toda a sua cena e é composto de pequenos "voxels" cúbicos. O tamanho dos voxels é determinado pela frequência de simulação, que é definida pela configuração **Simulation Resolution**. Cada voxel é marcado como "aberto" ou contendo geometria de cena. Se um voxel contém geometria, o voxel é marcado com o coeficiente de absorção do material atribuído a essa geometria.
2. Em seguida, ele usa os dados de navegação para calcular os locais acusticamente interessantes em que o jogador pode ir. Ele tenta encontrar um conjunto razoavelmente pequeno desses locais, que inclui áreas menores, como portas e corredores, e depois para os quartos, para espaços abertos. Para cenas pequenas, normalmente são menos de 100 locais, enquanto cenas grandes podem ter até mil.
3. Para cada um dos locais finais dos ouvintes que calcula, ele determina um número de parâmetros como "aberto" é o espaço, o tamanho da sala em que está, etc.
4. Os resultados desses cálculos são armazenados em arquivos no local especificado (consulte [Arquivos de Dados](#Data-Files), abaixo)

Dependendo do tamanho da sua cena e da velocidade da sua máquina, esses cálculos podem levar vários minutos.

Depois que esses cálculos forem concluídos, você poderá visualizar os dados de voxels e os locais dos pontos de sondas para ajudar a garantir que o assar lhe proporcione bons resultados. Coisas como uma malha de navegação ruim ou geometria extra / ausente normalmente serão rapidamente visíveis na visualização para que você possa corrigi-la.


## <a name="debug-display"></a>Exibição de depuração

Após o cálculo da sonda, um novo ator aparecerá no World Outliner chamado **AcousticsDebugRenderer**. Marcar as caixas de seleção **Renderizar Sondas** e **Renderizar Voxels** habilitará a exibição de depuração dentro do visor do editor.

![Captura de tela mostrando o ator Renderizador de Depuração Acústica no Editor do Unreal](media/acoustics-debug-renderer.png)

Se você não visualizar voxels nem sondas sobrepostas no nível, certifique-se de que a renderização em tempo real está habilitada no visor.

![Captura de tela da opção de renderização em tempo real no Unreal](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels

Os voxels são mostrados na janela da cena como cubos verdes em torno da geometria participante. Voxels que contêm apenas ar não são mostrados. Há uma grande caixa verde em torno de sua cena inteira que indica o volume completo de voxels que será usado na simulação.
Movimente pela cena e verifique se a geometria de oclusão acústica tem voxels. Além disso, verifique se objetos não acústicos, como malhas de colisão, não foram voxelizados. A câmera cena deve estar a cerca de 5 metros do objeto para os voxels mostrarem.

Se você comparar os voxels criados com resolução grossa vs resolução fina, verá que os voxels grossos serão duas vezes maiores.

![Captura de tela da Versão prévia de voxels acústicos no editor do Unreal](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Pontos de teste

Os pontos de teste são sinônimos de possíveis locais do player (ouvinte). Ao fazer bake, a simulação calcula a acústica que conecta todos os possíveis locais de origem para cada ponto de sonda. No tempo de execução, o local de escuta é interpolado entre pontos de sonda próximos.

É importante verificar se os pontos de sonda existem em qualquer lugar onde o player deverá percorrer na cena. Os pontos de sonda são colocados na malha de navegação pelo mecanismo do Projeto Acústico e não podem ser movidos ou editados, portanto, assegure-se de que a malha de navegação cubra todos os possíveis locais do player, inspecionando os pontos de sonda.

![Captura de tela da versão prévia de sondas acústicas no Unreal](media/unreal-probes-preview.png)

Veja [Fazer o Bake da Resolução](bake-resolution.md) para obter mais detalhes sobre resolução bruta vs. fina.

## <a name="bake-your-level-using-azure-batch"></a>Fazer o bake do nível usando Lote do Azure

É possível fazer o bake da cena com um cluster de cálculo na nuvem usando o serviço do Lote do Azure. O plug-in do Unreal do Projeto Acústico conecta-se diretamente ao Lote do Azure para instanciar, gerenciar e desativar um cluster do Lote do Azure para cada bake. Na guia Bake, insira as credenciais do Azure, selecione um tipo e tamanho de computador de cluster e clique em Bake.

### <a name="for-reference-parts-of-the-bake-tab"></a>Para referência: Partes da guia tortas

![Captura de tela da guia Bake da Acústica no Unreal](media/unreal-bake-tab-details.png)

1. O botão de incorporar essa guia usado para abrir essa página.
2. Uma breve descrição do que fazer nesta página.
3. Campos para inserir suas credenciais do Azure assim que sua conta do Azure for criada. Para obter mais informações, consulte [criar uma conta do lote do Azure](create-azure-account.md).
4. Inicie o portal do Azure para gerenciar suas assinaturas, monitorar o uso e exibir informações de cobrança etc. 
5. Tipo de nó de computação em lote do Azure a ser usado para o cálculo. O tipo de nó deve ser suportado pelo seu local do datacenter do Azure. Se não souber, deixe **Standard_F8s_v2**.
6. Número de nós a serem usados para este cálculo. O número digitado aqui afeta o tempo para concluir o cozimento e é limitado pela alocação principal do Lote do Azure. A alocação padrão permite apenas dois nós principais de 8 ou um nó de 16 núcleos, mas pode ser expandida. Para obter mais informações sobre restrições de alocação de core, consulte [criar uma conta do lote do Azure](create-azure-account.md).
7. Marque essa caixa de seleção para configurar seu pool de computação para usar [nós de baixa prioridade](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Os nós de computação de baixa prioridade têm um custo muito inferior, mas eles podem não estar sempre disponíveis ou podem admitir preempção a qualquer momento.
8. A quantidade de tempo decorrido esperada para que seu trabalho seja executado na nuvem. Isso não inclui o tempo de inicialização do nó. Uma vez que o trabalho comece a funcionar, isso é quanto tempo deve ser antes de você retornar os resultados. Observe que isso é apenas uma estimativa.
9. A quantidade total de tempo de computação necessária para executar as simulações. Essa é a quantidade total de tempo de computação do nó que será usado no Azure. Ver [estimativa tortas custo](#Estimating-bake-cost) abaixo para obter mais informações sobre como usar esse valor.
10. Clique no botão Assar para enviar o assar para a nuvem. Enquanto um trabalho está em execução, isso mostra **Cancelar trabalho**. Se houver algum erro nessa guia, ou se o fluxo de trabalho na guia **Sondas** não tiver sido concluído, esse botão será desabilitado.
11. A contagem de sondas para sua cena, conforme calculada na guia **Sondas**. O número de probes determina o número de simulações que precisam ser executadas na nuvem. Você não pode especificar mais nós do que probes.
12. Essa mensagem informa o status atual do trabalho ou, se houver algum erro nessa guia, quais são esses erros.

Você sempre poderá obter informações completas sobre trabalhos ativos, pools de computação e armazenamento no [portal do Azure](https://portal.azure.com).

Enquanto um trabalho está em execução, o botão **Bake** muda para **Cancelar trabalho**. Use este botão para cancelar o trabalho em andamento.Use esse botão para cancelar o trabalho em andamento. O cancelamento de um trabalho não pode ser desfeito, nenhum resultado estará disponível e você ainda será cobrado por qualquer tempo de computação do Azure usado antes do cancelamento.

Após ter iniciado um bake, você poderá fechar o Unreal. Dependendo do projeto, do tipo de nó e do número de nós, um cozimento na nuvem pode levar várias horas. O status do trabalho de cozimento será atualizado quando você recarregar o projeto e abrir a janela Acústica. Se o trabalho foi concluído, o arquivo de saída será baixado.

As credenciais do Azure são armazenadas com segurança no computador local e associadas ao projeto do Unreal. Eles são usados exclusivamente para estabelecer uma conexão segura com o Azure.

### <a name="Estimating-bake-cost"></a> Estimar o custo do bake do Azure

Para estimar o custo de um dado bake, pegue o valor mostrado para **Estimated Compute Cost**, que é uma duração, e multiplique-o pelo custo por hora em sua moeda local do **VM Node Type** você selecionou. O resultado não incluirá o tempo de nó necessárias para colocar os nós e em execução. Por exemplo, se você selecionar **Standard_F8s_v2** para seu tipo de nó, que tem um custo de US$ 0,40/h, e o custo estimado de computação for de 3 horas e 57 minutos, o custo estimado para executar o trabalho será de US$ 0,40 * ~ 4 horas = ~ US$ 1,60. O custo real provavelmente será um pouco maior devido ao tempo extra para iniciar os nós. Você pode encontrar o custo de nó por hora na página [Preço do lote do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (selecione "Computar otimizado" ou "Computação de alto desempenho" para a categoria).

### <a name="reviewing-the-bake-results"></a>Revendo os resultados do cozimento

Depois que o assar for concluído, verifique se os voxels e os pontos de teste estão em seus locais esperados, executando o plugin de tempo de execução.

## <a name="Data-Files"></a>Arquivos de dados

Existem quatro arquivos de dados criados por este plugin em vários pontos. Apenas um deles é necessário em tempo de execução e é colocado na pasta Conteúdo/Acústico do projeto, a qual é automaticamente adicionada ao caminho de empacotamento do projeto. Os outros três estão dentro da pasta Dados Acústicos e não são empacotados.

* **[Projeto]/Config/ProjectAcoustics.cfg**: Esse arquivo armazena os dados inseridos nos campos na Interface do Usuário do Modo de Acústica. O local e o nome deste arquivo não podem ser alterados. Existem outros valores armazenados nesse arquivo que afetam o bake, mas são para usuários avançados e não devem ser alterados.
* **[Projeto]/Content/Acoustics/[LevelName]\_AcousticsData.ace**: Esse arquivo é criado durante a simulação de bake e contém os dados de pesquisa usados pelo tempo de execução para renderizar a acústica da cena. O local e o nome desse arquivo podem ser alterados usando os campos na **investigações** guia. Se você quiser renomear esse arquivo após sua criação, exclua o UAsset do seu projeto Unreal, renomeie o arquivo fora do Unreal no Explorador de Arquivos e, em seguida, importe novamente o arquivo para Unreal a fim de produzir um novo UAsset. Renomear o UAsset por si só não funcionará.
* **[Projeto]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData.vox**: Esse arquivo armazena a geometria acústica voxelizada e as propriedades do material. Calculado usando o botão **Calcular** na guia **Sondas**. A localização e o nome desse arquivo podem ser alterados usando os campos da guia **Sondas**.
* **[Projeto]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config.xml**: Esse arquivo armazena os parâmetros calculados usando o botão **Calcular** na guia **Sondas**. A localização e o nome desse arquivo podem ser alterados usando os campos da guia **Sondas**.

Tenha cuidado para não excluir o arquivo *.ace baixado do Azure. Este arquivo não é recuperável, exceto por re-gravar a cena.

## <a name="next-steps"></a>Próximas etapas
* Explore os [controles de design do Unreal](unreal-workflow.md)
* Explore os [conceitos de design do Projeto Acústico](design-process.md)

