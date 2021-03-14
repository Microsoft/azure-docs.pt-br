---
title: Arquitetura do Azure FarmBeats
description: Descreve a arquitetura do Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: ebc1cdc7c762117851072037624f11e9fa98b6f1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182597"
---
# <a name="integration-patterns"></a>Padrões de integração

O Azure FarmBeats é uma oferta entre empresas, disponível no Azure Marketplace. O FarmBeats permite a agregação de conjuntos de dados de agricultura entre provedores e a geração de informações acionáveis criando modelos de ia (inteligência artificial) ou Machine Learning (ML), combinando os conjuntos de dados.

![Projeto do FarmBeats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

As seções a seguir descrevem o padrão de integração do Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Por que integrar com o Azure FarmBeats?

Esta seção se concentra em parceiros que desejam integrar seus sistemas de dados (como sensores, drones, climas) ao FarmBeats do Azure.

O Azure FarmBeats é uma oferta extensível, que permite que agricultural empresas adicionem seus conjuntos de agricultural de histórico e em tempo real diferentes em uma única plataforma. O Azure FarmBeats ajuda uma empresa agricultural a normalizar, Contextualize e agregar seus dados no contexto de um farm.

Ao se tornar um parceiro de dados com o Azure FarmBeats, você pode abrir seus sistemas para uma adoção mais ampla e entrar em mais clientes com suas ofertas de dados. O Azure FarmBeats fornece uma camada de API extensível chamada Datahub, que ajuda você a ingerir dados de seus dispositivos sistematicamente e em um esquema padronizado.

Depois que os dados estiverem disponíveis na instância do Azure FarmBeats de seus clientes, seus clientes poderão criar análises e ferramentas mais avançadas sobre seus dados.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a integração de dados de sensor, consulte [integração de dados de sensor](sensor-partner-integration-in-azure-farmbeats.md) e para a integração de parceiros de imagens, consulte imagens de integração de [parceiros](imagery-partner-integration-in-azure-farmbeats.md).
