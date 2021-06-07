---
title: Perguntas frequentes
description: Perguntas frequentes sobre o serviço Âncoras de Objeto do Azure.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 04/01/2020
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: aebc1013dcead6c32dab55512ce915e25f60f94a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047568"
---
# <a name="frequently-asked-questions-about-azure-object-anchors"></a>Perguntas frequentes sobre as Âncoras de Objeto do Azure

As Âncoras de Objeto do Azure permitem que um aplicativo detecte um objeto no mundo físico usando um modelo 3D e estime sua pose com 6DoF (Seis Graus de Liberdade).

Para obter mais informações, confira [Visão geral das Âncoras de Objeto do Azure](overview.md).

## <a name="product-faq"></a>Perguntas frequentes sobre o produto
**P. Que recomendações vocês fazem em relação aos objetos que devem ser usados?**

**R.** Recomendamos as seguintes propriedades para os objetos:

* 1 a 10 metros para cada dimensão
* Não simétrico, com variações suficientes na geometria
* Baixa refletividade (superfícies opacas) com cor brilhante
* Objetos estacionários
* Nenhuma ou pequenas quantidades de articulação
* Planos de fundo livre, com nenhuma ou pouca informação
* O objeto verificado deve ter correspondência 1:1 com o modelo treinado

**P. Quais são as dimensões máximas de objeto que podem ser processadas para a ingestão do modelo?**

**R.** Cada dimensão de um modelo de CAD deve ter menos de 10 metros.

**P. Qual é o tamanho máximo do modelo de CAD que pode ser processado para a ingestão do modelo?**

**R.** O tamanho do arquivo do modelo deve ser menor que 150 MB.

**P. Quais são os modelos de CAD com suporte?**

**R.** Atualmente, damos suporte aos tipos de arquivo `fbx`, `ply`, `obj`, `glb` e `gltf`.

**P. Qual é a direção da gravidade e a unidade exigida pelo serviço de ingestão do modelo? Como podemos descobrir?**

**R.** A direção da gravidade é o vetor para baixo que aponta para a terra. Para modelos de CAD, a direção da gravidade é normalmente o oposto de uma direção para cima. Por exemplo, em muitos casos, +Z representa para cima; nesse caso, -Z ou `Vector3(0.0, 0.0, -1.0)` representaria a direção da gravidade. Ao determinar a gravidade, você não deve apenas considerar o modelo, mas também a orientação na qual o modelo será visto durante o tempo de execução. Se estiver tentando detectar uma cadeira no mundo real em uma superfície plana, a gravidade poderá ser `Vector3(0.0, 0.0, -1.0)`. No entanto, se a cadeira estiver em uma inclinação de 45°, a gravidade poderá ser `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)`.

