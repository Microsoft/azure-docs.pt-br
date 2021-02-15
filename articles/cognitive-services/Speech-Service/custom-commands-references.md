---
title: Conceitos e definições de comandos personalizados – serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá sobre os conceitos e definições de aplicativos de comandos personalizados.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 98510132b2341736664dfafa52e9567df95652be
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561090"
---
# <a name="custom-commands-concepts-and-definitions"></a>Conceitos e definições de comandos personalizados

Este artigo serve como referência para conceitos e definições de aplicativos de comandos personalizados.

## <a name="commands-configuration"></a>Configuração de comandos
Os comandos são os blocos de construção básicos de um aplicativo de comandos personalizados. Um comando é um conjunto de configurações necessárias para concluir uma tarefa específica definida por um usuário.

### <a name="example-sentences"></a>Sentenças de exemplo
Exemplo declarações são os exemplos de conjunto que o usuário pode dizer para disparar um comando específico. Você precisa fornecer apenas uma amostra de declarações e não uma lista exaustiva. 

### <a name="parameters"></a>Parâmetros
Os parâmetros são informações requeridas pelos comandos para concluir uma tarefa. Em cenários complexos, os parâmetros também podem ser usados para definir condições que disparam ações personalizadas.

### <a name="completion-rules"></a>Regras de conclusão
As regras de conclusão são uma série de regras a serem executadas depois que o comando estiver pronto para ser cumprido, por exemplo, quando todas as condições das regras forem satisfeitas.

### <a name="interaction-rules"></a>Regras de interação
Regras de interação são regras adicionais para lidar com situações mais específicas ou complexas. Você pode adicionar validações adicionais ou configurar recursos avançados, como confirmações ou uma correção de uma etapa. Você também pode criar suas próprias regras de interação personalizadas.

## <a name="parameters-configuration"></a>Configuração de parâmetros

Os parâmetros são informações requeridas por comandos para concluir uma tarefa. Em cenários complexos, os parâmetros também podem ser usados para definir condições que disparam ações personalizadas.

### <a name="name"></a>Nome
Um parâmetro é identificado pela propriedade Name. Você sempre deve fornecer um nome descritivo para um parâmetro. Um parâmetro pode ser referenciado em diferentes seções, por exemplo, quando você constrói condições, respostas de fala ou outras ações.
 
### <a name="isglobal"></a>Caso IsGlobal
Essa caixa de seleção indica se o escopo desse parâmetro é compartilhado entre todos os comandos no aplicativo. Se um parâmetro for global, seu valor poderá ser potencialmente fornecido de qualquer escopo de comando. Depois que um valor é atribuído, ele pode ser referenciado de qualquer um dos comandos. 

### <a name="required"></a>Obrigatório
Essa caixa de seleção indica se um valor para esse parâmetro é necessário para preenchimento ou conclusão de comando. Você deve configurar as respostas para solicitar que o usuário forneça um valor se um parâmetro for marcado como obrigatório.

Observe que, se você configurou um **parâmetro obrigatório** para ter um **valor padrão** , o sistema ainda solicitará explicitamente o valor do parâmetro.

### <a name="type"></a>Tipo
Os comandos personalizados oferecem suporte aos seguintes tipos de parâmetro:

* DateTime
* painel Geografia do app&#39;s selecionado
* Número
* Cadeia de caracteres

Todos esses tipos de parâmetro, exceto a geografia, dão suporte à configuração de valor padrão, que pode ser configurada no Portal.

### <a name="configuration"></a>Configuração
A configuração é uma propriedade de parâmetro definida somente para a cadeia de caracteres de tipo. Os seguintes valores têm suporte:

* **None**.
* **Aceitar entrada completa** : Quando habilitada, um parâmetro aceita qualquer expressão de entrada. Essa opção é útil quando o usuário precisa de um parâmetro com o expressão completo. Um exemplo são os endereços postais.
* **Aceitar valores de entrada predefinidos de um catálogo externo** : esse valor é usado para configurar um parâmetro que pode assumir uma grande variedade de valores. Um exemplo é um catálogo de vendas. Nesse caso, o catálogo é hospedado em um ponto de extremidade da Web externo e pode ser configurado de forma independente.
* **Aceitar valores de entrada predefinidos do catálogo interno** : esse valor é usado para configurar um parâmetro que pode assumir alguns valores. Nesse caso, os valores devem ser configurados no Speech Studio.


