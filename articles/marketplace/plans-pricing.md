---
title: Planos e preços para ofertas de Marketplace comercial
description: Saiba mais sobre os planos para as ofertas do Microsoft Commercial Marketplace no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: e97171e61814b832e78f7bd815d8d6f341a90840
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858165"
---
# <a name="plans-and-pricing-for-commercial-marketplace-offers"></a>Planos e preços para ofertas de Marketplace comercial

Um plano define o escopo e os limites de uma oferta e o preço associado quando aplicável. Por exemplo, dependendo do tipo de oferta, você pode selecionar mercados regionais e escolher se um plano é visível para o público ou apenas para um público privado. Alguns tipos de oferta dão suporte a assinaturas recorrentes, algum suporte a preços com base no uso e alguns permitem que um cliente compre a oferta com uma licença adquirida diretamente do Publicador. Isso proporciona a você a flexibilidade de fornecer aos seus clientes opções de preços e técnicas diferentes, quando aplicável.

Você pode criar até 100 planos para cada oferta que dá suporte a planos e até 45 desses planos podem ser [privados](#plan-visibility). Você pode criar planos pagos apenas para ofertas de máquina virtual do Azure, ofertas de aplicativos do Azure (aplicativos gerenciados) e ofertas de software como serviço (SaaS). Quando você opta por vender qualquer uma dessas ofertas por meio da Microsoft (ofertas transdesejadas), é necessário criar pelo menos um plano. Você pode criar planos para alguns dos outros tipos de oferta, mas os planos para esses tipos de oferta não incluem opções de preços.

> [!TIP]
> Uma oferta de transação é aquela na qual a Microsoft facilita a troca de dinheiro por uma licença de software em nome do editor.

## <a name="plans-by-offer-type"></a>Planos por tipo de oferta

A tabela a seguir mostra as opções de plano para cada tipo de oferta. As seções a seguir explicarão mais sobre essas opções. 

| Tipo de oferta | Planos com opções de preço | Planos sem opções de preços | Opção público privado |
| ------------ | ------------- | ------------- | ------------- |
| Aplicativo gerenciado do Azure | &#10004; | | &#10004; |
| Modelo de solução do Azure | | &#10004; | &#10004; |
| Contêiner do Azure | | &#10004; (BYOL) | |
| Módulo do IoT Edge |  | &#10004; |  |
| Serviço gerenciado |  | &#10004; (BYOL) | &#10004; |
| Software como serviço | &#10004; |  | &#10004; |
| Máquina virtual do Azure | &#10004; |  | &#10004; |
|||||

Não há suporte para planos para os seguintes tipos de oferta:

- Serviço de consultoria
- Dynamics 365 Business Central
- Compromisso com o cliente do Dynamics 365 & PowerApps
- Dynamics 365 for Operations
- Aplicativo do Power BI

## <a name="plan-information"></a>Informações do plano

Cada tipo de oferta requer informações diferentes quando você cria um novo plano. Você pode encontrar links para artigos específicos da oferta no [Guia de publicação por tipo de oferta](publisher-guide-by-offer-type.md). Depois de criar um novo plano na página **visão geral do plano** , você verá guias diferentes, como **lista de planos** ou **preços e disponibilidade** para configurar detalhes diferentes para o plano. Cada guia mostrará um status de incompleto ou completo à medida que você trabalhar com esses campos.

![Ilustra a página de listagem de planos no Partner Center. Os campos nome do plano, Resumo do plano e descrição do plano são realçados.](./media/commercial-marketplace-plans/plan-listing-tab.png)

Há alguns detalhes comuns a serem concluídos para um novo plano:

- **ID do plano**: Crie uma ID exclusiva para cada plano nesta oferta. Use no máximo 50 caracteres: somente letras minúsculas, caracteres alfanuméricos, traços e sublinhados. A ID ficará visível para os clientes na URL do produto e em modelos do Azure Resource Manager (se aplicável). Você não pode alterar essa ID depois de publicar a oferta.
- **Nome do plano**: (balão 1 na imagem acima). Crie um nome exclusivo para cada plano nesta oferta. Use no máximo 50 caracteres. O nome do plano é usado para diferenciar os planos de software que podem fazer parte da mesma oferta (por exemplo, nome da oferta: plano padrão e plano Enterprise). Os clientes verão esse nome ao decidir qual plano selecionar dentro de sua oferta.
- **Resumo do plano**: (balão 2 na imagem acima). Esse resumo aparece nos resultados da pesquisa do Azure Marketplace e pode conter até 100 caracteres.
   > [!NOTE]
   > Este campo não se aplica às ofertas de SaaS.
- **Descrição do plano**: (balão 3 na imagem acima.) Adicione uma descrição do plano que explica o que torna esse plano exclusivo de outros planos para sua oferta. Use no máximo 500 caracteres. Esse conteúdo será exibido para seus clientes na página de listagem de ofertas à medida que navegam e selecionam um plano para sua oferta.

O nome e a descrição do plano são exibidos na página de listagem da oferta no loja online do Marketplace comercial. A captura de tela a seguir mostra três planos para uma listagem de oferta de SaaS no Azure Marketplace.

![Ilustra uma página de listagem de ofertas no Partner Center. Três planos são mostrados.](./media/commercial-marketplace-plans/offer-listing-page.png)

Depois de criar seus planos, a página **visão geral do plano** mostra uma lista de nome, ID, outros detalhes, status de publicação atual e quaisquer ações disponíveis. As ações disponíveis dependerão do status do seu plano e podem incluir:

- Se o status do plano for **Rascunho** – Excluir rascunho.
- Se o status do plano for **Live** – pare de vender ou Sincronize o público privado.

A captura de tela a seguir mostra duas ofertas de rascunho.

![Ilustra a página Visão geral do plano no Partner Center. Dois planos são mostrados.](./media/commercial-marketplace-plans/plan-overview-tab.png)

## <a name="pricing-and-availability"></a>Preços e disponibilidade

O Marketplace comercial opera em um modelo de agência, no qual os editores definem preços, a Microsoft Bills Customers e a Microsoft paga receita aos Publicadores ao mesmo tempo em que a agência é cobrada. Você define os mercados, a visibilidade e os preços da sua oferta (quando aplicável) na guia **preços e disponibilidade** ou **disponibilidade** .

- **Mercados**: cada plano deve estar disponível em pelo menos um mercado.  Você tem a opção de selecionar apenas os países "impostos remetidos", nos quais a Microsoft remete vendas e impostos sobre o uso em seu nome.
- **Preços**: os modelos de preços se aplicam apenas a planos para o aplicativo gerenciado do Azure, o SaaS e as ofertas de máquina virtual do Azure. Todos os planos para a mesma oferta devem usar o mesmo modelo de preços.  
- **Visibilidade do plano**: dependendo do tipo de oferta, você pode definir um público privado ou ocultar a oferta ou o plano do Azure Marketplace. Isso é explicado mais detalhadamente na [visibilidade do plano](#plan-visibility) posteriormente neste artigo.

> [!TIP]
> Recomendamos que você crie planos mais adequados para os padrões de uso de sua base de clientes de destino. Isso reduz os usuários de alternar freqüentemente planos com base em suas alterações de uso. Para obter um exemplo de uma oferta de SaaS com três planos de cobrança medidos, consulte [oferta de exemplo](./partner-center-portal/saas-metered-billing.md#sample-offer).

### <a name="pricing-models"></a>Modelos de preço

Você deve associar um modelo de preço a cada plano para os seguintes tipos de oferta. Cada um desses tipos de oferta tem modelos de preços diferentes disponíveis:

- **Aplicativo gerenciado do Azure**: taxa fixa (mensal) e preço com base no uso (dimensões de serviço de medição).
- **Software como serviço**: taxa fixa (mensal ou anual), por usuário e preço com base no uso (dimensões de serviço de medição). 
- **Máquina virtual do Azure**: traga sua própria licença (BYOL) e preços com base no uso. Para um modelo de preços com base no uso, você pode cobrar por núcleo, por tamanho de núcleo ou por mercado e tamanho de núcleo. Um modelo de licença do BYOL não permite cobranças adicionais com base no uso.   (As ofertas de máquina virtual BYOL não exigem um modelo de preços.)

Todos os planos para a mesma oferta devem usar o mesmo modelo de preços. Por exemplo, uma oferta de SaaS não pode ter um plano que seja uma taxa fixa e outro plano por usuário. Consulte a documentação da oferta específica para obter informações detalhadas.

Se você já tiver definido preços para seu plano em moeda de dólar americano (US$) e adicionar outra localização de mercado, o preço do novo mercado será calculado de acordo com as tarifas de câmbio atuais. Depois de salvar as alterações, você verá um link de **preços de exportação (xlsx)** que pode ser usado para revisar e alterar o preço de cada mercado antes da publicação.

> [!IMPORTANT]
> Depois que sua oferta for publicada, a opção de modelo de preços não poderá ser alterada.

Ofertas de SaaS de taxa simples e ofertas de aplicativos gerenciados dão suporte à cobrança limitada usando o serviço de medição do Marketplace. Esse é um modelo de cobrança baseado em uso que permite definir unidades não padrão, como largura de banda ou emails, que seus clientes pagarão com base no consumo. Consulte a documentação relacionada para saber mais sobre a cobrança limitada de [aplicativos gerenciados](./partner-center-portal/azure-app-metered-billing.md) e aplicativos [SaaS](./partner-center-portal/saas-metered-billing.md).

## <a name="custom-prices"></a>Preços personalizados

Para definir preços personalizados em um mercado individual, exporte, modifique e importe a planilha de preços. Você é responsável por validar esse preço e possuir essas configurações.

Examine seus preços cuidadosamente antes de publicar, pois há algumas restrições sobre o que pode ser alterado depois que um plano é publicado:

- Depois que um plano é publicado, o modelo de preços não pode ser alterado.
- Depois que um termo de cobrança for publicado para um plano, ele não poderá ser removido mais tarde.
- Depois que um preço para um mercado em seu plano for publicado, ele não poderá ser alterado posteriormente.

Os preços definidos em dólares de Estados Unidos (USD) são convertidos na moeda local de todos os mercados selecionados usando as taxas de câmbio atuais quando salvos. Valide esses preços antes de publicar exportando a planilha de preços e revisando o preço em cada mercado selecionado.

1. É necessário primeiro salvar as alterações de preços para habilitar a exportação de dados sobre preços. Próximo à parte inferior da guia **preços e disponibilidade** , selecione **salvar rascunho**.
1. Em **preços**, selecione o link **exportar dados de preços** .
1. Abra o arquivo exportedPrice.xlsx no Microsoft Excel.
1. Na planilha, faça as atualizações desejadas para suas informações de preço e, em seguida, salve o. Arquivo CSV.
    > [!NOTE]
    > Talvez seja necessário habilitar a edição no Excel antes que você possa atualizar o arquivo.
1. Na guia **preços e disponibilidade** , em **preços**, selecione o link **importar dados de preços** .
1. Na caixa de diálogo exibida, selecione **Sim**.
1. Selecione o arquivo de exportedPrice.xlsx que você atualizou e, em seguida, selecione **abrir**.

## <a name="plan-visibility"></a>Visibilidade do plano

Você pode fazer planos para alguns tipos de oferta publicamente disponíveis ou disponíveis para apenas um público (privado) específico. As ofertas com planos privados serão publicadas no portal do Azure. Saiba mais sobre planos privados em [ofertas privadas no Microsoft Commercial Marketplace](private-offers.md).

Você define seu público privado usando IDs de locatário do Azure ou IDs de assinatura do Azure, dependendo do tipo de oferta. Você pode inserir até 10 IDs manualmente ou importar até 10, 00 IDs de assinatura ou IDs de locatário 20.000 (conforme aplicável) com um arquivo. csv. Você também pode definir públicos privados para serviços de consultoria e as ofertas do Dynamics 365 que não têm planos.

Depois que sua oferta for publicada com um plano privado, você poderá atualizar o público ou optar por disponibilizar o plano para todos. Depois que um plano é publicado como visível para todos, ele deve permanecer visível para todos e não pode ser configurado como um plano privado novamente.

> [!NOTE]
> Um público privado difere de um público de visualização. Na página **disponibilidade** de alguns tipos de oferta, você pode definir um público que pode visualizar sua oferta antes que a oferta seja publicada ao vivo no mercado comercial. Embora a designação de público privado se aplique apenas a um plano específico, o público-alvo da visualização pode exibir todos os planos (privado ou não), mas somente durante o período de visualização limitado enquanto o plano está sendo testado e validado.

Você também pode optar por ocultar o plano inteiramente do Azure Marketplace se sua oferta for implantada como parte de um aplicativo gerenciado ou modelo de solução.

## <a name="free-trials"></a>Avaliações gratuitas

Você pode habilitar uma avaliação gratuita em planos para as ofertas de máquina virtual e SaaS do Azure.

> [!NOTE]
> Esta seção explica as avaliações gratuitas sobre os planos pagos para ofertas que são vendidas pela Microsoft. Isso é diferente das listagens de avaliação gratuita de parceiros que optam por processar suas transações de forma independente. Você pode criar uma listagem de avaliação gratuita (que não está associada a um plano) para os seguintes tipos de oferta:
> - Máquina virtual do Azure 
> - SaaS 
> - Dynamics 365 Business Central
> - Dynamics 365 para compromisso com o cliente & PowerApps
> - Dynamics 365 for Operations
>
> Para obter mais informações sobre opções de listagem, consulte [determinar sua opção de publicação](determine-your-listing-type.md).

As avaliações gratuitas têm suporte para todos os modelos de cobrança, exceto planos medidos. Os planos de SaaS permitem avaliações gratuitas de 1 mês. Os planos de máquina virtual do Azure permitem avaliações gratuitas de 1, 3 ou 6 meses.

Quando um cliente seleciona uma avaliação gratuita, coletamos suas informações de cobrança, mas não começamos a cobrar o cliente até que a avaliação seja convertida em uma assinatura paga. As avaliações gratuitas são automaticamente convertidas em uma assinatura paga no final da avaliação, a menos que o cliente cancele a assinatura antes que o período de avaliação termine.

Durante o período de avaliação, os clientes podem avaliar qualquer um dos planos com suporte na mesma oferta que tem uma avaliação gratuita habilitada. Se eles mudarem para uma avaliação diferente na mesma oferta, o período de avaliação não será reiniciado. Por exemplo, se um cliente usa uma avaliação gratuita por 15 dias e, em seguida, muda para uma avaliação gratuita diferente para a mesma oferta, o novo período de avaliação contará por 15 dias usado. A avaliação gratuita que o cliente estava avaliando quando o período de avaliação termina é aquela que é convertida automaticamente em uma assinatura paga.

Depois que um cliente seleciona uma avaliação gratuita para um plano, ele não pode converter para uma assinatura paga para esse plano até que o período de avaliação seja encerrado. Se um cliente optar por converter em um plano diferente dentro da oferta que não tem uma avaliação gratuita, a conversão ocorrerá, mas a avaliação gratuita será encerrada imediatamente e todos os dados serão perdidos.

> [!NOTE]
> Depois que um cliente começa a pagar por um plano, ele não pode obter uma avaliação gratuita na mesma oferta novamente, mesmo se ele mudar para outro plano que dá suporte a avaliações gratuitas.

Para obter informações sobre as assinaturas de cliente que estão participando de uma avaliação gratuita, use a nova propriedade `isFreeTrial` da API, que será marcada como verdadeira ou falsa. Para obter mais informações, confira a [API Get de Assinatura do SaaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md#get-subscription).

## <a name="next-steps"></a>Próximas etapas

- Para saber como adicionar ou atualizar planos em uma oferta existente, consulte [atualizar uma oferta existente no Marketplace comercial](./partner-center-portal/update-existing-offer.md).
- Para saber mais sobre as opções de transação e os modelos de preços associados, consulte recursos de transações do [Marketplace comercial](./marketplace-commercial-transaction-capabilities-and-considerations.md).
