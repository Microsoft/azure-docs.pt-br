---
title: Entender os relatórios de informações no Azure alcance
description: Este artigo explica quais informações estão no Azure alcance.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: d841fa336b2702a02f3215f97a6403217986d7e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96551562"
---
# <a name="understand-insights-in-azure-purview"></a>Noções básicas de Insights no Azure Purview

Este artigo fornece uma visão geral do recurso de informações no Azure alcance.

As informações são um dos pilares-chave de alcance. O recurso fornece aos clientes um único painel de visão de vidro em seu catálogo e tem mais objetivo fornecer informações específicas aos administradores de fonte de dados, usuários empresariais, administradores de dados, diretor de dados e administradores de segurança. Atualmente, o alcance tem os seguintes relatórios de informações que estarão disponíveis para os clientes na visualização pública.

## <a name="asset-insights"></a>Insights de ativos

Este relatório fornece uma visão geral do seu espaço de dados e sua distribuição por tipo de fonte, por classificação e por tamanho de arquivo como algumas das dimensões. Esse relatório atende a diferentes tipos de usuários que podem estar gerenciando a conta do alcance e executando verificações ou usuários empresariais que podem estar interessados em saber quantos ativos existem com uma determinada classificação dentro do espaço de dados de sua organização. 

O relatório fornece informações amplas por meio de grafos e KPIs, além de aprofundar-se em anomalias específicas, como arquivos em locais incorretos. O relatório também dá suporte a uma experiência de ponta a ponta do cliente, em que o cliente pode exibir a contagem de ativos com uma classificação específica, pode dividir as informações por tipos de origem e pastas principais e também pode exibir a lista de ativos para uma investigação mais aprofundada.

## <a name="scan-insights"></a>Examinar informações

O relatório permite que os administradores compreendam a integridade geral das verificações – quantas tiveram êxito, quantas falharam, quantas foram canceladas. Este relatório fornece uma atualização de status sobre as verificações que foram executadas na conta do alcance dentro de um período dos últimos sete dias ou nos últimos 30 dias.

O relatório também permite que os administradores se aprofundem e explorem quais verificações falharam e em quais tipos de origem específicos. Para permitir que os usuários investiguem, o relatório ajuda a navegar na página de histórico de verificação dentro da experiência de "origens".

## <a name="glossary-insights"></a>Ideias de Glossário

Este relatório fornece aos usuários de negócios e aos administradores de dados um relatório de status no Glossário. Os usuários podem exibir esse relatório para entender a distribuição de termos do glossário por status, saber quantos termos do glossário são anexados aos ativos e quantos ainda não estão anexados a nenhum ativo. Os usuários empresariais também podem aprender sobre a integridade de seus termos do glossário. 

Este relatório resume os principais itens em que um usuário ou administrador de dados corporativo precisa se concentrar para criar um glossário completo e utilizável para sua organização. Os usuários também podem navegar para a experiência de "Glossário" da experiência de "Glossário insights", para fazer alterações em um termo de Glossário específico.

## <a name="classification-insights"></a>Informações de classificação

Este relatório fornece detalhes sobre onde os dados classificados estão localizados, as classificações encontradas durante uma verificação e uma busca detalhada nos próprios arquivos classificados. Ele permite que os administradores de segurança compreendam os tipos de informações encontrados no espaço de dados da organização. 

No Azure alcance, as classificações são semelhantes às marcas de assunto e são usadas para marcar e identificar o conteúdo de um tipo específico em seu espaço de dados.

Use o relatório de informações de classificação para identificar o conteúdo com classificações específicas e entender as ações necessárias, como adicionar segurança adicional aos repositórios ou mover o conteúdo para um local mais seguro.

Para obter mais informações, consulte [percepções de classificação sobre seus dados do Azure alcance](classification-insights.md).

## <a name="sensitivity-labeling-insights"></a>Informações de rótulo de sensibilidade

Este relatório fornece detalhes sobre os rótulos de sensibilidade encontrados durante uma verificação, bem como uma busca detalhada dos próprios arquivos rotulados. Ele permite que os administradores de segurança assegurem a segurança das informações encontradas no espaço de dados da organização. 

No Azure alcance, os rótulos de sensibilidade são usados para identificar categorias de tipo de classificação, bem como as políticas de segurança de grupo que você deseja aplicar a cada categoria.

Use o relatório de informações de rótulos para identificar os rótulos de sensibilidade encontrados no seu conteúdo e entender as ações necessárias, como gerenciar o acesso a repositórios ou arquivos específicos.

Para saber mais, confira informações de [rótulo de sensibilidade sobre seus dados no Azure alcance](sensitivity-insights.md).

## <a name="file-extension-insights"></a>Informações de extensão de arquivo

Este relatório fornece detalhes sobre as extensões de arquivo, ou tipos de arquivo, encontrados durante uma verificação, bem como uma busca detalhada dos próprios arquivos. 

Use o relatório de informações de extensão de arquivo para entender quantos arquivos cada vez você tem, onde estão esses arquivos e se eles podem ser verificados quanto a dados confidenciais.

Para obter mais informações, consulte [File Extension percepções sobre seus dados do Azure alcance](file-extension-insights.md).

## <a name="next-steps"></a>Próximas etapas

* [Ideias de Glossário](glossary-insights.md)
* [Examinar informações](scan-insights.md)
* [Informações de classificação](./classification-insights.md)