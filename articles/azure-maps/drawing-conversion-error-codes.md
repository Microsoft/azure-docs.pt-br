---
title: Erros e avisos de conversão de desenho do Azure Mapas
description: Saiba mais sobre os erros e avisos de conversão que você pode encontrar enquanto estiver usando o Serviço de conversão do Azure Mapas. Leia as recomendações sobre como resolver erros e avisos junto de alguns exemplos.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: cecc19f0984ce1801d50e5cbda73e98a01e2825b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906209"
---
# <a name="drawing-conversion-errors-and-warnings"></a>Erros e avisos de conversão de desenho

O [Serviço de conversão do Azure Mapas](/rest/api/maps/conversion) permite converter pacotes de desenho carregados em dados de mapa. Os pacotes de desenho devem aderir aos [Requisitos do pacote de desenho](drawing-requirements.md). Caso um ou mais requisitos não sejam atendidos, o Serviço de conversão retornará erros ou avisos. Este artigo lista os códigos de erro e de aviso de conversão com recomendações sobre como resolvê-los. Ele também apresenta alguns exemplos de desenhos que podem fazer com que o Serviço de conversão retorne esses códigos.

O Serviço de conversão terá êxito se mesmo que haja avisos de conversão. No entanto, é recomendável que você analise e resolva todos os avisos. Um aviso indica que parte da conversão foi ignorada ou corrigida automaticamente. Não resolver os avisos pode resultar em erros em processos futuros.

## <a name="general-warnings"></a>Avisos gerais

### <a name="geometrywarning"></a>**geometryWarning**

#### <a name="description-for-geometrywarning"></a>*Descrição do geometryWarning*

Um **geometryWarning** ocorrerá quando o desenho contiver uma entidade inválida. Uma entidade inválida é aquela que não está em conformidade com as restrições geométricas. Os exemplos de uma entidade inválida são um polígono de interseção automática ou uma polilinha não fechada em uma camada que dá suporte apenas a geometria fechada.

O serviço de conversão não consegue criar um recurso de mapa a partir de uma entidade inválida e, portanto, ignora-o.

#### <a name="examples-for-geometrywarning"></a>*Exemplos do geometryWarning*

