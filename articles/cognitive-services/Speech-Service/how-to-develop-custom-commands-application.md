---
title: 'Instruções: desenvolver aplicativos de comandos personalizados – serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como desenvolver e personalizar aplicativos de comandos personalizados. Esses aplicativos de comando de voz são mais adequados para os cenários de conclusão de tarefas ou de comando e controle.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 1a002b6efbe2603ae254c19f9e3cc7377198cea2
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935811"
---
# <a name="develop-custom-commands-applications"></a>Desenvolver aplicativos de comandos personalizados

Neste artigo de instruções, você aprende a desenvolver e configurar aplicativos de comandos personalizados. O recurso de comandos personalizados ajuda a criar aplicativos avançados de comando de voz que são otimizados para experiências de interação de voz primeiro. O recurso é mais adequado para os cenários de conclusão de tarefas ou de comando e controle. Ele é particularmente adequado para dispositivos de Internet das Coisas (IoT) e para dispositivos de ambiente e sem periféricos.

Neste artigo, você cria um aplicativo que pode ativar e desativar uma TV, definir a temperatura e definir um alarme. Depois de criar esses comandos básicos, você aprenderá sobre as seguintes opções para personalizar comandos:

* Adicionando parâmetros a comandos
* Adicionando configurações a parâmetros de comando
* Criando regras de interação
* Criando modelos de geração de linguagem para respostas de fala
* Usando ferramentas de voz personalizadas

## <a name="create-an-application-by-using-simple-commands"></a>Criar um aplicativo usando comandos simples

Comece criando um aplicativo de comandos personalizados vazio. Para obter detalhes, consulte o guia de [início rápido](quickstart-custom-commands-application.md). Nesse aplicativo, em vez de importar um projeto, você cria um projeto em branco.

