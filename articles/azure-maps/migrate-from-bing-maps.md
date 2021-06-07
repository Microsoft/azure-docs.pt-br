---
title: 'Tutorial: Migrar do Bing Maps para o Azure Mapas | Microsoft Azure Mapas'
description: Um tutorial sobre como migrar do Bing Maps para o Microsoft Azure Mapas. As diretrizes orientam você sobre como alternar para as APIs e SDKs do Azure Mapas.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9bd0516889733a666bf15668cffd124dcc468f3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100388950"
---
# <a name="tutorial-migrate-from-bing-maps-to-azure-maps"></a>Tutorial: Migrar do Bing Maps para o Azure Mapas

Este guia fornecerá insights sobre como migrar aplicativos Web, móveis e baseados em servidor do Bing Maps para a plataforma do Azure Mapas. Este guia inclui exemplos de código comparativos, sugestões de migração e práticas recomendadas com o objetivo de fazer a migração para o Azure Mapas. 

Neste tutorial, você aprenderá:

> [!div class="checklist"]
> * Comparação de alto nível entre os recursos equivalentes do Bing Mapas disponíveis no Azure Mapas.
> * Quais diferenças de licenciamento devem ser levadas em consideração.
> * Como planejar sua migração.
> * Onde encontrar suporte e recursos técnicos.

## <a name="prerequisites"></a>Pré-requisitos

