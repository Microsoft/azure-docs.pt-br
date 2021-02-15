---
title: Criando Azure Functions para entrada idêntica
description: Criando Azure Functions para serem idempotentes
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.openlocfilehash: 15af60ac5a862e6fb20e65ba6fbb92482420b7c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74226867"
---
# <a name="designing-azure-functions-for-identical-input"></a>Criando Azure Functions para entrada idêntica

A realidade da arquitetura baseada em eventos e orientada a mensagens determina a necessidade de aceitar solicitações idênticas, preservando a integridade dos dados e a estabilidade do sistema.

Para ilustrar, considere um botão de chamada de elevador. À medida que você pressiona o botão, ele acende e um elevador é enviado para o andar. Alguns instantes depois, outra pessoa ingressa no lobby. Essa pessoa sorrisos em você e pressiona o botão iluminado uma segunda vez. Você vai fazer um sorriso e Chuckle como você está lembrado de que o comando para chamar um elevador é idempotente.

Pressionar um botão de chamada de elevador uma segunda, terceira ou quarta vez não tem nenhuma influência sobre o resultado final. Quando você pressiona o botão, independentemente do número de vezes, o elevador é enviado para o andar. Os sistemas idempotentes, como o elevador, resultam no mesmo resultado, não importa quantas vezes comandos idênticos são emitidos.

Quando se trata de criar aplicativos, considere os seguintes cenários:

- O que acontece se o seu aplicativo de controle de inventário tentar excluir o mesmo produto mais de uma vez?
- Como o aplicativo de recursos humanos se comporta se há mais de uma solicitação para criar um registro de funcionário para a mesma pessoa?
- Onde o dinheiro vai se seu aplicativo bancário receber 100 solicitações para fazer a mesma retirada?

Há muitos contextos em que as solicitações para uma função podem receber comandos idênticos. Algumas situações incluem:

- Políticas de repetição enviando a mesma solicitação muitas vezes
- Comandos em cache reproduzidos para o aplicativo
- Erros de aplicativo enviando várias solicitações idênticas

Para proteger a integridade dos dados e a integridade do sistema, um aplicativo idempotente contém uma lógica que pode conter os seguintes comportamentos:

- Verificando a existência de dados antes de tentar executar uma exclusão
- Verificando se os dados já existem antes de tentar executar uma ação de criação
- Reconciliação de lógica que cria consistência eventual nos dados
- Controles de simultaneidade
- Detecção de duplicação
- Validação de atualização de dados
- Lógica de proteção para verificar os dados de entrada

Em última instância, o Idempotência é obtido garantindo que uma determinada ação seja possível e seja executada apenas uma vez.