A direção da gravidade pode ser encontrada com uma ferramenta de renderização 3D, como a [MeshLab](http://www.meshlab.net/).

A unidade representa a unidade de medida do modelo. As unidades com suporte podem ser encontradas com a enumeração **Microsoft.Azure.ObjectAnchors.Ingestion.Unit**.

**P. Quanto tempo demora para ingerir um modelo de CAD?**

**R.** Para um modelo `ply`, normalmente de 3 a 15 minutos. Se estiver enviando modelos em outros formatos, talvez tenha que aguardar de 15 a 60 minutos, dependendo do tamanho do arquivo.

**P. As Âncoras de Objeto dão suporte a quais dispositivos?**

**R.** HoloLens 2 

**P. Qual build de sistema operacional deve ser executado pelo meu HoloLens?**

**R.** OS Build 18363.720 ou mais recente, lançado depois de 12 de março de 2020.

  Mais detalhes na [atualização do Windows 10 de 12 de março de 2020](https://support.microsoft.com/help/4551762).

**P. Quanto tempo demora para detectar um objeto no HoloLens?**

**R.** Depende do tamanho do objeto e do processo de verificação. Para obter uma detecção mais rápida, tente seguir as melhores práticas para uma verificação minuciosa. Para objetos menores dentro de 2 metros em cada dimensão, a detecção pode ocorrer em alguns segundos. Para objetos maiores, como um carro, o usuário deve percorrer uma volta completa em volta do objeto para obter uma detecção confiável, o que significa que a detecção poderá levar dezenas de segundos.

**P. Quais são as melhores práticas no uso das Âncoras de Objeto em um aplicativo HoloLens?**

**R.**

 1. Execute a calibragem de olho para obter uma renderização precisa.
 2. Verifique se a sala tem uma boa textura visual e uma boa iluminação.
 3. Mantenha o objeto estático, longe de outras informações, se possível.
 4. Opcionalmente, limpe o cache de [Mapeamento Espacial](/windows/mixed-reality/spatial-mapping) no seu dispositivo HoloLens.
 5. Leia o objeto andando em volta dele. Faça com que a maior parte do objeto seja observada.
 6. Defina uma área de pesquisa suficientemente grande para abranger o objeto.
 7. O objeto deve permanecer estacionário durante a detecção.
 8. Inicie a detecção de objeto e visualize a renderização com base na pose estimada.
 9. Bloqueie o objeto detectado ou interrompa o acompanhamento quando a pose estiver estável e precisa a fim de preservar a vida útil da bateria.

**P. Qual é a precisão de uma pose estimada?**

**R.** Depende do tamanho do objeto, do material, do ambiente, etc. Para objetos pequenos, o erro da pose estimada pode ficar no intervalo de 2 cm. Para objetos grandes, como um carro, o erro pode ser entre 2 e 8 cm.

**P. As Âncoras de Objeto podem lidar com objetos em movimento?**

**R.** Não damos suporte a objetos **em movimento contínuo** ou **dinâmicos**.

**P. As Âncoras de Objeto podem lidar com deformações ou articulações?**

**R.** Parcialmente, dependendo do quanto a forma ou a geometria do objeto muda devido à deformação ou à articulação. Se a geometria do objeto mudar muito, o usuário poderá criar outro modelo para essa configuração e usá-lo para detecção.

**P. Quantos objetos diferentes as Âncoras de Objeto podem detectar ao mesmo tempo?**

**R.** Atualmente, damos suporte à detecção de apenas um modelo de objeto de cada vez. 

**P. As Âncoras de Objeto podem detectar várias instâncias do mesmo modelo de objeto?**

**R.** Sim, você pode detectar até três objetos do mesmo tipo de modelo. O aplicativo chama `ObjectObserver.DetectAsync` várias vezes com consultas diferentes para detectar várias instâncias do mesmo modelo.

**P. O que devo fazer se meu objeto não for detectado no tempo de execução das Âncoras de Objeto?**

**R:**

* Faça com que a sala tenha texturas suficientes adicionando alguns cartazes.
* Leia o objeto mais completamente.
* Ajuste os parâmetros do modelo conforme descrito acima.
* Forneça uma caixa delimitadora estreita como área de pesquisa que inclua todo o objeto ou a maior parte dele.
* Limpe o cache de mapeamento espacial e examine novamente o objeto.
* Capture o diagnóstico e nos envie os dados.

**P. Como escolher os parâmetros de consulta de objeto?**

**R.**

* Forneça áreas de pesquisa estreitas para, idealmente, cobrir o objeto completo e melhorar a velocidade e a precisão da detecção.
* Normalmente é bom usar `ObjectQuery.MinSurfaceCoverage` do modelo de objeto como padrão; do contrário, use um valor menor para uma detecção mais rápida.
* Use um valor pequeno para `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` se esperar que o objeto esteja para cima na vertical.
* Um aplicativo deve sempre usar um modelo de objeto `1:1` na detecção. A escala estimada deve ficar próxima de 1, idealmente com margem de erro de menos de 1%. Um aplicativo poderia definir `ObjectQuery.MaxScaleChange` como `0` ou `0.1` para desabilitar ou habilitar a estimativa de escala e avaliar qualitativamente a pose da instância.

**P. Como fazer para obter dados de diagnóstico das Âncoras de Objeto do HoloLens?**

**R.** O aplicativo pode especificar a localização dos arquivos de diagnóstico. O aplicativo de amostra das Âncoras de Objeto grava diagnósticos na pasta **TempState**.

**P. Por que o modelo de origem não se alinha ao objeto físico quando usa a pose retornada pelo SDK do Unity das Âncoras de Objeto?**

**R.** O Unity pode mudar o sistema de coordenadas quando importa um modelo de objeto. Por exemplo, o SDK do Unity das Âncoras de Objeto inverte o eixo Z ao converter de um sistema de coordenadas destro para canhoto, mas o Unity pode aplicar uma rotação adicional sobre o eixo X ou Y. Um desenvolvedor pode determinar essa rotação adicional visualizando e comparando os sistemas de coordenadas.

**P. Vocês dão suporte a 2D?**

**R.** Como nos baseamos em geometria, damos suporte apenas a 3D.

**P. É possível diferenciar o mesmo modelo em cores diferentes?**

**R.** Como nossos algoritmos se baseiam em geometria, as cores diferentes do mesmo modelo não terão comportamentos diferentes durante a detecção.

**P. Posso usar as Âncoras de Objeto sem conectividade com a Internet?**

**R:** 
* É obrigatório ter conectividade para ingestão de modelos e treinamento, pois isso ocorre na nuvem.
* As sessões de tempo de execução são totalmente no dispositivo e não exigem conectividade, pois todos os cálculos ocorrem no HoloLens 2.

## <a name="privacy-faq"></a>Perguntas frequentes sobre privacidade
**P. Como as Âncoras de Objeto do Azure armazenam dados?**

**R.** Armazenamos apenas metadados do sistema, que são criptografados em repouso com uma chave de criptografia de dados gerenciados da Microsoft.