---
title: Camadas de dados e nós de extensão para SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Camadas de dados e nós de extensão para SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a292efc3e660379325ccb6870e540e38c6cdd5e9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709663"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Usar os nós de extensão e camadas de dados do SAP HANA

O SAP dá suporte a um modelo de camadas de dados para SAP BW de diferentes versões do SAP NetWeaver e SAP BW/4HANA. Para obter mais informações sobre o modelo de camadas de dados, consulte o documento do SAP [SAP BW/4HANA e SAP BW no HANA com nós de extensão do SAP HANA ](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Com o SAP HANA em Instâncias Grandes, é possível usar a configuração da opção 1 dos nós de extensão do SAP HANA, conforme explicado nos documentos de perguntas frequentes e no blog do SAP. Configurações de opção 2 podem ser configuradas com os seguintes SKUs do HANA grande instância: S72m, S192, S192m, S384 e S384m. 

Ao examinar a documentação, a vantagem pode não ser aparente à primeira vista. Mas quando você examina para as diretrizes de dimensionamento do SAP, é possível ver uma vantagem usando os nós de extensão da opção 1 e da opção 2 do SAP HANA. A seguir, são apresentados alguns exemplos:

- Diretrizes de dimensionamento do SAP HANA geralmente exigem duas vezes mais volume de dados do que memória. Ao executar a instância do SAP HANA com os dados ativos, você terá apenas 50% ou menos da memória preenchida com dados. Idealmente, o restante da memória é mantido para o SAP HANA fazer seu trabalho.
- Isso significa que, em uma unidade de instância do HANA grande S192 com 2 TB de memória executando um banco de dados do SAP BW, você tem apenas 1 TB como volume de dados.
- Se usar um nó de extensão SAP HANA adicional da opção 1, bem como uma SKU do SAP HANA em Instâncias Grandes S192, ele oferece uma capacidade adicional de 2 TB para o volume de dados. Na configuração de opção 2, oferece até mesmo 4 TB adicionais para o volume de dados mornos. Comparado ao nó ativo, a capacidade total de memória do nó de extensão "passivo" pode ser usada para armazenamento de dados para a opção 1. O dobro da memória pode ser usado para o volume de dados na configuração do nó de extensão da opção 2 do SAP HANA.
- Você terminará com uma capacidade de 3 TB dos dados e uma taxa de ativo para passivo de 1: 2 para a opção 1. Você tem 5 TB de dados e uma taxa de 1: 4 com a configuração do nó de extensão da opção 2.

Quanto maior o volume de dados em comparação com a memória, maiores as chances de que os dados passivos solicitados sejam armazenados no armazenamento em disco.

**Próximas etapas**
- Confira [Arquitetura do SAP HANA (Instâncias Grandes) no Azure](hana-architecture.md)