### <a name="validation"></a>Validação
Validações são construções aplicáveis a determinados tipos de parâmetro que permitem configurar restrições no valor de um parâmetro. Atualmente, os comandos personalizados oferecem suporte a validações nos seguintes tipos de parâmetro:

* DateTime
* Número

## <a name="rules-configuration"></a>Configuração de regras
Uma regra em comandos personalizados é definida por um conjunto de *condições* que, quando atendidas, executam um conjunto de *ações*. As regras também permitem que você configure o *estado de pós-execução* e as *expectativas* para a próxima vez.

### <a name="types"></a>Tipos
Os comandos personalizados oferecem suporte às seguintes categorias de regra:

* **Regras de conclusão** : essas regras devem ser executadas após o preenchimento do comando. Todas as regras configuradas nesta seção para as quais as condições são verdadeiras serão executadas. 
* **Regras de interação** : essas regras podem ser usadas para configurar validações personalizadas adicionais, confirmações e uma correção de uma etapa ou para realizar qualquer outra lógica de caixa de diálogo personalizada. As regras de interação são avaliadas em cada ativação no processamento e podem ser usadas para disparar regras de conclusão.

As diferentes ações configuradas como parte de uma regra são executadas na ordem em que aparecem no portal de criação.

### <a name="conditions"></a>Condições
As condições são os requisitos que devem ser atendidos para que uma regra seja executada. As condições de regras podem ser dos seguintes tipos:

* **Valor do parâmetro igual** a: o valor do parâmetro configurado é igual a um valor específico.
* **Nenhum valor de parâmetro** : os parâmetros configurados não devem ter nenhum valor.
* **Parâmetros obrigatórios** : o parâmetro configurado tem um valor.
* **Todos os parâmetros obrigatórios** : todos os parâmetros que foram marcados como obrigatórios têm um valor.
* **Parâmetros atualizados** : um ou mais valores de parâmetro foram atualizados como resultado do processamento da entrada atual (expressão ou Activity).
* A **confirmação foi bem-sucedida** : a expressão de entrada ou a atividade foi uma confirmação bem-sucedida (Sim).
* A **confirmação foi negada** : a expressão de entrada ou a atividade não foi uma confirmação bem-sucedida (não).
* O **comando anterior precisa ser atualizado** : essa condição é usada em instâncias quando você deseja capturar uma confirmação negada juntamente com uma atualização. Nos bastidores, essa condição é configurada para quando o mecanismo de caixa de diálogo detecta uma confirmação negativa em que a intenção é a mesma que a de ativação anterior, e o usuário respondeu com uma atualização.

### <a name="actions"></a>Ações
* **Enviar resposta de fala** : Enviar uma resposta de fala de volta ao cliente.
* **Atualizar valor do parâmetro** : Atualize o valor de um parâmetro de comando para um valor especificado.
* **Limpar valor do parâmetro** : Limpe o valor do parâmetro de comando.
* **Chamar ponto de extremidade da Web** : faça uma chamada para um ponto de extremidade da Web.
* **Enviar atividade para o cliente** : Enviar uma atividade personalizada para o cliente.

### <a name="expectations"></a>As
As expectativas são usadas para configurar dicas para o processamento da próxima entrada do usuário. Os seguintes tipos têm suporte:

* **Esperando confirmação do usuário** : essa expectativa especifica que o aplicativo está esperando uma confirmação (Sim/não) para a próxima entrada do usuário.
* **Esperando entrada (ões) parâmetro (s) do usuário** : essa expectativa especifica um ou mais parâmetros de comando que o aplicativo está esperando da entrada do usuário.

### <a name="post-execution-state"></a>Estado de pós-execução
O estado de pós-execução é o estado da caixa de diálogo após o processamento da entrada atual (expressão ou Activity). Eles são dos seguintes tipos:

* **Manter estado atual** : manter apenas o estado atual.
* **Conclua o comando** : conclua o comando e nenhuma regra adicional do comando será processada.
* **Executar regras de conclusão** : execute todas as regras de conclusão válidas.
* **Aguardar a entrada do usuário** : Aguarde a próxima entrada do usuário.



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Consulte os exemplos no GitHub](https://aka.ms/speech/cc-samples)
