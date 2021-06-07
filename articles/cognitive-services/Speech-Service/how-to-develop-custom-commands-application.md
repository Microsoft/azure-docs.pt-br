---
title: 'Instruções: desenvolver aplicativos de Comandos Personalizados – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como desenvolver e personalizar aplicativos de Comandos Personalizados. Esses aplicativos de comando de voz são mais adequados para cenários de conclusão de tarefas ou de comando e controle.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: ddf36530e52703ab1033b8e2e787b42b6dc60332
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553270"
---
# <a name="develop-custom-commands-applications"></a>Desenvolver aplicativos de Comandos Personalizados

Neste artigo de instruções, você aprenderá a desenvolver e configurar aplicativos de Comandos Personalizados. O recurso Comandos Personalizados ajuda a criar aplicativos avançados de comando de voz que são otimizados para experiências de interação que priorizam a voz. O recurso é mais adequado para cenários de conclusão de tarefas ou de comando e controle. Ele é particularmente adequado para dispositivos IoT (Internet das Coisas) e para dispositivos de ambiente e sem periféricos.

Neste artigo, você criará um aplicativo que pode ligar e desligar uma TV, definir a temperatura e definir um alarme. Depois de criar esses comandos básicos, você aprenderá sobre as seguintes opções para Personalizar Comandos:

* Adicionar parâmetros aos comandos
* Adicionar configurações aos parâmetros dos comandos
* Criar regras de interação
* Criar modelos de geração de linguagem para respostas de Fala
* Usar ferramentas de Voz Personalizada

## <a name="create-an-application-by-using-simple-commands"></a>Criar um aplicativo usando comandos simples

Começar criando um aplicativo vazio de Comandos Personalizados. Para obter detalhes, confira o [início rápido](quickstart-custom-commands-application.md). Nesse aplicativo, em vez de importar um projeto, você cria um projeto em branco.

