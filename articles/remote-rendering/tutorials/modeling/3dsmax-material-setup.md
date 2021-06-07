---
title: Configurar materiais de PBR no 3ds Max
description: Explica como configurar materiais de renderização baseada fisicamente no 3ds e exportá-los para o formato FBX.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 12407d6344c69c747230e9db6fa4d53b4520dc82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96020272"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3ds-max"></a>Tutorial: Configurar materiais de renderização baseada fisicamente no 3ds Max

## <a name="overview"></a>Visão geral
Neste tutorial, você aprenderá como:

>[!div class="checklist"]
>
> * Atribuir materiais com iluminação avançada aos objetos em uma cena.
> * Processar a instanciação de objetos e materiais.
> * Exportar uma cena para o formato FBX e selecionar opções importantes.

> [!Note]
> O procedimento descrito neste tutorial funciona em 3ds Max 2019 e 3ds Max 2020.
> Uma alteração em como o 3ds Max 2021 exporta os mapas de relevo significa que os mapas normais não serão encontrados pelo serviço de conversão se essa versão for usada.

A criação de [materiais de PBR (renderização baseada fisicamente)](../../overview/features/pbr-materials.md) no 3ds Max é uma tarefa relativamente simples. Ela é semelhante em muitas maneiras à configuração de PBR em outros aplicativos de criação de conteúdo, como o Maya. Este tutorial é um guia para a instalação básica do sombreador de PBR e a exportação do FBX para projetos do Azure Remote Rendering.

A cena de exemplo deste tutorial contém vários objetos de caixa poligonais. Diferentes materiais, como madeira, metal, metal pintado, plástico e borracha são atribuídos a eles. Em geral, cada material contém todas ou a maioria das seguintes texturas:

* **Albedo**, que é o mapa de cores do material e também é chamado de **Difuso** e **BaseColor**.
* **Metalicidade**, que determina se um material é metálico e quais partes dele são metálicas. 
* **Aspereza**, que determina o nível de aspereza ou suavidade de uma superfície.
Também afeta a nitidez ou o desfoque dos reflexos e dos realces em uma superfície.
* **Normal**, que adiciona detalhes a uma superfície sem adicionar mais polígonos. Exemplos de detalhes são furos e entalhes em uma superfície metálica ou textura em madeira.
* **Oclusão de Ambiente**, que é usada para adicionar um sombreamento suave e sombras de contato a um modelo. Trata-se de um mapa em escala de cinza que indica quais áreas do modelo recebem iluminação total (branco) ou sombreamento total (preto).

## <a name="prepare-the-scene"></a>Preparar a cena
No 3ds Max, o processo de configuração de um material de PBR é o descrito a seguir.

Para começar, criaremos vários objetos de caixa, cada um representando um tipo diferente de material.

>[!TIP]
>Antes de você começar a criar ativos para o Remote Rendering, vale a pena observar que ele usa metros para medição.  
>
>Portanto, é uma boa ideia definir as unidades do sistema da sua cena em metros. Também é recomendável definir as **Unidades** em metros nas configurações de exportação do FBX ao exportar uma cena.

A captura de tela a seguir ilustra as etapas necessárias para definir as unidades do sistema em metros no 3ds Max. 

1. No menu principal, acesse **Personalizar** > **Configuração das Unidades** > **Configuração das Unidades do Sistema**. Em **Escala das Unidades do Sistema**, selecione **Metros**: ![Captura de tela que mostra como definir as unidades do sistema.](media/3dsmax/system-units.jpg)

1. Agora, podemos começar a criar os modelos. Na cena de exemplo, criaremos vários objetos de caixa, cada um representando um tipo de material diferente. Por exemplo, metal, borracha e plástico. 

   >[!TIP]
   >Ao criar ativos, uma boa prática é nomeá-los adequadamente conforme o uso. Isso facilitará a localização deles posteriormente se a cena tiver muitos objetos.

