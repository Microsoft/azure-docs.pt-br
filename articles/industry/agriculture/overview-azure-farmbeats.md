---
title: O que é o Azure FarmBeats
description: Fornece uma visão geral do Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: f173dfb055f3d9c75ee63570cd413276ffb6ba37
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182818"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Visão geral do Azure FarmBeats (versão prévia)

O Azure FarmBeats é uma oferta entre empresas disponível no Azure Marketplace. Ele habilita a agregação de conjuntos de dados de agricultura entre provedores. O Azure FarmBeats permite que você crie modelos de IA (inteligência artificial) ou de ML (machine learning) com base em conjuntos de dados combinados. Usando o Azure FarmBeats, as empresas agricultura podem se concentrar nos principais valores agregados em vez de no trabalho pesado não diferenciado de engenharia de dados.

> [!NOTE]
> O Azure FarmBeats está atualmente em versão prévia pública. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). O Azure FarmBeats é fornecido sem um contrato de nível de serviço. Use o [Fórum de suporte do Azure FarmBeats](/answers/topics/azure-farmbeats.html) para obter suporte.

![Projeto do FarmBeats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Com a versão prévia do Azure FarmBeats, você pode:

- Avaliar a integridade da fazenda usando os índices de vegetação e de água baseados em imagens de satélite.
- Obter recomendações sobre quantos sensores de umidade do solo devem ser usados e onde posicioná-los.
- Controlar as condições da fazenda visualizando os dados do terreno coletados de sensores de vários fornecedores.
- Obter o mapa de umidade do solo com base em dados unificados de sensores e satélites.
- Obter insights acionáveis ao complicar modelos de IA/ML usando conjuntos de dados agregados.
- Compilar ou aumentar a solução digital agrícola ao fornecer consultorias de integridade sobre a fazenda.

## <a name="datahub"></a>Datahub

O Datahub do Azure FarmBeats é uma camada de API que permite a agregação, a normalização e a contextualização de vários conjuntos de dados agrícolas entre provedores. Você pode usar o Azure FarmBeats para obter:
- **Dados do sensor** de dois provedores de sensores, [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), [Pessl Instruments](https://metos.at/)
- **Imagens satélite** da missão de satélite [Sentinel-2](https://sentinel.esa.int/web/sentinel/home) da Agência Espacial Europeia
- **Imagens de drone** de três provedores de imagens de drone, [senseFly](https://www.sensefly.com/), [SlantRange](https://slantrange.com/) e [DJI](https://dji.com/)

O Datahub é projetado como uma plataforma de API extensível. Estamos trabalhando com muitos outros provedores para integrar com o Azure FarmBeats, assim você terá mais opções ao criar sua solução.

## <a name="accelerator"></a>Acelerador

O Acelerador do Azure FarmBeats é um aplicativo Web de exemplo criado com base no Datahub. O Acelerador impulsiona seu desenvolvimento de modelo e interface do usuário. O Acelerador do Azure FarmBeats usa as APIs do Azure FarmBeats. Ele visualiza os dados de sensor ingeridos como gráficos e saídas de modelo como mapas. Por exemplo, você pode usar o Acelerador para criar rapidamente uma fazenda e obter facilmente um mapa de índice da vegetação ou um mapa de posicionamento de sensores para essa fazenda.

## <a name="azure-role-based-access-control-azure-rbac"></a>RBAC do Azure (controle de acesso baseado em função do Azure)

Um administrador pode definir regras de acesso para o Azure FarmBeats usando uma das funções predefinidas. As funções determinam a quais áreas do aplicativo um usuário tem acesso e quais ações ele pode executar. Há dois tipos de funções no Azure FarmBeats – para usuários e parceiros.

### <a name="user-roles"></a>Funções de usuário

Um [administrador pode adicionar e gerenciar usuários](manage-users-in-azure-farmbeats.md) e definir os níveis de acesso deles com base em duas funções de usuário: Administrador e Somente leitura.

### <a name="partner-roles"></a>Funções de Parceiro

Um administrador pode adicionar vários parceiros como provedores de dados ao Azure FarmBeats. Os seguintes itens resumem as funções de parceiro disponíveis no FarmBeats e as permissões delas:

| Tipo de Parceiro    |   Ações  | Escopo |
| ---- | -------- | -------- |
| Parceiro de Sensor  |   Criar, Ler, Atualizar <br/> <br/> Ler, Atualizar | DeviceModel, Device, SensorModel, Sensor <br/> <br/> ExtendedType |
| Parceiro de Imagens  |   Criar, Ler, Atualizar <br/> <br/> Ler, Atualizar <br/> <br/> Ler | Scene, SceneFile <br/> <br/> ExtendedType <br/> <br/> Farm |
| Parceiro Meteorológico* <br/> <br/>  (* Em breve) |   Criar, Ler, Atualizar <br/> <br/> Ler, Atualizar <br/> <br/> Ler | WeatherDataModel, WeatherDataLocation, JobType <br/> <br/> ExtendedType <br/> <br/> Farm |

## <a name="resources"></a>Recursos

O Azure FarmBeats é oferecido sem custo adicional e você paga apenas pelos recursos do Azure que você usa. Você pode usar os recursos abaixo para saber mais sobre a oferta:

- Mantenha-se informado sobre as últimas notícias do Azure FarmBeats visitando o [blog do Azure FarmBeats](https://aka.ms/farmbeatsblog).
- Busque ajuda postando uma pergunta no [Fórum de suporte do Azure FarmBeats](/answers/topics/azure-farmbeats.html).
- Forneça comentários postando ou votando em uma ideia de recurso no [Fórum de comentários do Azure FarmBeats](https://aka.ms/farmbeatsfeedback).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Instalar o Azure FarmBeats](install-azure-farmbeats.md)