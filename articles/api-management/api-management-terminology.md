---
title: Terminologia do Gerenciamento de API do Azure | Microsoft Docs
description: Este artigo fornece definições para termos que são específicos para o Gerenciamento de API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: b99ca444532799d21850058eae0e3f40ed871135
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074047"
---
# <a name="terminology"></a>Terminologia

Este artigo fornece definições para termos que são específicos para o APIM (Gerenciamento de API).

## <a name="term-definitions"></a>Definições de termos

* **API de back-end** – Um serviço HTTP que implementa sua API e suas operações. 
* **API de front-end**/**API de APIM** – Uma API de APIM não hospeda APIs, ela cria fachadas para suas APIs para personalizar a fachada de acordo com suas necessidades sem tocar na API de back-end. Para obter mais informações, consulte [Importar e publicar uma API](import-and-publish.md).
* **Produto de APIM** – Um produto contém uma ou mais APIs, bem como uma cota de uso e os termos de uso. Você pode incluir várias APIs e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. Para obter mais informações, consulte [Criar e publicar um produto](api-management-howto-add-products.md).
* **Operação da API de APIM** – Cada API de APIM representa um conjunto de operações disponíveis para desenvolvedores. Cada API de APIM contém uma referência para serviço back-end que implementa a API, e suas operações são mapeadas para as operações implementadas pelo serviço de back-end. Para obter mais informações, consulte [Simular respostas de API](mock-api-responses.md).
* **Versão** – Às vezes, você deseja publicar recursos novos ou diferentes da API para alguns usuários, enquanto outros preferem continuar com a API que funciona no momento para eles. Para obter mais informações, consulte [Publicar várias versões de sua API](api-management-get-started-publish-versions.md).
* **Revisão** – Quando sua API estiver pronta e começar a ser usada por desenvolvedores, será necessário tomar cuidado com as alterações feitas nessa API e ao mesmo tempo não interromper os chamadores de sua API. Também é útil permitir que os desenvolvedores saibam sobre as alterações feitas por você. Para obter mais informações, consulte [Usar revisões](api-management-get-started-revise-api.md).
* **Portal do desenvolvedor** – Seus clientes (desenvolvedores) devem usar o Portal do desenvolvedor para acessar suas APIs. O Portal do desenvolvedor pode ser personalizado. Para obter mais informações, consulte [Personalizar o Portal do desenvolvedor](api-management-customize-styles.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma instância](get-started-create-service-instance.md)

