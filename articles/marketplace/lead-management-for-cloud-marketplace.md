---
title: Líder de gerenciamento para o marketplace de nuvem | Azure Marketplace e AppSource
description: Uma visão geral dos vários tópicos relacionados a publicação de ofertas e artefatos técnicos para o Azure Marketplace e AppSource
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/05/2018
ms.author: yijenj
ms.openlocfilehash: 810298fc45becf132da6f082df7ad33e7af828aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769882"
---
# <a name="lead-management-for-cloud-marketplace"></a>Gerenciamento de cliente potencial para marketplace de nuvem


Os clientes são a essência de qualquer bom negócio. Na transformação das aquisições de produtos atuais, os comerciantes precisam se concentrar em se conectar diretamente com os clientes e criar uma relação. Por isso, gerar leads de alta qualidade é uma ferramenta essencial no ciclo de vendas. Depois de listar sua oferta no [Portal do Cloud Partner](https://cloudpartner.azure.com/), existem ferramentas habilitadas para que você receba informações de contato do cliente de modo programático, imediatamente depois que um cliente demonstra interesse ou implanta seu produto no Marketplace. 



## <a name="what-are-leads-in-the-marketplace"></a>Quais são os clientes potenciais no marketplace?

São clientes potenciais de clientes que estão interessados ou implantando seus produtos do Marketplace. Se seu produto estiver listado no Microsoft Azure Marketplace ou AppSource, você poderá receber clientes pontenciais dos clientes depois que estiver configurado corretamente do seu CRM para às suas listagens no Portal do Cloud Partner 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Como conectar seu sistema CRM com o portal do cloud partner

Para começar a receber clientes potenciais, o conector do Portal do Microsoft Cloud Partner é projetado de forma que possa ser conectado com a sua informação CRM para uma lista de sistema CRM disponível. Agora você pode aproveitar com facilidade os leads gerados pelo marketplace sem um esforço significativo de engenharia para integração a um sistema externo.

Aqui estão as instruções passo a passo sobre como se conectar a cada um dos destinos possíveis do cliente potencial:

**Dynamics CRM Online** - [Clique aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) para obter instruções sobre como configurar o Dynamics CRM Online para obter clientes potenciais.

**Marketo** - [Clique aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) para obter instruções sobre como configurar configurações de Lead do Marketo para obter leads.

**SalesForce** - [Clique aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) para obter instruções sobre como configurar sua instância do Salesforce para obter clientes potenciais.

**Tabela do Azure** – [Clique aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) para obter instruções sobre como configurar sua conta de armazenamento do Azure para obter clientes potenciais em uma tabela do Azure.

**Ponto de extremidade HTTPS** – [Clique aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) para obter instruções sobre como configurar o ponto de extremidade Https para obter clientes potenciais.

Depois de configurar corretamente o destino do lead e acessar Publicar na oferta, validaremos a conexão e enviaremos um lead de teste. Quando você exibe a oferta antes de entrar no ar, pode também testar a conexão do lead tentando adquirir a oferta no ambiente de visualização. É importante verificar se as configurações do lead estão atualizadas para que você não perca nenhum lead. Portanto, certifique-se de atualizar essas conexões sempre que algo for alterado do seu lado.


### <a name="what-are-the-next-steps"></a>Quais são as próximas etapas?

Quando a instalação técnica estiver em vigor, incorpore esses leads à estratégia atual de vendas e marketing e aos processos operacionais. Estamos interessados em entender melhor seu processo geral de vendas e queremos trabalhar estreitamente com você para fornecer leads de alta qualidade e dados suficientes para o seu sucesso. Apreciamos seus comentários sobre como otimizar e melhorar os leads que enviamos a você, com dados adicionais para ajudar a tornar esses clientes bem-sucedidos. Informe-nos se estiver interessado em fornecer feedback e sugestões para que sua equipe de vendas tenha mais sucesso com os Clientes Potenciais do Marketplace.



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Erros comuns de configuração de cliente potencial durante a publicação no portal do cloud partner 

**Não foi possível salvar o cliente potencial ao Dynamics CRM. Verifique as configurações de conta do Dynamics CRM. LastCRMError: Não é possível entrar no Dynamics CRM, LastCRMException:** 

> Se a autenticação do O365 foi selecionada, verifique se a conta de usuário e senha é válida. Se AAD tiver sido selecionado, verifique a ID de locatário, ID do aplicativo e correspondências de chave secreta do aplicativo que foi configurado no AAD. Siga as instruções [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics). Se o nome de usuário/senha da conta é válida, verifique se ele tem acesso ao Dynamics 365 e tem uma licença atribuída (as etapas 11 a 15 se usando o Microsoft Azure Active Directory ou as configurações de segurança se usando um usuário do Office). 

 
**Não foi possível salvar o cliente potencial ao Dynamics CRM. O usuário não tem permissões de criação para o atributo leadsourcecode na entidade de cliente potencial** 

> O aplicativo/usuário não tem funções de segurança para o gravador de cliente potencial do Microsoft Marketplace. Siga as etapas 11 a 15 se usando o Microsoft Azure Active Directory ou as configurações de segurança se usar um usuário do Office [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics).

**Não foi possível salvar o cliente potencial ao Dynamics CRM usando o Microsoft Azure Active Directory. Exception:: Locatário não encontrado. Esta instância pode ocorrer se não houver nenhuma assinatura ativa para o locatário.**  

> A ID de diretório fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Obtenha a ID de diretório com base nas instruções da etapa 2 (no Azure Active Directory, de [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Não foi possível salvar o cliente potencial ao Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser falhou - não há função atribuída ao usuário.**  

> Resolução: Atribua a função de segurança ao gravador de cliente potencial do Microsoft Marketplace. Siga as instruções [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) em configurações de Segurança 

**Não foi possível salvar o cliente potencial ao Dynamics CRM usando o Microsoft Azure Active Directory. Exception:: Aplicativo com identificador não foi localizado no diretório** 

> A Id do aplicativo fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Obtenha a ID de diretório com base nas instruções da Etapa 8 (no Microsoft Azure Active Directory de [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Não foi possível salvar o cliente potencial ao Dynamics CRM usando o Microsoft Azure Active Directory. Exception:: O identificador do locatário solicitado não é válido nem é um formato de domínio externo válido** 

> A ID de diretório fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Obtenha a ID de diretório com base nas instruções da Etapa 2 (no Microsoft Azure Active Directory de [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Não foi possível salvar o cliente potencial ao Dynamics CRM usando o Microsoft Azure Active Directory. Exception:: Erro ao validar credenciais.: Segredo do cliente inválido é fornecido.** 

> Resolução: Entre no portal do Azure, verifique se a chave do aplicativo corresponde ao que está no Portal do Cloud Partner. Gere senha com base na instrução na Etapa 10 (no Azure Active Directory), de [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Não foi possível salvar o cliente potencial ao Dynamics CRM. LastCRMError: O canal de solicitação atingiu o tempo limite ao aguardar uma resposta após 00:02:00. Aumente o valor de tempo limite passado à chamada para solicitação ou aumente o valor de SendTimeout na associação. O tempo alocado para essa operação pode ter sido uma parte de um tempo limite maior.**  

> Resolução: Entre no Portal do Cloud Partner, verifique os Detalhes da vitrine eletrônica >> Destino de cliente potencial >> URL, verifique se é uma instância válida do Dynamic CRM

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Quais são os clientes potenciais e por que são importantes para mim como um publicador no Marketplace?** 

Clientes potenciais são clientes que estão implantando seus produtos usando o Marketplace. Se o seu produto estiver listado no [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) ou [AppSource](https://appsource.microsoft.com/), você poderá receber os clientes potenciais de clientes que estejam interessados em seu produto, se você configurou o destino de cliente potencial em sua oferta.  


**Onde posso obter ajuda na configuração de meu destino de cliente potencial?** 

É possível encontrar documentação aqui: Para [Obter leads de clientes](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) ou enviar um tíquete de suporte por meio de aka.ms/marketplacepublishersupport selecione o tipo de oferta e o gerenciamento de leads. 



**É necessário configurar um destino de cliente potencial para publicar uma oferta no Marketplace?**

Sim, se você estiver publicando um aplicativo SaaS do contato Me ou dos Serviços de Consultoria.  
 


**Como confirmar se a configuração de cliente potencial está correta?**

Depois de configurar sua oferta e o destino do cliente potencial, publique sua oferta. Na etapa de validação de cliente potencial, o Marketplace enviará um líder de teste para o destino do cliente potencial configurado em sua oferta. 


**Como encontrar o cliente potencial de teste?**


Pesquise "MSFT_TEST" no destino do cliente potencial, aqui está um dados de clientes potenciais de teste de exemplo: 

company = MSFT_TEST_636573304831318844 

país: EUA 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

codificação = UTF-8 

codificação = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name> 

oid = 00Do0000000ZHog 

telefone = 1234567890 

title = MSFT_TEST_636573304831318844 

 

**Eu tenho uma oferta online, mas não estou vendo os clientes potenciais?**

Cada cliente potencial terá dados passados nos campos no destino do cliente potencial selecionado, os clientes potenciais virão neste formato: **Ação de origem | Oferta** 

  *Origens*:

    “AzureMarketplace”, 
    “AzurePortal”, 
    “TestDrive”,  
    “SPZA” (acronym for AppSource) 

  *Ações:*

    “INS” – Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    “PLT” – Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    “DNC” – Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    “Create” – This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    “StartTestDrive” – This is for Test Drives only and is whenever a customer starts their test drive. 


  *Ofertas:*:

    “checkpoint.check-point-r77-10sg-byol”, 
    “bitnami.openedxcypress”, 
    “docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a” 

 

  *Aqui estão os dados de exemplo das informações do cliente*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Saiba mais em [Informações de cliente potencial](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads). 


**Configurei o BLOB do Azure como meu cliente potencial de destino, por que não vejo o cliente potencial?** 

O cliente potencial só fica gravado quando você seleciona o armazenamento de BLOBs do Azure como destino do lead. Alterne para a tabela do Azure para receber o prazo de entrega real 


**Recebi um email do Marketplace, por que não é possível localizar o cliente potencial no meu CRM?**  

É possível que o domínio de email do usuário final é de. edu. Por motivos de privacidade, não passe dados de PII do domínio .edu. Envie um tíquete de suporte por meio de aka.ms/marketplacepublishersupport 


 **Configurei a Tabela do Azure/BLOB do Azure de acordo com o destino do meu cliente potencial, eu consigo ver os clientes potenciais?** 

Você pode acessar o blob ou tabela do portal do Azure, ou você pode baixar e instalar o [Gerenciador de Armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para exibir tabelas/blobs da sua conta de armazenamento do Azure. 


**Configurei a tabela do Azure como destino de cliente potencial, posso ser notificado sempre que um novo cliente potencial é enviado pelo Marketplace?** 

Sim, siga as instruções para configurar o backup de tabelas do Azure + função sobre a documentação [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table). 



**Configurei o Salesforce como destino como meu cliente potencial, por que não é possível localizar os clientes potenciais?** 

Verifique se a web para o formulário de cliente potencial é um campo obrigatório com base em uma lista de seleção. Em caso afirmativo, alterne o campo para um campo de texto não obrigatório.  
 

**Houve um problema com meu destino de cliente potencial e eu me perdi de alguns clientes potenciais. É possível tê-los enviados para mim por email?** 

Devido às diretivas de PII (informações de identificação particular), não é possível compartilhamos as informações de cliente potencial por meio de email não seguro. 



**Configurei o Armazenamento do Microsoft Azure (BLOB/Tabela) como meu destino de cliente potencial, quanto isso custará?** 

Dados de geração de cliente potencial (<1 GB para quase todos os publicadores). O custo dependerá do número de clientes potenciais recebido, se 1.000 potenciais são recebidos em um mês, isso custará cerca de 50 centavos. 
