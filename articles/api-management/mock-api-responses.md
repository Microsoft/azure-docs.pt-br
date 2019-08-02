---
title: Simular respostas de API com o Portal do Azure | Microsoft Docs
description: Este tutorial mostra como usar o APIM (Gerenciamento de API) para definir uma política em uma API para que ela retorne uma resposta fictícia. Este método permite que os desenvolvedores prossigam com a implementação e teste da instância de Gerenciamento de API no caso de o back-end não estar disponível para enviar respostas reais.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 9b9a691cb2bce2357d184420912ab340aee534e8
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205313"
---
# <a name="mock-api-responses"></a>Simular respostas de API

As APIs de back-end podem ser importadas em uma API de APIM ou criadas e gerenciadas manualmente. As etapas neste tutorial mostram como usar o APIM para criar uma API em branco e gerenciá-la manualmente. O tutorial mostra como definir uma política em uma API para que ela retorne uma resposta fictícia. Este método permite que os desenvolvedores prossigam com a implementação e teste da instância de APIM mesmo se o back-end não está disponível para enviar respostas reais. A capacidade de simular respostas pode ser útil em diversos cenários:

+ Quando a fachada da API é criada primeiro e a implementação do back-end ocorre posteriormente. Ou, o back-end está sendo desenvolvido paralelamente.
+ Quando o back-end temporariamente não está operacional ou não pode ser escalado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma API de teste 
> * Adicionar uma operação à API de teste
> * Habilitar a simulação de resposta
> * Testar a API fictícia

![Resposta de operação fictícia](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>Pré-requisitos

+ Conheça a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
+ Compreenda o [conceito de políticas no Gerenciamento de API do Azure](api-management-howto-policies.md).
+ Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Criar uma API de teste 

As etapas nesta seção mostram como criar uma API em branco sem back-end. Elas também mostram como adicionar uma operação à API. Chamar a operação depois de concluir as etapas nesta seção produz um erro. Você não receberá erros após concluir as etapas na seção “Habilitar a simulação de resposta”.

![Criar API em branco](./media/mock-api-responses/03-MockAPIResponses-01-CreateTestAPI.png)

1. Selecione **APIs** no serviço **Gerenciamento de API**.
2. No menu à esquerda, selecione **+ Adicionar API**.
3. Selecione **API em Branco** na lista.
4. Insira "*API de Teste*" para **Nome de exibição**.
5. Insira "*Ilimitado*" para **Produtos**.
6. Selecione **Criar**.

## <a name="add-an-operation-to-the-test-api"></a>Adicionar uma operação à API de teste

![Adicionar operação à API](./media/mock-api-responses/03-MockAPIResponses-02-AddOperation.png)

1. Selecione a API que você criou na etapa anterior.
2. Clique em **+ Adicionar Operação**.

    | Configuração             | Valor                             | DESCRIÇÃO                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nome de exibição**    | *Chamada de teste*                       | O nome exibido no **Portal do desenvolvedor**.                                                                                                                                       |
    | **URL** (verbo HTTP) | GET                               | Você pode escolher entre um dos verbos HTTP predefinidos.                                                                                                                                         |
    | **URL**             | */test*                           | Um caminho de URL para a API.                                                                                                                                                                       |
    | **Descrição**     |                                   | Forneça uma descrição para a operação que é usada para fornecer a documentação para os desenvolvedores usando essa API no **Portal do desenvolvedor**.                                                    |
    | Guia **Consulta**       |                                   | Você pode adicionar parâmetros de consulta. Além de fornecer um nome e uma descrição, você pode fornecer valores que podem ser atribuídos a esse parâmetro. Um dos valores pode ser marcado como padrão (opcional). |
    | Guia **Solicitação**     |                                   | Você pode definir esquemas, exemplos e tipos de conteúdo de solicitação.                                                                                                                                  |
    | Guia **Resposta**    | Consulte as etapas após essa tabela. | Defina códigos de status de resposta, tipos de conteúdo, exemplos e esquemas.                                                                                                                           |

3. Selecione a guia **Resposta**, localizada os campos URL, Nome de exibição e Descrição.
4. Clique em **+ Adicionar resposta**.
5. Selecione **200 OK** na lista.
6. Sob o cabeçalho **Representações** à direita, selecione **+ Adicionar representação**.
7. Insira "*application/json*" na caixa de pesquisa e selecione o tipo de conteúdo **application/json**.
8. Na caixa de texto **Exemplo**, insira `{ 'sampleField' : 'test' }`.
9. Selecione **Criar**.

## <a name="enable-response-mocking"></a>Habilitar a simulação de resposta

![Habilitar a simulação de resposta](./media/mock-api-responses/03-MockAPIResponses-03-EnableMocking.png)

1. Selecione a API que você criou na etapa "Criar uma API de teste".
2. Selecione a operação de teste que você adicionou.
3. Na janela à direita, clique na guia **Design**.
4. Na janela **Processamento de entrada**, clique em **+ Adicionar política**.
5. Selecione o bloco **Respostas fictícias** da galeria.

    ![Bloco de política de respostas fictícias](./media/mock-api-responses/mock-responses-policy-tile.png)

6. Na caixa de texto **Resposta de Gerenciamento de API**, digite **200 OK, application/json**. Essa seleção indica que a sua API deve retornar o exemplo de resposta definido na seção anterior.

    ![Habilitar a simulação de resposta](./media/mock-api-responses/mock-api-responses-set-mocking.png)

7. Clique em **Save** (Salvar).

## <a name="test-the-mocked-api"></a>Testar a API fictícia

![Testar API fictícia](./media/mock-api-responses/03-MockAPIResponses-04-TestMocking.png)

1. Selecione a API que você criou na etapa "Criar uma API de teste".
2. Abra a guia **Testar**.
3. Certifique-se de que a API **Chamada de teste** esteja selecionada.

    > [!TIP]
    > Uma barra amarela com o texto **Mocking is enabled (A simulação está habilitada)** indica que as respostas retornadas do Gerenciamento de API enviam uma política fictícia e não uma resposta de back-end real.

4. Selecione **Enviar** para fazer uma chamada de teste.
5. A **Resposta HTTP** exibe o JSON fornecido como um exemplo na primeira seção desse tutorial.

    ![Habilitar a simulação de resposta](./media/mock-api-responses/mock-api-responses-test-response.png)

## <a name="video"></a>Vídeo

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma API de teste
> * Adicionar uma operação à API de teste
> * Habilitar a simulação de resposta
> * Testar a API fictícia

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Transformar e proteger uma API publicada](transform-api.md)
