---
title: Atualizar entidade composta-LUIS
description: Atualize a entidade composta para a entidade de aprendizado de máquina com o processo de atualização no portal do LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 46e9ece70d9f980065c719ee1205eb46591b45c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025235"
---
# <a name="upgrade-composite-entity-to-machine-learning-entity"></a>Atualizar entidade composta para entidade de aprendizado de máquina

Atualize a entidade composta para a entidade de aprendizado de máquina para criar uma entidade que receba previsões mais completas com melhor desdação para depurar a entidade.

## <a name="current-version-model-restrictions"></a>Restrições do modelo de versão atual

O processo de atualização cria entidades de aprendizado de máquina, com base nas entidades compostas existentes encontradas em seu aplicativo, em uma nova versão do seu aplicativo. Isso inclui as funções e filhos da entidade composta. O processo também alterna os rótulos no exemplo declarações para usar a nova entidade.

## <a name="upgrade-process"></a>Processo de atualização

O processo de atualização:
* Cria uma nova entidade de aprendizado de máquina para cada entidade composta.
* Entidades filho:
    * Se a entidade filho for usada somente na entidade composta, ela só será adicionada à entidade de aprendizado de máquina.
    * Se a entidade filho for usada em composição _e_ como uma entidade separada (rotulada no exemplo declarações), ela será adicionada à versão como uma entidade e como uma subentidade à nova entidade de aprendizado de máquina.
    * Se a entidade filho usar uma função, cada função será convertida em uma subentidade de mesmo nome.
    * Se a entidade filho for uma entidade que não seja de aprendizado de máquina (expressão regular, entidade de lista ou entidade predefinida), uma nova subentidade será criada com o mesmo nome e a nova subentidade terá um recurso que usa a entidade de aprendizagem não Machine com o recurso necessário adicionado.
* Os nomes são retidos, mas devem ser exclusivos no mesmo nível de subentidade/irmão. Consulte [os limites de nomenclatura exclusivos](./luis-limits.md#name-uniqueness).
* Os rótulos no exemplo declarações são alternados para a nova entidade de aprendizado de máquina com subentidades.

Use o gráfico a seguir para entender como seu modelo é alterado:

|Objeto antigo|Novo objeto|Observações|
|--|--|--|
|Entidade composta|entidade de aprendizado de máquina com estrutura|Ambos os objetos são objetos pai.|
|A entidade filho do composto é uma **entidade simples**|subentidade|Ambos os objetos são objetos filho.|
|A entidade filho do composto é uma **entidade predefinida** , como número|a subentidade com o nome da entidade predefinida, como número, e a subentidade tem _recurso_ de entidade de número predefinida com a opção de restrição definida como _true_.|a subentidade contém recurso com restrição no nível da subentidade.|
|A entidade filho do composto é uma **entidade predefinida** , como número, e a entidade predefinida tem uma **função**|a subentidade com o nome da função e a subentidade tem o recurso de entidade de número predefinido com a opção de restrição definida como true.|a subentidade contém recurso com restrição no nível da subentidade.|
|Função|subentidade|O nome da função se torna o nome da subentidade. A subentidade é um descendente direto da entidade de aprendizado de máquina.|

## <a name="begin-upgrade-process"></a>Iniciar processo de atualização

Antes de atualizar, certifique-se de:

* Altere as versões se você não estiver na versão correta para atualizar


1. Inicie o processo de atualização da notificação ou você pode aguardar até o próximo prompt agendado.

    > [!div class="mx-imgBorder"]
    > ![Iniciar atualização de notificações](./media/update-composite-entity/notification-begin-update.png)

1. No pop-up, selecione **Atualizar agora**.

1. Examine o **que acontece quando você atualiza** as informações e selecione **continuar**.

1. Selecione as entidades compostas da lista para atualizar e selecione **continuar**.

1. Você pode mover quaisquer alterações não treinadas da versão atual para a versão atualizada marcando a caixa de seleção.

1. Selecione **continuar** para iniciar o processo de atualização.

1. A barra de progresso indica o status do processo de atualização.

1. Quando o processo for concluído, você estará em uma nova versão treinada com as novas entidades de aprendizado de máquina. Selecione **experimentar as novas entidades** para ver a nova entidade.

    Se a atualização ou o treinamento falhar para a nova entidade, uma notificação fornecerá mais informações.

1. Na página lista de entidades, as novas entidades são marcadas com **novo** ao lado do nome do tipo.

## <a name="next-steps"></a>Próximas etapas

* [Autores e colaboradores](luis-how-to-collaborate.md)