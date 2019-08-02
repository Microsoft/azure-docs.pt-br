---
title: Como definir ou editar políticas de Gerenciamento de API do Azure | Microsoft Docs
description: Este tópico mostra como definir ou editar políticas de Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 3d1847b6001ef8e32f00a4e1cd9728d5ca0662f8
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205291"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Como definir ou editar políticas de Gerenciamento de API do Azure

A definição da política é um documento XML que descreve uma sequência de instruções de entrada e de saída. O XML pode ser editado diretamente na janela de definição. Você também pode selecionar uma política predefinida na lista fornecida à direita da janela de política. As instruções aplicáveis ao escopo atual estão habilitadas e realçadas. Clicar em uma instrução habilitada adiciona o XML adequado ao local onde estiver o cursor na exibição de definição. 

Para obter informações detalhadas sobre políticas, consulte [Políticas do Gerenciamento de API do Azure](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Definir ou editar uma política

Para definir ou editar uma política, siga as etapas a seguir:

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até sua instância de APIM.
3. Clique na guia **APIs**.

    ![Editar política](./media/set-edit-policies/code-editor.png)

4. Selecione uma das APIs que você importou anteriormente.
5. Selecione a guia **Design**.
6. Selecione uma operação à qual você deseja aplicar a política. Se você deseja aplicar a política a todas as operações, selecione **Todas as operações**.
7. Selecione o **</>** ícone (editor de código) na **processamento de entrada** ou **processamento de saída da seção**.
8. Cole o código de política desejado em um dos blocos adequados.
         
        <policies>
             <inbound>
                 <base />
             </inbound>
             <backend>
                 <base />
             </backend>
             <outbound>
                 <base />
             </outbound>
             <on-error>
                 <base />
             </on-error>
         </policies>
 
## <a name="configure-scope"></a>Configurar o escopo

As políticas podem ser configuradas globalmente ou no escopo de um Produto, API ou Operação. Para começar a configurar uma política, você deve primeiro selecionar o escopo ao qual ela deverá se aplicar.

Os escopos de política são avaliados na seguinte ordem:

1. Escopo global
2. Escopo do produto
3. Escopo de API
4. Escopo de operação

As declarações dentro das políticas serão avaliadas de acordo com o posicionamento do elemento `base`, se ele estiver presente. Uma política global não tem nenhuma política pai e usar o elemento `<base>` nela não terá nenhum efeito.

Para ver as políticas no escopo atual no editor de política, clique em **Recalcular a política efetiva para o escopo selecionado**.

### <a name="global-scope"></a>Escopo global

O escopo global é configurado para **Todas as APIs** em sua instância de APIM.

1. Entre no [Portal do Azure](https://portal.azure.com/) e navegue até sua instância de APIM.
2. Clique em **Todas as APIs**.

    ![Escopo global](./media/api-management-howto-policies/global-scope.png)

3. Clique no ícone de triângulo.
4. Selecione **Editor de código**.
5. Adicione ou edite as políticas.
6. Pressione **Salvar**. 

    As alterações são propagadas para o gateway de Gerenciamento de API imediatamente.

### <a name="product-scope"></a>Escopo do produto

O escopo do produto é configurado para o produto selecionado.

1. Clique em **Produtos**.

    ![Escopo do produto](./media/api-management-howto-policies/product-scope.png)

2. Selecione o produto ao qual você deseja aplicar políticas.
3. Clique em **Políticas**.
4. Adicione ou edite as políticas.
5. Pressione **Salvar**. 

### <a name="api-scope"></a>Escopo de API

O escopo de API é configurado para **Todas as Operações** da API selecionada.

1. Selecione a **API** à qual deseja aplicar políticas.

    ![Escopo de API](./media/api-management-howto-policies/api-scope.png)

2. Selecione **Todas as operações**
3. Clique no ícone de triângulo.
4. Selecione **Editor de código**.
5. Adicione ou edite as políticas.
6. Pressione **Salvar**. 

### <a name="operation-scope"></a>Escopo de operação 

O escopo de operação é configurado para a operação selecionada.

1. Selecione uma **API**.
2. Selecione a operação à qual deseja aplicar políticas.

    ![Escopo de operação](./media/api-management-howto-policies/operation-scope.png)

3. Clique no ícone de triângulo.
4. Selecione **Editor de código**.
5. Adicione ou edite as políticas.
6. Pressione **Salvar**. 

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes tópicos relacionados:

+ [Transformar APIs](transform-api.md)
+ [Referência de Política](api-management-policy-reference.md) para uma lista completa das instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)