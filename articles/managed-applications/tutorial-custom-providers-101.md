---
title: Criar ações e recursos personalizados no Azure
description: Este tutorial apresentará como criar ações e recursos personalizados no Azure Resource Manager e como integrá-los a fluxos de trabalho personalizados para Modelos do Azure Resource Manager, CLI do Azure, Azure Policy e Log de Atividades.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 4bbfcf070611e3df5c0fe47070f2ab6961111e07
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799185"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Criar ações e recursos personalizados no Azure

Provedores personalizados permitem que você personalize os fluxos de trabalho no Azure. Um provedor personalizado é um contrato entre o Azure e um `endpoint`. Ele permite adicionar novas APIs personalizadas ao Azure Resource Manager para habilitar novas funcionalidades de gerenciamento e implantação. Este tutorial apresentará um exemplo simples de como adicionar novas ações e recursos ao Azure e como integrá-los.

Este tutorial é dividido nas seguintes etapas:

- Configurar o Azure Functions para provedores personalizados do Azure
- Como criar um ponto de extremidade RESTful para provedores personalizados
- Criar e utilizar o provedor personalizado

## <a name="setup-azure-functions-for-azure-custom-providers"></a>Configurar o Azure Functions para provedores personalizados do Azure

Esta parte do tutorial apresentará detalhes sobre como configurar uma Função do Azure para funcionar com provedores personalizados. Provedores personalizados podem funcionar com qualquer URL pública.

- [Configurar o Azure Functions para provedores personalizados do Azure](./tutorial-custom-providers-function-setup.md)

## <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Como criar um ponto de extremidade RESTful para provedores personalizados

Esta parte do tutorial apresentará detalhes sobre a criação de um ponto de extremidade RESTful para provedores personalizados.

- [Como criar um ponto de extremidade RESTful para provedores personalizados](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-and-utilizing-the-custom-provider"></a>Criar e utilizar o provedor personalizado

Esta parte do tutorial apresentará detalhes sobre como criar um provedor personalizado e usar suas ações e seus recursos personalizados.

- [Criar e utilizar um Provedor Personalizado do Azure](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, aprendemos sobre provedores personalizados e como criar um. Para prosseguir para a próxima etapa do tutorial:

- [Tutorial: Configurar o Azure Functions para provedores personalizados do Azure](./tutorial-custom-providers-function-setup.md)

Se você estiver procurando referências ou um guia de início rápido, aqui estão alguns links úteis:

- [Início Rápido: Criar Provedor de Recursos Personalizado do Azure e implantar recursos personalizados](./create-custom-provider.md)
- [Como: adicionar ações personalizadas à API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Como: adicionar recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
