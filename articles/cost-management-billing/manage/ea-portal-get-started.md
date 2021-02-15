---
title: Introdução ao Azure Enterprise Portal
description: Este artigo explica como os clientes do Azure EA (Contrato Enterprise do Azure) usam o Azure Enterprise Portal.
author: bandersmsft
ms.reviewer: baolcsva
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 88bb4c24489b973aa4230ba6f2b34756f83c7dda
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030681"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Introdução ao Azure Enterprise Portal

Este artigo ajuda os clientes diretos e indiretos do Azure EA (Contrato Enterprise do Azure) a começar a usar o [Azure Enterprise Portal](https://ea.azure.com). Obtenha informações básicas sobre:

- A estrutura do Azure Enterprise Portal.
- Funções usadas no Azure Enterprise Portal.
- Criação de assinatura.
- Análise de custos no Azure Enterprise Portal e no portal do Azure.

## <a name="get-started-with-ea-onboarding"></a>Introdução à integração do EA

Para obter um guia de integração do EA do Azure, confira [Guia de integração do EA do Azure (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).

Veja este vídeo para assistir a uma sessão de integração completa do Azure Enterprise Portal:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="understanding-ea-user-roles-and-introduction-to-user-hierarchy"></a>Noções básicas sobre as funções de usuário do EA e introdução à hierarquia de usuário

Para ajudar a gerenciar o uso e o gasto de sua organização, os clientes do Azure com um Contrato Enterprise (EA) podem atribuir cinco funções administrativas distintas:

- Administrador corporativo
- Administrador da empresa (somente leitura)
- Administrador de departamento
- Administrador de departamento (somente leitura)
- Proprietário da conta

Cada função tem um grau variável de limites e permissões de usuário. Para obter mais informações, confira [Estrutura da organização e permissões por função](./understand-ea-roles.md#organization-structure-and-permissions-by-role).

## <a name="activate-your-enrollment-create-a-subscription-and-other-administrative-tasks"></a>Ativar seu registro, criar uma assinatura e outras tarefas administrativas

Para obter mais informações sobre como ativar seu registro, criar um departamento ou uma assinatura, adicionar administradores e proprietários de conta e outras tarefas administrativas, confira [Administração do Portal do EA do Azure](./ea-portal-administration.md).

Se você quiser saber mais sobre como transferir uma assinatura Enterprise para uma assinatura Paga Conforme o Uso, confira [Transferências do Azure Enterprise](./ea-transfers.md).

## <a name="view-usage-summary-and-download-reports"></a>Exibir resumo de uso e baixar relatórios

Você pode gerenciar e agir sobre a fatura do EA do Azure. A fatura representa a cobrança e deve ser examinada quanto à precisão.

Para ver o resumo de uso, baixar relatórios e gerenciar as faturas de registro, confira [Faturas de registro do Azure Enterprise](./ea-portal-enrollment-invoices.md).

## <a name="now-that-youre-familiar-with-the-basics-here-are-some-additional-links-to-help-you-get-onboarded"></a>Agora que você está familiarizado com os conceitos básicos, aqui estão alguns links adicionais para ajudá-lo a se integrar

[Os preços do EA do Azure](./ea-pricing-overview.md) fornecem detalhes sobre como o uso é calculado e passa pelos encargos de vários serviços do Azure no Contrato Enterprise em que os cálculos são mais complexos.

Se você quiser saber como as reservas do Azure para instâncias reservadas de VM podem ajudar você a economizar dinheiro com seu Registro Enterprise, confira [instâncias reservadas de VM do EA do Azure](./ea-portal-vm-reservations.md).

Para obter informações sobre quais APIs REST usar com o registro do Azure Enterprise e uma explicação sobre como resolver problemas comuns com APIs REST, confira [APIs REST do Azure Enterprise](./ea-portal-rest-apis.md).

[Os contratos e as emendas do EA do Azure](./ea-portal-agreements.md) descrevem como os contratos e as emendas do EA do Azure podem afetar o acesso, o uso e os pagamentos dos serviços do Azure.

O [Azure Marketplace](./ea-azure-marketplace.md) explica como os clientes e parceiros do EA podem ver os encargos do marketplace e habilitar as compras no Azure Marketplace.

Para obter explicações sobre as tarefas comuns que um administrador do EA de parceiro realiza no Portal do EA do Azure, confira [Administração do Portal do EA do Azure para parceiros](./ea-partner-portal-administration.md).

## <a name="get-started-on-azure-ea---faq"></a>Introdução às perguntas frequentes do EA do Azure

Esta seção fornece detalhes sobre as perguntas típicas feitas pelos clientes durante o processo de integração.  

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>Associei acidentalmente minha conta do Azure existente ao registro EA do Azure. Como resultado, perdi meu crédito mensal. Posso ter meu crédito mensal de volta?

Se você entrar como um proprietário da conta do EA do Azure com as mesmas credenciais da sua Assinatura do Visual Studio, poderá recuperar seu benefício individual do Azure da Assinatura do Visual Studio executando uma das seguintes ações:

- Exclua o proprietário da conta do Azure Enterprise Portal, depois de remover ou mover as assinaturas do Azure associadas. Em seguida, inscreva-se novamente para os benefícios individuais do Azure para o Visual Studio.
- Exclua o assinante do Visual Studio do site de administração no VLSC e atribua novamente a assinatura a uma conta com credenciais diferentes desta vez. Em seguida, inscreva-se novamente para os benefícios individuais do Azure para o Visual Studio.

### <a name="what-type-of-subscription-should-i-create"></a>Que tipo de assinatura devo criar?

O Azure Enterprise Portal oferece dois tipos de assinaturas para clientes empresariais:

- Microsoft Azure Enterprise, que é ideal para:
  - Utilização em qualquer tipo de produção
  - Melhores preços com base no gasto da infraestrutura

  Para obter mais informações, [entre em contato com o departamento de vendas do Azure](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Desenvolvimento/Teste Enterprise, que é ideal para:
  - Todas as cargas de trabalho de desenvolvimento/teste da equipe
  - Cargas de trabalho de desenvolvimento/teste individual, de média a pesada
  - Acesso a imagens especiais do MSDN e taxas de serviço preferenciais

  Para obter mais informações, confira [oferta Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>O nome da minha assinatura é o mesmo que o nome da oferta. Devo alterar o nome da assinatura para algo significativo para minha organização?

Quando você cria uma assinatura, o nome usa como padrão o tipo de oferta que você escolhe. Recomendamos alterar o nome da assinatura para algo que facilite o rastreamento da assinatura.

Para alterar o nome:

1. Entre em [https://account.windowsazure.com](https://account.windowsazure.com).
1. Selecione a lista de assinaturas.
1. Selecione a assinatura que deseja editar.
1. Selecione o ícone **Gerenciar Assinatura**.
1. Edite os detalhes da assinatura.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Como faço para acompanhar os custos incorridos por um centro de custo?

Para acompanhar o custo por centro de custo, você precisa definir o centro de custo em um dos seguintes níveis:

- department
- Conta
- Subscription

Dependendo daquilo que precisa, você poderá usar o mesmo centro de custo para acompanhar o uso e os custos associados a um centro de custo específico.

Por exemplo, para acompanhar os custos de um projeto especial em que vários departamentos estão envolvidos, pode ser interessante definir o centro de custo no nível de uma assinatura para controlar o uso e os custos.

Você não pode definir um centro de custo no nível de serviço. Caso você queira controlar o uso no nível de serviço, você pode usar o recurso _Marca_ disponível no nível de serviço.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Como faço para acompanhar o uso e o gasto pelos diversos departamentos da organização?

Você pode criar quantos departamentos forem necessários em seu registro do EA do Azure. Para acompanhar o uso corretamente, não compartilhe assinaturas entre departamentos.

Depois de criar departamentos e assinaturas, você poderá ver os dados no relatório de uso. Essas informações podem ajudá-lo a acompanhar o uso e gerenciar custos e gastos no nível do departamento.

Você também pode acessar os dados de uso por meio da API de relatórios. Para obter informações detalhadas e o código de exemplo, confira as [APIs REST do Azure Enterprise](./ea-portal-rest-apis.md).

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Posso definir uma cota de gastos e receber alertas à medida que me aproximar do meu limite?

Você pode definir uma cota de gastos no nível do departamento e o sistema o notificará automaticamente quando seus limites de gastos atingirem 50%, 75%, 90% e 100% da cota definida.

Para definir sua cota de gastos, selecione um departamento e, em seguida, selecione o ícone Editar. Depois de editar os detalhes do limite de gastos, selecione **Salvar**.

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Usei grupos de recursos para implementar o RBAC e acompanhar o uso. Como faço para exibir os detalhes de uso associados?

Se você usar _grupos de recursos_ e _marcas_, essas informações serão rastreadas no nível de serviço e você poderá acessá-las no arquivo detalhado de uso (CSV) para download. Confira a opção [baixar o relatório de uso](https://ea.azure.com/report/downloadusage) no Azure Enterprise Portal.

Você também pode acessar o uso por meio da API. Para obter informações detalhadas e o código de exemplo, confira as [APIs REST do Azure Enterprise](./ea-portal-rest-apis.md).

> [!NOTE]
> Você só pode aplicar marcas em recursos com suporte a operações do Azure Resource Manager. Se você tiver criado uma máquina virtual, uma rede virtual ou um armazenamento por meio do modelo de implantação clássica (como por meio do portal clássico), não poderá aplicar uma marcação a esse recurso. Para dar suporte à marcação, implante esses recursos novamente por meio do Resource Manager. Todos os outros recursos oferecem suporte à marcação.

### <a name="can-i-perform-analyses-using-power-bi"></a>Posso executar análises usando o Power BI?

Sim. Com o pacote de conteúdo do Microsoft Azure Enterprise para Power BI, você pode:

- Importar e analisar rapidamente o consumo do Azure para seu Registro Enterprise.
- Descobrir qual departamento, conta ou assinatura consumiu a maior parte do uso.
- Saber qual serviço sua organização usou mais.
- Acompanhar as tendências de gastos e uso.

Para usar o Power BI:

1. Acesse o site do Power BI.
1. Entre com uma conta corporativa ou de estudante válida.

   A conta corporativa ou de estudante pode ser a mesma ou uma diferente da usada para acessar o registro pelo Azure Enterprise Portal.
1. No painel de serviços, selecione o bloco Microsoft Azure Enterprise e selecione **Conectar**.
1. Na tela **Conectar-se ao Azure Enterprise**, insira:
    - URL do ambiente do Azure: [https://ea.azure.com](https://ea.azure.com)
    - Número de meses: entre 1 e 36
    - Número de registro: o número de seu registro
1. Selecione **Avançar**.
1. Insira a Chave de API na caixa da **Chave de Conta**.

   Você pode encontrar a chave de API no Azure Enterprise Portal. Procure na guia **Baixar Uso** e, em seguida, selecione **Chave de acesso da API**. Copie e cole a chave na caixa **Chave de Conta** no Power BI.

Dependendo do tamanho do conjunto de dados, pode levar entre cinco e 30 minutos para que os dados sejam carregados no Power BI.

O relatório do Power BI está disponível para clientes diretos, indiretos e parceiros do EA do Azure que podem exibir informações de cobrança.

## <a name="next-steps"></a>Próximas etapas

- Os administradores do Azure Enterprise Portal devem ler [Administração do Azure Enterprise Portal](ea-portal-administration.md) para aprender sobre tarefas administrativas comuns.
- Se você precisar de ajuda com a solução de problemas do Azure Enterprise Portal, confira [Solucionar problemas de acesso ao Azure Enterprise Portal](ea-portal-troubleshoot.md).