1. Na caixa **Nome**, insira o nome do projeto *Smart-Room-Lite* (ou outro nome de sua escolha).
1. Na lista **Idiomas**, selecione **Inglês (Estados Unidos)** .
1. Selecione ou crie um recurso LUIS.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela mostrando a janela "Novo projeto".](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Atualizar recursos LUIS (opcional)

Você pode atualizar o recurso de criação que selecionou na janela **Novo projeto**. Você também pode definir um recurso de previsão. 

Um recurso de previsão é usado para reconhecimento quando o aplicativo de Comandos Personalizados é publicado. Você não precisa de um recurso de previsão durante as fases de desenvolvimento e teste.

### <a name="add-a-turnon-command"></a>Adicionar um comando TurnOn

Adicione um comando no aplicativo vazio de Comandos Personalizados, Smart-Room-Lite, que você criou. O comando processará um enunciado, `Turn on the tv`. Ele responderá com a mensagem `Ok, turning the tv on`.

1. Crie um comando selecionando **Novo comando**, na parte superior do painel esquerdo. A janela **Novo comando** será exibida.
1. Para o campo **Nome**, forneça o valor `TurnOn`.
1. Selecione **Criar**.

O painel central lista as propriedades do comando. 

A tabela a seguir explica as propriedades de configuração do comando. Para obter mais informações, confira [Definições e conceitos de Comandos Personalizados](./custom-commands-references.md).

| Configuração            | Descrição                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Sentenças de exemplo | Exemplos de enunciados que o usuário pode falar para disparar esse comando.                                                                 |
| Parâmetros       | Informações necessárias para concluir o comando.                                                                                |
| Regras de conclusão | Ações a serem executadas para concluir o comando. Exemplos: respondendo ao usuário ou se comunicando com um serviço Web. |
| Regras de interação   | Outras regras para lidar com situações mais específicas ou complexas.                                                              |


> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando onde criar um comando.](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Adicionar sentenças de exemplo

Na seção **Sentenças de exemplo**, você fornece um exemplo daquilo que o usuário pode dizer.

1. No painel central, selecione **Sentenças de exemplo**.
1. No painel à direita, adicione exemplos:

    ```
    Turn on the tv
    ```

1.  Na parte superior do painel, selecione **Salvar**.

Você ainda não tem parâmetros, portanto, você pode ir para a seção **Regras de conclusão**.

#### <a name="add-a-completion-rule"></a>Adicionar uma regra de conclusão

Em seguida, o comando precisa de uma regra de conclusão. Essa regra informa ao usuário que uma ação de conclusão está sendo executada. 

Para obter mais informações sobre regras e regras de conclusão, confira [Definições e conceitos de Comandos Personalizados](./custom-commands-references.md).

1. Selecione a regra de conclusão padrão **Concluída**. Em seguida, edite-a da seguinte maneira:

    
    | Configuração    | Valor sugerido                          | Descrição                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Nome**       | `ConfirmationResponse`                  | Um nome que descreve a finalidade da regra          |
    | **Condições** | Nenhum                                     | Condições que determinam quando a regra pode ser executada    |
    | **Ações**    | **Enviar resposta de Fala** > **Editor simples** > **Primeira variação** > `Ok, turning the tv on` | A ação a ser tomada quando a condição da regra for verdadeira |

   > [!div class="mx-imgBorder"]
   > ![Captura de tela mostrando onde criar uma resposta de Fala.](media/custom-commands/create-speech-response-action.png)

1. Selecione **Salvar** para salvar a ação.
1. De volta à seção **Regras de conclusão**, selecione **Salvar** para salvar todas as alterações. 

    > [!NOTE]
    > Você não precisa usar a regra de conclusão padrão que vem com o comando. Você pode excluir a regra de conclusão padrão e adicionar a sua regra.

### <a name="add-a-settemperature-command"></a>Adicionar um comando SetTemperature

Agora, adicione mais um comando, `SetTemperature`. Esse comando usará apenas um enunciado, `Set the temperature to 40 degrees`, e responderá com a mensagem `Ok, setting temperature to 40 degrees`.

Para criar o novo comando, siga as etapas usadas para o comando `TurnOn`, mas use a sentença de exemplo `Set the temperature to 40 degrees`.

Em seguida, edite as regras de conclusão **Concluídas** existentes, da seguinte maneira:

| Configuração    | Valor sugerido                          |
| ---------- | ---------------------------------------- |
| **Nome**  | `ConfirmationResponse`                  |
| **Condições** | Nenhum                                     |
| **Ações**    | **Enviar resposta de Fala** > **Editor simples** > **Primeira variação** > `Ok, setting temperature to 40 degrees` |

Selecione **Salvar** para salvar todas as alterações feitas no comando.

### <a name="add-a-setalarm-command"></a>Adicionar um comando SetAlarm

Crie um comando `SetAlarm`. Use a sentença de exemplo `Set an alarm for 9 am tomorrow`. Em seguida, edite as regras de conclusão **Concluídas** existentes, da seguinte maneira:

| Configuração    | Valor sugerido                          |
| ---------- | ---------------------------------------- |
| **Nome**  | `ConfirmationResponse`                  |
| **Condições** | Nenhum                                     |
| **Ações**    | **Enviar resposta de Fala** > **Editor simples** > **Primeira variação** > `Ok, setting an alarm for 9 am tomorrow` |

Selecione **Salvar** para salvar todas as alterações feitas no comando.

### <a name="try-it-out"></a>Experimente

Teste o comportamento do aplicativo usando o painel de teste: 

1. No canto superior direito do painel, selecione o ícone **Treinar**. 
1. Quando o treinamento for concluído, selecione **Testar**. 

Experimente os seguintes exemplos de enunciado usando voz ou texto:

- Você digita: *definir a temperatura como 40 graus*
- Resposta esperada: ok, definindo a temperatura como 40 graus
- Você digita: *ligar a TV*
- Resposta esperada: ok, ligando a TV
- Você digita: *definir um alarme para 9h, amanhã*
- Resposta esperada: ok, definindo um alarme para 9h, amanhã

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando o teste em uma interface de webchat.](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> No painel de teste, você pode selecionar **Ativar detalhes** para obter informações sobre como essa entrada de voz ou de texto foi processada.

## <a name="add-parameters-to-commands"></a>Adicionar parâmetros a comandos

Nesta seção, você aprenderá como adicionar parâmetros aos seus comandos. Os comandos exigem parâmetros para concluir uma tarefa. Em cenários complexos, os parâmetros podem ser usados para definir condições que disparam ações personalizadas.

### <a name="configure-parameters-for-a-turnon-command"></a>Configurar parâmetros do comando TurnOn

Comece editando o comando `TurnOn` existente para ligar e desligar vários dispositivos.

1. Agora que o comando gerenciará os cenários ativado e desativado, renomeie o comando como *TurnOnOff*.
   1. No painel à esquerda, selecione o comando **TurnOn**. Em seguida, ao lado do **Novo comando**, na parte superior do painel, selecione o botão de reticências ( **...** ).
   
   1. Selecione **Renomear**. Na janela **Renomear comando**, altere o nome para *TurnOnOff*.

1. Adicione um novo parâmetro ao comando. O parâmetro representa se o usuário deseja ativar ou desativar o dispositivo.
   1. Na parte superior do painel central, selecione **Adicionar**. No menu suspenso, selecione **Parâmetro**.
   1. No painel à direita, na seção **Parâmetros**, na caixa **Nome**, adicione `OnOff`.
   1. Selecione **Obrigatório**. Na janela **Adicionar resposta para um parâmetro obrigatório**, selecione **Editor simples**. No campo **Primeira variação**, adicione *ativado ou desativado?* .
   1. Selecione **Atualizar**.

       > [!div class="mx-imgBorder"]
       > ![Captura de tela que mostra a seção "Adicionar resposta para um parâmetro obrigatório" com a guia "Editor simples" selecionada.](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Configure as propriedades do parâmetro usando a tabela a seguir. Para obter informações sobre todas as propriedades de configuração de um comando, confira [Definições e conceitos de Comandos Personalizados](./custom-commands-references.md).
      

       | Configuração      | Valor sugerido     | Descrição                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **Nome**               | `OnOff`           | Um nome descritivo para o parâmetro                                                                           |
       | **É global**          | Não selecionado       | Caixa de seleção que indica se um valor desse parâmetro será aplicado em âmbito global, a todos os comandos do aplicativo.|
       | **Necessário**           | Selecionado         | Caixa de seleção que indica se é obrigatório fornecer um valor para esse parâmetro antes que o comando seja concluído. |
       | **Resposta para o parâmetro obrigatório**      |**Editor simples** > `On or Off?`      | Um prompt solicitando o valor desse parâmetro quando ele não for conhecido. |
       | **Tipo**               | **Cadeia de caracteres**          | Tipo do parâmetro, tal como número, cadeia de caracteres, datetime ou geografia.   |
       | **Configuration**      | **Aceitar valores de entrada predefinidos provenientes de um catálogo interno** | Para cadeias de caracteres, essa configuração limita as entradas a um conjunto de valores possíveis. |
       | **Valores de entrada predefinidos**     | `on`, `off`           | Conjunto de valores possíveis e seus alias.         |
       
        
   1. Para adicionar valores de entrada predefinidos, selecione **Adicionar uma entrada predefinida**. Na janela **Novo item**, digite o *Nome*, conforme mostrado na tabela anterior. Nesse caso, como você não está usando alias, pode deixar esse campo em branco.
   
      > [!div class="mx-imgBorder"]
      > ![Captura de tela mostrando como criar um parâmetro.](media/custom-commands/create-on-off-parameter.png)

   1. Selecione **Salvar** para salvar todas as configurações do parâmetro.
 
#### <a name="add-a-subjectdevice-parameter"></a>Adicionar um parâmetro SubjectDevice

1. Para adicionar um segundo parâmetro a fim de representar o nome dos dispositivos que podem ser controlados usando esse comando, selecione **Adicionar**. Use a configuração a seguir.


    | Configuração            | Valor sugerido       |
    | ------------------ | --------------------- |
    | **Nome**               | `SubjectDevice`         |
    | **É global**          | Não selecionado             |
    | **Necessário**           | Selecionado               |
    | **Resposta para o parâmetro obrigatório**     | **Editor simples** > `Which device do you want to control?`    | 
    | **Tipo**               | **Cadeia de caracteres**                |     
    | **Configuration**      | **Aceitar valores de entrada predefinidos provenientes de um catálogo interno** | 
    | **Valores de entrada predefinidos** | `tv`, `fan`               |
    | **Alias** (`tv`)      | `television`, `telly`     |

1. Clique em **Salvar**.

#### <a name="modify-example-sentences"></a>Modificar sentenças de exemplo

Para comandos que usam parâmetros, é útil adicionar frases de exemplo que abrangem todas as combinações possíveis. Por exemplo:

* Informações completas sobre o parâmetro: `turn {OnOff} the {SubjectDevice}`
* Informações parciais sobre o parâmetro: `turn it {OnOff}`
* Nenhuma informação sobre o parâmetro: `turn something`

As sentenças de exemplo que usam diferentes graus de informações permitem que o aplicativo de Comandos Personalizados solucione as resoluções pontuais e as resoluções recorrentes usando informações parciais.

Com essas informações em mente, edite as sentenças de exemplo para usar esses parâmetros sugeridos:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Clique em **Salvar**.

> [!TIP]
> No editor de sentenças de exemplo, use os símbolos de chaves para fazer referência aos parâmetros. Por exemplo, `turn {OnOff} the {SubjectDevice}`.
> Use uma guia para a conclusão automática apoiada por parâmetros criados anteriormente.

#### <a name="modify-completion-rules-to-include-parameters"></a>Modificar regras de conclusão para incluir parâmetros

Modifique a regra de conclusão `ConfirmationResponse` existente.

1. Na seção **Condições**, selecione **Adicionar uma condição**.
1. Na janela **Nova condição**, na lista **Tipo**, selecione **Parâmetros obrigatórios**. Na lista que vem a seguir, marque **OnOff** e **SubjectDevice**.
1. Deixe a opção **IsGlobal** desmarcada.
1. Selecione **Criar**.
1. Na seção **Ações**, edite a ação **Enviar resposta de Fala** passando o mouse sobre ela e selecionando o botão Editar. Desta vez, use os parâmetros criados recentemente `OnOff` e `SubjectDevice`:

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Clique em **Salvar**.

Experimente as alterações selecionando o ícone **Treinar**, na parte superior do painel à direita. 

Quando o treinamento for concluído, selecione **Testar**. A janela **Testar seu aplicativo** será exibida. Experimente as seguintes interações:

- Entrada: *desligar a TV*
- Saída: ok, desligando a TV
- Entrada: *desligar a televisão*
- Saída: ok, desligando a TV
- Entrada: *desligar*
- Saída: qual dispositivo você deseja controlar?
- Entrada: *a TV*
- Saída: ok, desligando a TV

### <a name="configure-parameters-for-a-settemperature-command"></a>Configurar parâmetros do comando SetTemperature

Modifique o comando `SetTemperature` a fim de habilitá-lo para definir a temperatura conforme determinado pelo usuário.

Adicionar um parâmetro `Temperature`. Use a seguinte configuração:

| Configuração      | Valor sugerido     |
| ------------------ | ----------------|
| **Nome**               | `Temperature`           |
| **Necessário**           | Selecionado         |
| **Resposta para o parâmetro obrigatório**      | **Editor simples** > `What temperature would you like?`
| **Tipo**               | `Number`          |


Edite os exemplos de enunciados a fim de usar os valores a seguir.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Edite as regras de conclusão existentes. Use a configuração a seguir.

| Configuração      | Valor sugerido     |
| ------------------ | ----------------|
| **Condições**         | **Parâmetro obrigatório** > **Temperatura**           |
| **Ações**           | **Enviar resposta de Fala** > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>Configurar parâmetros do comando SetAlarm

Adicione um parâmetro chamado `DateTime`. Use a configuração a seguir.

   | Configuração                           | Valor sugerido                     | 
   | --------------------------------- | ----------------------------------------|
   | **Nome**                              | `DateTime`                               |
   | **Necessário**                          | Selecionado                                 |
   | **Resposta para o parâmetro obrigatório**   | **Editor simples** > `For what time?`            | 
   | **Tipo**                              | **DateTime**                                |
   | **Padrões de data**                     | Se a data estiver ausente, use o dia atual.            |
   | **Padrões de hora**                     | Se a hora estiver ausente, use o início do dia.     |


> [!NOTE]
> Este artigo usa principalmente os tipos de parâmetro cadeia de caracteres, número e datetime. Para obter uma lista com todos os tipos de parâmetro compatíveis e suas respectivas propriedades, confira [Definições e conceitos de Comandos Personalizados](./custom-commands-references.md).


Edite os enunciados de exemplo. Use os seguintes valores.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Edite as regras de conclusão existentes. Use a configuração a seguir.

   | Configuração    | Valor sugerido                               |
   | ---------- | ------------------------------------------------------- |
   | **Ações**    | **Enviar resposta de Fala** > `Ok, alarm set for {DateTime}`  |

Teste os três comandos juntos usando enunciados relacionados a diferentes comandos. (Você pode alternar entre os diferentes comandos.)

- Entrada: *definir um alarme*
- Saída: para que hora?
- Entrada: *ligar a TV*
- Saída: ok, ligando a TV
- Entrada: *definir um alarme*
- Saída: para que hora?
- Entrada: *17:00*
- Saída: ok, alarme definido para 2020-05-01 17:00:00

## <a name="add-configurations-to-command-parameters"></a>Adicionar configurações aos parâmetros do comando

Nesta seção, você aprenderá mais sobre a configuração avançada de parâmetros, incluindo:

 - Como os valores de parâmetro podem pertencer a um conjunto que é definido fora do aplicativo de Comandos Personalizados.
 - Como adicionar cláusulas de validação nos valores de parâmetro.

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>Configurar um parâmetro como uma entidade do catálogo externo

O recurso de Comandos Personalizados permite que você configure parâmetros do tipo cadeia de caracteres para se referir a catálogos externos hospedados em um ponto de extremidade da Web. Portanto, você pode atualizar o catálogo externo de maneira independe, sem editar o aplicativo de Comandos Personalizados. Essa abordagem é útil para casos em que as entradas do catálogo são numerosas.

Reutilize o parâmetro `SubjectDevice` do comando `TurnOnOff`. A configuração atual desse parâmetro é **Aceitar entradas predefinidas do catálogo interno**. Essa configuração se refere a uma lista estática de dispositivos na configuração do parâmetro. Mova esse conteúdo para uma fonte de dados externa que possa ser atualizada de maneira independente.

Para mover o conteúdo, comece adicionando um novo ponto de extremidade da Web. No painel à esquerda, vá para a seção **pontos de extremidade da Web**. Lá, adicione um novo ponto de extremidade da Web. Use a configuração a seguir.

| Configuração | Valor sugerido |
|----|----|
| **Nome** | `getDevices` |
| **URL** | `https://aka.ms/speech/cc-sampledevices` |
| **Método** | **GET** |


Se o valor sugerido para a URL não funcionar para você, configure e hospede um ponto de extremidade da Web que retorne um arquivo JSON que consista na lista de dispositivos que podem ser controlados. O ponto de extremidade da Web deve retornar um arquivo JSON formatado da seguinte maneira:
    
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

Em seguida, vá para a página de configurações do parâmetro **SubjectDevice**. Defina as propriedades a seguir.

| Configuração | Valor sugerido |
| ----| ---- |
| **Configuration** | **Aceitar entradas predefinidas do catálogo externo** |                               
| **Ponto de extremidade do catálogo** | `getDevices` |
| **Método** | **GET** |

Em seguida, selecione **Salvar**.

> [!IMPORTANT]
> Você não verá uma opção para configurar um parâmetro a fim de aceitar entradas de um catálogo externo, a menos que tenha o ponto de extremidade da Web definido na seção **Ponto de extremidade da Web**, no painel à esquerda.

Experimente selecionando **Treinar**. Após a conclusão do treinamento, selecione **Testar** e experimente algumas interações.

* Entrada: *ligar*
* Saída: qual dispositivo você deseja controlar?
* Entrada: *luzes*
* Saída: ok, acendendo as luzes

> [!NOTE]
> Agora você pode controlar todos os dispositivos hospedados no ponto de extremidade da Web. Mas você ainda precisa treinar o aplicativo para testar as novas alterações e, em seguida, republicar o aplicativo.

### <a name="add-validation-to-parameters"></a>Adicionar validação aos parâmetros

*Validações* são constructos que se aplicam a determinados tipos de parâmetro que permitem configurar restrições para o valor do parâmetro. Elas solicitam correções se os valores não ficam dentro das restrições. Para obter uma lista de tipos de parâmetro que estendem o constructo de validação, confira [Definições e conceitos de Comandos Personalizados](./custom-commands-references.md).

Teste as validações usando o comando `SetTemperature`. Use as etapas a seguir para adicionar uma validação ao parâmetro `Temperature`.

1. No painel à esquerda, selecione o comando **SetTemperature**.
1. No painel central, selecione **Temperatura**.
1. No painel à direita, selecione **Adicionar uma validação**.
1. Na janela **Nova validação**, configure a validação, conforme mostrado na tabela a seguir. Em seguida, selecione **Criar**.


    | Configuração de parâmetro | Valor sugerido | Descrição |
    | ---- | ---- | ---- |
    | **Valor mínimo** | `60` | Para parâmetros numéricos, o valor mínimo que o parâmetro pode assumir |
    | **Valor máximo** | `80` | Para parâmetros numéricos, o valor máximo que o parâmetro pode assumir |
    | **Resposta a falhas** |  **Editor simples** > **Primeira variação** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Um prompt para solicitar um novo valor se a validação falhar |

    > [!div class="mx-imgBorder"]
    > ![Captura de tela mostrando como adicionar uma validação de intervalo.](media/custom-commands/add-validations-temperature.png)

Experimente selecionando o ícone **Treinar**, na parte superior do painel à direita. Depois que o treinamento for concluído, selecione **Testar**. Experimente algumas interações:

- Entrada: *definir a temperatura como 72 graus*
- Saída: ok, definindo a temperatura como 72 graus
- Entrada: *definir a temperatura como 45 graus*
- Saída: só posso definir a temperatura entre 60 e 80 graus
- Entrada: *então, definir como 72 graus*
- Saída: ok, definindo a temperatura como 72 graus

## <a name="add-interaction-rules"></a>Adicionar regras de interação

Regras de interação são regras *adicionais* que gerenciam situações específicas ou complexas. Embora você esteja livre para criar suas próprias regras de interação, neste exemplo, você usa regras de interação para os seguintes cenários:

* Confirmar comandos
* Adicionar uma correção de uma etapa aos comandos

Para obter mais informações sobre as regras de interação, confira [Definições e conceitos de Comandos Personalizados](./custom-commands-references.md).

### <a name="add-confirmations-to-a-command"></a>Adicionar confirmações a um comando

Para adicionar uma confirmação, use o comando `SetTemperature`. Para alcançar a confirmação, crie regras de interação usando as seguintes etapas:

1. No painel à esquerda, selecione o comando **SetTemperature**.
1. No painel central, adicione regras de interação selecionando **Adicionar**. Em seguida, selecione **Regras de interação** > **Confirmar comando**.

    Essa ação adiciona três regras de interação. As regras solicitam que o usuário confirme a data e a hora do alarme. Elas esperam uma confirmação (sim ou não) para a próxima rodada.

    1. Modifique a regra de interação do **comando Confirmar** usando a seguinte configuração:
        1. Altere o nome para **Confirmar temperatura**.
        1. Adicione uma nova condição: **Parâmetros obrigatórios** > **Temperatura**.
        1. Adicione uma nova ação: **Tipo** > **Enviar resposta de Fala** > **Tem certeza de que deseja definir a temperatura como {Temperature} graus?**
        1. Na seção **Expectativas**, deixe o valor padrão de **Esperando confirmação do usuário**.
      
         > [!div class="mx-imgBorder"]
         > ![Captura de tela mostrando como criar a resposta para o parâmetro obrigatório.](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modifique a regra de interação **Confirmação bem-sucedida** para gerenciar uma confirmação bem-sucedida (em que o usuário diz sim).
      
          1. Altere o nome para **Confirmação de temperatura bem-sucedida**.
          1. Deixe a condição existente **A confirmação foi bem-sucedida**.
          1. Adicione uma nova condição: **Tipo** > **Parâmetros obrigatórios** > **Temperatura**.
          1. Deixe o valor padrão do **Estado pós-execução** como **Executar regras de conclusão**.

    1. Modifique a regra de interação **Confirmação negada** para gerenciar cenários quando a confirmação for negada (em que o usuário diz não).

          1. Altere o nome para **Confirmação de temperatura negada**.
          1. Deixe a condição existente **A confirmação foi negada**.
          1. Adicione uma nova condição: **Tipo** > **Parâmetros obrigatórios** > **Temperatura**.
          1. Adicione uma nova ação: **Tipo** > **Enviar resposta de fala** > **Sem problemas. Qual temperatura, então?** .
          1. Altere o valor padrão do **Estado pós-execução** para **Aguardar a entrada do usuário**.

> [!IMPORTANT]
> Neste artigo, você usa a funcionalidade interna de confirmação. Você também pode adicionar manualmente as regras de interação, uma a uma.
   
Experimente as alterações selecionando **Treinar**. Quando o treinamento for concluído, selecione **Testar**.

- **Entrada**: *defina a temperatura como 80 graus*
- **Saída**: tem certeza de que deseja definir a temperatura como 80 graus?
- **Entrada**: *não*
- **Saída**: sem problema. Qual temperatura, então?
- **Entrada**: *72 graus*
- **Saída**: tem certeza de que deseja definir a temperatura como 72 graus?
- **Entrada**: *sim*
- **Saída**: ok, definindo a temperatura como 72 graus

### <a name="implement-corrections-in-a-command"></a>Implementar correções em um comando

Nesta seção, você vai configurar uma correção de uma etapa. Essa correção é usada após a execução da ação de conclusão. Você também verá um exemplo de como uma correção é habilitada por padrão se o comando ainda não for concluído. Para adicionar uma correção quando o comando não for concluído, adicione o novo parâmetro `AlarmTone`.

No painel esquerdo, selecione o comando **SetAlarm**. Em seguida, adicione o novo parâmetro **AlarmTone**.
        
- **Nome** > `AlarmTone`
- **Tipo** > **Cadeia de caracteres**
- **Valor padrão** > **Chimes**
- **Configuração** > **Aceitar valores de entrada predefinidos do catálogo interno**
- **Valores de entrada predefinidos** > **Chimes**, **Jingle** e **Echo** (esses valores são entradas individuais predefinidas.)


Em seguida, atualize a resposta para o parâmetro **DateTime** como **Pronto para definir o alarme com o tom {AlarmTone}. Para que hora?** . Então, modifique a regra de conclusão da seguinte maneira:

1. Selecione a regra de conclusão existente **ConfirmationResponse**.
1. No painel à direita, passe o mouse sobre a ação existente e selecione **Editar**.
1. Atualize a resposta de Fala para `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}`.

> [!IMPORTANT]
> O tom de alarme pode ser alterado sem nenhuma configuração explícita no comando em andamento. Por exemplo, ele pode mudar quando o comando ainda não tiver terminado. Uma correção é habilitada *por padrão* para todos os parâmetros do comando, independentemente da ordem, se o comando ainda não tiver sido concluído.

#### <a name="implement-a-correction-when-a-command-is-finished"></a>Implementar uma correção quando um comando for concluído

A plataforma de Comandos Personalizados permite a correção de uma etapa mesmo quando o comando está concluído. Esse recurso não é habilitado por padrão. Ele deve ser configurado explicitamente. 

Execute as seguintes etapas para configurar uma conexão de uma etapa:

1. No comando **SetAlarm**, adicione uma regra de interação do tipo **Atualizar comando anterior** para atualizar o alarme definido anteriormente. Renomeie a regra de interação como **Atualizar alarme anterior**.
1. Deixe a condição padrão: **Comando anterior precisa ser atualizado**.
1. Adicione uma nova condição: **Tipo** > **Parâmetro Obrigatório** > **DateTime**.
1. Adicionar uma nova ação: **Tipo** > **Enviar resposta de fala** > **Editor simples** > **Atualizando a hora do alarme anterior para {DateTime}** .
1. Deixe o valor padrão do **Estado pós-execução** como **Comando concluído**.

Experimente as alterações selecionando **Treinar**. Aguarde a conclusão do treinamento e, em seguida, selecione **Testar**.

- **Entrada**: *definir um alarme.*
- **Saída**: pronto para definir o alarme com o tom Chimes. Para que hora?
- **Entrada**: *definir um alarme com o tom Jingle para 9:00, amanhã.*
- **Saída**: ok, alarme definido para 2020-05-21 09:00:00. O tom do alarme é o Jingle.
- **Entrada**: *não, 8:00.*
- **Saída**: atualizando a hora do alarme anterior para 29/05/2020 08:00.

> [!NOTE]
> Em um aplicativo real, na seção **Ações** dessa regra de correção, você também precisará enviar de volta uma atividade para o cliente ou chamar um ponto de extremidade HTTP para atualizar a hora do alarme no seu sistema. Essa ação deve ser exclusivamente responsável por atualizar a hora do alarme. Ela não deve ser responsável por nenhum outro atributo do comando. Nesse caso, esse atributo seria o tom do alarme.

## <a name="add-language-generation-templates-for-speech-responses"></a>Adicionar modelos de geração de linguagem para respostas de Fala

Os modelos de LG (geração de linguagem) permitem que você personalize as respostas enviadas ao cliente. Eles introduzem a variação nas respostas. Você pode obter a geração de linguagem usando:

* Modelos de geração de linguagem.
* Expressões adaptáveis.

Os modelos de Comandos Personalizados são baseados nos [modelos de LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) do Bot Framework. Como o recurso de Comandos Personalizados cria um modelo de LG quando necessário (para respostas de Fala em parâmetros ou ações), você não precisa especificar o nome do modelo de LG. 

Portanto, você não precisa definir seu modelo da seguinte maneira:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Em vez disso, você pode definir o corpo do modelo sem o nome, desta forma:

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando um exemplo de editor de modelo.](./media/custom-commands/template-editor-example.png)


Essa alteração introduz a variação nas respostas de Fala que são enviadas ao cliente. Para um enunciado, a resposta de Fala correspondente é separada aleatoriamente das opções fornecidas.

Aproveitando os modelos de LG, você também pode definir respostas de Fala complexas para os comandos usando expressões adaptáveis. Para obter mais informações, confira o [Formato de modelos de LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates). 

Por padrão, o recurso de Comandos Personalizados dá suporte a todas as funcionalidades, com as seguintes diferenças secundárias:

* Nos modelos de LG, as entidades são representadas como `${entityName}`. O recurso de Comandos Personalizados não usa entidades. Mas você pode usar parâmetros como variáveis com a representação `${parameterName}` ou a representação `{parameterName}`.
* O recurso de Comandos Personalizados não dá suporte à composição e expansão de modelos, pois você nunca edita o arquivo *.lg* diretamente. Você edita apenas as respostas de modelos criados automaticamente.
* O recurso de Comandos Personalizados não dá suporte a funções personalizadas que a LG injeta. Há suporte para funções predefinidas.
* O recurso de Comandos Personalizados não oferece suporte a opções como `strict`, `replaceNull` e `lineBreakStyle`.

### <a name="add-template-responses-to-a-turnonoff-command"></a>Adicionar respostas de modelo ao comando TurnOnOff

Modifique o comando `TurnOnOff` para adicionar um novo parâmetro. Use a configuração a seguir.

| Configuração            | Valor sugerido       | 
| ------------------ | --------------------- | 
| **Nome**               | `SubjectContext`         | 
| **É global**          | Não selecionado             | 
| **Necessário**           | Não selecionado               | 
| **Tipo**               | **Cadeia de caracteres**                |
| **Valor padrão**      | `all` |
| **Configuration**      | **Aceitar valores de entrada predefinidos provenientes do catálogo interno** | 
| **Valores de entrada predefinidos** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>Modificar uma regra de conclusão

Edite a seção **Ações** da regra de conclusão existente **ConfirmationResponse**. Na janela **Editar ação**, alterne para o **Editor de modelos**. Então, substitua o texto pelos exemplos a seguir.

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

* Entrada: *ligar a TV*
* Saída: ok, ligando a TV
* Entrada: *ligar a TV*
* Saída: concluído, TV ligada
* Entrada: *desligar as luzes*
* Saída: ok, desligando todas as luzes
* Entrada: *desligar as luzes da sala*
* Saída: ok, desligando as luzes da sala

## <a name="use-a-custom-voice"></a>Usar uma voz personalizada

Outra maneira de personalizar as respostas de Comandos Personalizados é selecionando uma voz de saída. Use as seguintes etapas para alternar a voz padrão para uma voz personalizada:

1. Em seu aplicativo de Comandos Personalizados, no painel à esquerda, selecione **Configurações**.
1. No painel central, selecione **Voz Personalizada**.
1. Na tabela, selecione uma voz personalizada ou uma voz pública.
1. Clique em **Salvar**.

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando frases e parâmetros de exemplo.](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> Para vozes públicas, os tipos neurais estão disponíveis apenas para regiões específicas. Para obter mais informações, confira [Regiões com suporte para o serviço de Fala](./regions.md#neural-and-standard-voices).
>
> Você pode criar vozes personalizadas na página do projeto de **Voz Personalizada**. Para saber mais, confira [Introdução à Voz Personalizada](./how-to-custom-voice.md).

Agora, o aplicativo responderá na voz selecionada, em vez da voz padrão.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [integrar seu aplicativo de Comandos Personalizados](how-to-custom-commands-setup-speech-sdk.md) a um aplicativo cliente usando o SDK de Fala.
* [Configure a implantação contínua](how-to-custom-commands-deploy-cicd.md) para o seu aplicativo de Comandos Personalizados usando o Azure DevOps. 
                      