* As duas imagens abaixo mostram exemplos de polígonos com autointerseção.

     ![Exemplo de um polígono com interseção automática, exemplo um.](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![Exemplo de um polígono de intersecção automática, exemplo dois.](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* Abaixo, há uma imagem que mostra uma polilinha não fechada. Suponha que a camada tenha suporte apenas a geometrias fechadas.

    ![Exemplo de uma polilinha não fechada](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>*Como corrigir o geometryWarning*

Inspecione o **geometryWarning** de cada entidade para verificar se ele segue restrições geométricas.

### <a name="unexpectedgeometryinlayer"></a>**unexpectedGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>*Descrição do unexpectedGeometryInLayer*

Um aviso de **unexpectedGeometryInLayer** ocorre quando o desenho contém uma geometria incompatível com o tipo de geometria esperado em uma determinada camada. Quando o Serviço de conversão apresentar um aviso **unexpectedGeometryInLayer**, ele ignorará essa geometria.

#### <a name="example-for-unexpectedgeometryinlayer"></a>*Exemplo de unexpectedGeometryInLayer*

A imagem abaixo mostra uma polilinha não fechada. Suponha que a camada tenha suporte apenas a geometrias fechadas.

![Exemplo de uma polilinha não fechada](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>*Como corrigir o unexpectedGeometryInLayer*

Inspecione cada aviso **unexpectedGeometryInLayer** e mova a geometria incompatível para uma camada compatível. Caso não seja compatível com nenhuma das outras camadas, ela deverá ser removida.

### <a name="unsupportedfeaturerepresentation"></a>**unsupportedFeatureRepresentation**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>*Descrição do unsupportedFeatureRepresentation*

O aviso de **unsupportedFeatureRepresentation** ocorre quando o desenho contém um tipo de entidade sem suporte.

#### <a name="example-for-unsupportedfeaturerepresentation"></a>*Exemplo de unsupportedFeatureRepresentation*

A imagem abaixo exibe um tipo de entidade sem suporte como um objeto de texto de várias linhas em uma camada de rótulo.
  
![Exemplo de um objeto de texto de várias linhas na camada de rótulo](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>*Como corrigir o unsupportedFeatureRepresentation*

Verifique se os arquivos DWG contêm apenas tipos de entidade com suporte. Os tipos com suporte são listados na [seção Requisitos de arquivos de desenho no artigo Requisitos de pacote de desenho](drawing-requirements.md#drawing-package-requirements).

### <a name="automaticrepairperformed"></a>**automaticRepairPerformed**

#### <a name="description-for-automaticrepairperformed"></a>*Descrição de automaticRepairPerformed*

O aviso de **automaticRepairPerformed** ocorre quando o Serviço de conversão repara a geometria inválida automaticamente.

#### <a name="examples-for-automaticrepairperformed"></a>*Exemplos de automaticRepairPerformed*

* A imagem a seguir mostra como o Serviço de conversão reparou um polígono de autointerseção em uma geometria válida.

    ![Exemplo de um polígono com autointerseção reparado](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* A imagem abaixo mostra como o Serviço de conversão ajustava o primeiro e o último vértice de uma polilinha não fechada para criar uma polilinha fechada, na qual o primeiro e o último vértice tinham menos de 1 mm de distância.  

    ![Exemplo de uma polilinha ajustada](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* A imagem abaixo mostra como, em uma camada que dá suporte apenas a polilinhas fechadas, o Serviço de conversão reparou várias polilinhas não fechadas. Para evitar descartar as polilinhas não fechadas, o serviço as combinou em uma única polilinha fechada.

    ![Exemplo de polilinhas não fechadas combinadas em uma única polilinha fechada](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>*Como corrigir o automaticRepairPerformed*

Para corrigir um aviso de **automaticRepairPerformed**, execute as seguintes ações:

1. Inspecione a geometria de cada aviso e o texto de aviso específico.
2. Determine se o reparo automatizado está correto.
3. Se o reparo estiver correto, continue. Caso contrário, vá até o arquivo de design e resolva o aviso manualmente.

>[!TIP]
>Para suprimir um aviso no futuro, faça alterações no desenho original para que ele corresponda ao desenho reparado.

## <a name="manifest-warnings"></a>Avisos de manifesto

### <a name="redundantattribution"></a>**redundantAttribution**

#### <a name="description-for-redundantattribution"></a>*Descrição do redundantAttribution*

O aviso de **redundantAttribution** ocorre quando o manifesto contém propriedades de objeto redundantes ou conflitantes.

#### <a name="examples-for-redundantattribution"></a>*Exemplos de redundantAttribution*

* O trecho de código JSON abaixo contém dois ou mais objetos `unitProperties` com o mesmo `name`.

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* No trecho de código JSON abaixo, dois ou mais objetos `zoneProperties` têm o mesmo `name`.

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>*Como corrigir o redundantAttribution*

Para corrigir um aviso **redundantAttribution*, remova as propriedades de objeto redundantes ou conflitantes.

### <a name="manifestwarning"></a>**manifestWarning**

#### <a name="description-for-manifestwarning"></a>*Descrição do manifestWarning*

Um **manifestWarning** ocorre quando o manifesto contém objetos unitProperties ou zoneProperties que não são usados durante a conversão.

#### <a name="examples-for-manifestwarning"></a>*Exemplos do manifestWarning*

* O manifesto contém um objeto `unitProperties` com um `unitName` que não tem nenhum rótulo correspondente em uma camada `unitLabel`.

* O manifesto contém um objeto `zoneProperties` com um `zoneName` que não tem nenhum rótulo correspondente em uma camada `zoneLabel`.

#### <a name="how-to-fix-manifestwarning"></a>*Como corrigir o manifestWarning*

Para corrigir um **manifestWarning**, remova o objeto `unitProperties` ou `zoneProperties` do manifesto ou adicione um rótulo de unidade/zona ao desenho para que o objeto de propriedades seja usado durante a conversão.

## <a name="wall-warnings"></a>Avisos de parede

### <a name="walloutsidelevel"></a>**wallOutsideLevel**

#### <a name="description-for-walloutsidelevel"></a>*Descrição do wallOutsideLevel*

O aviso de **wallOutsideLevel** ocorre quando o desenho contém uma geometria de parede fora dos limites de um contorno de nível.

#### <a name="example-for-walloutsidelevel"></a>*Exemplo de wallOutsideLevel*

* A imagem abaixo mostra uma parede interna, em vermelho, fora do limite de nível, em amarelo.

    ![Exemplo de parede interna fora do limite de nível](./media/drawing-conversion-error-codes/wall-outside-level.png)

* A imagem a seguir mostra uma parede externa, em vermelho, fora do limite de nível, em amarelo.

    ![Exemplo de parede externa fora do limite de nível](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>*Como corrigir o wallOutsideLevel*

Para corrigir um aviso de **wallOutsideLevel**, expanda a geometria do nível para incluir todas as paredes. Ou modifique os limites de parede para ajustá-los ao limite de nível.

## <a name="unit-warnings"></a>Avisos de unidade

### <a name="unitoutsidelevel"></a>**unitOutsideLevel**

#### <a name="description-for-unitoutsidelevel"></a>*Descrição do unitOutsideLevel*

Um aviso de **unitOutsideLevel** ocorre quando o desenho contém uma geometria de unidade fora dos limites do contorno de nível.

#### <a name="example-for-unitoutsidelevel"></a>*Exemplo de unitOutsideLevel*

 Na imagem a seguir, a geometria da unidade, em vermelho, excede os limites do limite de nível, em amarelo.

 ![Exemplo de unidade que excede o limite de nível](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>*Como corrigir o unitOutsideLevel*

Para corrigir um aviso de **unitOutsideLevel**, expanda o limite de nível para incluir todas as unidades. Ou modifique a geometria da unidade para ajustá-la ao limite de nível.

### <a name="partiallyoverlappingunit"></a>**partiallyOverlappingUnit**

#### <a name="description-for-partiallyoverlappingunit"></a>*Descrição do partiallyOverlappingUnit*

Um aviso de **partiallyOverlappingUnit** ocorre quando o desenho contém uma geometria de unidade parcialmente sobreposta a outra geometria de unidade. O Serviço de conversão descarta as unidades sobrepostas.

#### <a name="example-scenarios-partiallyoverlappingunit"></a>*Cenários de exemplo do partiallyOverlappingUnit*

Na imagem a seguir, a unidade de sobreposição está realçada em vermelho. `UNIT110` e `HALLWAY` são descartados.

![Exemplo de unidades sobrepostas](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>*Como corrigir o partiallyOverlappingUnit*

Para corrigir um aviso **partiallyOverlappingUnit**, redesenhe cada unidade parcialmente sobreposta para que ela não se sobreponha a nenhuma outra unidade.

## <a name="door-warnings"></a>Avisos de porta

### <a name="dooroutsidelevel"></a>**doorOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>*Descrição do doorOutsideLevel*

Um aviso de **doorOutsideLevel** ocorre quando o desenho contém uma geometria de porta fora dos limites da geometria de nível.

#### <a name="example-for-dooroutsidelevel"></a>*Exemplo de doorOutsideLevel*

Na imagem a seguir, a geometria da porta, realçada em vermelho, sobrepõe o limite de nível, em amarelo.

![Exemplo de uma porta sobreposta a um limite de nível](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>*Como corrigir o doorOutsideLevel*

Para corrigir um aviso de **doorOutsideLevel**, redesenhe a geometria da porta para que ela fique dentro dos limites de nível.

## <a name="zone-warnings"></a>Avisos de zona

### <a name="zonewarning"></a>**zoneWarning**

#### <a name="description-for-zonewarning"></a>*Descrição de zoneWarning*

Um **zoneWarning** ocorre quando uma zona não contém um rótulo. O Serviço de conversão descarta uma zona que não está rotulada.

#### <a name="example-for-zonewarning"></a>*Exemplo de zoneWarning*

A imagem a seguir exibe uma zona que não contém um rótulo.

![Exemplo de uma zona não contém um rótulo](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>*Como corrigir o zoneWarning*

Para corrigir um **zoneWarning**, verifique se cada zona tem um único rótulo.

## <a name="label-warnings"></a>Avisos de rótulo

### <a name="labelwarning"></a>*labelWarning*

#### <a name="description-for-labelwarning"></a>*Descrição de labelWarning*

Um **labelWarning** ocorre quando o desenho contém um recurso de rótulos ambíguos ou contraditórios.

Um **labelWarning** ocorre devido a um ou mais dos seguintes motivos:

* Um rótulo de unidade não está em nenhuma unidade.
* Um rótulo de zona não está nenhuma zona.
* Um rótulo de zona está dentro de duas ou mais zonas.

#### <a name="example-for-labelwarning"></a>*Exemplo de labelWarning*

A imagem a seguir exibe um rótulo que está dentro de duas zonas.

![Exemplo de um rótulo dentro de duas zonas ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>*Como corrigir o labelWarning*

Para corrigir um **labelWarning**, verifique se:

* Todos os rótulos de unidade estão dentro de unidades.
* Todos os rótulos de zona estão dentro de zonas.
* Todos os rótulos de zona estão em apenas uma zona.

## <a name="drawing-package-errors"></a>Erros do pacote de desenho

### <a name="invalidarchiveformat"></a>**invalidArchiveFormat**

#### <a name="description-for-invalidarchiveformat"></a>*Descrição de invalidArchiveFormat*

Um erro **invalidArchiveFormat** ocorre quando o pacote de desenho está em um formato de arquivo inválido, como GZIP ou 7-zip. Só há suporte para o formato de arquivo ZIP.

Também ocorrerá um erro **invalidArchiveFormat** se o arquivo ZIP estiver vazio.

#### <a name="how-to-fix-invalidarchiveformat"></a>*Como corrigir o invalidArchiveFormat*

Para corrigir um erro **invalidArchiveFormat**, verifique se:

* O nome do arquivo morto termina em _.zip_.
* Seu arquivo ZIP contém dados.
* Você pode abrir seu arquivo ZIP.

### <a name="invaliduserdata"></a>**invalidUserData**

#### <a name="description-for-invaliduserdata"></a>*Descrição do invalidUserData*

Um erro **invalidUserData** ocorre quando o Serviço de conversão não consegue ler um objeto de dados do usuário que está no armazenamento.

#### <a name="example-scenario-for-invaliduserdata"></a>*Cenário de exemplo do invalidUserData*

Você tentou carregar um pacote de desenho com um parâmetro `udid` incorreto.

#### <a name="how-to-fix-invaliduserdata"></a>*Como corrigir o invalidUserData*

Para corrigir um erro **invalidUserData**, verifique se:

* Você forneceu um `udid` correto para o pacote carregado.
* O Azure Maps Creator (versão prévia) foi habilitado para a conta do Azure Maps que você usou para carregar o pacote de desenho.
* A solicitação de API para o Serviço de conversão contém a chave de assinatura para a conta do Azure Mapas que você usou para carregar o pacote de desenho.

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>*Descrição do dwgError*

Um **dwgError** ocorre quando o pacote de desenho contém um problema com um ou mais arquivos DWG do arquivo ZIP carregado.

O **dwgError** ocorre quando o pacote de desenho contém um arquivo DWG que não pode ser aberto por ser inválido ou estar corrompido.

* Um arquivo DWG não é um desenho válido do formato de arquivo DWG do AutoCAD.
* Um arquivo DWG está corrompido.
* Um arquivo DWG está listado no arquivo _manifest.json_, mas está ausente no arquivamento ZIP.

#### <a name="how-to-fix-dwgerror"></a>*Como corrigir o dwgError*

Para corrigir um **dwgError**, inspecione seu arquivo _manifest.json_ confirme que:

* Todos os arquivos DWG em seu arquivo ZIP são desenhos válidos no formato DWG e são abertos no AutoCAD. Remova ou corrija todos os desenhos inválidos.
* A lista de arquivos DWG no _manifest.json_ corresponde aos arquivos DWG no arquivo ZIP.

## <a name="manifest-errors"></a>Erros de manifesto

### <a name="invalidjsonformat"></a>**invalidJsonFormat**

#### <a name="description-for-invalidjsonformat"></a>Descrição do invalidJsonFormat

Um erro **invalidJsonFormat** ocorre quando o arquivo _manifest.json_ não pode ser lido.

O _manifest. json_file não pode ser lido devido a erros de sintaxe ou de formatação JSON. Para saber mais sobre o formato JSON e a sintaxe, consulte [O formato de intercâmbio de dados JSON (JavaScript Object Notation)](https://tools.ietf.org/html/rfc7159)

#### <a name="how-to-fix-invalidjsonformat"></a>*Como corrigir o invalidJsonFormat*

Para corrigir um erro **invalidJsonFormat**, use um linter de JSON para detectar e resolver erros JSON.

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>*Descrição do missingRequiredField*

Um erro **missingRequiredField** ocorre quando o arquivo _manifest.json_ não tem dados necessários.

#### <a name="how-to-fix-missingrequiredfield"></a>*Como corrigir o missingRequiredField*

Para corrigir um erro **missingRequiredField**, verifique se o manifesto contém todas as propriedades necessárias. Para obter uma lista completa do objeto de manifesto necessário, consulte a [seção sobre manifesto nos requisitos do pacote de desenho](drawing-requirements.md#manifest-file-requirements)  

### <a name="missingmanifest"></a>**missingManifest**

#### <a name="description-for-missingmanifest"></a>*Descrição do missingManifest*

O erro **missingManifest** ocorre quando o arquivo _manifest.json_ está ausente no arquivo ZIP.

Um erro **missingManifest** ocorre devido a um ou mais dos seguintes motivos:

* O arquivo _manifest.json_ tem erro de grafia.
* O arquivo _manifest.json_ está ausente.
* O _manifest.json_ não está dentro do diretório raiz do arquivo ZIP.

#### <a name="how-to-fix-missingmanifest"></a>*Como corrigir o missingManifest*

Para corrigir um erro **missingManifest**, confirme se o arquivo tem um nome _manifest.json_ no nível raiz do arquivo ZIP.

### <a name="conflict"></a>**conflict**

#### <a name="description-for-conflict"></a>*Descrição do conflict*

O erro **conflict** ocorre quando o arquivo _manifest.json_ contém informações conflitantes.

#### <a name="example-scenario-for-conflict"></a>*Cenário de exemplo para conflict*

O Serviço de conversão retornará um erro **conflict** quando mais de um nível for definido com o mesmo ordinal de nível. O trecho de código JSON a seguir exibe dois níveis definidos com o mesmo ordinal.

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>*Como corrigir o conflict*

Para corrigir um erro **conflict**, inspecione o _manifest.json_ e remova todas as informações conflitantes.

### <a name="invalidgeoreference"></a>**invalidGeoreference**

#### <a name="description-for-invalidgeoreference"></a>*Descrição do invalidGeoreference*

O erro **invalidGeoreference** ocorre quando um arquivo _manifest.json_ contém uma referência de georeferência inválida.

Um erro **invalidGeoreference** ocorre devido a um ou mais dos seguintes motivos:

* O usuário está georeferenciando um valor de latitude ou longitude que está fora do intervalo.
* O usuário está georeferenciando um valor de rotação que está fora do intervalo.

#### <a name="example-scenario-for-invalidgeoreference"></a>*Cenário de exemplo do invalidGeoreference*

No trecho de código JSON abaixo, a latitude está acima do limite superior.

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>*Como corrigir o invalidGeoreference*

Para corrigir um erro **invalidGeoreference**, verifique se os valores georeferenciados estão dentro do intervalo.

>[!IMPORTANT]
>No GeoJSON, a ordem das coordenadas é longitude e latitude. Caso não use a ordem correta, poderá referenciar um valor de latitude ou longitude fora do intervalo acidentalmente.

## <a name="wall-errors"></a>Erros de parede

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>*Descrição do wallError*

O **wallError** ocorre quando o desenho contém um erro ao tentar criar um recurso de parede.

#### <a name="example-scenario-for-wallerror"></a>*Cenário de exemplo para wallError*

A imagem a seguir exibe um recurso de parede que não se sobrepõe a nenhuma unidade.

![Exemplo de recurso de parede que não se sobrepõe a nenhuma unidade](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>*Como corrigir o wallError*

Para corrigir um erro **wallError**, redesenhe a parede para que ela se sobreponha a pelo menos uma unidade. Ou crie uma nova unidade que se sobreponha à parede.

## <a name="vertical-penetration-errors"></a>Erros de penetração vertical

### <a name="verticalpenetrationerror"></a>**verticalPenetrationError**

#### <a name="description-for-verticalpenetrationerror"></a>*Descrição do verticalPenetrationError*

O **verticalPenetrationError** ocorre quando o desenho contém um recurso de penetração vertical ambíguo.

Um erro **verticalPenetrationError** ocorre devido a um ou mais dos seguintes motivos:

* O desenho contém uma área de penetração vertical sem esse tipo de área sobreposta em nenhum dos níveis acima ou abaixo dela.
* O pacote de desenho contém um nível com dois ou mais recursos de penetração vertical presentes que se sobrepõem a um único recurso de penetração vertical em outro nível diretamente acima ou abaixo dele.

#### <a name="example-scenario-for-verticalpenetrationerror"></a>*Cenário de exemplo do verticalPenetrationError*

A imagem abaixo exibe uma área de penetração vertical sem esse tipo de área sobreposta em níveis acima ou abaixo dela.

![Exemplo 1 de uma penetração vertical](./media/drawing-conversion-error-codes/vrt-2.png)

A imagem a seguir exibe uma área de penetração vertical que sobrepõe mais de uma área de penetração vertical em um nível adjacente.

![Exemplo 2 de uma penetração vertical](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>Como corrigir o verticalPenetrationError

Para corrigir um erro **verticalPenetrationError**, leia sobre como usar um recurso de penetração vertical no artigo [Requisitos do pacote de desenho](drawing-requirements.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como usar o visualizador de erros de desenho do Azure Mapas](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [Criador (visualização) para mapeamento interno](creator-indoor-maps.md)