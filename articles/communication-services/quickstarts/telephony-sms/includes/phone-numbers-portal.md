---
ms.openlocfilehash: 82e475c97aa2c68a33a48b04fe3e45fb13728b88
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629320"
---

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Um recurso ativo dos Serviços de Comunicação.](../../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Obter um número de telefone

Para começar a provisionar números, vá até seu recurso dos Serviços de Comunicação no [portal do Azure](https://portal.azure.com).

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Captura de tela mostrando a página principal de um recurso dos Serviços de Comunicação.":::

### <a name="search-for-available-phone-numbers"></a>Pesquisar números de telefone disponíveis

Acesse a folha **Números de Telefone** no menu de recursos.

:::image type="content" source="../../media/manage-phone-azure-portal-phone-page.png" alt-text="Captura de tela mostrando a página de telefone de um recurso dos Serviços de Comunicação.":::

Clique no botão **Obter** para iniciar o assistente. O assistente na folha **Números de telefone** orientará você em uma série de perguntas que ajudarão a escolher o número de telefone mais adequado para seu cenário. 

Primeiro, você precisará escolher o **País/região** em que deseja provisionar o número de telefone. Depois de selecionar o País/a região, você precisará escolher o **Caso de uso** mais adequado às suas necessidades. 

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers.png" alt-text="Captura de tela mostrando a exibição Obter números de telefone.":::

### <a name="select-your-phone-number-features"></a>Selecionar os recursos de número de telefone

A configuração do número de telefone é dividida em duas etapas: 

1. A seleção do [tipo de número](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. A seleção das [funcionalidades de número](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)

Você pode selecionar um de dois tipos de números de telefone: **Geográfico** e **Chamada gratuita**. Quando selecionar um tipo de número, escolha o recurso.

Em nosso exemplo, selecionamos um tipo de número **Chamada gratuita** com os recursos **Chamada de saída** e **SMS de Entrada e Saída**.

:::image type="content" source="../../media/manage-phone-azure-portal-select-plans.png" alt-text="Captura de tela que mostra a exibição Selecionar recursos.":::

Aqui, clique no botão **Avançar: Números** na parte inferior da página para personalizar os números de telefone que deseja provisionar.

### <a name="customizing-phone-numbers"></a>Personalizando números de telefone

Na página **Números**, você personalizará os números de telefone que deseja provisionar.

:::image type="content" source="../../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Captura de tela mostrando a página de seleção de Números.":::

> [!NOTE]
> Este guia de início rápido mostra o fluxo de personalização do Tipo de número **Chamada gratuita**. A experiência poderá ser um pouco diferente se você tiver escolhido o Tipo de número **Geográfico**, mas o resultado final será o mesmo.

Escolha o **Código de área** na lista de Códigos de área disponíveis, insira a quantidade que deseja provisionar e clique em **Pesquisar** para encontrar números que atendam aos requisitos selecionados. Os números de telefone que atenderem às suas necessidades serão mostrados, bem como seu custo mensal.

:::image type="content" source="../../media/manage-phone-azure-portal-found-numbers.png" alt-text="Captura de tela mostrando a página de seleção de Números com números reservados.":::

> [!NOTE]
> A disponibilidade depende do Tipo de número, da localização e dos recursos selecionados.
> Os números ficam reservados por um período curto antes que a transação expire. Se a transação expirar, você precisará selecionar os números novamente.

Para ver o resumo da compra e fazer seu pedido, clique no botão **Avançar: Resumo** na parte inferior da página.

### <a name="purchase-phone-numbers"></a>Comprar números de telefone

A página de resumo trará uma revisão com o Tipo de número, os Recursos, os Números de telefone e o Custo mensal total para provisionar os números de telefone.

> [!NOTE]
> Os preços mostrados são os **encargos mensais recorrentes**, que cobrem o custo de cessão do número de telefone selecionado a você. Nessa exibição, não estão incluídos os **Custos pagos conforme o uso**, que são cobrados quando você faz ou recebe chamadas. As listas de preços estão [disponíveis aqui](../../../concepts/pricing.md). Esses custos dependem do tipo de número e dos destinos das chamadas. Por exemplo, o preço por minuto de uma chamada de um número regional de Seattle para um número regional de Nova York e de uma chamada do mesmo número para um número de celular do Reino Unido poderão ser diferentes.

Por fim, clique em **Fazer pedido** na parte inferior da página para confirmá-lo.

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Captura de tela mostrando a página de Resumo com o Tipo de número, os Recursos, os Números de telefone e o Custo mensal total.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Localizar seus números de telefone no portal do Azure

Navegue até seu Recurso de Comunicação do Azure no [portal do Azure](https://portal.azure.com):

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Captura de tela mostrando a página principal de um Recurso dos Serviços de Comunicação.":::

Selecione a folha Números de Telefone no menu para gerenciar seus números de telefone.

:::image type="content" source="../../media/manage-phone-azure-portal-phones.png" alt-text="Captura de tela mostrando a página de número de telefone de um Recurso dos Serviços de Comunicação.":::

> [!NOTE]
> Poderão ser necessários alguns minutos para que os números provisionados sejam mostrados nesta página.


### <a name="update-phone-number-capabilities"></a>Atualizar as funcionalidades do número de telefone

Na página **Números**, você poderá selecionar um número de telefone para configurá-lo.

:::image type="content" source="../../media/manage-phone-azure-portal-capability-update.png" alt-text="Captura de tela que mostra a página Atualizar recursos.":::

Selecione os recursos nas opções disponíveis e clique em **Confirmar** para aplicar a seleção.

### <a name="release-phone-number"></a>Liberar número de telefone

Na página **Números**, você pode liberar números de telefone.

:::image type="content" source="../../media/manage-phone-azure-portal-release-number.png" alt-text="Captura de tela mostrando a página de liberação de números de telefone.":::

Selecione o número de telefone que deseja liberar e clique no botão **Liberar**.