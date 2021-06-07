---
title: Trabalhar com mapas em modo interno no Azure Maps Creator (versão prévia)
description: Este artigo apresenta os conceitos que se aplicam aos serviços do Azure Maps Creator (versão prévia)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4ab00317e71f832bb677c4c7587e2356a37cb7a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96903557"
---
# <a name="creator-preview-for-indoor-maps"></a>Criador (visualização) para mapas de interno


> [!IMPORTANT]
> Os serviços do Criador do Azure Mapas estão em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Este artigo apresenta conceitos e ferramentas que se aplicam ao Criador do Azure Mapas. Recomendamos que você leia este artigo antes de começar a usar a API e o SDK do Criador do Azure Mapas.

Você pode usar o Criador para desenvolver aplicativos com recursos de mapa baseados em dados de mapas internos. Este artigo descreve o processo de carregar, converter, criar e usar seus dados de mapa. Todo o fluxo de trabalho é ilustrado no diagrama a seguir.

![Fluxo de trabalho de dados de mapa do Criador](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator-preview"></a>Criar criador do Azure Maps (versão prévia) 

Para usar os serviços criadores (versão prévia), o Azure Maps Creator deve ser criado em uma conta do Azure Maps. Para obter informações sobre como criá-lo no Azure Mapas, confira [Gerenciar o Criador do Azure Mapas](how-to-manage-creator.md).

## <a name="upload-a-drawing-package"></a>Carregar um pacote do Drawing

O criador (versão prévia) coleta dados de mapas em modo interno convertendo um pacote de desenho carregado. O pacote do Drawing representa um recurso construído ou modelado. Para obter informações sobre os requisitos de pacotes do Drawing, confira os [Requisitos do pacote do Drawing](drawing-requirements.md).

Use a [API de carregamento de dados do Azure Maps (versão prévia)](/rest/api/maps/data/uploadpreview) para carregar um pacote de desenho.  Após o upload bem-sucedido, a API de carregamento de dados retornará um identificador de dados do usuário (`udid`). O `udid` será usado na próxima etapa para converter o pacote carregado em dados de mapa interno.

## <a name="convert-a-drawing-package"></a>Converter um pacote do Drawing

O [serviço de Conversão do Azure Mapas](/rest/api/maps/conversion) converte um pacote do Drawing carregado em dados de mapa interno. O serviço de Conversão também valida o pacote. Os problemas de validação são classificados em dois tipos: erros e avisos. Se forem detectados erros, o processo de conversão apresentará falha. Caso sejam detectados avisos, a conversão será bem-sucedida. No entanto, é recomendável que você analise e resolva todos os avisos. Um aviso indica que parte da conversão foi ignorada ou corrigida automaticamente. Não resolver os avisos pode resultar em erros em processos futuros. Para obter mais informações, confira [Avisos e erros do pacote do Drawing](drawing-conversion-error-codes.md).

Quando ocorre um erro, o serviço de Conversão fornece um link para o [Visualizador de Erros de Desenho no Azure Mapas](drawing-error-visualizer.md) aplicativo Web autônomo. Você pode usar o Visualizador de Erros de Desenho para inspecionar [erros e avisos do pacote do Drawing](drawing-conversion-error-codes.md) ocorridos durante o processo de conversão. Depois de corrigir os erros, você poderá tentar carregar e converter o pacote.

## <a name="create-indoor-map-data"></a>Criar dados de mapa interno

O Azure Maps Creator (versão prévia) fornece três serviços:

* [Serviço de conjunto de dados](/rest/api/maps/dataset/createpreview).
Use o serviço de conjunto de dados para criar um conjunto de dados de um pacote do Drawing convertido.
* [Serviço de conjunto de peças](/rest/api/maps/tileset/createpreview).
Use o serviço de conjunto de peças para criar uma representação de um conjunto de dados baseada em vetor. Os aplicativos podem usar um conjunto de peças para apresentar uma exibição do conjunto de dados baseada em peças visuais.
* [Serviço de estado do recurso](/rest/api/maps/featurestate). Use o serviço de Estado do recurso para dar suporte ao estilo de mapa dinâmico. O estilo de mapa dinâmico permite que os aplicativos reflitam eventos em tempo real em espaços fornecidos pelo sistema de IoT.

### <a name="datasets"></a>Conjunto de dados

Um conjunto de dados é uma coleção de recursos de mapa interno. Os recursos de mapa interno representam instalações definidas em um pacote do Drawing convertido. Depois de criar um conjunto de dados com o [Serviço de conjunto de dados](/rest/api/maps/dataset/createpreview), você pode criar vários [conjunto de peças](#tilesets) ou [conjuntos de estados do recurso](#feature-statesets).

O [Serviço de conjunto de dados](/rest/api/maps/dataset/createpreview) permite aos desenvolvedores adicionar ou remover instalações de um conjunto de dados a qualquer momento. Para obter mais informações sobre como atualizar um conjunto de dados usando a API, confira as opções de acréscimo no [Serviço de conjunto de dados](/rest/api/maps/dataset/createpreview). Para obter um exemplo de como atualizar um conjunto de dados, confira [Manutenção de Dados](#data-maintenance).

### <a name="tilesets"></a>Conjuntos de peças

Um conjunto de peças é uma coleção de dados de vetor que representa um conjunto de blocos de grade uniformes. Os desenvolvedores podem usar o [Serviço de conjunto de peças](/rest/api/maps/tileset/createpreview) para criar conjuntos de peças oriundos de um conjunto de dados.

Para refletir as diferentes fases de conteúdo, você poderá criar vários conjuntos de peças usando o mesmo conjunto de dados. Por exemplo, você poderia criar um conjunto de peças com mobília e equipamento e outro sem mobília e equipamento.  Você pode optar por gerar um conjunto de peças com as atualizações de dados mais recentes e outra sem as atualizações de dados mais recentes.

Além dos dados de vetor, o conjunto de peças fornece metadados para da renderização do mapa. Por exemplo, os metadados do conjunto de peças contêm níveis de zoom mínimo e máximo para o conjunto de peças. Os metadados também oferecem uma caixa delimitadora que define a extensão geográfica do conjunto de peças. A caixa delimitadora permite que um aplicativo defina o ponto central correto programaticamente. Para obter mais informações sobre metadados de conjunto de peças, confira [API de lista do conjunto de peças](/rest/api/maps/tileset/listpreview).

Depois que um conjunto de peças é criado, ele pode ser recuperado pelo [serviço Render V2](#render-v2-service).

Se um conjunto de peças de peças ficar desatualizado e não for mais útil, você poderá excluí-lo. Para obter mais informações sobre como excluir os conjuntos de peças, confira [Manutenção de dados](#data-maintenance).

>[!NOTE]
>Um conjunto de peças é independente em relação ao conjunto de dados que o criou. Se você criar os conjuntos de peças com um conjunto de dados e, em seguida, atualizar esse conjunto de dados, os conjuntos de peças não serão atualizados. Para refletir as alterações em um conjunto de dados, você precisará criar novos conjuntos de peças. Da mesma forma, se você excluir um conjunto de peças, o conjunto de dados não será afetado.

### <a name="feature-statesets"></a>Conjuntos de estados do recurso

Os conjuntos de estados do recurso são coleções de propriedades dinâmicas (*estados*) atribuídas a recursos de conjunto de dados, como salas ou equipamentos. Um exemplo de um *estado* pode ser temperatura ou ocupação. Cada *estado* é um par chave/valor que contém o nome da propriedade, o valor e o carimbo de data/hora da última atualização.

O [serviço de Estado do Recurso](/rest/api/maps/featurestate/createstatesetpreview) permite que você crie e gerencie um conjunto de estados de recurso para um conjunto de dados. O conjunto de estados do recurso é definido por um ou mais *estados*. Cada recurso, como uma sala, pode ter um *estado* anexado a ele.

O valor de cada *estado* em um conjunto de estados pode ser atualizado ou recuperado por dispositivos IoT ou outros aplicativos.  Por exemplo, usando a [API de Atualização de Estado de Recurso](/rest/api/maps/featurestate/updatestatespreview), os dispositivos que medem a ocupação de um espaço podem, de forma sistemática, postar a alteração do estado de uma sala.

Um aplicativo pode usar um conjunto de estados do recurso para renderizar dinamicamente recursos em uma instalação de acordo com seu estado atual e o respectivo estilo do mapa. Para obter mais informações sobre como usar os conjuntos de estados do recurso para definir o estilo dos recursos em um mapa de renderização, confira o [Módulo de SDK da Web interno](#indoor-maps-module).

>[!NOTE]
>Assim como nos conjuntos de peças, a alteração de um conjunto de dados não afetará o estado do recurso existente, e a exclusão de um conjunto de estados do recurso não terá nenhum efeito sobre o conjunto de recursos ao qual ele está anexado.

## <a name="using-indoor-maps"></a>Usando mapas internos

### <a name="render-v2-service"></a>Serviço Render V2

O serviço v2 de renderização do Azure Maps [-obter API de bloco do mapa (versão prévia)](/rest/api/maps/renderv2/getmaptilepreview) foi estendido para dar suporte ao criador (versão prévia) Tilesets.

O serviço Render V2 – API de obtenção de estado de mapa permite que os aplicativos solicitem conjuntos de peças. Os conjuntos de peças poderão ser integrados a um controle de mapa ou SDK. Para obter um exemplo de um controle de mapa que use o serviço Render V2, confira o [Módulo de mapas internos](#indoor-maps-module).

### <a name="web-feature-service-api"></a>API do Web Feature Service

Os conjuntos de dados podem ser consultados por meio da [API do WFS (Web Feature Service)](/rest/api/maps/wfs). O WFS segue os [recursos de API do Open Geospatial Consortium](http://docs.opengeospatial.org/DRAFTS/17-069r1.html). A API do WFS permite consultar recursos dentro do próprio conjunto de dados. Por exemplo, você pode usar o WFS para localizar todas as salas de reunião de tamanho médio de um determinado local e piso.

### <a name="indoor-maps-module"></a>Módulo de mapas internos

O SDK da Web do [Azure Mapas](./index.yml) inclui o Módulo de mapas internos. Esse módulo oferece funcionalidades estendidas para a biblioteca de *Controle de Mapa* do Azure Mapas. O módulo de mapas em modo interno Renderiza mapas de interno criados no criador (versão prévia). Ele integra widgets como *seletor de piso*, o que permite que os usuários visualizem vários pisos.

O Módulo de mapas internos permite que você crie aplicativos Web que integram dados de mapas internos a outros [serviços do Azure Mapas](./index.yml). As configurações de aplicativo mais comuns podem incluir a adição de conhecimento de outros mapas, como estradas, imagens, clima e trânsito, aos mapas internos.

O Módulo de mapas internos também dá suporte ao estilo de mapa dinâmico. Para obter instruções passo a passo sobre como implementar estilos dinâmicos de conjunto de estados de recurso em um aplicativo, confira [Como usar o Módulo de mapa interno](how-to-use-indoor-module.md)

### <a name="azure-maps-integration"></a>Integração com o Azure Mapas

Ao começar a desenvolver soluções para mapas internos, você poderá descobrir maneiras de integrar os recursos do Azure Mapas. Por exemplo, o acompanhamento de ativos ou cenários de segurança podem ser implementados pela [API de Cerca Geográfica do Azure Mapas](/rest/api/maps/spatial/postgeofence) com mapas internos do Criador. A API de Cerca Geográfica poderia ser usada para determinar, por exemplo, se um trabalhador entrou ou saiu de áreas internas específicas. Encontre mais informações sobre como conectar o Azure Mapas à telemetria de IoT [aqui](tutorial-iot-hub-maps.md).

### <a name="data-maintenance"></a>Manutenção de dados

 A lista de APIs do Azure Maps Creator (versão prévia), atualização e exclusão permite listar, atualizar e excluir seus conjuntos de tilesets e recursos statesets.

>[!NOTE]
>Sempre que você analisar uma lista de itens e decidir excluí-los, deverá considerar o impacto dessa exclusão em todas as APIs ou aplicativos dependentes. Por exemplo, se você excluir um conjunto de peças que está sendo usado por um aplicativo por meio do [Render v2 – API de obtenção de peça de mapa](/rest/api/maps/renderv2/getmaptilepreview), a exclusão desse conjunto de peças resultará em uma falha de aplicativo na sua renderização.

### <a name="example-updating-a-dataset"></a>Exemplo: atualizando um conjunto de dados

O exemplo a seguir mostra como atualizar um conjunto de dados, criar um conjunto de peças e excluir um conjunto de peças antigo.

1. Siga as etapas nas seções [Carregando um pacote do Drawing](#upload-a-drawing-package) e [Converter um pacote do Drawing](#convert-a-drawing-package) para carregar e converter o novo pacote do Drawing.

2. Use a [API de criação de conjunto de dados](/rest/api/maps/dataset/createpreview) para acrescentar os dados convertidos ao conjunto de dados.

3. Use a [API de criação de conjunto de peças](/rest/api/maps/tileset/createpreview) para gerar um conjunto de peças com o conjunto de dados atualizado. Salve o identificador do novo conjunto de peças para a etapa 4.

4. Atualize o identificador do conjunto de peças em seu aplicativo para habilitar a visualização do conjunto de dados do campus atualizado. Se o conjunto de peças anterior não for mais usado, você poderá excluí-lo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Criando um mapa interno de criador (visualização)](tutorial-creator-indoor-maps.md)