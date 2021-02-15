---
title: Perguntas e solução de problemas de gerenciamento de leads-Microsoft Partner Center
description: Leia sobre erros e perguntas comuns ao configurar clientes potenciais do Marketplace comercial no Partner Center
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 681498073a5f111e3babd50443623589ffeec02f
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330913"
---
# <a name="common-questions-and-troubleshooting-for-lead-configuration"></a>Perguntas comuns e solução de problemas de configuração de cliente potencial

Este artigo responde a algumas perguntas comuns sobre o gerenciamento de leads para suas ofertas de mercado comercial. Ele também aborda os erros que você pode encontrar ao configurar clientes potenciais para seu sistema CRM (gerenciamento de relacionamento com o cliente) no Partner Center.

## <a name="common-questions-about-lead-management"></a>Perguntas comuns sobre o gerenciamento de vendas potenciais

#### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Como posso obter ajuda na configuração de meu destino de cliente potencial?

Confira [clientes potenciais de sua oferta do Marketplace comercial](partner-center-portal/commercial-marketplace-get-customer-leads.md) para obter uma visão geral de como conectar seu sistema CRM às ofertas do seu Marketplace comercial. Se você tiver um erro, examine as diretrizes de solução de problemas abaixo. Para obter mais suporte, envie um tíquete de suporte por meio da [ajuda e suporte do Partner Center](https://aka.ms/marketplacepublishersupport). Em seguida, selecione **Criação de oferta** > **Seu tipo de oferta** > **Configuração de gerenciamento de vendas potenciais**.

#### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>É necessário configurar um destino de venda potencial para publicar uma oferta no marketplace comercial?

A resposta depende do tipo de oferta que você está publicando. SaaS (software como serviço) e o Dynamics 365 Customer Engagement usam **Entre em contato comigo** para listar todas as ofertas do Dynamics 365 para finanças e operações, todas as ofertas do Dynamics 365 Business Central e todas as ofertas de Serviços de Consultoria. Assim, eles exigem uma conexão com um destino de venda potencial. Se o tipo de oferta não estiver listado, uma conexão com um destino de venda potencial não será necessária. Recomendamos configurar um destino de venda potencial para não perder as oportunidades de negócios.

#### <a name="how-can-i-find-the-test-lead"></a>Como encontrar a venda potencial de teste?

Pesquise `"MSFT_TEST"` em seu destino de venda potencial. Veja abaixo um exemplo de líder de teste da Microsoft. Observe que o formato do Lead de teste varia dependendo do destino do cliente potencial.

```
{
    "userDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

#### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Eu tenho uma oferta online, por que não estou vendo as vendas potenciais?

Verifique se a conexão com o destino da venda potencial é válida. Enviaremos a você uma venda potencial de teste depois de selecionar **Publicar** na oferta no Partner Center. Se você vir a venda potencial de teste, a conexão será válida. Você também pode testar sua conexão de venda potencial tentando adquirir a versão prévia da oferta durante a etapa de versão prévia. Selecione **Obter agora**, **Entre em contato comigo** ou **Avaliação gratuita** na listagem no marketplace comercial.

Além disso, verifique se você está procurando os dados corretos. Consulte [entender os dados do Lead](partner-center-portal/commercial-marketplace-get-customer-leads.md) para obter uma explicação dos dados do Lead que enviamos para o destino do cliente potencial.

#### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Configurei o armazenamento de Blobs do Azure como meu destino de venda potencial. Por que não vejo a venda potencial?

O armazenamento de Blobs do Azure não tem mais suporte como um destino de venda potencial, portanto, você não tem nenhuma venda potencial do cliente gerada pela sua oferta. Alterne para qualquer uma das outras [opções de destino de venda potencial](partner-center-portal/commercial-marketplace-get-customer-leads.md). 

#### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Recebi um email do marketplace comercial. Por que não consigo encontrar a venda potencial no meu CRM?

É possível que o domínio de email do usuário final é de. edu. Por motivos de privacidade, não enviamos informações pessoais do domínio .edu. Envie um tíquete de suporte por meio da [Ajuda e suporte do Partner Center](https://aka.ms/marketplacepublishersupport).

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Configurei uma tabela do Azure como meu destino de venda potencial. Como posso exibir as vendas potenciais?

Você pode acessar os dados da venda potencial armazenados na tabela do Azure por meio do portal do Azure. Você também pode baixar e instalar o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para exibir os dados da tabela da sua conta de armazenamento do Azure.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Configurei uma tabela do Azure como meu destino de venda potencial. Posso ser notificado sempre que uma nova venda potencial do marketplace comercial for enviada?

Sim. Siga as instruções em [Configurar o gerenciamento de venda potencial usando uma tabela do Azure](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) para configurar um Microsoft Flow que envia um email se uma venda potencial é adicionada à tabela do Azure.

#### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Configurei o Salesforce como destino como meu cliente potencial. Por que não consigo localizar as vendas potenciais?

Verifique se o formulário Web-to-lead é um campo obrigatório com base em uma lista de seleção. Se for, mude o campo para um campo de texto não obrigatório.

#### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Houve um problema com meu destino de venda potencial e eu perdi algumas vendas potenciais. Elas podem ser enviadas por email para mim?

Devido a políticas de informações pessoais, não podemos compartilhar informações de venda potencial por meio de emails não seguros.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Configurei uma tabela do Azure como meu destino de venda potencial. Quanto isso custará?

Os dados de geração de venda potencial estão baixos. São inferiores a 1 GB para quase todos os editores. O custo depende do número de vendas potenciais recebidas. Por exemplo, se 1.000 vendas potenciais forem recebidas em um mês, o custo será de cerca de 50 centavos. Para obter mais informações sobre preços de armazenamento, confira [Visão geral de preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

Se sua pergunta não for respondida, entre em contato com o Suporte da Microsoft por meio da [Ajuda e suporte do Partner Center](https://aka.ms/marketplacepublishersupport). Em seguida, selecione **Criação de oferta** > **Seu tipo de oferta** > **Configuração de gerenciamento de vendas potenciais**.

#### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Estou recebendo notificações por email quando novas vendas potenciais do cliente são recebidas. Como posso configurar outra pessoa para receber esses emails?

Acesse sua oferta no Partner Center e vá para a página de **Configuração da oferta** > **Gerenciamento de venda potencial** > **Editar**. Atualize os endereços de email no campo **Email de contato**.

## <a name="troubleshooting-lead-configuration-errors"></a><a id="publishing-config-errors"></a> Solucionando problemas de erros de configuração de Lead

**Não foi possível salvar o cliente potencial ao Dynamics CRM. Verifique as configurações de conta do Dynamics CRM. LastCRMError: Não é possível entrar no Dynamics CRM, LastCRMException:** 

> Se Microsoft 365 autenticação foi selecionada, verifique se a conta de usuário e a senha são válidas. Se Azure Active Directory foi selecionado, verifique se a ID do locatário, a ID do aplicativo e a chave secreta do aplicativo correspondem ao que foi configurado na Azure Active Directory. Siga as instruções [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Se o nome de usuário/senha da conta forem válidos, verifique se eles têm acesso ao Dynamics 365 e têm uma licença atribuída (as etapas 11 a 15 se estiver sendo usado o Microsoft Azure Active Directory ou as configurações de segurança se estiver sendo usado um usuário do Office). 

**Não foi possível salvar o cliente potencial ao Dynamics CRM. O usuário não tem permissões de criação para o atributo leadsourcecode na entidade de cliente potencial** 

> O aplicativo/usuário não tem funções de segurança para o gravador de cliente potencial do Microsoft Marketplace. Siga as etapas 11-15 se estiver usando Azure Active Directory ou configurações de segurança se estiver usando um usuário do Office [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Não foi possível salvar o cliente potencial ao Dynamics CRM usando o Microsoft Azure Active Directory. Exception:: Locatário não encontrado. Esta instância pode ocorrer se não houver nenhuma assinatura ativa para o locatário.**  

> A ID de diretório fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Obtenha a ID do diretório com base nas instruções na etapa 2 (em Azure Active Directory) [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Não foi possível salvar o cliente potencial ao Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser falhou - não há função atribuída ao usuário.**  

> Resolução: Atribua a função de segurança ao gravador de cliente potencial do Microsoft Marketplace. Siga as instruções [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) em Configurações de segurança.

**Não foi possível salvar o cliente potencial ao Dynamics CRM usando o Microsoft Azure Active Directory. Exception:: Aplicativo com identificador não foi localizado no diretório** 

> A Id do aplicativo fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Obtenha a ID de diretório com base nas instruções da Etapa 8 (no Microsoft Azure Active Directory de [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Não foi possível salvar o cliente potencial ao Dynamics CRM usando o Microsoft Azure Active Directory. Exception:: O identificador do locatário solicitado não é válido nem é um formato de domínio externo válido** 

> A ID de diretório fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Obtenha a ID de diretório com base nas instruções da Etapa 2 (no Microsoft Azure Active Directory de [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Não foi possível salvar o cliente potencial ao Dynamics CRM usando o Microsoft Azure Active Directory. Exception:: Erro ao validar credenciais.: Segredo do cliente inválido é fornecido.** 

> Resolução: Entre no portal do Azure, verifique se a chave do aplicativo corresponde ao que está no Partner Center. Gere senha com base na instrução na Etapa 10 (no Azure Active Directory), de [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Não foi possível salvar o cliente potencial ao Dynamics CRM. LastCRMError: O canal de solicitação atingiu o tempo limite ao aguardar uma resposta após 00:02:00. Aumente o valor de tempo limite passado à chamada para solicitação ou aumente o valor de SendTimeout na associação. O tempo alocado para essa operação pode ter sido uma parte de um tempo limite maior.**  

> Resolução: entre no Partner Center, marque a configuração da oferta >> clientes potenciais do cliente >> URL, verifique se é uma instância dinâmica de CRM válida.

