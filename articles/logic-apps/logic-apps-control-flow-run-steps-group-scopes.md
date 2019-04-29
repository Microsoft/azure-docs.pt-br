---
title: Adicionar escopos que executam ações com base no status do grupo – Aplicativos Lógicos do Azure | Microsoft Docs
description: Como criar escopos que executam ações de fluxo de trabalho com base no status da ação de grupo nos Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.reviewer: klam, LADocs
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 48fb2d14cd4cf99510fff88b25b9ae45814a92a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60685262"
---
# <a name="run-actions-based-on-group-status-with-scopes-in-azure-logic-apps"></a>Executar ações com base no status do grupo com os escopos em aplicativos lógicos do Azure

Para executar ações somente após um outro grupo de ações ter êxito ou falhar, agrupe essas ações em um *escopo*. Essa estrutura é útil quando deseja organizar ações como um grupo lógico, avaliar o status desse grupo e executar ações baseadas no status do escopo. Depois que todas as ações em um escopo concluem a execução, o escopo também obtém seu próprio status. Por exemplo, é possível usar escopos quando você quiser implementar [tratamento de erro e exceção](../logic-apps/logic-apps-exception-handling.md#scopes). 

Para verificar o status de um escopo, é possível usar os mesmos critérios utilizados para determinar um status de execução de aplicativos lógicos, como "Com êxito", "Com falha", "Cancelado", e assim por diante. Por padrão, quando todas as ações do escopo são bem-sucedidas, o status do escopo é marcado como "Com êxito." Mas, quando qualquer ação no escopo apresenta falha ou é cancelada, o status do escopo é marcado como "Com falha." Para limites nos escopos, consulte [Limites e configurações](../logic-apps/logic-apps-limits-and-config.md). 

Por exemplo, aqui é um aplicativo lógico de alto nível que usa um escopo para executar ações específicas e uma condição para verificar o status do escopo. Se alguma ação no escopo falhar ou encerrar inesperadamente, o escopo é marcado como "Com falha" ou "Anulado", respectivamente, e o aplicativo lógico envia uma mensagem "Falha de escopo". Se todas as ações de escopo tiverem êxito, o aplicativo lógico envia uma mensagem "Escopo com êxito".

![Configurar o gatilho "Agenda - Recorrência"](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Pré-requisitos

Para seguir o exemplo neste artigo, é necessário estes itens:

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Uma conta de email de qualquer provedor de email com suporte de Aplicativos Lógicos. Este exemplo usa Outlook.com. Se você utiliza um provedor diferente, o fluxo geral permanece o mesmo, mas sua interface do usuário aparece diferente.

* Uma chave do Bing Mapas. Para obter essa chave, consulte <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Obter uma chave do Bing Mapas</a>.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Criar aplicativo lógico de exemplo

Primeiro, crie esse aplicativo lógico de exemplo para que seja possível adicionar um escopo posteriormente:

![Criar aplicativo lógico de exemplo](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Um gatilho **Agenda - Recorrência** que verifica o serviço Bing Mapas em um intervalo especificado
* Uma ação do **Bing Mapas - Obter rota** que verifica o tempo de viagem entre dois locais
* Uma instrução condicional que verifica se o tempo de viagem excede o tempo de viagem especificado
* Uma ação que envia o email de que o tempo de viagem atual excede o tempo especificado

É possível salvar o aplicativo lógico a qualquer momento, portanto, salve seu trabalho frequentemente.

1. Entre no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>, se você ainda não fez isso. Criar um aplicativo lógico em branco.

1. Adicione a **agenda - recorrência** disparador com estas configurações: **Intervalo** = "1" e **frequência** = "Minuto"

   ![Configurar o gatilho "Agenda - Recorrência"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Para simplificar visualmente a exibição e ocultar os detalhes de cada ação no designer, recolha a forma de cada ação na medida em que você progride através dessas etapas.

1. Adicione a ação **Bing Mapas - Obter rota**. 

   1. Se você ainda não tiver uma conexão do Bing Mapas, a criação será solicitada.

      | Configuração | Valor | DESCRIÇÃO |
      | ------- | ----- | ----------- |
      | **Nome da Conexão** | BingMapsConnection | Forneça um nome para a conexão. | 
      | **Chave de API** | <*your-Bing-Maps-key*> | Insira a chave do Bing Mapas que você recebeu anteriormente. | 
      ||||  

   1. Configure a ação **Obter rota**, conforme mostrado na tabela abaixo da imagem:

      ![Configurar a ação "Bing Mapas - Obter rota"](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Para saber mais sobre esses parâmetros, consulte [Calcular uma rota](https://msdn.microsoft.com/library/ff701717.aspx).

      | Configuração | Valor | DESCRIÇÃO |
      | ------- | ----- | ----------- |
      | **Localizador 1** | <*iniciar*> | Insira a origem da rota. | 
      | **Localizador 2** | <*finalizar*> | Insira o destino da rota. | 
      | **Evitar** | Nenhum | Insira os itens a serem evitados na rota, como rodovias, pedágios, e assim por diante. Para possíveis valores, consulte [Calcular uma rota](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Otimizar** | timeWithTraffic | Selecione um parâmetro para otimizar a rota, como distância, tempo com informações de trânsito atuais, e assim por diante. Esse exemplo usa este valor: "timeWithTraffic" | 
      | **Unidades de distância** | <*your-preference*> | Insira a unidade de distância para calcular a rota. Este exemplo usa este valor: "Milha" | 
      | **Modo de navegação** | Automóvel | Insira o modo de viagem para a rota. Esse exemplo usa este valor "Automóvel" | 
      | **Data /Hora de trânsito** | Nenhum | Aplica-se apenas ao modo de trânsito. | 
      | **Tipo de data de trânsito** | Nenhum | Aplica-se apenas ao modo de trânsito. | 
      ||||  

1. [Adicione uma condição](../logic-apps/logic-apps-control-flow-conditional-statement.md) que verifique se o tempo de viagem atual com tráfego excede um tempo especificado. 
   Neste exemplo, siga estas etapas:

   1. Renomeie a condição com esta descrição: **Se o tempo de tráfego é mais do que o tempo especificado**

   1. Na coluna mais à esquerda, clique dentro da caixa **Escolher um valor** para que a lista de conteúdo dinâmico seja exibida. A partir dessa lista, selecione o campo **Tráfego de Duração de Viagem**, que está em segundos. 

      ![Condição da compilação](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Na caixa do meio, selecione este operador: **é maior que**

   1. Na coluna mais à direita, insira esse valor de comparação, que é em segundos e equivalente a 10 minutos: **600**

      Ao concluir, a sua condição será semelhante a este exemplo:

      ![Condição concluída](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. No **se for verdadeiro** ramificar, adicionar uma ação "enviar email" para seu provedor de e-mail. 
   Configure esta ação seguindo as etapas abaixo desta imagem:

   ![Adicionar ação "Enviar um email" para branch "Se verdadeiro"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. No campo **Para**, insira seu endereço de e-mail para fins de teste.

   1. No **assunto**, insira este texto:

      ```Time to leave: Traffic more than 10 minutes```

   1. No **corpo**, insira este texto com um espaço à direita: 

      ```Travel time:```

      Enquanto o cursor aparecer no campo **Corpo**, a lista de conteúdo dinâmico permanecerá aberta para que você possa selecionar os parâmetros disponíveis neste ponto.

   1. Na lista Conteúdo dinâmico, escolha **Expressão**.

   1. Localizar e selecionar a função **div( )**. 
      Coloque o cursor no dentro dos parênteses da função.

   1. Enquanto o cursor estiver dentro dos parênteses da função, escolha **Conteúdo dinâmico** para que a lista de conteúdo dinâmico seja exibida. 
   
   1. Na seção **Obter rota**, selecione o campo **Traffic Duration Traffic**.

      ![Selecionar "Tráfego de Duração do Tráfego"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Depois que o campo resolver o formato JSON, adicione uma **vírgula** (```,```) seguida do número ```60``` para converter o valor no **Tráfego de Duração do Tráfego** de segundos para minutos. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      A expressão agora é semelhante a este exemplo:

      ![Concluir expressão](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Quando terminar, escolha **OK**.

   <!-- markdownlint-disable MD038 -->
   1. Depois que a expressão for resolvida, adicione este texto com um espaço à esquerda: ``` minutes```
  
       O campo **Corpo** agora é semelhante a este exemplo:

       ![Campo de "Corpo" concluído](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Salve seu aplicativo lógico.

Em seguida, adicione um escopo para que seja possível agrupar ações específicas e avaliar os respectivos status.

## <a name="add-a-scope"></a>Adicionar um escopo

1. Abra o aplicativo lógico no Designer do Aplicativo Lógico, caso ainda não tenha feito isso. 

1. Adicione um escopo na localização do fluxo de trabalho desejado. Por exemplo, para adicionar um escopo entre etapas existentes no fluxo de trabalho de aplicativo lógico, siga estas etapas: 

   1. Mova o ponteiro sobre a seta onde você deseja adicionar o escopo. 
   Escolha o **sinal de adição** (**+**) > **adicionar uma ação**.

      ![Adicionar um escopo](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. Na caixa de pesquisa, insira "escopo" como filtro. 
   Selecione a ação **escopo**.

## <a name="add-steps-to-scope"></a>Adicionar etapas ao escopo

1. Agora, adicione as etapas ou arraste as etapas existentes que deseja executar dentro do escopo. Para esse exemplo, arraste estas ações para o escopo:
      
   * **Obter rota**
   * **Se o tempo de tráfego for maior que o tempo especificado**, o que inclui os ramos **verdadeiro** e **falso**

   O aplicativo lógico agora é semelhante a este exemplo:

   ![Escopo adicionado](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. No escopo, adicione uma condição que verifica o status do escopo. Renomeie a condição com esta descrição: **Se a falha de escopo**

   ![Adicionar condição para verificar o status do escopo](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. Na condição, adicione essas expressões que verificam se o status do osciloscópio é igual a "Falhou" ou "Anulado". 

   1. Para adicionar outra linha, escolha **adicionar**. 

   1. Em cada linha, clique dentro da caixa à esquerda para que apareça na lista de conteúdo dinâmica. 
   Na lista de conteúdo dinâmica, escolha **expressão**. Na caixa de edição, digite esta expressão e, em seguida, escolha **Okey**: 
   
      `result('Scope')[0]['status']`

      ![Adicionar expressão que verifica o status do escopo](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Para ambas as linhas, selecione **é igual a** como o operador. 
   
   1. Para os valores de comparação, na primeira linha, insira `Failed`. 
   Na segunda linha, digite `Aborted`. 

      Ao concluir, a sua condição será semelhante a este exemplo:

      ![Adicionar expressão que verifica o status do escopo](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Agora, defina a propriedade `runAfter` da condição para que a condição verifique o status do escopo e execute a ação correspondente que você define nas etapas posteriores.

   1. Na condição **Se o escopo falhar**, escolha o botão **reticências** (...) e, em seguida, escolha **Configurar execução depois**.

      ![Configurar a propriedade 'runAfter'](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Selecione todos esses status do escopo: **for bem-sucedida**, **falhou**, **será ignorado**, e **foi atingido**

      ![Selecione o status do escopo](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Quando tiver terminado, escolha **Concluído**. 
   A condição agora mostra um ícone de "informações".

1. Nas **Se as ramificações** e **Falso** forem verdadeiras, adicione as ações que você deseja executar com base em cada status do escopo, por exemplo, enviar um e-mail ou uma mensagem.

   ![Adicionar ações a serem tomadas com base no status do escopo](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Salve seu aplicativo lógico.

Seu aplicativo lógico concluído agora se parece com este exemplo:

![Aplicativo lógico concluído com escopo](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Teste o trabalho

Na barra de ferramentas do Designer, escolha **Executar**. Se todas as ações com escopo tiverem êxito, você receberá uma mensagem "Escopo com êxito". Se alguma ação de escopo não tiver êxito, você receberá uma mensagem "Escopo com falha". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definição JSON

Se você estiver trabalhando na exibição de código, será possível definir uma estrutura de escopo na definição JSON do aplicativo lógico. Por exemplo, aqui é a definição JSON para gatilho e ações no aplicativo lógico anterior:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em recursos e sugestões, visite o [site de comentários do usuário de Aplicativos Lógicos do Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Executar etapas baseadas em uma condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar etapas baseadas em valores diferentes (instruções de comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou mesclar etapas paralelas (branches)](../logic-apps/logic-apps-control-flow-branches.md)
