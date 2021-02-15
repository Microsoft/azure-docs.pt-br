---
title: Materiais de PBR
description: Descreve o tipo de material do PBR.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: e9908c106e57801cb1b7def8b3353a983cc97de0
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591932"
---
# <a name="pbr-materials"></a>Materiais de PBR

Os *materiais de PBR* são um dos [tipos de material](../../concepts/materials.md) com suporte na renderização remota do Azure. Eles são usados para [malhas](../../concepts/meshes.md) que devem receber iluminação realista.

PBR significa **P** hysically **B** ased **R** endering e significa que o material descreve as propriedades visuais de uma superfície de uma maneira realmente plausível, de modo que os resultados realistas sejam possíveis em todas as condições de iluminação. A maioria dos mecanismos de jogo e ferramentas de criação de conteúdo modernos dão suporte aos materiais do PBR porque eles são considerados a melhor aproximação de cenários do mundo real para renderização em tempo real.

![Modelo de exemplo de capacete glTF renderizado por ARR](media/helmet.png)

No entanto, os materiais de PBR não são uma solução universal. Há materiais que refletem a cor de forma diferente, dependendo do ângulo de exibição. Por exemplo, algumas malhas ou pinturas de carros. Esses tipos de materiais não são tratados pelo modelo PBR padrão e atualmente não têm suporte da renderização remota do Azure. Isso inclui extensões de PBR, como o *filme fino* (superfícies de várias camadas) e *a rerevestimento clara* (para pinturas de carros).

## <a name="common-material-properties"></a>Propriedades do material comum

Essas propriedades são comuns a todos os materiais:

* **albedoColor:** Essa cor é multiplicada por outras cores, como *albedoMap* ou *:::no-loc text="vertex "::: cores*. Se a *transparência* estiver habilitada em um material, o canal alfa será usado para ajustar a opacidade, com `1` significado totalmente opaco e `0` significado totalmente transparente. O padrão é branco.

  > [!NOTE]
  > Quando um material de PBR é totalmente transparente, como um pedaço de vidro perfeitamente limpo, ele ainda reflete o ambiente. Pontos brilhantes, como o sol, ainda estão visíveis na reflexão. Isso é diferente para [materiais de cores](color-materials.md).

* **albedoMap:** Uma [textura 2D](../../concepts/textures.md) para valores de albedo por pixel.

* **alphaClipEnabled** e **AlphaClipThreshold:** se *alphaClipEnabled* for true, todos os pixels em que o valor de alfa albedo for menor que *alphaClipThreshold* não serão desenhados. O recorte alfa pode ser usado mesmo sem habilitar a transparência e é muito mais rápido de renderizar. No entanto, os materiais recortados alfa ainda são mais lentos do que os materiais totalmente opacos. Por padrão, o recorte alfa é desabilitado.

* **textureCoordinateScale** e **textureCoordinateOffset:** a escala é multiplicada nas coordenadas de textura UV, o deslocamento é adicionado a ela. Pode ser usado para alongar e deslocar as texturas. A escala padrão é (1, 1) e o deslocamento é (0, 0).

* **useVertexColor:** Se a malha contiver :::no-loc text="vertex"::: cores e essa opção estiver habilitada, a cor das malhas :::no-loc text="vertex"::: será multiplicada em *albedoColor* e *albedoMap*. Por padrão, o *useVertexColor* está desabilitado.

* **isDoubleSided:** Se Double-sidedness for definido como true, os triângulos com esse material serão renderizados mesmo se a câmera estiver observando suas faces de apoio. Para a iluminação de materiais do PBR também é computada corretamente para faces traseiras. Por padrão, essa opção está desabilitada. Consulte também [ :::no-loc text="Single-sided"::: renderização](single-sided-rendering.md).

* **TransparencyWritesDepth:** Se o sinalizador TransparencyWritesDepth estiver definido no material e o material for transparente, os objetos que usam esse material também contribuirão para o buffer de profundidade final. Consulte o sinalizador de material do PBR *transparente* na próxima seção. Habilitar esse recurso é recomendado se o caso de uso precisar de uma [Reprojeção de estágio tardia](late-stage-reprojection.md) mais plausível de cenas totalmente transparentes. Para cenas mistas/transparentes misturadas, essa configuração pode introduzir comportamento de Reprojeção inplausível ou artefatos de Reprojeção. Por esse motivo, a configuração padrão e recomendada para o caso de uso geral é desabilitar esse sinalizador. Os valores de profundidade gravados são obtidos da camada de profundidade por pixel do objeto mais próximo da câmera.

* **FresnelEffect:** Esse sinalizador de material habilita o [efeito Fresnel](../../overview/features/fresnel-effect.md) aditivo no respectivo material. A aparência do efeito é regida pelos outros parâmetros de Fresnel explicados a seguir. 

* **FresnelEffectColor:** A cor Fresnel usada para este material. Somente importante quando o bit de efeito Fresnel tiver sido definido neste material (veja acima). Essa propriedade controla a cor base do brilho Fresnel (consulte [Fresnel Effect](../../overview/features/fresnel-effect.md) para obter uma explicação completa). No momento, apenas os valores de canal RGB são importantes e o valor alfa será ignorado.

* **FresnelEffectExponent:** O expoente Fresnel usado para este material. Somente importante quando o bit de efeito Fresnel tiver sido definido neste material (veja acima). Essa propriedade controla a disseminação do Fresnel brilhar. O valor mínimo 0, 1 causa uma propagação em todo o objeto. O valor máximo 10,0 restringe o brilho para apenas as bordas mais gracing visíveis.

## <a name="pbr-material-properties"></a>Propriedades do material PBR