1. Na caixa **nome** , insira o nome do projeto *Smart-Room-Lite* (ou outro nome de sua escolha).
1. Na lista **idioma** , selecione **Inglês (Estados Unidos)**.
1. Selecione ou crie um recurso LUIS.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela mostrando a janela "novo projeto".](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Atualizar recursos do LUIS (opcional)

Você pode atualizar o recurso de criação que você selecionou na janela **novo projeto** . Você também pode definir um recurso de previsão. 

Um recurso de previsão é usado para reconhecimento quando o aplicativo de comandos personalizados é publicado. Você não precisa de um recurso de previsão durante as fases de desenvolvimento e teste.

### <a name="add-a-turnon-command"></a>Adicionar um comando de ativação

No aplicativo de comandos personalizados do espaço inteligente vazio-Lite que você criou, adicione um comando. O comando processará um expressão, `Turn on the tv` . Ele responderá com a mensagem `Ok, turning the tv on` .

1. Crie um novo comando selecionando **novo comando** na parte superior do painel esquerdo. A **nova** janela de comando é aberta.
1. Para o campo **nome** , forneça o valor `TurnOn` .
1. Selecione **Criar**.

O painel central lista as propriedades do comando. 

A tabela a seguir explica as propriedades de configuração do comando. Para obter mais informações, consulte [definições e conceitos de comandos personalizados](./custom-commands-references.md).

| Configuração            | Descrição                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Sentenças de exemplo | Exemplo declarações o usuário pode dizer para disparar esse comando.                                                                 |
| Parâmetros       | Informações necessárias para concluir o comando.                                                                                |
| Regras de conclusão | Ações a serem tomadas para atender ao comando. Exemplos: respondendo ao usuário ou se comunicando com um serviço Web. |
| Regras de interação   | Outras regras para lidar com situações mais específicas ou complexas.                                                              |


> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando onde criar um comando.](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Adicionar sentenças de exemplo

Na seção **sentenças de exemplo** , você fornece um exemplo do que o usuário pode dizer.

1. No painel central, selecione **sentenças de exemplo**.
1. No painel à direita, Adicione exemplos:

    ```
    Turn on the tv
    ```

1.  Na parte superior do painel, selecione **Salvar**.

Você ainda não tem parâmetros, portanto, você pode ir para a seção **regras de conclusão** .

#### <a name="add-a-completion-rule"></a>Adicionar uma regra de conclusão

Em seguida, o comando precisa de uma regra de conclusão. Essa regra informa ao usuário que uma ação de preenchimento está sendo executada. 

Para obter mais informações sobre regras e regras de conclusão, consulte [definições e conceitos de comandos personalizados](./custom-commands-references.md).

1. Selecione a regra de conclusão padrão **concluída**. Em seguida, edite-o da seguinte maneira:

    
    | Configuração    | Valor sugerido                          | Descrição                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Nome**       | `ConfirmationResponse`                  | Um nome que descreve a finalidade da regra          |
    | **Condições** | Nenhum                                     | Condições que determinam quando a regra pode ser executada    |
    | **Ações**    | **Enviar resposta**  >  de fala **Editor simples**  >  **Primeira variação** > `Ok, turning the tv on` | A ação a ser tomada quando a condição da regra for verdadeira |

   > [!div class="mx-imgBorder"]
   > ![Captura de tela mostrando onde criar uma resposta de fala.](media/custom-commands/create-speech-response-action.png)

1. Selecione **salvar** para salvar a ação.
1. De volta à seção **regras de conclusão** , selecione **salvar** para salvar todas as alterações. 

    > [!NOTE]
    > Você não precisa usar a regra de conclusão padrão que vem com o comando. Você pode excluir a regra de conclusão padrão e adicionar sua própria regra.

### <a name="add-a-settemperature-command"></a>Adicionar um comando settemperaturas

Agora, adicione mais um comando, `SetTemperature` . Esse comando usará um único expressão, `Set the temperature to 40 degrees` e responderá com a mensagem `Ok, setting temperature to 40 degrees` .

Para criar o novo comando, siga as etapas usadas para o `TurnOn` comando, mas use a sentença de exemplo `Set the temperature to 40 degrees` .

Em seguida, edite as regras de conclusão **realizadas** existentes da seguinte maneira:

| Configuração    | Valor sugerido                          |
| ---------- | ---------------------------------------- |
| **Nome**  | `ConfirmationResponse`                  |
| **Condições** | Nenhum                                     |
| **Ações**    | **Enviar resposta**  >  de fala **Editor simples**  >  **Primeira variação** > `Ok, setting temperature to 40 degrees` |

Selecione **salvar** para salvar todas as alterações no comando.

### <a name="add-a-setalarm-command"></a>Adicionar um comando setAlarm

Crie um novo `SetAlarm` comando. Use a sentença de exemplo `Set an alarm for 9 am tomorrow` . Em seguida, edite as regras de conclusão **realizadas** existentes da seguinte maneira:

| Configuração    | Valor sugerido                          |
| ---------- | ---------------------------------------- |
| **Nome**  | `ConfirmationResponse`                  |
| **Condições** | Nenhum                                     |
| **Ações**    | **Enviar resposta**  >  de fala **Editor simples**  >  **Primeira variação** > `Ok, setting an alarm for 9 am tomorrow` |

Selecione **salvar** para salvar todas as alterações no comando.

### <a name="try-it-out"></a>Experimente

Teste o comportamento do aplicativo usando o painel de teste: 

1. No canto superior direito do painel, selecione o ícone de **treinamento** . 
1. Quando o treinamento for concluído, selecione **testar**. 

Experimente os exemplos de expressão a seguir usando voz ou texto:

- Digite: *defina a temperatura como 40 graus*
- Resposta esperada: Ok, definindo a temperatura como 40 graus
- Você digita: *ativar a TV*
- Resposta esperada: Ok, ativando a TV
- Você digita: *definir um alarme para 9h, amanhã*
- Resposta esperada: Ok, configurando um alarme para 9h, amanhã

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando o teste em uma interface de chat Web.](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> No painel de teste, você pode selecionar **Ativar detalhes** para obter informações sobre como essa entrada de voz ou entrada de texto foi processada.

## <a name="add-parameters-to-commands"></a>Adicionar parâmetros a comandos

Nesta seção, você aprenderá a adicionar parâmetros aos seus comandos. Os comandos exigem parâmetros para concluir uma tarefa. Em cenários complexos, os parâmetros podem ser usados para definir condições que disparam ações personalizadas.

### <a name="configure-parameters-for-a-turnon-command"></a>Configurar parâmetros para um comando de ativação

Comece editando o `TurnOn` comando existente para ativar e desativar vários dispositivos.

1. Agora que o comando tratará os cenários on e off, renomeie o comando como *TurnOnOff*.
   1. No painel à esquerda, selecione o comando **Ativar** . Em seguida, ao lado do **comando novo** na parte superior do painel, selecione o botão de reticências (**...**).
   
   1. Selecione **renomear**. Na janela de **comando renomear** , altere o nome para *TurnOnOff*.

1. Adicione um novo parâmetro ao comando. O parâmetro representa se o usuário deseja ativar ou desativar o dispositivo.
   1. Na parte superior do painel central, selecione  **Adicionar**. No menu suspenso, selecione **parâmetro**.
   1. No painel à direita, na seção **parâmetros** , na caixa **nome** , adicione `OnOff` .
   1. Selecione **obrigatório**. Na janela **Adicionar resposta para um parâmetro necessário** , selecione **editor simples**. No primeiro campo de **variação** , adicionar *ativado ou desativado?*.
   1. Selecione **Atualizar**.

       > [!div class="mx-imgBorder"]
       > ![Captura de tela que mostra a seção ' Adicionar resposta para um parâmetro obrigatório ' com a guia ' editor simples ' selecionada.](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Configure as propriedades do parâmetro usando a tabela a seguir. Para obter informações sobre todas as propriedades de configuração de um comando, consulte [definições e conceitos de comandos personalizados](./custom-commands-references.md).
      

       | Configuração      | Valor sugerido     | Descrição                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **Nome**               | `OnOff`           | Um nome descritivo para o parâmetro                                                                           |
       | **É global**          | Não selecionado       | Caixa de seleção que indica se um valor para esse parâmetro é aplicado globalmente a todos os comandos no aplicativo.|
       | **Necessária**           | Selecionada         | Caixa de seleção que indica se um valor para esse parâmetro é necessário antes de o comando ser concluído. |
       | **Resposta para o parâmetro obrigatório**      |**Editor simples** > `On or Off?`      | Um prompt solicitando o valor desse parâmetro quando ele não for conhecido. |
       | **Tipo**               | **Cadeia de caracteres**          | Tipo de parâmetro, como número, Cadeia de caracteres, data/hora ou geografia.   |
       | **Configuration**      | **Aceitar valores de entrada predefinidos de um catálogo interno** | Para cadeias de caracteres, essa configuração limita as entradas a um conjunto de valores possíveis. |
       | **Valores de entrada predefinidos**     | `on`, `off`           | Conjunto de valores possíveis e seus aliases.         |
       
        
   1. Para adicionar valores de entrada predefinidos, selecione **Adicionar uma entrada predefinida**. Na janela **novo item**  , digite o *nome* , conforme mostrado na tabela anterior. Nesse caso, você não está usando aliases, portanto, você pode deixar esse campo em branco.
   
      > [!div class="mx-imgBorder"]
      > ![Captura de tela mostrando como criar um parâmetro.](media/custom-commands/create-on-off-parameter.png)

   1. Selecione **salvar** para salvar todas as configurações do parâmetro.
 
#### <a name="add-a-subjectdevice-parameter"></a>Adicionar um parâmetro SubjectDevice

1. Para adicionar um segundo parâmetro para representar o nome dos dispositivos que podem ser controlados usando esse comando, selecione **Adicionar**. Use a configuração a seguir.


    | Configuração            | Valor sugerido       |
    | ------------------ | --------------------- |
    | **Nome**               | `SubjectDevice`         |
    | **É global**          | Não selecionado             |
    | **Necessária**           | Selecionada               |
    | **Resposta para o parâmetro obrigatório**     | **Editor simples** > `Which device do you want to control?`    | 
    | **Tipo**               | **Cadeia de caracteres**                |          |
    | **Configuration**      | **Aceitar valores de entrada predefinidos de um catálogo interno** | 
    | **Valores de entrada predefinidos** | `tv`, `fan`               |
    | **Aliases** ( `tv` )      | `television`, `telly`     |

1. Selecione **Salvar**.

#### <a name="modify-example-sentences"></a>Modificar sentenças de exemplo

Para comandos que usam parâmetros, é útil adicionar frases de exemplo que abrangem todas as combinações possíveis. Por exemplo:

* Informações completas do parâmetro: `turn {OnOff} the {SubjectDevice}`
* Informações de parâmetro parcial: `turn it {OnOff}`
* Nenhuma informação de parâmetro: `turn something`

As frases de exemplo que usam graus de informações variadas permitem que o aplicativo de comandos personalizados resolva as resoluções de uma imagem e resoluções de várias desligamentos usando informações parciais.

Com essas informações em mente, edite as sentenças de exemplo para usar esses parâmetros sugeridos:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Selecione **Salvar**.

> [!TIP]
> No editor de sentenças de exemplo, use chaves para fazer referência aos parâmetros. Por exemplo, `turn {OnOff} the {SubjectDevice}`.
> Use uma guia para a conclusão automática apoiada por parâmetros criados anteriormente.

#### <a name="modify-completion-rules-to-include-parameters"></a>Modificar regras de conclusão para incluir parâmetros

Modifique a regra de conclusão existente `ConfirmationResponse` .

1. Na seção **condições** , selecione **Adicionar uma condição**.
1. Na janela **nova condição** , na lista **tipo** , selecione **parâmetros necessários**. Na lista a seguir, selecione **Onoff** e **SubjectDevice**.
1. Deixe **IsGlobal** não selecionado.
1. Selecione **Criar**.
1. Na seção **ações** , edite a ação **Enviar resposta de fala** passando o mouse sobre ela e selecionando o botão Editar. Desta vez, use os parâmetros criados recentemente `OnOff` e `SubjectDevice` :

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Selecione **Salvar**.

Experimente as alterações selecionando o ícone de **treinamento** na parte superior do painel à direita. 

Quando o treinamento for concluído, selecione **testar**. Uma janela **testar seu aplicativo** é exibida. Tente as seguintes interações:

- Entrada: desligar *a TV*
- Saída: Ok, desligando a TV
- Entrada: desligar *a televisão*
- Saída: Ok, desligando a TV
- Entrada: *desative-* a
- Saída: qual dispositivo você deseja controlar?
- Entrada: *a TV*
- Saída: Ok, desligando a TV

### <a name="configure-parameters-for-a-settemperature-command"></a>Configurar parâmetros para um comando settemperaturas

Modifique o `SetTemperature` comando para habilitá-lo para definir a temperatura conforme o usuário direciona.

Adicione um `Temperature` parâmetro. Use a seguinte configuração:

| Configuração      | Valor sugerido     |
| ------------------ | ----------------|
| **Nome**               | `Temperature`           |
| **Necessária**           | Selecionada         |
| **Resposta para o parâmetro obrigatório**      | **Editor simples** > `What temperature would you like?`
| **Tipo**               | `Number`          |


Edite o exemplo declarações para usar os valores a seguir.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Edite as regras de conclusão existentes. Use a configuração a seguir.

| Configuração      | Valor sugerido     |
| ------------------ | ----------------|
| **Condições**         | **Parâmetro obrigatório**  >  **Temperatura**           |
| **Ações**           | **Enviar resposta de fala** > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>Configurar parâmetros para um comando setAlarm

Adicione um parâmetro chamado `DateTime` . Use a configuração a seguir.

   | Configuração                           | Valor sugerido                     | 
   | --------------------------------- | ----------------------------------------|
   | **Nome**                              | `DateTime`                               |
   | **Necessária**                          | Selecionada                                 |
   | **Resposta para o parâmetro obrigatório**   | **Editor simples** > `For what time?`            | 
   | **Tipo**                              | **DateTime**                                |
   | **Padrões de data**                     | Se a data estiver ausente, use hoje mesmo.            |
   | **Padrões de tempo**                     | Se o tempo estiver ausente, use o início do dia.     |


> [!NOTE]
> Este artigo usa principalmente os tipos de parâmetro String, Number e DateTime. Para obter uma lista de todos os tipos de parâmetro com suporte e suas propriedades, consulte [definições e conceitos de comandos personalizados](./custom-commands-references.md).


Edite o exemplo declarações. Use os seguintes valores.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Edite as regras de conclusão existentes. Use a configuração a seguir.

   | Configuração    | Valor sugerido                               |
   | ---------- | ------------------------------------------------------- |
   | **Ações**    | **Enviar resposta de fala** > `Ok, alarm set for {DateTime}`  |

Teste os três comandos juntos usando declarações relacionados a comandos diferentes. (Você pode alternar entre os diferentes comandos.)

- Entrada: *definir um alarme*
- Saída: por que tempo?
- Entrada: *ativar a TV*
- Saída: Ok, ligando a TV
- Entrada: *definir um alarme*
- Saída: por que tempo?
- Entrada: *5 PM*
- Saída: Ok, alarme definido para 2020-05-01 17:00:00

## <a name="add-configurations-to-command-parameters"></a>Adicionar configurações a parâmetros de comando

Nesta seção, você aprenderá mais sobre a configuração avançada de parâmetros, incluindo:

 - Como os valores de parâmetro podem pertencer a um conjunto que é definido fora do aplicativo de comandos personalizados.
 - Como adicionar cláusulas de validação nos valores de parâmetro.

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>Configurar um parâmetro como uma entidade de catálogo externo

O recurso de comandos personalizados permite que você configure parâmetros de tipo de cadeia de caracteres para se referir a catálogos externos hospedados em um ponto de extremidade da Web. Portanto, você pode atualizar o catálogo externo independentemente sem editar o aplicativo de comandos personalizados. Essa abordagem é útil em casos em que as entradas do catálogo são numerosas.

Reutilize o `SubjectDevice` parâmetro do `TurnOnOff` comando. A configuração atual para esse parâmetro é **aceitar entradas predefinidas do catálogo interno**. Essa configuração refere-se a uma lista estática de dispositivos na configuração do parâmetro. Mova esse conteúdo para uma fonte de dados externa que possa ser atualizada de forma independente.

Para mover o conteúdo, comece adicionando um novo ponto de extremidade da Web. No painel à esquerda, vá para a seção **pontos de extremidade da Web** . Lá, adicione um novo ponto de extremidade da Web. Use a configuração a seguir.

| Configuração | Valor sugerido |
|----|----|
| **Nome** | `getDevices` |
| **URL** | `https://aka.ms/speech/cc-sampledevices` |
| **Método** | **GET** |


Se o valor sugerido para a URL não funcionar para você, configure e hospede um ponto de extremidade da Web que retorne um arquivo JSON que consiste na lista de dispositivos que podem ser controlados. O ponto de extremidade da Web deve retornar um arquivo JSON formatado da seguinte maneira:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

Em seguida, vá para a página Configurações de parâmetro **SubjectDevice** . Configure as propriedades a seguir.

| Configuração | Valor sugerido |
| ----| ---- |
| **Configuration** | **Aceitar entradas predefinidas do catálogo externo** |                               
| **Ponto de extremidade do catálogo** | `getDevices` |
| **Método** | **GET** |

Em seguida, selecione **Salvar**.

> [!IMPORTANT]
> Você não verá uma opção para configurar um parâmetro para aceitar entradas de um catálogo externo, a menos que tenha o ponto de extremidade da Web definido na seção **ponto de extremidade da Web** no painel à esquerda.

Experimente selecionando **treinar**. Após a conclusão do treinamento, selecione **testar** e tente algumas interações.

* Entrada: *Ativar*
* Saída: qual dispositivo você deseja controlar?
* Entrada: *luzes*
* Saída: Ok, acendendo as luzes

> [!NOTE]
> Agora você pode controlar todos os dispositivos hospedados no ponto de extremidade da Web. Mas você ainda precisa treinar o aplicativo para testar as novas alterações e, em seguida, republicar o aplicativo.

### <a name="add-validation-to-parameters"></a>Adicionar validação aos parâmetros

*Validações* são construções que se aplicam a determinados tipos de parâmetro que permitem configurar restrições no valor do parâmetro. Eles solicitarão correções se os valores não ficarem dentro das restrições. Para obter uma lista de tipos de parâmetro que estendem a construção de validação, consulte [definições e conceitos de comandos personalizados](./custom-commands-references.md).

Teste as validações usando o `SetTemperature` comando. Use as etapas a seguir para adicionar uma validação para o `Temperature` parâmetro.

1. No painel à esquerda, selecione o comando **settemperaturas** .
1. No painel central, selecione **temperatura**.
1. No painel à direita, selecione **Adicionar uma validação**.
1. Na janela **nova validação** , configure a validação, conforme mostrado na tabela a seguir. Em seguida, selecione **Criar**.


    | Configuração do parâmetro | Valor sugerido | Descrição |
    | ---- | ---- | ---- |
    | **Valor mínimo** | `60` | Para parâmetros numéricos, o valor mínimo que esse parâmetro pode assumir |
    | **Valor máximo** | `80` | Para parâmetros numéricos, o valor máximo que esse parâmetro pode assumir |
    | **Resposta de falha** |  **Editor simples**  >  **Primeira variação** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Um prompt para solicitar um novo valor se a validação falhar |

    > [!div class="mx-imgBorder"]
    > ![Captura de tela mostrando como adicionar uma validação de intervalo.](media/custom-commands/add-validations-temperature.png)

Experimente selecionando o ícone de **treinamento** na parte superior do painel à direita. Após a conclusão do treinamento, selecione **testar**. Experimente algumas interações:

- Entrada: *defina a temperatura como 72 graus*
- Saída: Ok, definindo a temperatura como 72 graus
- Entrada: *defina a temperatura como 45 graus*
- Saída: Desculpe, só posso definir a temperatura entre 60 e 80 graus
- Entrada: *torne-o 72 graus*
- Saída: Ok, definindo a temperatura como 72 graus

## <a name="add-interaction-rules"></a>Adicionar regras de interação

Regras de interação são regras *adicionais* que tratam de situações específicas ou complexas. Embora você esteja livre para criar suas próprias regras de interação, neste exemplo, você usa regras de interação para os seguintes cenários:

* Confirmando comandos
* Adicionando uma correção de uma etapa aos comandos

Para obter mais informações sobre regras de interação, consulte [definições e conceitos de comandos personalizados](./custom-commands-references.md).

### <a name="add-confirmations-to-a-command"></a>Adicionar confirmações a um comando

Para adicionar uma confirmação, use o `SetTemperature` comando. Para obter a confirmação, crie regras de interação usando as seguintes etapas:

1. No painel à esquerda, selecione o comando **settemperaturas** .
1. No painel central, adicione regras de interação selecionando **Adicionar**. Em seguida, selecione o comando **regras de interação**  >  **confirmar**.

    Essa ação adiciona três regras de interação. As regras solicitam que o usuário confirme a data e a hora do alarme. Eles esperam uma confirmação (Sim ou não) para a próxima rodada.

    1. Modifique a regra de interação de **comando Confirm** usando a seguinte configuração:
        1. Altere o nome para **confirmar a temperatura**.
        1. Adicione uma nova condição: temperatura dos **parâmetros necessários**  >  .
        1. Adicionar uma nova ação: **tipo**  >  **Enviar resposta**  >  **de fala tem certeza de que deseja definir a temperatura como {temperatura} graus?**
        1. Na seção **expectativas** , deixe o valor padrão de **confirmação esperada do usuário**.
      
         > [!div class="mx-imgBorder"]
         > ![Captura de tela mostrando como criar a resposta de parâmetro necessária.](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modifique a regra de interação de **confirmação com êxito** para tratar de uma confirmação bem-sucedida (o usuário disse sim).
      
          1. Altere o nome para a **temperatura de confirmação bem-sucedida**.
          1. Deixe que a **confirmação existente tenha sido bem-sucedida** .
          1. Adicione uma nova condição: **tipo** de temperatura de  >  **parâmetros necessários**  >  .
          1. Deixe o valor do **estado de pós-execução** padrão como **regras de conclusão de execução**.

    1. Modifique a regra de interação de **confirmação negada** para lidar com cenários quando a confirmação for negada (o usuário não disse).

          1. Altere o nome para a **temperatura de confirmação negada**.
          1. Deixe a condição **confirmação existente negada** .
          1. Adicione uma nova condição: **tipo** de temperatura de  >  **parâmetros necessários**  >  .
          1. Adicione uma nova ação: **tipo**  >  **Enviar resposta de fala**  >  **sem problema. Em que temperatura?**
          1. Altere o valor do **estado de pós-execução** padrão para **aguardar a entrada do usuário**.

> [!IMPORTANT]
> Neste artigo, você usa a funcionalidade de confirmação interna. Você também pode adicionar manualmente as regras de interação uma a uma.
   
Experimente as alterações selecionando **treinar**. Quando o treinamento for concluído, selecione **testar**.

- **Entrada**: *defina a temperatura como 80 graus*
- **Saída**: tem certeza de que deseja definir a temperatura como 80 graus?
- **Entrada**: *não*
- **Saída**: sem problema. Qual temperatura?
- **Entrada**: *72 graus*
- **Saída**: tem certeza de que deseja definir a temperatura como 72 graus?
- **Entrada**: *Sim*
- **Saída**: Ok, definindo a temperatura como 72 graus

### <a name="implement-corrections-in-a-command"></a>Implementar correções em um comando

Nesta seção, você configurará uma correção de uma etapa. Essa correção é usada após a execução da ação de preenchimento. Você também verá um exemplo de como uma correção é habilitada por padrão se o comando ainda não for atendido. Para adicionar uma correção quando o comando não for concluído, adicione o novo parâmetro `AlarmTone` .

No painel esquerdo, selecione o comando **setAlarm** . Em seguida, adicione o novo parâmetro **AlarmTone**.
        
- **Nomes** > `AlarmTone`
- **Tipo**  >  de **Cadeia de caracteres**
- **Valor padrão**  >  **Chimes**
- **Configuração**  >  do **Aceitar valores de entrada predefinidos do catálogo interno**
- Valores de entrada **predefinidos**  >  **Chimes**, **jingle** e **Echo** (esses valores são entradas predefinidas individuais.)


Em seguida, atualize a resposta para o parâmetro **DateTime** como **pronto para definir o alarme com Tom como {AlarmTone}. Por que tempo?**. Em seguida, modifique a regra de conclusão da seguinte maneira:

1. Selecione a regra de conclusão **ConfirmationResponse** existente.
1. No painel à direita, passe o mouse sobre a ação existente e selecione **Editar**.
1. Atualize a resposta de fala para `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}` .

> [!IMPORTANT]
> O tom de alarme pode ser alterado sem nenhuma configuração explícita em um comando em andamento. Por exemplo, ele pode mudar quando o comando ainda não tiver terminado. Uma correção é habilitada *por padrão* para todos os parâmetros de comando, independentemente do número de folheio, se o comando ainda estiver sendo atendido.

#### <a name="implement-a-correction-when-a-command-is-finished"></a>Implementar uma correção quando um comando for concluído

A plataforma de comandos personalizados permite a correção de uma etapa mesmo quando o comando for concluído. Esse recurso não está habilitado por padrão. Ele deve ser configurado explicitamente. 

Use as etapas a seguir para configurar uma correção de uma etapa:

1. No comando **setAlarm** , adicione uma regra de interação do tipo **Atualizar comando anterior** para atualizar o alarme definido anteriormente. Renomeie a regra de interação como **Atualizar alarme anterior**.
1. Deixe a condição padrão: o **comando anterior precisa ser atualizado**.
1. Adicione uma nova condição: **tipo**  >  DateTime de **parâmetro necessário**  >  .
1. Adicionar uma nova ação: **tipo**  >  **Enviar resposta de fala**  >  **editor simples**  >  **atualizando o tempo de alarme anterior para {DateTime}**.
1. Deixe o valor do **estado de pós-execução** padrão como **comando concluído**.

Experimente as alterações selecionando **treinar**. Aguarde até que o treinamento seja concluído e, em seguida, selecione **testar**.

- **Entrada**: *defina um alarme.*
- **Saída**: pronto para definir o alarme com Tom como Chimes. Por que tempo?
- **Entrada**: *defina um alarme com o Tom como jingle para 9h, amanhã.*
- **Saída**: Ok, alarme definido para 2020-05-21 09:00:00. O tom de alarme é jingle.
- **Entrada**: *não, 8:00.*
- **Saída**: atualizando a hora do alarme anterior para 2020-05-29 08:00.

> [!NOTE]
> Em um aplicativo real, na seção **ações** dessa regra de correção, você também precisará enviar de volta uma atividade para o cliente ou chamar um ponto de extremidade http para atualizar a hora do alarme em seu sistema. Essa ação deve ser exclusivamente responsável por atualizar a hora do alarme. Ele não deve ser responsável por nenhum outro atributo do comando. Nesse caso, esse atributo seria o sinal de alarme.

## <a name="add-language-generation-templates-for-speech-responses"></a>Adicionar modelos de geração de idioma para respostas de fala

Os modelos de geração de linguagem (LG) permitem que você personalize as respostas enviadas ao cliente. Eles introduzem a variação nas respostas. Você pode obter a geração de idioma usando:

* Modelos de geração de idioma.
* Expressões adaptáveis.

Os modelos de comandos personalizados são baseados nos [modelos LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)do bot Framework. Como o recurso de comandos personalizados cria um novo modelo LG quando necessário (para respostas de fala em parâmetros ou ações), você não precisa especificar o nome do modelo LG. 

Portanto, você não precisa definir seu modelo como este:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Em vez disso, você pode definir o corpo do modelo sem o nome, desta forma:

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando um exemplo de editor de modelo.](./media/custom-commands/template-editor-example.png)


Essa alteração introduz a variação nas respostas de fala que são enviadas ao cliente. Para um expressão, a resposta de fala correspondente é separada aleatoriamente das opções fornecidas.

Aproveitando os modelos de LG, você também pode definir respostas de fala complexas para comandos usando expressões adaptáveis. Para obter mais informações, consulte o [formato de modelos LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates). 

Por padrão, o recurso de comandos personalizados dá suporte a todos os recursos, com as seguintes diferenças secundárias:

* Nos modelos LG, as entidades são representadas como `${entityName}` . O recurso de comandos personalizados não usa entidades. Mas você pode usar parâmetros como variáveis com a `${parameterName}` representação ou a `{parameterName}` representação.
* O recurso de comandos personalizados não dá suporte à composição e expansão de modelos, pois você nunca edita o arquivo *. LG* diretamente. Você edita apenas as respostas de modelos criados automaticamente.
* O recurso de comandos personalizados não dá suporte a funções personalizadas que o LG injeta. Há suporte para funções predefinidas.
* O recurso de comandos personalizados não oferece suporte a opções, como `strict` , `replaceNull` e `lineBreakStyle` .

### <a name="add-template-responses-to-a-turnonoff-command"></a>Adicionar respostas de modelo a um comando TurnOnOff

Modifique o `TurnOnOff` comando para adicionar um novo parâmetro. Use a configuração a seguir.

| Configuração            | Valor sugerido       | 
| ------------------ | --------------------- | 
| **Nome**               | `SubjectContext`         | 
| **É global**          | Não selecionado             | 
| **Necessária**           | Não selecionado               | 
| **Tipo**               | **Cadeia de caracteres**                |
| **Valor padrão**      | `all` |
| **Configuration**      | **Aceitar valores de entrada predefinidos do catálogo interno** | 
| **Valores de entrada predefinidos** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>Modificar uma regra de conclusão

Edite a seção **ações** da regra de conclusão **ConfirmationResponse** existente. Na janela **Editar ação** , alterne para o **Editor de modelos**. Em seguida, substitua o texto pelo exemplo a seguir.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Treine e teste seu aplicativo usando a entrada e a saída a seguir. Observe a variação de respostas. A variação é criada por várias alternativas do valor do modelo e também pelo uso de expressões adaptáveis.

* Entrada: *ativar a TV*
* Saída: Ok, ligando a TV
* Entrada: *ativar a TV*
* Saída: concluído, ativado na TV
* Entrada: *desative as luzes*
* Saída: Ok, desligando todas as luzes
* Entrada: *desligar as luzes de sala*
* Saída: Ok, desligando as luzes de sala

## <a name="use-a-custom-voice"></a>Usar uma voz personalizada

Outra maneira de personalizar as respostas de comandos personalizados é selecionar uma voz de saída. Use as etapas a seguir para alternar a voz padrão para uma voz personalizada:

1. Em seu aplicativo de comandos personalizados, no painel à esquerda, selecione **configurações**.
1. No painel central, selecione **voz personalizada**.
1. Na tabela, selecione uma voz personalizada ou voz pública.
1. Selecione **Salvar**.

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando frases e parâmetros de exemplo.](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> Para vozes públicas, os tipos neurais estão disponíveis apenas para regiões específicas. Para obter mais informações, consulte [regiões com suporte do Speech Service](./regions.md#standard-and-neural-voices).
>
> Você pode criar vozes personalizadas na página projeto de **voz personalizado** . Para obter mais informações, consulte Introdução [à voz personalizada](./how-to-custom-voice.md).

Agora, o aplicativo responderá na voz selecionada, em vez da voz padrão.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [integrar seu aplicativo de comandos personalizados](how-to-custom-commands-setup-speech-sdk.md) a um aplicativo cliente usando o SDK de fala.
* [Configure a implantação contínua](how-to-custom-commands-deploy-cicd.md) para seu aplicativo de comandos personalizados usando o Azure DevOps. 
                      