1. Renomeie os objetos, conforme mostrado na seguinte captura de tela: 

   ![Captura de tela que mostra como renomear objetos.](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Atribuir materiais

Agora que temos alguns objetos na cena, nesse caso, vários cubos, podemos iniciar a configuração de PBR:

1. Na barra de ferramentas principal, selecione o ícone **Editor de Material**, conforme mostrado na captura de tela a seguir. Selecione também **M** no teclado para abrir o editor. O Editor de Material tem dois modos que você pode selecionar na lista **Modos**: **Editor de Material Compacto** e **Editor de Material Esquemático**. Como essa cena é relativamente simples, usaremos o modo compacto.

1. No Editor de Material, você verá várias esferas. Essas esferas são os materiais. Atribuiremos um desses materiais a cada objeto (cada caixa) na cena. Para atribuir os materiais, primeiro selecione um dos objetos no visor principal. Em seguida, selecione a primeira esfera no Editor de Material. Depois que recebe um objeto, o material selecionado é realçado conforme mostrado na imagem a seguir.

1. Selecione **Atribuir Material à Seleção**, conforme mostrado. O material agora é atribuído ao objeto selecionado.

   ![Captura de tela que mostra como atribuir materiais.](media/3dsmax/assign-material.jpg)

    No Editor de Material, você pode escolher entre vários tipos de materiais, dependendo das suas necessidades. Normalmente, por padrão, o tipo de material é definido como **Padrão**. Esse é um material básico que não é adequado para a configuração de PBR. Portanto, precisamos alterar o tipo de material para um material de PBR. O Material Físico é o preferencial no 3ds Max para projetos do Azure Remote Rendering.

1. No Editor de Material, selecione a guia **Padrão**. No **Navegador de Material/Mapa**, selecione **Material Físico**. Essa ação converte o material **Padrão** atribuído em um Material Físico de PBR.

   ![Captura de tela que mostra como alterar o material.](media/3dsmax/physical-material.jpg)

    No Editor de Material, agora é possível ver as propriedades do Material Físico, conforme mostrado na captura de tela a seguir. Agora você pode começar a atribuir texturas ao ativo.

   ![Captura de tela que mostra a lista de texturas.](media/3dsmax/textures-list.jpg)

Como você pode ver, há uma ampla variedade de mapas e texturas que podem ser adicionados ao material. Para este tutorial, usamos apenas cinco slots de textura no material.

>[!TIP]
>Uma boa prática é nomear os materiais de maneira adequada, conforme mostrado na captura de tela anterior.

A forma como você gera as texturas pode variar de acordo com a preferência ou o uso. Por exemplo, talvez você queira usar texturas de blocos que possam ser aplicadas a qualquer ativo. Ou talvez você precise que partes específicas de um projeto ou ativo tenham seus próprios conjuntos personalizados de texturas. Talvez você queira usar texturas de blocos genéricas que encontrou online. Você também pode criá-las em aplicativos como Photoshop, Quixel Suite e Substance Suite.

Antes de começarmos a atribuir texturas, precisamos considerar as coordenadas de textura (UVW) do ativo. Uma prática recomendada ao aplicar qualquer textura a um modelo é garantir que o modelo esteja desencapsulado. (As texturas não serão exibidas corretamente sem o devido desencapsulamento de UV.) Isso é especialmente importante para nossos propósitos porque queremos usar um mapa de AO (Oclusão de Ambiente) em nosso modelo. Ao contrário do Sombreador Stingray no Maya, o Material Físico no 3ds Max não tem um slot de textura de AO dedicado. Portanto, aplicaremos o mapa de AO a outro slot. Para permitir que ele seja usado separadamente das outras texturas (texturas de blocos, por exemplo), atribuiremos a ele um canal de mapa de UVW próprio. 

Vamos começar atribuindo um modificador de desencapsulamento de UVW ao modelo, conforme mostrado na captura de tela a seguir. 

- No editor de propriedades de objetos selecionado, escolha a lista de modificadores. Na lista suspensa exibida, role para baixo e selecione **Desencapsular UVW**. Essa ação aplica um modificador de desencapsulamento de UVW ao ativo.
![Captura de tela que mostra como selecionar Desencapsular UVW.](media/3dsmax/unwrap-modifier.jpg)

  O canal do mapa é definido como 1. Normalmente, você faz o desencapsulamento principal no canal 1 do mapa. Neste caso, o objeto foi desencapsulado sem nenhuma coordenada de textura sobreposta (UV).
![Captura de tela que mostra coordenadas de textura desencapsuladas (UVW).](media/3dsmax/unwrapped-uvw.jpg)

A próxima etapa será criar um segundo canal de mapa UV.

1. Feche o Editor de UV caso ele esteja aberto. Na seção **Canal** do menu **Editar UVs**, altere o número do canal para **2**. O canal 2 do mapa é o canal esperado para mapas AO. 

1. Na caixa de diálogo **Aviso de Alteração de Canal**, você pode **Mover** o UV existente no canal 1 para o novo canal 2 ou **Abandonar** os UVs existentes, o que criará um desencapsulamento de UV automaticamente. Só selecione **Abandonar** se você pretende criar um desencapsulamento de UV para o mapa de AO diferente dos UVs no canal 1 do mapa. (Por exemplo, se você quiser usar texturas de blocos no canal 1.) Neste tutorial, moveremos os UVs do canal 1 para o canal 2, pois não precisamos editar o novo canal de UV.

   >[!NOTE]
   >Mesmo que você tenha copiado (movido) o desencapsulamento de UV do canal 1 do mapa para o canal 2, você pode fazer as edições necessárias nos novos UVs do canal sem afetar o canal do mapa original.

   ![Captura de tela que mostra o Aviso de Alteração de Canal.](media/3dsmax/channel-change.jpg)

Agora que criamos o canal do mapa, podemos voltar ao Material Físico no Editor de Material e começar a adicionar nossas texturas a ele. Primeiro, adicionaremos o mapa de AO porque há outra etapa para permitir que ele funcione corretamente. Depois que o mapa de AO for conectado ao material, precisaremos configurá-lo para usar o canal 2 do mapa.

Conforme observado anteriormente, não há slot dedicado para mapas de AO no Material Físico do 3ds Max. Em vez disso, aplicaremos o mapa de AO ao slot de **Aspereza Difusa**.

1. Na lista **Mapas Genéricos** do Material Físico,selecione o slot **Sem Mapa** ao lado de **Aspereza Difusa** e carregue o mapa de AO.

1. Nas propriedades de texturas de AO, o canal do mapa é definido como **1** por padrão. Altere esse valor para **2**. Essa ação conclui as etapas necessárias para adicionar o mapa de AO.

   >[!IMPORTANT]
   >Essa é uma etapa importante, especialmente se os UVs do canal 2 forem diferentes daqueles do canal 1, porque a AO não será mapeada corretamente se o canal incorreto for selecionado.

   ![Captura de tela que mostra como atribuir um mapa de AO.](media/3dsmax/assign-ao-map.jpg)

Agora, atribuiremos o mapa normal ao material de PBR. Essa ação difere um pouco do processo no Maya. O mapa normal não é aplicado diretamente ao slot do mapa de rugosidade. (Não há um slot de mapa normal no Material Físico do 3ds Max.) Em vez disso, você adiciona o mapa normal a um modificador de mapa normal, que é conectado ao slot de normal.

1. Na seção **Mapas Especiais** das propriedades do Material Físico (no Editor de Material), selecione o slot **Sem Mapa** ao lado de **Mapa de Rugosidade**. 

1. No **Navegador de Material/Mapa**, localize e selecione **Rugosidade Normal**. Essa ação adiciona um modificador de **Rugosidade Normal** ao material.

1. No modificador de **Rugosidade Normal**, selecione **Sem Mapa** ao lado de **Normal**. Localize e carregue seu mapa normal.

1. Verifique se o método está definido como **Tangente**. (Essa opção deve ser padrão.) Se necessário, alterne **Inverter Verde (Y)** .

   ![Captura de tela que mostra como selecionar uma Rugosidade Normal.](media/3dsmax/normal-bump.jpg)
   ![Captura de tela que mostra o carregamento do mapa normal.](media/3dsmax/load-normal-map.jpg)

Com o mapa normal atribuído corretamente, podemos atribuindo as texturas restantes para concluir a configuração do Material Físico. Esse processo é simples. Não há configurações especiais a serem consideradas. A seguinte captura de tela mostra o conjunto completo de texturas atribuídas ao material: 

![Captura de tela que mostra o conjunto completo de texturas atribuídas ao material.](media/3dsmax/all-textures.jpg)

Agora que os materiais de PBR foram criados e configurados, vale a pena pensar em instanciar objetos na cena. Crie instâncias de objetos similares na cena, como porcas, pregos, parafusos e arruelas. Todos os objetos que são os mesmos podem gerar uma economia significativa em termos de tamanho do arquivo. As instâncias de um objeto mestre podem ter escala, rotação e transformações próprias, para que possam ser colocadas conforme necessário em sua cena. No 3ds Max, o processo de criação de instância é simples.

1. No visor principal, selecione os objetos que você deseja exportar.

1. Segure a tecla **Shift** e arraste os ativos para cima usando a ferramenta para transformar (mover). 

1. Na caixa de diálogo **Opções de Clonagem**, defina **Objeto** como **Instância** e selecione **OK**:

   ![Captura de tela da caixa de diálogo Opções de Clonagem.](media/3dsmax/instance-object.jpg)

Essa ação cria uma instância do objeto que pode ser movida, girada ou dimensionada de maneira independente do pai dela e de outras instâncias desse pai.

>[!IMPORTANT]
>As alterações feitas na instância enquanto você está no modo de subobjeto são transmitidas para todas as instâncias do objeto. Portanto, se você estiver trabalhando com componentes de um objeto instanciado, como vértices e faces de polígono, verifique se você deseja que as alterações feitas afetem todas as instâncias. Lembre-se de que qualquer objeto instanciado pode ser transformado em um objeto exclusivo a qualquer momento. 

>[!TIP]
>Durante a criação de uma instância na sua cena, é uma boa ideia criar instâncias à medida que você avança. Substituir cópias por objetos em instâncias posteriormente é difícil. 

Um aspecto final a ser considerado antes de passarmos ao processo de exportação é como empacotar a cena/o ativo para compartilhamento. O ideal é que, se você passar o ativo para clientes ou membros da equipe, eles possam abrir e ver o ativo, pois ele deverá ser visto com o mínimo de confusão. Portanto, é importante manter os caminhos de textura dos ativos relativos ao arquivo da cena. Se os caminhos de textura do seu ativo apontarem para uma unidade local ou uma localização/um caminho absoluto, eles não serão carregados na cena se estiverem abertos em outro computador, mesmo que o arquivo .max esteja na mesma pasta das texturas. Tornar os caminhos de textura relativos no 3ds Max resolve esse problema e é bem simples.

1. Na barra de ferramentas principal, acesse **Arquivo** > **Referência** > **Alternância de Rastreamento de Ativos**. 

1. Na janela de Rastreamento de Ativos, você verá todas ou a maioria das texturas que aplicou aos materiais de PBR listados na coluna **Mapas/Sombreadores**.

1. Ao lado delas, na coluna **Caminho Completo**, você verá o caminho do local das texturas, mais provavelmente, o caminho de onde elas se encontram no computador local.

1. Por fim, você verá uma coluna chamada **Status**. Essa coluna indica se determinada textura foi localizada e aplicada à sua cena. Ela sinaliza a textura com um destes termos: **Ok**, **Encontrado** ou **Arquivo Ausente**. Os dois primeiros indicam que o arquivo foi encontrado e carregado. O último, obviamente, significa que o rastreador falhou ao localizar o arquivo.
 
   ![Captura de tela que mostra a janela de Rastreamento de Ativos.](media/3dsmax/texture-paths.jpg)

Observe que nem todas as texturas estão listadas na janela de Rastreamento de Ativos quando você a abre pela primeira vez. Não precisa se preocupar com isso. A execução do processo de localização de caminho uma ou duas vezes geralmente localiza todas as texturas da cena. O processo de localização de caminho é o seguinte: 

1. Na janela de Rastreamento de Ativos, pressione a tecla **Shift** e selecione a textura superior na lista **Mapas/Sombreadores**, continue segurando a tecla **Shift** e selecione a última textura da lista. Essa ação selecionará todas as texturas da lista. As texturas selecionadas são destacadas em azul. (Confira a seção anterior.)

1. Clique com o botão direito na seleção e escolha **Definir Caminho**.

1. Na caixa **Especificar Caminho do Ativo**, selecione o caminho local para suas texturas e substitua-o por `.\`.  Selecione **OK**. 

    A janela de Rastreamento de Ativos será atualizada conforme mostrado na captura de tela a seguir. Essa atualização pode demorar um pouco, dependendo de quantas texturas há na cena e do tamanho dela.
![Captura de tela que mostra a janela atualizada de Rastreamento de Ativos.](media/3dsmax/resolve-textures.jpg)

Observe que a coluna **Caminho Completo** agora está em branco. Isso significa que a cena não está mais procurando as texturas relevantes em um local específico (absoluto). Ela sempre as encontrará desde que o arquivo .max ou o arquivo FBX relacionado esteja na mesma pasta que as texturas. 

>[!NOTE]
>Talvez você precise repetir esse processo algumas vezes para localizar e resolver todas as texturas e os caminhos. Não precisa se preocupar com isso. Basta repetir o processo até que todos os ativos relevantes sejam contabilizados. Em alguns casos, não é possível encontrar alguns arquivos. Nesses casos, basta selecionar todos os ativos na lista e escolher **Remover Caminhos Ausentes**. (Confira a imagem anterior.)

## <a name="fbx-export"></a>Exportação do FBX

Agora que tornamos os caminhos de textura relativos, podemos passar para a exportação do FBX. Novamente, o processo é simples e pode ser feito de duas maneiras. 

>[!TIP]
>A menos que você queira exportar toda a cena, é uma boa ideia selecionar a exportação apenas dos ativos de que você precisa. Em cenas com uso intensivo de recursos, a exportação pode levar muito tempo.
>
>Se você usou modificadores como o Turbosmooth ou o Open SubDiv, recomendamos recolhê-los antes da exportação, porque eles podem causar problemas durante o processo. Lembre-se de salvar sua cena antes de recolhê-la. 

1. Na cena, selecione os ativos que você deseja exportar. Na barra de ferramentas principal, vá para **Arquivo** > **Exportar** > **Exportar Selecionados**.

1. Na caixa de diálogo **Selecionar o Arquivo a ser Exportado**, digite ou selecione um nome de arquivo de saída. Na lista **Salvar como tipo**, selecione **Autodesk (*.fbx)** . Essa ação abre a janela de Exportação do FBX.

  >[!IMPORTANT] 
  >Se você criou instâncias na cena, é importante selecionar **Preservar Instâncias** nas configurações de exportação do FBX. 

  ![Captura de tela que mostra como exportar para FBX.](media/3dsmax/fbx-export.jpg)

  Lembre-se de que há duas maneiras de exportar o arquivo. Se a intenção é compartilhar o FBX com seus arquivos de textura em uma pasta/diretório, as configurações mostradas na captura de tela a seguir devem funcionar bem. 

   Se você preferir não compartilhar grandes pastas/diretórios de texturas com o FBX, opte por inserir as texturas no FBX. Se você inserir as texturas, todo os ativos, inclusive as texturas, serão adicionados a um único FBX. Isso combina a exportação em um único ativo, mas, como resultado, o arquivo FBX será consideravelmente maior.

   >[!IMPORTANT]
   >Se o arquivo FBX resultante tiver mais de 2,4 GB, a versão mínima especificada nas configurações de exportação do FBX deverá ser a 2016 ou posterior. (Confira a seção anterior.) Versões mais recentes têm suporte a 64 bits e, portanto, dão suporte a arquivos maiores.

1. Se você quiser exportar a cena com texturas incluídas, na janela de Exportação do *FBX, selecione **Inserir Mídia**. 

1. Selecione o restante das suas configurações e **OK**:

    ![Captura de tela que mostra as configurações de exportação do FBX.](media/3dsmax/fbx-settings.jpg)


   Ao exportar para FBX enquanto usa um Material Físico, você provavelmente verá o seguinte aviso depois de selecionar **OK** na janela de Exportação do FBX: 

   ![Captura de tela que mostra o aviso de Falha na exportação do material.](media/3dsmax/export-warnings.jpg)

   Esse aviso indica que os materiais exportados podem não ser compatíveis com outros pacotes de software. Como o Material Físico é compatível com o Azure Remote Rendering, não é preciso se preocupar com esse aviso. 

1. Selecione **OK** para concluir o processo e fechar a janela.

## <a name="conclusion"></a>Conclusão

Em geral, esse tipo de material parece mais realista, pois se baseia na física da luz do mundo real. Ele cria um efeito de imersão adicional, em que a cena parece existir no mundo real.

## <a name="next-steps"></a>Próximas etapas

Agora você sabe como configurar materiais com iluminação avançada para objetos em uma cena. Você também sabe como exportar objetos para o formato FBX, que é compatível com o Azure Remote Rendering. A próxima etapa será converter o arquivo FBX e visualizá-lo no Azure Remote Rendering.

>[!div class="nextstepaction"]
>[Início Rápido: Converter um modelo para renderização](../../quickstarts\convert-model.md)
