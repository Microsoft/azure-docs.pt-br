---
title: Gerenciamento de vendas potenciais do marketplace comercial da Microsoft
description: Saiba mais sobre como gerar e receber vendas potenciais de clientes do seu Microsoft AppSource e ofertas do Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/01/2020
ms.openlocfilehash: 1d5f2248d94796d5e3ee76301642a95abddebfe4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94489328"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Vendas potenciais do cliente da sua oferta do marketplace comercial

AS vendas potenciais são clientes interessados ou que estão implantando suas ofertas do [Microsoft AppSource](https://appsource.microsoft.com) e do [Azure Marketplace](https://azuremarketplace.microsoft.com). Você poderá receber vendas potenciais do cliente depois que sua oferta for publicada no marketplace comercial. Este artigo explica os seguintes conceitos de gerenciamento de vendas potenciais:

* Como sua oferta do marketplace comercial gera vendas potenciais do cliente para garantir que você não perca as oportunidades de negócios. 
* Como conectar seu sistema de CRM (gerenciamento de relacionamento com o cliente) à sua oferta para que você possa gerenciar suas vendas potenciais em um local central.
* Os dados de vendas potenciais que enviamos para você para que você possa acompanhar os clientes que entraram em contato com você.

## <a name="generate-customer-leads"></a>Gerar vendas potenciais do cliente

Aqui estão os locais em que uma venda potencial é gerada:

- Um cliente consente em compartilhar informações depois de selecionar **Entrar em contato comigo** no marketplace comercial. Essa é uma venda potencial de *interesse inicial*. Compartilhamos informações com você sobre o cliente que expressou interesse em obter seu produto. O cliente potencial está no topo do funil de aquisição.

    ![Entre em contato comigo do Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Um cliente seleciona **Obtê-lo agora** (ou seleciona **Criar** no [portal do Azure](https://portal.azure.com/)) para obter sua oferta. Essa venda potencial é um *ativa*. Compartilhamos informações com você sobre o cliente que começou a implantar seu produto.

    ![Botão Obter agora do SQL](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Botão Criar do Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Um cliente seleciona **Test Drive** ou **Avaliação Gratuita** para experimentar sua oferta. As unidades de teste ou as avaliações gratuitas são oportunidades aceleradas para você compartilhar seus negócios instantaneamente com clientes potenciais sem nenhuma barreira de entrada.

    ![Botão Test Drive do Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Botão Avaliação Gratuita do Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Conectar-se ao seu sistema CRM

[!INCLUDE [Links to lead configuration for different CRM systems](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Entender os dados da venda potencial

Cada cliente potencial que você recebe durante o processo de aquisição de cliente tem dados em campos específicos. O primeiro campo a ser examinado é `LeadSource`, que segue este formato: **Ação-de-Origem** | **Oferta**.

**Origens**: O valor desse campo é preenchido com base no marketplace que gerou a venda potencial. Os valores possíveis são `"AzureMarketplace"`, `"AzurePortal"` e `"AppSource (SPZA)"`.

**Ações**: O valor desse campo é preenchido com base na ação que o cliente realizou no marketplace que gerou a venda potencial.

Os valores possíveis são:

- **"INS"** : significa *instalação*. Essa ação ocorre no Azure Marketplace ou no AppSource quando um cliente adquire seu produto.
- **"PLT"** : significa *avaliação conduzida pelo parceiro*. Essa ação ocorre no AppSource quando um cliente usa a opção **Entrar em contato comigo**.
- **"DNC"** : significa *não entrar em contato*. Essa ação ocorre no AppSource quando um parceiro que foi listado de modo cruzado na página do aplicativo solicitado ser contatado. Compartilhamos uma notificação de que esse cliente foi listado de modo cruzado em seu aplicativo, mas ele não precisa ser contatado.
- **"Create"** : essa ação ocorre apenas dentro do portal do Azure e é gerada quando um cliente compra sua oferta na conta dele.
- **"StartTestDrive"** : essa ação é apenas para a opção **Test Drive** e é gerada quando um cliente inicia o test drive.

**ofertas**: Você pode ter várias ofertas no marketplace comercial. O valor desse campo é preenchido com base na oferta que gerou a venda potencial. A ID do editor e a ID da oferta são enviadas neste campo e são os valores que você forneceu quando publicou a oferta no marketplace.

Os exemplos a seguir mostram valores no formato esperado `publisherid.offerid`: 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Informações do cliente

As informações do cliente são enviadas usando vários campos. O exemplo a seguir mostram as informações do cliente que estão contidas em um cliente potencial:

- Nome: John
- Sobrenome: Smith
- Email: crlima\@microsoft.com
- Telefone: 1234567890
- País: EUA
- Empresa: Microsoft
- Título: CTO

>[!NOTE]
>Nem todos os dados no exemplo anterior estão sempre disponíveis para cada cliente potencial. Como você obterá clientes potenciais de várias etapas, conforme mencionado na seção "Gerar vendas potenciais do cliente", a melhor maneira de lidar com vendas potenciais é eliminar a duplicação dos registros e personalizar os acompanhamentos. Dessa forma, cada cliente recebe uma mensagem apropriada e você cria uma relação exclusiva.

## <a name="best-practices-for-lead-management"></a>Melhores práticas para o gerenciamento de vendas potenciais

Aqui estão algumas recomendações para orientar os leads pelo seu ciclo de vendas:

- **Processo**: Defina um processo de vendas claro, com marcos, análise e responsabilidade clara da equipe.
- **Qualificação**: defina os pré-requisitos, que indicam se um cliente potencial foi totalmente qualificado. Os representantes de vendas ou marketing devem qualificar as vendas potenciais cuidadosamente antes de conduzi-las pelo processo de vendas completo.
- **Acompanhamento**: não se esqueça de acompanhar em 24 horas. Você obterá o cliente potencial em seu CRM de escolha imediatamente depois que o cliente implantar um test drive; Envie-os por email enquanto eles ainda estiverem em aquecimento. Solicite o agendamento de uma chamada telefônica para entender melhor se o seu produto é uma boa solução para seu problema. Espere que a transação típica exija várias chamadas de acompanhamento.
- **Incentivo**: incentivá suas vendas potenciais para ajudar você a obter uma margem de lucro mais alta. Fazer check-in, mas não Bombard-los. Recomendamos que você envie um email aos clientes potenciais pelo menos algumas vezes antes de fechá-los; Não desistir após a primeira tentativa. Lembre-se, esses clientes estão diretamente envolvidos em seu produto e gastaram tempo em uma avaliação gratuita; Eles são ótimos clientes potenciais.

Depois que a instalação técnica estiver em vigor, incorpore essas vendas potenciais à estratégia atual de vendas e marketing e aos processos operacionais. Estamos interessados em entender melhor seu processo geral de vendas e queremos trabalhar estreitamente com você para fornecer vendas potenciais de alta qualidade e dados suficientes para o seu sucesso. Apreciamos seus comentários sobre como otimizar e melhorar os leads que enviamos a você, com dados adicionais para ajudar a tornar esses clientes bem-sucedidos. Informe-nos se estiver interessado em [enviar comentários](mailto:AzureMarketOnboard@microsoft.com) e sugestões para que sua equipe de vendas tenha mais sucesso com vendas potenciais do marketplace comercial.

## <a name="next-steps"></a>Próximas etapas

- [Perguntas frequentes sobre gerenciamento de leads e solução de problemas](../lead-management-faq.md)