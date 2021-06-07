---
title: Tutorial – Fazer a migração do Google Maps para o Azure Mapas | Microsoft Azure Mapas
description: Tutorial sobre como fazer a migração do Google Maps para o Microsoft Azure Mapas. As diretrizes orientam você sobre como alternar para as APIs e SDKs do Azure Mapas.
author: rbrundritt
ms.author: richbrun
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6241f6156b01c3c90f00578ae5416e4e77270930
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386791"
---
# <a name="tutorial-migrate-from-google-maps-to-azure-maps"></a>Tutorial: Migrar do Google Maps para o Azure Mapas

Este artigo fornece informações sobre como migrar aplicativos Web, móveis e baseados em servidor do Google Maps para a plataforma do Microsoft Azure Mapas. Este tutorial inclui exemplos comparativos de código, sugestões de migração e práticas recomendadas para migração para o Azure Mapas. Neste tutorial, você irá aprender:

> [!div class="checklist"]
> * Comparação de alto nível para os recursos equivalentes do Google Maps disponíveis no Azure Mapas.
> * Quais diferenças de licenciamento devem ser levadas em consideração.
> * Como planejar sua migração.
> * Onde encontrar suporte e recursos técnicos.

## <a name="prerequisites"></a>Pré-requisitos

1. Entre no [portal do Azure](https://portal.azure.com). Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. [Faça uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](how-to-manage-authentication.md).

## <a name="azure-maps-platform-overview"></a>Visão geral da plataforma do Azure Mapas

O Azure Mapas fornece funcionalidades geoespaciais poderosas para desenvolvedores de todos os setores. As funcionalidades são empacotadas com os dados de mapa atualizados regularmente, a fim de fornecer contexto geográfico para aplicativos Web e móveis. Os Azure Mapas têm um conjunto de APIs REST em conformidade com o OneAPI do Azure. As APIs REST oferecem renderização de mapas, pesquisa, roteiros, tráfego, fusos horários, geolocalização, delimitação geográfica, dados de mapa, clima, mobilidade e Operações Espaciais. As operações são acompanhadas pelos SDKs da Web e do Android para tornar o desenvolvimento fácil, flexível e portátil em várias plataformas.

## <a name="high-level-platform-comparison"></a>Comparação de plataforma de alto nível

A tabela fornece uma lista de alto nível dos recursos do Azure Mapas, que correspondem aos recursos do Google Maps. Essa lista não mostra todos os recursos do Azure Mapas. Os recursos adicionais dos Azure Mapas incluem: acessibilidade, delimitação geográfica, isócronos, operações espaciais, acesso direto a peças de mapa, serviços em lotes e comparações de cobertura de dados (ou seja, cobertura de imagens).

| Recurso do Google Maps         | Suporte no Azure Mapas                     |
|-----------------------------|:--------------------------------------:|
| SDK da Web                     | ✓                                      |
| SDK do Android                 | ✓                                      |
| SDK do iOS                     | Planejado                                |
| APIs de Serviço REST           | ✓                                      |
| Trajeto (roteiros)        | ✓                                      |
| Matriz de Distância             | ✓                                      |
| Elevação                   | ✓ (versão prévia)                            |
| Geocódigo (avançar/recuar) | ✓                                      |
| Geolocalização                 | N/D                                    |
| Estradas mais próximas               | ✓                                      |
| Pesquisa de locais               | ✓                                      |
| Detalhes de locais              | N/A – site e número de telefone disponível |
| Fotos de locais               | N/D                                    |
| Preenchimento automático de locais          | ✓                                      |
| Ajustar à estrada                | ✓                                      |
| Limites de Velocidade                | ✓                                      |
| Mapas estáticos                 | ✓                                      |
| Exibição estática de rua          | N/D                                    |
| Fuso horário                   | ✓                                      |
| API inserida do Mapas           | N/D                                    |
| URLs de mapa                    | N/D                                    |

O Google Maps fornece autenticação básica baseada em chave. Os Azure Mapas fornecem autenticação básica baseada em chave e autenticação do Azure Active Directory. A autenticação do Azure Active Directory fornece mais recursos de segurança comparado à autenticação básica baseada em chave.

## <a name="licensing-considerations"></a>Considerações sobre licenciamento

Ao migrar do Google Maps para o Azure Mapas, considere os seguintes pontos sobre licenciamento.

* O Azure Mapas cobra pelo uso de mapas interativos, com base no número de peças de mapa carregadas. Por outro lado, o Google Maps cobra pelo carregamento do controle de mapa. Nos SDKs interativos do Azure Mapas, as peças de mapa são armazenadas em cache automaticamente para reduzir o custo de desenvolvimento. Para cada 15 peças de mapa que são carregadas, uma transação do Azure Mapas é gerada. Os SDKs interativos dos Azure Mapas usam peças de 512 pixels e, em média, geram uma transação ou menos por exibição de página.
* Geralmente, é mais econômico substituir imagens de mapa estáticas dos serviços Web do Google Maps pelo SDK da Web dos Azure Mapas. O SDK da Web dos Azure Mapas usa peças de mapa. A menos que o usuário aplique panorâmica ao mapa e o amplie, muitas vezes, o serviço vai gerar apenas uma fração de uma transação por carregamento de mapa. O SDK Web do Azure Mapas tem opções para desabilitar a aplicação de panorâmica e de zoom, se desejado. Além disso, o SDK Web do Azure Mapas fornece muito mais opções de visualização do que o serviço Web do mapa estático.
* O Azure Mapas permite que os dados de sua plataforma sejam armazenados no Azure. Além disso, os dados podem ser armazenados em cache em outro lugar por até seis meses, de acordo com os [termos de uso](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Aqui estão alguns recursos relacionados para o Azure Mapas:

* [Página de preços do Azure Mapas](https://azure.microsoft.com/pricing/details/azure-maps/)
* [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
* [Termo de uso do Azure Mapas](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (incluído nos termos do Microsoft Online Services)
* [Escolha o tipo de preço certo no Azure Mapas](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Plano de migração sugerido

O plano de migração a seguir é de alto nível.

1. Faça o inventário dos SDKs e dos serviços do Google Maps que seu aplicativo usa. Verifique se o Azure Mapas fornece SDKs e serviços alternativos.
2. Caso você ainda não tenha, crie uma assinatura do Azure em [https://azure.com](https://azure.com).
3. Crie uma conta ([documentação](./how-to-manage-account-keys.md)) e chave de autenticação do Azure Mapas ou do Azure Active Directory ([documentação](./how-to-manage-authentication.md)).
4. Migre o código do aplicativo.
5. Teste seu aplicativo migrado.
6. Implante seu aplicativo migrado para produção.

## <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure

Para criar uma conta do Azure Mapas e obter acesso à plataforma do Azure Mapas, siga estas etapas:

1. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. Entre no [portal do Azure](https://portal.azure.com/).
3. Crie uma [conta do Azure Mapas](./how-to-manage-account-keys.md). 
4. [Obtenha a chave de assinatura do Azure Mapas](./how-to-manage-authentication.md#view-authentication-details) ou configure a autenticação do Azure Active Directory para aumentar a segurança.

## <a name="azure-maps-technical-resources"></a>Recursos técnicos do Azure Mapas

Aqui está uma lista de recursos técnicos úteis para o Azure Mapas.

- Visão geral: [https://azure.com/maps](https://azure.com/maps)
- Documentação: [https://aka.ms/AzureMapsDocs](./index.yml)
- Exemplos de código do SDK Web: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Fóruns de desenvolvedores: [https://aka.ms/AzureMapsForums](/answers/topics/azure-maps.html)
- Vídeos: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Blog de tecnologia: [https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Comentários do Azure Mapas (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Jupyter Notebook dos Azure Mapas](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)

## <a name="migration-support"></a>Suporte à migração

Os desenvolvedores podem buscar suporte à migração por meio dos [fóruns](/answers/topics/azure-maps.html) ou por meio de uma das muitas opções de Suporte do Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="clean-up-resources"></a>Limpar recursos

Não há recursos a serem limpos.

## <a name="next-steps"></a>Próximas etapas

Saiba os detalhes de como migrar seu aplicativo do Google Maps com estes artigos:

> [!div class="nextstepaction"]
> [Migrar um aplicativo Web](migrate-from-google-maps-web-app.md)
