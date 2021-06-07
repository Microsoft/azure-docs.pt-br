---
title: Práticas Recomendadas
description: Práticas recomendadas para obter resultados aprimorados
author: ariye
ms.author: crtreasu
ms.date: 03/12/2021
ms.topic: best-practice
ms.service: azure-object-anchors
ms.openlocfilehash: e287d8305b3fd85fc992417e1563b1e58e6f8424
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462993"
---
# <a name="best-practices"></a>Práticas recomendadas

Recomendamos que você experimente algumas dessas etapas para obter os melhores resultados.

## <a name="ingestion"></a>Ingestão

- Verifique as dimensões de seus objetos físicos. As âncoras de objeto do Azure funcionam melhor para objetos cuja menor dimensão está no intervalo de 1m-10 milhões recomendado.
- Inspecione seu modelo 3D em software como [**MeshLab**](https://www.meshlab.net/) para obter os detalhes a seguir.
  - Certifique-se de que o modelo 3D tenha uma malha de triângulo e que os triângulos na superfície exterior se facem para fora. Ou seja, os vértices devem ser orientados a fazer com que os normais sigam a regra à direita em sua orientação para cima.
  - Verifique se o modelo 3D está especificado com as unidades de escala corretas em relação aos objetos físicos. As unidades devem ser um entre: ***centímetros, Decimeters, pés, polegadas, quilômetros, metros, milímetros, jardas***.
  - Confirme a direção da gravidade nominal que corresponde à orientação vertical do mundo real do objeto. Se a Cruz vertical/gravidade do objeto for-Y, use ***(0,-1, 0)** _ ou _*_ (0, 0,-1) _ * * para-Z e, da mesma forma, para qualquer outra direção.
  - Verifique se o modelo 3D está codificado em um dos formatos com suporte:,,, `.glb` `.gltf` `.ply` `.fbx` , `.obj` .
- Nosso serviço de conversão de modelo pode levar muito tempo para processar um modelo grande de alta LOD (nível de detalhes). Para a eficácia, você pode pré-processar seu modelo 3D para remover os rostos interiores.

## <a name="detection"></a>Detecção

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Mixed-Reality/Azure-Object-Anchors-Detection-and-Alignment-Best-Practices/player]

- O SDK de tempo de execução fornecido requer uma região de pesquisa fornecida pelo usuário para pesquisar e detectar os objetos físicos. A região de pesquisa pode ser uma caixa delimitadora, uma esfera, uma exibição frustum ou qualquer combinação delas. Para evitar uma detecção falsa, é preferível definir uma região de pesquisa grande o suficiente para cobrir o objeto. Ao usar os aplicativos de exemplo fornecidos, você pode destacar um lado do objeto cerca de 2 metros de distância da superfície mais próxima e iniciar o aplicativo.
- Antes de iniciar o aplicativo âncoras de objeto do Azure em um dispositivo de HoloLens 2, remova os hologramas na vizinhança do seu local de trabalho por meio de configurações principais de dispositivos por meio de ***configurações->>hologramas do sistema***

  Essa etapa garante que se um novo objeto, como um carro, estiver presente no mesmo espaço ocupado por outro anteriormente, ou se o objeto tiver sido movido do espaço de destino, os hologramas antigos e irrelevantes não persistirão e criarão uma visualização confusa para o objeto atualmente em exibição.
- Depois de remover os hologramas e antes de iniciar o aplicativo, digitalize o objeto, como um carro, examinando o objeto enquanto aproveitava o dispositivo de cerca de 1-2 metros e passando-o lentamente para o objeto uma ou duas vezes.

  Essa etapa garante que qualquer estimativa de superfície residual criada em seu espaço por objetos anteriores e verificações seja atualizada com as superfícies do objeto de destino atual com o qual você vai trabalhar. Caso contrário, o aplicativo poderá ver superfícies fantasmas duplas que levam ao alinhamento impreciso de seu modelo 3D e aos hologramas associados. A verificação prévia do objeto também reduzirá muito a latência de detecção de âncoras de objetos do Azure, digamos, de 30 segundos a 5 segundos.
- Para objetos escuros e altamente refletiis, talvez você precise digitalizar o objeto em um intervalo mais próximo e também movendo a sua parte para cima e para baixo e para a esquerda e direita para permitir que o dispositivo Veja as superfícies de vários ângulos e várias distâncias.
- Quando você vir uma detecção de objeto incorreto, como a orientação sendo invertida ou a pose estar incorreta, como um modelo inclinado, você deve Visualizar o mapeamento espacial. Geralmente, os resultados incorretos são devido à reconstrução de superfície ruim ou incompleta. Você pode remover os hologramas, verificar o objeto e executar a detecção de objeto no aplicativo novamente.
- O SDK de tempo de execução fornecido fornece alguns parâmetros para permitir que os usuários ajustem a detecção, conforme demonstrado em nossos aplicativos de exemplo. Os parâmetros padrão funcionam bem para a maioria dos objetos. Se você achar que precisa ajustá-los para objetos específicos, aqui estão algumas recomendações:
  - Use um limite de cobertura de superfície inferior se o objeto físico for grande, escuro ou brilhante.
  - Permitir uma pequena alteração de escala (por exemplo, 0,1) para um objeto grande como um carro.
  - Permitir algum desvio em graus entre a direção vertical local do objeto e a gravidade quando o objeto estiver em uma inclinação.