A ideia principal da renderização com base fisicamente é usar as propriedades *BaseColor*, *metal* e *de propriedade* para emular uma ampla gama de materiais do mundo real. Uma descrição detalhada do PBR está além do escopo deste artigo. Para obter mais informações sobre o PBR, consulte [outras fontes](http://www.pbr-book.org). As propriedades a seguir são específicas para os materiais do PBR:

* **baseColor:** Em materiais de PBR, a *cor albedo* é conhecida como a *cor base*. Na renderização remota do Azure, a propriedade de *cor albedo* já está presente nas propriedades de material comuns, portanto, não há nenhuma propriedade de cor de base adicional.

* **áspero** e **roughnessMap:** a irlisação define quão aproximado ou suave a superfície é. As superfícies aproximadas dispersão da luz em mais direções do que as superfícies suaves, o que torna os reflexos desfocados em vez de nítido. O intervalo de valores é de `0.0` a `1.0` . Quando `roughness` for igual a `0.0` , os reflexos serão nítidos. Quando `roughness` for igual a `0.5` , os reflexos ficarão desfocados.

  Se um valor de áspero e um mapa de esboço forem fornecidos, o valor final será o produto dos dois.

* **metal** e **metalnessMap:** na física, essa propriedade corresponde a se uma superfície é conduzida ou interelétrica. Os materiais de condução têm diferentes propriedades reflexivas e tendem a ser refletidas sem nenhuma cor albedo. Em materiais de PBR, essa propriedade afeta o quanto uma superfície reflete o ambiente ao redor. Os valores variam de `0.0` a `1.0` . Quando a metal é `0.0` , a cor do albedo é totalmente visível e o material parece plástico ou Ceramics. Quando a metal é `0.5` , parece metal pintado. Quando a metal é `1.0` , a superfície perde quase completamente sua cor albedo e reflete apenas os arredores. Por exemplo, se `metalness` for `1.0` e `roughness` for `0.0` , uma superfície será como um espelho do mundo real.

  Se um valor de metal e um mapa de metal forem fornecidos, o valor final será o produto dos dois.

  ![As várias são renderizadas com valores de metal e de irregularidades diferentes](./media/metalness-roughness.png)

  Na imagem acima, a esfera no canto inferior direito se parece com um material de metal real, a parte inferior esquerda é como Ceramic ou plástico. A cor albedo também é alterada de acordo com as propriedades físicas. Com o aumento da nitidez, o material perde a necessidade de reflexo.

* **normalMap:** Para simular detalhes refinados, um [mapa normal](https://en.wikipedia.org/wiki/Normal_mapping) pode ser fornecido.

* **occlusionMap** e **aoScale:** [ambiente oclusão](https://en.wikipedia.org/wiki/Ambient_occlusion) faz com que os objetos com crevices pareçam mais realistas adicionando sombras a obstruído áreas. Oclusão valor intervalo de `0.0` a `1.0` , onde `0.0` significa a escuridão (obstruído) e `1.0` significa que não há occlusions. Se uma textura 2D for fornecida como um mapa oclusão, o efeito será habilitado e *aoScale* agirá como um multiplicador.

  ![Um objeto renderizado com e sem oclusão ambiente](./media/boom-box-ao2.gif)

* **transparente:** Para materiais de PBR, há apenas uma configuração de transparência: ela está habilitada ou não. A opacidade é definida pelo canal alfa da cor albedo. Quando habilitado, um pipeline de renderização mais complexo é invocado para desenhar superfícies semitransparentes. A renderização remota do Azure implementa a OIT ( [transparência de ordem](https://en.wikipedia.org/wiki/Order-independent_transparency) verdadeira).

  A geometria transparente é cara para renderizar. Se você precisar apenas de buracos em uma superfície, por exemplo, para as folhas de uma árvore, será melhor usar o recorte alfa em vez disso.

  ![As esferas renderizadas com zero para aviso de transparência total ](./media/transparency.png) na imagem acima, como a esfera mais à direita é totalmente transparente, mas a reflexão ainda está visível.

  > [!IMPORTANT]
  > Se algum material deve ser alternado de opaco para transparente no tempo de execução, o renderizador deve usar o [modo de renderização](../../concepts/rendering-modes.md) *TileBasedComposition* . Essa limitação não se aplica aos materiais convertidos como materiais transparentes para começar.

## <a name="technical-details"></a>Detalhes técnicos

A renderização remota do Azure usa o Cook-Torrance micro-faceta BRDF com GGX NDF, Schlick Fresnel e um termo de visibilidade correlacionado de GGX Smith com um termo difuso Lambert. Esse modelo é o padrão do setor de fato no momento. Para obter detalhes mais detalhados, consulte este artigo: [processamento com base fisicamente em Torrance de cookie](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Uma alternativa para o modelo de PBR de realce de *metal* usado na renderização remota do Azure é o modelo de PBR *de glossários de especulação* . Esse modelo pode representar uma gama mais ampla de materiais. No entanto, ele é mais caro e geralmente não funciona bem para casos em tempo real.
Nem sempre é possível converter de glossários de *especulação* para a *irregularidade* , pois há pares de valores *(difuso, especular)* que não podem ser convertidos em *(BaseColor, metalness)*. A conversão na outra direção é mais simples e mais precisa, já que todos os pares *(BaseColor, metal)* correspondem aos pares bem definidos *(difuso, especular)* .

## <a name="api-documentation"></a>Documentação da API

* [Classe C# PbrMaterial](/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RenderingConnection. creatematerial ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [Classe C++ PbrMaterial](/cpp/api/remote-rendering/pbrmaterial)
* [C++ RenderingConnection:: creatematerial ()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>Próximas etapas

* [Materiais de cores](color-materials.md)
* [Texturas](../../concepts/textures.md)
* [Malhas](../../concepts/meshes.md)