1. Entre no [portal do Azure](https://portal.azure.com). Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. [Faça uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](how-to-manage-authentication.md).

## <a name="azure-maps-platform-overview"></a>Visão geral da plataforma do Azure Mapas

O Azure Mapas fornecerá aos desenvolvedores de todos os setores recursos geoespaciais poderosos, repletos dos mais recentes dados de mapeamento disponíveis a fim de fornecer contexto geográfico para aplicativos Web e móveis. O Azure Mapas é um conjunto de APIs REST em conformidade com a API do Azure One para obter mapas, pesquisas, roteiros, tráfegos, fusos horários, delimitações geográficas, dados de mapas, dados meteorológicos e muitos outros serviços. Tudo isso acompanhado por SDKs da Web e do Android com o objetivo de tornar o desenvolvimento fácil, flexível e portátil em várias plataformas. [O Azure Mapas também está disponível no Power BI](power-bi-visual-getting-started.md).

## <a name="high-level-platform-comparison"></a>Comparação de plataforma de alto nível

A tabela a seguir fornecerá uma lista de alto nível de recursos do Bing Maps e um suporte correspondente a esses recursos no Azure Mapas. Essa lista não incluirá recursos adicionais do Azure Mapas, como acessibilidade, APIs de delimitação geográfica, serviços de tráfego, operações espaciais, acesso direto a peças de mapa e serviços em lotes.

| Recurso do Bing Maps                     | Suporte no Azure Mapas |
|---------------------------------------|:------------------:|
| SDK da Web                               | ✓                  |
| SDK do Android                           | ✓                  |
| SDK do iOS                               | Planejado            |
| SDK da UWP                               | N/D                 |
| SDK de WPF                               | N/D                 |
| APIs de Serviço REST                     | ✓                  |
| Sugestão automática                           | ✓                  |
| Trajetos (incluindo caminhões)          | ✓                  |
| Matriz de Distância                       | ✓                  |
| Elevações                            | ✓ (versão prévia)        |
| Imagens – Mapa estático                  | ✓                  |
| Metadados de imagens                      | ✓                  |
| Isócronos                            | ✓                  |
| Percepções Locais                        | ✓                  |
| Pesquisa local                          | ✓                  |
| Reconhecimento de localização                  | ✓                  |
| Localizações (geocódigo direto/reverso) | ✓                  |
| Rotas de itinerário otimizadas            | Planejado            |
| Ajustar às estradas                         | ✓                  |
| SDS (Serviços de Dados Espaciais)           | Parcial            |
| Fuso horário                             | ✓                  |
| Incidentes de tráfego                     | ✓                  |
| Mapas controlados por configuração             | N/D                |

O Bing Maps fornece uma autenticação básica baseada em chave. O Azure Mapas fornece a autenticação básica baseada em chave e a autenticação altamente segura do Azure Active Directory.

## <a name="licensing-considerations"></a>Considerações sobre licenciamento

Na migração do Bing Mapas para o Azure Mapas, os itens a seguir deverão ser considerados em relação ao licenciamento.

* Os encargos do Azure Mapas pelo uso de mapas interativos são baseados no número de peças de mapa carregados, enquanto os encargos do Bing Maps são baseados no carregamento do controle de mapeamento (sessões). A fim de reduzir os custos para os desenvolvedores, o Azure Mapas armazena em cache as peças de mapa automaticamente. Para cada 15 peças de mapa que são carregadas, uma transação do Azure Mapas é gerada. Os SDKs interativos do Azure Mapas usam peças de 512 pixels e, em média, geram uma transação ou menos por exibição de página.

* O Azure Mapas permite que os dados de sua plataforma sejam armazenados no Azure. Eles também podem ser armazenados em cache em outro lugar por até seis meses, de acordo com os [Termos de uso](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Veja abaixo alguns recursos relacionados ao licenciamento do Azure Mapas:

-   [Página de preços do Azure Mapas](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Termo de uso do Azure Mapas](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (incluído nos termos do Microsoft Online Services)
-   [Escolha o tipo de preço certo no Azure Mapas](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Plano de migração sugerido

Este é um exemplo de um plano de migração de alto nível.

1.  Faça um inventário de quais SDKs e serviços do Bing Maps seu aplicativo está usando e verifique se o Azure Mapas fornece SDKs e serviços alternativos para os quais você poderá migrar.
2.  Crie uma assinatura do Azure (caso você ainda não tenha uma) em <https://azure.com>.
3.  Crie uma conta ([documentação](./how-to-manage-account-keys.md)) e chave de autenticação do Azure Mapas ou do Azure Active Directory ([documentação](./how-to-manage-authentication.md)).
4.  Migre o código do aplicativo.
5.  Teste seu aplicativo migrado.
6.  Implante seu aplicativo migrado para produção.

## <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure

Para criar uma conta do Azure Mapas e obter acesso à plataforma do Azure Mapas, siga estas etapas:

1. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. Entre no [portal do Azure](https://portal.azure.com/).
3. Crie uma [conta do Azure Mapas](./how-to-manage-account-keys.md).
4. [Obtenha a chave de assinatura do Azure Mapas](./how-to-manage-authentication.md#view-authentication-details) ou configure a autenticação do Azure Active Directory para aumentar a segurança.

## <a name="azure-maps-technical-resources"></a>Recursos técnicos do Azure Mapas

Aqui está uma lista de recursos técnicos úteis para o Azure Mapas.

* Visão geral: <https://azure.com/maps>
* Documentação: <https://aka.ms/AzureMapsDocs>
* Exemplos de código do SDK Web: <https://aka.ms/AzureMapsSamples>
* Fóruns de desenvolvedores: <https://aka.ms/AzureMapsForums>
* Vídeos: <https://aka.ms/AzureMapsVideos>
* Blog: <https://aka.ms/AzureMapsBlog>
* Comentários do Azure Mapas (UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>Suporte à migração

Os desenvolvedores podem buscar suporte à migração por meio dos [fóruns](/answers/topics/azure-maps.html) ou por meio de uma das muitas opções de Suporte do Azure: <https://azure.microsoft.com/support/options/>

## <a name="new-terminology"></a>Nova terminologia

A lista a seguir contém os termos comuns do Bing Mapas e os termos correspondentes do Azure Mapas.

| Termo do Bing Maps                    | Termo do Azure Mapas                                                |
|-----------------------------------|----------------------------------------------------------------|
| Vista aérea                            | Satélite ou Vista Aérea                                            |
| Instruções                        | Também podem ser chamadas de Roteiros                             |
| Entidades                          | Geometrias ou Recursos                                         |
| `EntityCollection`                | Fonte de dados ou Camada                                           |
| `Geopoint`                        | Posição                                                       |
| `GeoXML`                          | Arquivos XML no módulo de E/S Espacial                             |
| Sobreposição terrestre                    | Camada de imagem                                                    |
| Híbrido (em referência ao tipo de mapa) | Satélite com estradas                                           |
| Infobox                           | Pop-up                                                          |
| Local                          | Posição                                                       |
| `LocationRect`                    | Caixa delimitadora                                                   |
| Tipo de mapa                          | Estilo de mapa                                                      |
| Barra de navegação                    | Seletor de estilo de mapa, controle de zoom, controle de inclinação e controle de bússola |
| Marcador                           | Camada de bolha, camada de símbolo ou marcador HTML                      |

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos que exijam limpeza.

## <a name="next-steps"></a>Próximas etapas

Saiba mais detalhes sobre como migrar seu aplicativo do Bing Maps com estes artigos:

> [!div class="nextstepaction"]
> [Migrar um aplicativo Web](migrate-from-bing-maps-web-app.md)
