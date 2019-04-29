---
title: Configurar clientes potenciais | Microsoft Docs
description: Configure clientes potenciais no Portal do Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 2a425e607ea7dac394ab90a3fed4d4026056bbc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61069470"
---
<a name="get-customer-leads"></a>Obter clientes potenciais
==================

Este artigo explica como criar clientes potenciais usando o Portal do Cloud Partner. Você pode se conectar esses clientes potenciais ao seu sistema de CRM e integrá-los ao pipeline de vendas.

## <a name="leads"></a>Clientes potenciais

Clientes potenciais são aqueles que estão interessados ou estão implantando seus produtos usando o [Azure Marketplace](https://azuremarketplace.microsoft.com/) ou o [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Marketplace

1.  O cliente usa um "test drive" da sua oferta. Os Test Drives são uma oportunidade acelerada de compartilhar seus negócios instantaneamente com clientes potenciais sem as barreiras à entrada. Todos os test drives geram um cliente potencial para o cliente que está interessado em experimentar seu produto para saber mais sobre ele. Saiba mais sobre os test drives em [Azure Marketplace Test Drive](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf) (Test drive do Azure Marketplace).

    ![Exemplos de test drive do Marketplace](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. O cliente consente em compartilhar suas informações depois de selecionar "Obter Agora". Esse cliente potencial é um **interesse inicial**, em que podemos compartilhar informações sobre o cliente que demonstrou interesse em obter o produto. O cliente potencial está no topo do funil de aquisição.

   ![Opção Obter agora](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Cliente seleciona "Comprar" no [Portal do Azure](https://portal.azure.com/) para obter seu produto. Esse cliente potencial é **ativo**, no qual podemos compartilhar informações sobre um cliente que começou a implantar seu produto.

   ![Opção de compra](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  O cliente faz um "Test Drive" da sua oferta. Os Test Drives são uma oportunidade acelerada de compartilhar seus negócios instantaneamente com clientes potenciais sem as barreiras à entrada. Todos os test drives gerarão um cliente potencial de um cliente que ficou interessado em experimentar o produto para conhecê-lo melhor. Saiba mais sobre Test Drives no [Test Drive do AppSource](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Exemplo de test drive](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  O cliente consente em compartilhar suas informações depois de selecionar "Obter Agora". Esse cliente potencial é um **interesse inicial**, no qual podemos compartilhar informações sobre o cliente que expressa interesse em obter o produto. O cliente potencial está no topo do funil de aquisição.

      ![Opção Obter agora](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  O cliente seleciona "Entrar em contato comigo" em sua oferta. Esse é um cliente potencial **ativo**, no qual podemos compartilhar informações sobre um cliente que solicita acompanhar as informações sobre seu produto.

    ![Opção Entrar em contato comigo](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Dados do lead
---------

Cada cliente potencial que você recebe durante o processo de aquisição de cliente tem dados em campos específicos. Como você obterá clientes potenciais de várias etapas, a melhor maneira de lidar com eles é eliminar a duplicação e personalizar os acompanhamentos. Dessa forma, cada cliente recebe uma mensagem apropriada, e você cria uma relação exclusiva.

### <a name="lead-source"></a>Origem do cliente potencial

O formato de uma fonte de cliente potencial é **Fonte**-**Ação** |  **Oferta**

**Origens**: "AzureMarketplace", "AzurePortal", "TestDrive", and "AppSource (SPZA)"

**Ações**:
- "INS" – instalação. Essa ação ocorre no Azure Marketplace ou no AppSource quando um cliente compra seu produto.
- "PLT" – significa Partner Led Trial (parceiro lidera a avaliação). Essa ação ocorre no AppSource quando um cliente usa a opção Entrar em contato comigo.
- "DNC" – Do Not Contact (Não entrar em contato). Essa ação ocorre no AppSource quando um parceiro que foi listado de forma cruzada na página do aplicativo solicitado ser contatado. Estamos compartilhando a informação de que esse cliente foi listado de forma cruzada em seu aplicativo, mas ele não precisa ser contatado.
- "Criar" – essa ação ocorre apenas dentro do Portal do Azure e é gerada quando um cliente compra sua oferta na conta dele.
- "StartTestDrive" – essa ação ocorre apenas para test drives e é gerada quando um cliente inicia o test drive.

**Ofertas**

Os exemplos a seguir mostram os identificadores exclusivos que são atribuídos a um publicador e uma oferta específica: checkpoint.check-ponto-r77-10sg-byol, bitnami.openedxcypress e docusign.3701c77e-1cfa - 4c 56 91e6 3ed0b622145a.


### <a name="customer-info"></a>Informações do cliente

Os campos no exemplo a seguir mostram as informações do cliente que estão contidas em um cliente potencial.
- FirstName: John
- Sobrenome: Smith
- Email: crlima\@microsoft.com
- Telefone: 1234567890
- País: EUA
- Empresa: Microsoft
- Título: CTO

>[!Note]
>Nem todos os dados no exemplo anterior estão sempre disponíveis para cada cliente potencial.

Estamos trabalhando ativamente para aperfeiçoar os leads, portanto, se houver um campo de dados que você não visualizou aqui, mas gostaria de ter, [envie-nos seus comentários](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Como conectar o sistema CRM com o Portal do Cloud Partner
------------------------------------------------------------

Para começar a conquistar clientes potenciais, criamos o conector de Gerenciamento de Clientes Potenciais no Portal do Cloud Partner para que você possa inserir facilmente as informações de CRM e deixar que a gente faça a conexão para você. Agora você pode aproveitar com facilidade os leads gerados pelo marketplace sem um esforço significativo de engenharia para integração a um sistema externo.

![Conector de Gerenciamento de Clientes Potenciais](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Podemos gravar clientes potenciais em uma variedade de sistemas de CRM ou diretamente em uma Tabela do Armazenamento do Azure, na qual você pode gerenciar os leads da forma que quiser. Cada um dos links a seguir fornecem instruções para conectar a possíveis destinos de clientes potenciais:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) para obter instruções de como configurar o Dynamics CRM Online para obter leads.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) para obter instruções de como definir configurações de cliente potencial do Marketo para obter clientes potenciais.
-    [SalesForce](./cloud-partner-portal-lead-management-instructions-salesforce.md) para obter instruções de como configurar sua instância do Salesforce para obter clientes potenciais.
-    [Tabela do Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md) para obter instruções de como configurar a conta de Armazenamento do Azure para obter clientes potenciais em uma Tabela do Azure.
-   [Ponto de extremidade HTTPS](./cloud-partner-portal-lead-management-instructions-https.md) para obter instruções de como configurar o ponto de extremidade HTTPS para obter clientes potenciais.

Depois de configurar o destino do cliente potencial e publicar sua oferta, validaremos a conexão e enviaremos um cliente potencial de teste. Quando você exibe a oferta antes de entrar no ar, pode também testar a conexão do lead tentando adquirir a oferta no ambiente de visualização. É importante verificar se as configurações de cliente potencial estão atualizadas para que você não perca nenhum cliente potencial. Portanto, atualize essas conexões sempre que algo for alterado do seu lado.

<a name="what-next"></a>Próximos passos
----------

Quando a instalação técnica estiver em vigor, incorpore esses leads à estratégia atual de vendas e marketing e aos processos operacionais. Estamos muito interessados em entender melhor seu processo geral de vendas e queremos trabalhar estreitamente com você para fornecer leads de alta qualidade e dados suficientes para o seu sucesso. Apreciamos seus comentários sobre como otimizar e melhorar os leads que enviamos a você, com dados adicionais para ajudar a tornar esses clientes bem-sucedidos. Informe-nos se você tiver interesse em [fornecer comentários](mailto:AzureMarketOnboard@microsoft.com) e sugestões para que sua equipe de vendas tenha mais sucesso com os clientes potenciais do Marketplace.
