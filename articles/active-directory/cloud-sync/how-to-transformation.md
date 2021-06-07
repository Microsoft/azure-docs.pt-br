---
title: Azure AD Connect transformações de sincronização de nuvem
description: Este artigo descreve como usar transformações para alterar os mapeamentos de atributo padrão.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 1c81ee0ebace65e50cdab5b5b223d5e5eae4ff9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98612978"
---
# <a name="transformations"></a>Transformações

Com uma transformação, você pode alterar o comportamento padrão de como um atributo é sincronizado com Azure Active Directory (AD do Azure) usando a sincronização de nuvem.

Para executar essa tarefa, você precisa editar o esquema e reenviá-lo por meio de uma solicitação da Web.

Para obter mais informações sobre atributos de sincronização de nuvem, consulte [noções básicas sobre o esquema do Azure ad](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Recuperar o esquema
Para recuperar o esquema, siga as etapas em [Exibir o esquema](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Mapeamento de atributo personalizado
Para adicionar um mapeamento de atributo personalizado, siga estas etapas.

1. Copie o esquema em um editor de texto ou de código, como [Visual Studio Code](https://code.visualstudio.com/).
1. Localize o objeto que você deseja atualizar no esquema.

   ![Objeto no esquema](media/how-to-transformation/transform-1.png)</br>
1. Localize o código para `ExtensionAttribute3` sob o objeto de usuário.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. Edite o código para que o atributo da empresa seja mapeado para `ExtensionAttribute3` .

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. Copie o esquema de volta para o explorador do Graph, altere o **tipo de solicitação** para **Put** e selecione **Executar consulta**.

    ![Executar Consulta](media/how-to-transformation/transform-2.png)

 1. Agora, na portal do Azure, vá para a configuração de sincronização de nuvem e selecione **reiniciar provisionamento**.

    ![Reiniciar o provisionamento](media/how-to-transformation/transform-3.png)

 1. Após alguns instantes, verifique se os atributos estão sendo populados executando a seguinte consulta no Gerenciador de gráficos: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}` .
 1. Agora você deve ver o valor.

    ![O valor é exibido](media/how-to-transformation/transform-4.png)

## <a name="custom-attribute-mapping-with-function"></a>Mapeamento de atributo personalizado com função
Para um mapeamento mais avançado, você pode usar funções que permitem manipular os dados e criar valores para atributos para atender às necessidades da sua organização.

Para executar essa tarefa, siga as etapas anteriores e edite a função que é usada para construir o valor final.

Para obter informações sobre a sintaxe e exemplos de expressões, consulte [escrevendo expressões para mapeamentos de atributo em Azure Active Directory](reference-expressions.md).


## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem do Azure AD Connect?](what-is-cloud-sync.md)
