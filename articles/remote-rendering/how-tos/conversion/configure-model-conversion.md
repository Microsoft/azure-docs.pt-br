---
title: Configurar a conversão de modelo
description: Descrição de todos os parâmetros da conversão de modelo
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: 1cb5312e164bac09930497c377f1590b6a77ca05
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92205312"
---
# <a name="configure-the-model-conversion"></a>Configurar a conversão de modelo

Esse capítulo documenta as opções para a conversão de modelo.

## <a name="settings-file"></a>Arquivo de configurações

Se um arquivo chamado `<modelName>.ConversionSettings.json` for encontrado no contêiner de entrada ao lado do modelo de entrada `<modelName>.<ext>` , ele será usado para fornecer configuração adicional para o processo de conversão de modelo.
Por exemplo, `box.ConversionSettings.json` seria usado ao converter `box.gltf` .

O conteúdo do arquivo deve satisfazer o esquema JSON a seguir:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "deduplicateMaterials" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        },
        "metadataKeys": {
            "type": "array",
            "items": {
                "type": "string"
            }
        }
    },
    "additionalProperties" : false
}
```

Um exemplo de arquivo `box.ConversionSettings.json` pode ser:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Parâmetros de geometria

* `scaling` – esse parâmetro dimensiona um modelo uniformemente. O dimensionamento pode ser usado para aumentar ou reduzir um modelo para, por exemplo, exibir um modelo de construção sobre uma mesa.
O dimensionamento também é importante quando um modelo é definido em unidades diferentes de metros, pois o mecanismo de renderização espera metros.
Por exemplo, caso um modelo seja definido em centímetros, a aplicação de uma escala de 0,01 deverá renderizar o modelo no tamanho correto.
Alguns formatos de dados de origem (por exemplo, .fbx) fornecem uma dica de dimensionamento de unidade; nesse caso, a conversão dimensiona implicitamente o modelo para unidades de metro. O dimensionamento implícito dado pelo formato de origem será aplicado na parte superior do parâmetro de dimensionamento.
O fator de dimensionamento final será aplicado aos vértices de geometria e às transformações locais dos nós do grafo de cena. O dimensionamento para a transformação da entidade raiz continua inalterado.

* `recenterToOrigin` – informa que um modelo deve ser convertido para que sua caixa delimitadora seja centralizada na origem.
Se um modelo de origem for inserido longe da origem, problemas de precisão de ponto flutuante poderão causar artefatos de renderização.
Centralizar o modelo pode ajudar nessa situação.

* `opaqueMaterialDefaultSidedness` – o mecanismo de renderização pressupõe que os materiais opacos têm dois lados.
Se essa suposição não for verdadeira de um modelo específico, esse parâmetro deverá ser definido como "SingleSided". Para obter mais informações, consulte [ :::no-loc text="single sided"::: renderização](../../overview/features/single-sided-rendering.md).

### <a name="material-de-duplication"></a>Eliminação de duplicação do material

* `deduplicateMaterials` – esse parâmetro habilita ou desabilita a eliminação de duplicação automática de materiais que compartilham as mesmas propriedades e texturas. A eliminação de duplicação ocorre depois de as substituições de material terem sido processadas. Ele é habilitado por padrão.

* Se, mesmo após a eliminação de duplicação, um modelo tiver mais de 65.535 materiais, o serviço tentará mesclar materiais com propriedades semelhantes. Como último recurso, todos os materiais que excederem o limite serão substituídos por um material de erro vermelho.

![Imagem mostra dois cubos de 68.921 triângulos coloridos.](media/mat-dedup.png?raw=true)

Dois cubos de 68.921 triângulos coloridos. Esquerda: antes da eliminação de duplicação com 68.921 materiais de cor. Direita: após a eliminação de duplicação com 64.000 materiais de cor. O limite é de 65.535 materiais. (Consulte [limites](../../reference/limits.md).)

### <a name="color-space-parameters"></a>Parâmetros de espaço de cores

O mecanismo de renderização espera que os valores de cores estejam em um espaço linear.
Caso um modelo seja definido por meio do uso do espaço gama, essas opções deverão ser definidas como true.

* `gammaToLinearMaterial` – converte cores de material do espaço gama para um espaço linear.
* `gammaToLinearVertex` -Converter :::no-loc text="vertex"::: cores de espaço gama em espaço linear

> [!NOTE]
> Em arquivos FBX, essas configurações são definidas como `true` por padrão. Em todos os outros tipos de arquivo, o padrão é `false`.

### <a name="scene-parameters"></a>Parâmetros de cena

* `sceneGraphMode` – define como o grafo de cena no arquivo de origem é convertido:
  * `dynamic` (padrão): todos os objetos no arquivo são expostos como [entidades](../../concepts/entities.md) na API e podem ser transformados de modo independente. A hierarquia de nó no runtime é idêntica à estrutura no arquivo de origem.
  * `static`: Todos os objetos são expostos na API, mas não podem ser transformados de forma independente.
  * `none`: o grafo de cena é reunido em um objeto.

Cada modo tem um desempenho de runtime diferente. No modo `dynamic`, o custo de desempenho é dimensionado linearmente com o número de [entidades](../../concepts/entities.md) no grafo, mesmo quando nenhuma parte for movida. Use `dynamic` o modo somente quando for necessário para mover partes individualmente, por exemplo, para uma animação de "exibição de explosão".

O modo `static` exporta o grafo de cena completo, mas partes dentro desse grafo se transformam constantemente em relação à sua parte raiz. O nó raiz do objeto, no entanto, ainda pode ser movido, girado ou dimensionado sem prejudicar o desempenho de modo significativo. Além disso, [consultas espaciais](../../overview/features/spatial-queries.md) retornarão partes individuais, e cada parte poderá ser modificada por meio de [substituições de estado](../../overview/features/override-hierarchical-state.md). Com esse modo, a sobrecarga do runtime por objeto é insignificante. Ela é ideal para grandes cenas, nas quais você ainda precisa de uma inspeção por objeto, mas nenhuma transformação por objeto é alterada.

O modo `none` tem a menor sobrecarga do runtime, além de tempos de carregamento um pouco melhores. Não é possível fazer a inspeção ou a transformação de objetos únicos nesse modo. Os casos de uso são, por exemplo, modelos de fotogrametria que já não tenham um grafo de cena significativo.

> [!TIP]
> Diversos aplicativos carregarão vários modelos. Você deve otimizar os parâmetros de conversão de cada modelo conforme ele será usado. Por exemplo, caso queira exibir o modelo de um carro para que o usuário separar e inspecionar em detalhes, você precisará convertê-lo com o modo `dynamic`. No entanto, se você também quiser inserir o carro em um ambiente de apresentação, esse modelo poderá ser convertido com o `sceneGraphMode` definido como `static` ou até mesmo `none`.

### <a name="physics-parameters"></a>Parâmetros físicos

* `generateCollisionMesh` – caso precise de suporte para [consultas espaciais](../../overview/features/spatial-queries.md) em um modelo, essa opção deverá ser habilitada. Na pior das hipóteses, a criação de uma malha de colisão poderá dobrar o tempo de conversão. Modelos com malhas de colisão demoram mais para serem carregados e, ao usar um `dynamic` grafo de cena, eles também apresentam uma sobrecarga de desempenho de runtime maior. Para obter um desempenho ideal geral, você deve desabilitar essa opção em todos os modelos nos quais consultas espaciais não sejam necessárias.

### <a name="unlit-materials"></a>Materiais não iluminados

* `unlitMaterials` – por padrão, a conversão preferirá criar [materiais de PBR](../../overview/features/pbr-materials.md). Essa opção informa o conversor para tratar todos os materiais como [materiais de cor](../../overview/features/color-materials.md). Caso tenha dados que já incorporem a iluminação, como modelos criados por meio de fotogrametria, essa opção permitirá que você aplique rapidamente a conversão correta para todos os materiais, sem a necessidade de [substituir cada material](override-materials.md) individualmente.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Conversão a partir de formatos FBX mais antigos, com um modelo de material Phong

* `fbxAssumeMetallic` – versões mais antigas do formato FBX definem seus materiais usando um modelo de material Phong. O processo de conversão deve inferir como esses materiais são mapeados para o [modelo de PRB](../../overview/features/pbr-materials.md) do renderizador. Normalmente, isso funciona bem, mas é possível que surja uma ambiguidade quando um material não tiver texturas, valores especulares altos e uma cor de albedo não cinza. Nessa circunstância, a conversão deve escolher entre priorizar os valores especulares altos, definir um material metálico altamente reflexivo, em que a cor albedo é descartada, ou priorizar a cor albedo, definindo algo como um plástico colorido brilhante. Por padrão, o processo de conversão pressupõe que valores altamente especulares sugiram um material metálico nos casos em que a ambiguidade se aplica. É possível definir esse parâmetro como `false` para mudar para o oposto.

### <a name="coordinate-system-overriding"></a>Sobreposição do sistema de coordenadas

* `axis` – para substituir os vetores de unidade do sistema de coordenadas. Os valores padrão são `["+x", "+y", "+z"]`. Teoricamente, o formato FBX tem um cabeçalho no qual esses vetores são definidos, e a conversão usa essas informações para transformar a cena. O formato glTF também define um sistema de coordenadas fixo. Na prática, alguns ativos têm informações incorretas em seus cabeçalhos ou foram salvos com uma convenção de sistema de coordenadas diferente. Essa opção permite que você substitua o sistema de coordenadas como forma de compensação. Por exemplo: `"axis" : ["+x", "+z", "-y"]` trocará o eixo Z e o eixo Y e manterá a destreza do sistema de coordenadas ao inverter a direção do eixo Y.

### <a name="node-meta-data"></a>Metadados do nó

* `metadataKeys` -Permite especificar chaves de propriedades de metadados de nó que você deseja manter no resultado da conversão. Você pode especificar chaves exatas ou chaves curinga. As chaves curinga são do formato "ABC *" e correspondem a qualquer chave que comece com "ABC". Os tipos de valor de metadados com suporte são `bool` ,, `int` `float` e `string` .

    Para arquivos GLTF, esses dados são provenientes do [objeto extras em nós](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#nodeextras). Para Arquivos FBX, esses dados são provenientes dos `Properties70` dados em `Model nodes` . Consulte a documentação da sua ferramenta de ativo 3D para obter mais detalhes.

### <a name="no-loc-textvertex-format"></a>:::no-loc text="Vertex"::: ao

É possível ajustar o :::no-loc text="vertex"::: formato para uma malha, a fim de trocar a precisão pela economia de memória. Um volume de memória menor permite que você carregue modelos maiores ou obtenha um melhor desempenho. Porém, dependendo dos seus dados, o formato incorreto pode afetar significativamente a qualidade da renderização.

> [!CAUTION]
> A alteração do :::no-loc text="vertex"::: formato deve ser um último recurso quando os modelos não se ajustam mais à memória ou ao otimizar o melhor desempenho possível. As alterações podem introduzir artefatos de renderização facilmente, tanto os óbvios quanto os sutis. A menos que saiba o que deve procurar, você não deve alterar o padrão.

Os seguintes ajustes são possíveis:

* Fluxos de dados específicos podem ser incluídos ou excluídos de maneira explícita.
* É possível diminuir a precisão dos fluxos de dados para reduzir o volume de memória.

A seção `vertex` a seguir do arquivo `.json` é opcional. Para cada parte que não seja especificada explicitamente, o serviço de conversão volta à sua configuração padrão.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

Ao forçar um componente a `NONE`, garante-se que a malha de saída não terá o respectivo fluxo.

#### <a name="component-formats-per-no-loc-textvertex-stream"></a>Formatos de componente por :::no-loc text="vertex"::: fluxo

Os formatos a seguir são permitidos para os respectivos componentes:

| componente :::no-loc text="Vertex"::: | Formatos com suporte (negrito = padrão) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, NONE |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED, **NONE**|
|normal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|tangent| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, NONE |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, NONE |

#### <a name="supported-component-formats"></a>Formatos de componentes com suporte

Os volumes de memória dos formatos são os seguintes:

| Formatar | Descrição | Bytes por :::no-loc text="vertex"::: |
|:-------|:------------|:---------------|
|32_32_FLOAT|Precisão do ponto flutuante completo de dois componentes|8
|16_16_FLOAT|Precisão de meio ponto flutuante de dois componentes|4
|32_32_32_FLOAT|Precisão de ponto flutuante completo de três componentes|12
|16_16_16_16_FLOAT|Precisão de meio ponto flutuante de quatro componentes|8
|8_8_8_8_UNSIGNED_NORMALIZED|Byte de quatro componentes, normalizado para o intervalo de `[0; 1]`|4
|8_8_8_8_SIGNED_NORMALIZED|Byte de quatro componentes, normalizado para o intervalo de `[-1; 1]`|4

#### <a name="best-practices-for-component-format-changes"></a>Melhores práticas para alterações de formato de componente

* `position`: raramente a precisão reduzida é suficiente. **16_16_16_16_FLOAT** introduz artefatos de quantificação perceptíveis, mesmo em modelos pequenos.
* `normal`, `tangent`, `binormal`: normalmente, esses valores são alterados em conjunto. A menos que haja artefatos de iluminação perceptíveis que sejam resultado da quantificação normal, não há motivos para aumentar sua precisão. Em alguns casos, porém, esses componentes podem ser definidos como **NONE**:
  * `normal`, `tangent` e `binormal` só são necessários quando pelo menos um material no modelo devesse estar iluminado. No ARR, é esse o caso quando um [material de PBR](../../overview/features/pbr-materials.md) é usado no modelo a qualquer momento.
  * `tangent` e `binormal` só serão necessários quando qualquer um dos materiais iluminados usar uma textura de mapa normal.
* `texcoord0`, `texcoord1`: as coordenadas de textura podem usar a precisão reduzida (**16_16_FLOAT**) quando seus valores permanecerem no intervalo de `[0; 1]` e quando as texturas abordadas tiverem um tamanho máximo de 2.048 x 2.048 pixels. Caso esses limites sejam excedidos, a qualidade do mapeamento de textura será prejudicada.

#### <a name="example"></a>Exemplo

Suponha que você tenha um modelo de fotogrametria, o qual tenha iluminação nas texturas. Tudo o que é necessário para renderizar o modelo são :::no-loc text="vertex"::: posições e coordenadas de textura.

Por padrão, o conversor deve pressupor que você talvez queira usar os materiais de PBR em um modelo em algum momento, assim, ele gerará os dados `normal`, `tangent` e `binormal` para você. Consequentemente, a memória usada por vértice será de `position` (12 bytes) + `texcoord0` (8 bytes) + `normal` (4 bytes) + `tangent` (4 bytes) + `binormal` (4 bytes) = 32 bytes. Modelos maiores desse tipo podem facilmente ter muitos milhões de :::no-loc text="vertices"::: resultados resultantes de modelos que podem ocupar vários gigabytes de memória. Essas grandes quantidades de dados afetarão o desempenho, podendo até levar à falta de memória.

Sabendo que você nunca precisa de iluminação dinâmica no modelo e sabendo que todas as coordenadas de textura estão no `[0; 1]` intervalo, você pode definir `normal` , `tangent` e `binormal` para `NONE` e `texcoord0` a metade de precisão ( `16_16_FLOAT` ), resultando em apenas 16 bytes por :::no-loc text="vertex"::: . Cortar os dados de malha pela metade permite que você carregue modelos maiores e potencialmente melhore o desempenho.

## <a name="memory-optimizations"></a>Otimizações de memória

O consumo de memória do conteúdo carregado pode se tornar um afunilamento no sistema de renderização. Se a carga de memória ficar muito grande, ela poderá comprometer o desempenho de renderização ou fazer com que o modelo não seja totalmente carregado. Este parágrafo discute algumas estratégias importantes para reduzir o volume de memória.

### <a name="instancing"></a>Instanciação

A instanciação é um conceito em que as malhas são reutilizadas para partes com transformações espaciais distintas, em oposição a cada parte que faz referência a sua própria geometria exclusiva. A instanciação tem um impacto significativo na superfície da memória.
Exemplos de casos de uso para instanciação são os parafusos em um modelo de mecanismo ou cadeiras em um modelo de arquitetura.

> [!NOTE]
> A instanciação pode melhorar o consumo de memória (e, portanto, o tempo de carregamento) significativamente, no entanto, as melhorias no lado do desempenho de renderização são insignificantes.

O serviço de conversão respeita a instanciação se as partes estiverem marcadas adequadamente no arquivo de origem. No entanto, a conversão não executa uma análise profunda adicional de dados de malha para identificar partes reutilizáveis. Portanto, a ferramenta de criação de conteúdo e seu pipeline de exportação são os critérios decisivos para a configuração de instanciação adequada.

Uma maneira simples de testar se as informações de instanciação são preservadas durante a conversão é dar uma olhada nas [Estatísticas de saída](get-information.md#example-info-file), especificamente o `numMeshPartsInstanced` membro. Se o valor de `numMeshPartsInstanced` for maior que zero, ele indica que as malhas são compartilhadas entre instâncias.

#### <a name="example-instancing-setup-in-3ds-max"></a>Exemplo: configuração de instanciação em 3ds Max

O [Autodesk 3ds Max](https://www.autodesk.de/products/3ds-max) tem modos de clonagem de objeto distintos chamados **`Copy`** , **`Instance`** , e **`Reference`** que se comportam de forma diferente com relação à instanciação no arquivo exportado `.fbx` .

![Clonagem em 3ds Max](./media/3dsmax-clone-object.png)

* **`Copy`** : Nesse modo, a malha é clonada, portanto, nenhuma instanciação é usada ( `numMeshPartsInstanced` = 0).
* **`Instance`** : Os dois objetos compartilham a mesma malha, portanto, a instanciação é usada ( `numMeshPartsInstanced` = 1).
* **`Reference`** : Modificadores distintos podem ser aplicados às geometrias, portanto, o exportador escolhe uma abordagem conservadora e não usa instanciação ( `numMeshPartsInstanced` = 0).


### <a name="depth-based-composition-mode"></a>Modo de composição com base em profundidade

Se a memória for uma preocupação, configure o renderizador com o [modo de composição baseado em profundidade](../../concepts/rendering-modes.md#depthbasedcomposition-mode). Nesse modo, a carga de GPU é distribuída entre várias GPUs.

### <a name="decrease-vertex-size"></a>Diminuir tamanho do vértice

Conforme discutido na seção [práticas recomendadas para alterações de formato de componente](configure-model-conversion.md#best-practices-for-component-format-changes) , ajustar o formato de vértice pode diminuir o volume de memória. No entanto, essa opção deve ser o último recurso.

### <a name="texture-sizes"></a>Tamanhos de textura

Dependendo do tipo de cenário, a quantidade de dados de textura pode exceder a memória usada para dados de malha. Os modelos Photogrammetry são candidatos.
A configuração de conversão não fornece uma maneira de reduzir verticalmente as texturas automaticamente. Se necessário, o dimensionamento de textura deve ser feito como uma etapa de pré-processamento do lado do cliente. No entanto, a etapa de conversão escolhe um [formato de compactação de textura](/windows/win32/direct3d11/texture-block-compression-in-direct3d-11)adequado:

* `BC1` para texturas de cores opacas
* `BC7` para texturas de cores de origem com canal alfa

Como `BC7` o formato tem duas vezes o espaço de memória comparado a `BC1` , é importante certificar-se de que as texturas de entrada não forneçam um canal alfa desnecessariamente.

## <a name="typical-use-cases"></a>Casos de uso típicos

Buscar boas configurações de importação para um determinado caso de uso pode ser um processo entediante. Por outro lado, as configurações de conversão podem ter um impacto significativo no desempenho do runtime.

Há certas classes de casos de uso qualificadas para otimizações específicas. A seguir, são apresentados alguns exemplos.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Caso de uso: visualização arquitetônica/grandes mapas de outdoor

* Esses tipos de cenas tendem a ser estáticos, o que significa que não precisam de partes móveis. De modo semelhante, `sceneGraphMode` pode ser definido como `static` ou até mesmo `none`, o que melhora o desempenho do runtime. Com o modo de `static`, o nó raiz da cena ainda pode ser movido, girado e dimensionado para, por exemplo, alternar dinamicamente entre a escala de 1:1 (para a exibição em primeira pessoa) e uma exibição superior da tabela.

* Quando precisar mover partes, isso normalmente também significa que você precisará de suporte para raycasts ou outras [consultas espaciais](../../overview/features/spatial-queries.md), de modo que você possa começar escolhendo essas partes. Por outro lado, caso não pretenda mover algo, é muito provável que você também não precise disso para participar de consultas espaciais e, portanto, pode desativar o sinalizador `generateCollisionMesh`. Essa opção tem um impacto significativo nos tempos de conversão, de carregamento e também nos custos de atualização por quadro do runtime.

* Caso o aplicativo não use [planos de corte](../../overview/features/cut-planes.md), o sinalizador `opaqueMaterialDefaultSidedness` deverá ser desativado. O ganho de desempenho geralmente é de 20 a 30%. Os planos de recorte ainda podem ser usados, mas não haverá faces traseiras ao examinar as partes internas de objetos, o que parece um contador intuitivo. Para obter mais informações, consulte [ :::no-loc text="single sided"::: renderização](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Caso de uso: Modelos de fotogrametria

Ao renderizar modelos de fotogrametria, normalmente não haverá necessidade de um grafo de cena, portanto, você pode definir o `sceneGraphMode` como `none`. No entanto, como esses modelos raramente contêm um grafo de cena complexo, o impacto dessa opção deve ser insignificante.

Como a iluminação já está presente nas texturas, nenhuma iluminação dinâmica é necessária. Portanto:

* Defina o sinalizador `unlitMaterials` como `true` para transformar todos os materiais em [materiais de cores](../../overview/features/color-materials.md) não iluminados.
* Remova dados desnecessários do formato de vértice. Veja o [exemplo](#example) acima.

### <a name="use-case-visualization-of-compact-machines-etc"></a>Caso de uso: visualização de máquinas compactas etc.

Nesses casos de uso, os modelos geralmente apresentam muitos detalhes em um volume pequeno. O renderizador está altamente otimizado para lidar com esses casos. Contudo, a maioria das otimizações mencionadas no caso de uso anterior não se aplicam aqui:

* As partes individuais devem ser selecionáveis e móveis, portanto, `sceneGraphMode` deve ser deixado como `dynamic`.
* Os ray casts normalmente são parte integrante do aplicativo, de modo que as malhas de colisão devem ser geradas.
* Os planos de corte têm uma aparência melhor quando o sinalizador `opaqueMaterialDefaultSidedness` está habilitado.

## <a name="deprecated-features"></a>Recursos preteridos

O fornecimento de configurações usando o nome de arquivo não específico ao modelo `conversionSettings.json` ainda é suportado, mas preterido.
Em vez disso, use o nome de arquivo específico do modelo `<modelName>.ConversionSettings.json` .

O uso de uma `material-override` configuração para identificar um [arquivo de substituição de material](override-materials.md) no arquivo de configurações de conversão ainda é suportado, mas preterido. Em vez disso, use o nome de arquivo específico do modelo `<modelName>.MaterialOverrides.json` .

## <a name="next-steps"></a>Próximas etapas

* [Conversão de modelo](model-conversion.md)
* [Materiais de cores](../../overview/features/color-materials.md)
* [Materiais de PBR](../../overview/features/pbr-materials.md)
* [Substituir materiais durante a conversão de modelo](override-materials.md)