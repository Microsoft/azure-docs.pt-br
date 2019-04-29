---
title: Requisitos por Tipo de Listagem | Azure
description: Este artigo descreve os critérios de qualificação e os parceiros de requisitos de publicação tentando entender como publicar aplicativos no Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: ebe344d9f596f862fe5ffbfef083725e6527d0d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599325"
---
# <a name="requirements-by-listing-type"></a>Requisitos por tipo de listagem  
Os requisitos de conteúdo técnico e de marketing variam de acordo com a vitrine, com o tipo de oferta e com o tipo de listagem. Analise as especificações a seguir para verificar sua conformidade.  
1. Requisitos da Vitrine:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Requisitos do tipo de listagem e do tipo de oferta:  
    *   Para obter mais informações sobre tipos de listagem e tipos de oferta, visite a página Determinar o tipo de listagem para sua solução localizada em [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Requisitos da Vitrine: AppSource  
A tabela a seguir descreve os pré-requisitos necessários para a publicação no AppSource.  

| Requisito | Detalhes | Necessários ou recomendados |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Seu aplicativo deve permitir o logon único federado do Azure Active Directory (SSO federado do Azure AD) com o consentimento habilitado.<ul> <li>Para obter mais informações sobre como habilitar o SSO federado do Azure AD, visite a página Configurar o logon único para aplicativos que não estão na galeria de aplicativos do Azure Active Directory localizada em [docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Obrigatório |   
| ***Integração aos Serviços do Microsoft Cloud*** | Seu aplicativo deve se integrar com outros serviços do Microsoft Cloud, como serviços do Microsoft Power BI, Cortana Intelligence ou Microsoft Azure.<ul> <li>Um exemplo de um serviço do Microsoft Cloud é a Internet das Coisas.</li> </ul> | Recomendadas |  
| ***Público-alvo*** | Seu aplicativo deve ser para usuários de linha de negócios e proprietários de empresas. | Obrigatório | 
| ***Aplicativo SaaS (software como serviço) para empresas*** | Seu aplicativo deve atender aos seguintes requisitos.<ul> <li>Um aplicativo SaaS de linha de negócios</li> <li>Com foco no processo comercial</li> <li>Destinado a clientes comerciais</li> <li>Permita que os usuários usem suas credenciais de trabalho para entrar, como o nome de usuário e a senha</li> </ul> | Obrigatório |  
| ***Período de avaliação gratuita e experiência de avaliação*** | Seu aplicativo deve incluir uma das opções a seguir para um cliente usar seu aplicativo gratuitamente por um período limitado.<ul> <li>Forneça um método `try` para que os clientes possam iniciar uma avaliação do seu aplicativo no AppSource</li> <li>Forneça uma opção `request trial` no AppSource, para que os clientes possam solicitar uma versão de avaliação do seu aplicativo</li> </ul>A avaliação gratuita que você fornecer deve oferecer ao cliente um intervalo de tempo predefinido para testar seu aplicativo sem nenhum custo adicional. | Obrigatório |  
| ***Solução facilmente configurável, pronta para uso*** | Seu aplicativo deve ser fácil e rápido de instalar e configurar, sem nenhuma personalização necessária. | Obrigatório |  
| ***Gerenciamento de cliente potencial*** | Habilite o CRM para aceitar dados de clientes potenciais antes de recebê-los da vitrine.<ul> <li>Como exemplos de CRMs temos: Marketo, Microsoft Dynamics ou Salesforce</li> </ul> | Obrigatório |  
| ***Política de privacidade e termos de uso*** | Seu aplicativo deve fornecer um link para sua página de política de privacidade usando uma URL pública. Seus termos de uso devem ser fornecidos durante a publicação como texto. | Obrigatório |  
| ***Suporte*** | Seu aplicativo deve fornecer um link para sua página de suporte ao cliente usando uma URL pública. Se seu aplicativo for uma versão de avaliação, você deverá oferecer suporte sem custo adicional durante o período de avaliação. | Obrigatório |  

## <a name="storefront-requirements-azure-marketplace"></a>Requisitos da Vitrine: Azure Marketplace  
A seguir estão os pré-requisitos necessários para os tipos de listagem no Azure Marketplace.  

| Requisito | Detalhes | Tipo de listagem |  
|:--- |:--- |:--- |  
| ***Políticas de participação*** | Seu aplicativo deve seguir as políticas de participação do Azure Marketplace.<ul> <li>Para obter mais informações sobre as políticas de participação, visite a página Políticas de Participação do Azure Marketplace localizada em [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />transação<br />versão de avaliação |  
| ***Integração com a Microsoft*** | Suas ofertas devem usar ou estender os tipos de serviço do Microsoft Azure, como computação, rede ou armazenamento. Suas ofertas devem ser alinhadas a uma categoria existente do Azure Marketplace, como bancos de dados, segurança ou rede.<ul> <li>Para obter mais informações sobre as ofertas do Marketplace, visite a página Aplicativos do Marketplace localizada em [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | list<br />transação<br />versão de avaliação |  
| ***Público-alvo*** | Suas ofertas devem ser para profissionais de TI, desenvolvedores de nuvem ou outras funções técnicas do cliente. | list<br />transação<br />versão de avaliação |  
| ***Gerenciamento de cliente potencial*** | Habilite seu CRM (Marketo, Microsoft Dynamics ou Salesforce) para aceitar dados de cliente potencial antes de recebê-los da vitrine. | list<br />transação<br />versão de avaliação |  
| ***Política de privacidade e termos de uso*** | Seu aplicativo deve fornecer um link para sua página de política de privacidade usando uma URL pública. Seus termos de uso devem ser fornecidos durante a publicação como texto. | list<br />transação<br />versão de avaliação |  
| ***Suporte*** | Sua oferta deve fornecer um link para sua página de suporte ao cliente usando uma URL pública. Se sua oferta for uma versão de avaliação, você deverá oferecer suporte sem custo adicional durante o período de avaliação. | transação<br />versão de avaliação |    

## <a name="non-transact-listings"></a>Listagens não transacionais  
Esta seção descreve todos os tipos de oferta que não usam o tipo de listagem de Transação. 

### <a name="list"></a>Listar  
O tipo de listagem de Lista inclui os tipos de oferta a seguir nas vitrines do Marketplace.  

| Tipo de oferta | Vitrine | Detalhes |  
|:---        |:---        |:---     |  
| Serviços de Consultoria | AppSource | Requisitos: AppSource: Lista: Serviços de Consultoria |  
| Serviços de Consultoria | Azure Marketplace | Requisitos: Azure Marketplace: Lista: Serviços de Consultoria |  
| Entre em contato comigo | AppSource | [](#) |  
| Entre em contato comigo | Azure Marketplace | Requisitos: AppSource: Lista: Entre em contato comigo |  

#### <a name="requirements-appsource-list-consulting-service"></a>Requisitos: AppSource: Lista: Serviço de consultoria  

| Requisitos | Detalhes |  
|:--- |:--- |  
| Características de oferta do serviço | O serviço de consultoria deve atender aos critérios a seguir.<ul> <li>Entregar um contrato de escopo fixo, duração fixa, preço fixo (ou gratuito).</li> <li>Orientação principalmente para pré-vendas.</li> <li>Limitado a um único cliente.</li> <li>Realizar no site.</li> </ul> |  
| Requisitos do parceiro para Serviços de Consultoria | Você atende aos critérios na área de dados relevante para seu serviço.<table><tr><th>Área da solução</th><th>Critérios</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Tem competência Gold ou Silver de Gerenciamento de Relacionamento com o Cliente na Nuvem.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Enterprise Edition</td><td>Tem competência Gold ou Silver de Planejamento de Recursos Empresariais e receita de suas operações de nuvem nos 12 meses seguintes de US$ 25.000 ou mais.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Business Edition</td><td>Serve como o CSP (Provedor de Serviços de Nuvem) ou DPOR (Parceiro Digital de Registro) para um ou mais clientes.</td></tr><tr><td>Power BI</td><td>Atende aos critérios do Parceiro da Solução.</td></tr><tr><td>PowerApps</td><td>Tem uma solução Demonstração do Parceiro.</td></tr></table><ul> <li>Para obter mais informações sobre o gerenciamento de relacionamento com o cliente, visite a página Gerenciamento de Relacionamento com o Cliente na Nuvem localizada em [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Para obter mais informações sobre o planejamento de recursos, visite a página Planejamento de Recursos Empresariais localizada em [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Para obter mais informações sobre o CSP, visite a página Provedor de Serviços de Nuvem localizada em [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Para obter mais informações sobre o DPOR, visite a página Parceiro de Registro Digital e Associação de Parceiro localizada em [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Para obter mais informações sobre critérios de parceiro de solução, visite o documento Visão Geral do Parceiro de Solução e Incentivos localizado em [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Para obter mais informações sobre a demonstração do parceiro, visite a página Demonstração do Parceiro localizada em [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Requisitos: Azure Marketplace: Lista: Serviço de consultoria  

| Requisitos | Detalhes |  
|:--- |:--- |  
| Características de oferta do serviço | O serviço de consultoria deve atender aos critérios a seguir.<ul> <li>Entregar um contrato de escopo fixo, duração fixa, preço fixo (ou gratuito).</li> <li>Orientação principalmente para pré-vendas.</li> <li>Limitado a um único cliente.</li> <li>Realizar no site.</li> </ul> |  
| Requisitos do parceiro para Serviços de Consultoria | Você deve ter Silver ou Gold em uma das competências a seguir na área de dados relevante para seu serviço. <table><tr><th>Área da solução</th><th>Competência</th></tr><td>Infraestrutura e Cloud Platform</td><td>Plataforma de Nuvem<br />Data Center</td><tr><td>ISV e Desenvolvimento de Aplicativo</td><td>Desenvolvimento de Aplicativo<br />Integração de aplicativos<br />DevOps</td></tr><tr><td>Análise e Gerenciamento de Dados</td><td>Análise de dados<br />Plataforma de dados</td></tr></table><ul> <li>Para obter mais informações sobre as competências, visite a página Competências no Microsoft Partner Network localizada em [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Para obter mais informações sobre a listagem, visite a página Serviços de Consultoria do Azure Marketplace localizada em [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Avaliação  

| Tipo de oferta | Vitrine | Detalhes |  
|:---        |:---        |:---     |  
| Avaliação gratuita/SaaS | AppSource | Requisitos por tipo de listagem: Avaliação |  
| Avaliação gratuita/SaaS | Azure Marketplace | Requisitos: Azure Marketplace: Versão de avaliação: Avaliação gratuita/avaliação de SaaS |  
| Demonstração interativa | AppSource | Requisitos por tipo de listagem: Avaliação |  
| Demonstração interativa | Azure Marketplace | [Requisitos: Azure Marketplace: Versão de avaliação: Demonstração interativa](#requirements-azure-marketplace-trial-interactive-demo) |  
| Test drive | AppSource | Requisitos por tipo de listagem: Avaliação |  
| Test drive | Azure Marketplace | [Requisitos: Azure Marketplace: Versão de avaliação: Test drive](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Requisitos: Azure Marketplace: Avaliação  

| Requisito | Detalhes |  
|:--- |:--- |  
| Período de avaliação gratuita e experiência de avaliação | Seu cliente pode usar seu aplicativo gratuitamente por um tempo limitado.<br /><br />Seu cliente não é solicitado a pagar valores de licença ou de assinatura de seu aplicativo ou oferta. Seu cliente não é solicitado a pagar pelo produto ou serviço interno subjacentes da Microsoft. Todas as opções da versão de avaliação são implantadas em sua assinatura do Azure. Você tem a avaliação de controle exclusivo do gerenciamento e otimização de custos.<br /><br />Você deve escolher um avaliação gratuita, demonstração interativa ou test drive. Independente do que escolher, sua avaliação gratuita deverá oferecer ao cliente uma quantidade de tempo pré-definida para experimentar o aplicativo sem custo adicional.<ul> <li>Para iniciar o processo de criação de um test drive, envie um email para [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com).</li> </ul>Observação: As experiências de avaliação SaaS do Azure Marketplace devem permitir que os clientes usem suas credenciais de trabalho para entrar.<ul> <li>Para obter mais informações, visite a seção de experiências de avaliação do AppSource localizada em [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Solução facilmente configurável, pronta para uso | Seu aplicativo deve ser rápido para configurar e fácil de usar. |  
| Disponibilidade/tempo de atividade | Seu aplicativo ou plataforma SaaS deve ter um tempo de atividade de pelo menos 99,9%. |  
| Azure Active Directory | Sua oferta deve permitir o SSO (logon único) federado do Azure AD (Azure Active Directory) (SSO federado do Azure AD) com o consentimento habilitado. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Requisitos: Azure Marketplace: Versão de avaliação: Avaliação gratuita/avaliação de SaaS  

| Benefício | Requisito |  
|:--- |:--- |  
| Permite que um cliente teste seu produto antes de comprar com um método automático para converter para uso pago. Também permite provas de conceito para o cliente e o engajamento conjunto com as equipes de vendas da Microsoft. | A solução é uma máquina virtual ou modelo de solução.<br /><br />A solução é uma oferta de SaaS e você oferece um produto de SaaS multilocatário.<br /><br />Você tem uma experiência de primeira execução para colocar um cliente em atividade e execução rapidamente.<br /><br />Você tem um único locatário, mas está adicionando os clientes como usuários convidados. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Requisitos: Azure Marketplace: Versão de avaliação: Demonstração interativa  

| Benefício | Requisito |  
|:--- |:--- |  
| Permite que os clientes vejam sua solução em ação sem a complexidade da configuração. | Sua solução requer configurações complexas que seriam difíceis de obter no período da avaliação. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Requisitos: Azure Marketplace: Versão de avaliação: Test drive  

| Benefício | Requisito |  
|:--- |:--- |  
| Permite que um cliente teste o produto antes de comprar.<br /><br />Fornece uma experiência interativa de sua solução usando configurações pré-configuradas.<br /><br />Veja os benefícios adicionais pelo uso de um test drive abaixo.<ul> <li>27% das pesquisas de usuários no marketplace são refinadas pelos usuários para mostrar apenas ofertas com test drives.</li> <li>As ofertas com test drives geram 38% mais clientes potenciais que as ofertas sem test drive.</li> <li>36% das novas aquisições de clientes no marketplace originam-se de clientes que participaram de um test drive.</li> <li>Tests drive permitem que vendedores de campo da Microsoft entendam melhor o produto para esforços de venda conjunta.</li> </ul> | Sua solução é uma máquina virtual, um modelo de solução ou um aplicativo SaaS com um locatário único, ou é complicado de provisionar. <br /><br />Você não tem um método para converter sua avaliação para uma oferta paga. |  

---

## <a name="transact-specific-listings"></a>Listagens específicas da Transação

### <a name="transact"></a>Transação  

| Tipo de oferta | Vitrine | Detalhes |   
|:---        |:---        | :--- |  
| Aplicativos do Azure: Aplicativo gerenciado | Azure Marketplace | Requisitos: Azure Marketplace: Transação: Aplicativos do Azure: Aplicativo gerenciado |  
| Aplicativos do Azure: Modelo de solução | Azure Marketplace | Requisitos: Azure Marketplace: Transação: Aplicativos do Azure: Modelo de solução |  
| Contêineres | Azure Marketplace | [Requisitos: Azure Marketplace: Transação: Contêiner](#requirements-azure-marketplace-transact-container) |  
| Aplicativo SaaS  | Azure Marketplace | [Requisitos: Azure Marketplace: Transação: Aplicativo SaaS](#requirements-azure-marketplace-transact-saas-app) |  
| Máquina virtual | Azure Marketplace | [Requisitos: Azure Marketplace: Transação: Máquina virtual](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Requisitos: Azure Marketplace: Transação: Contêiner  

| Requisito | Detalhes |  
|:--- |:--- |  
| Cobrança e medição | Suporte a qualquer modelo de cobrança gratuito ou BYOL. |  
| Imagem baseada em docker | Suas imagens de contêiner devem estar baseadas no formato de imagem do Docker e devem ser extraídas dos Registros de Contêiner do Azure. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Requisitos: Azure Marketplace: Transação: Aplicativo SaaS  

| Requisito | Detalhes |  
|:--- |:--- |  
| Cobrança e medição | Sua oferta é estimada em uma taxa mensal de simples. No momento, não há suporte para opções *true-up* com base em uso e de preço com base em uso. |  
| Cancelamento | Sua oferta é cancelável pelo cliente a qualquer momento. |  
| Página inicial da transação | Hospede uma página inicial de transação do compartilhamento de marca do Azure. A página inicial permite aos clientes criar e gerenciar sua conta de serviço de SaaS. |  
| API de Assinatura de SaaS | Forneça um serviço que interaja com a Assinatura de SaaS para criar, atualizar e excluir um plano de serviço e uma conta de usuário. Todas as alterações críticas na API devem ter suporte em até 24 horas. Quaisquer alterações não críticas na API são atualizadas periodicamente. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Requisitos: Azure Marketplace: Transação: Máquina virtual  

| Requisito | Detalhes |  
|:--- |:--- | 
| Cobrança e medição | Sua VM deve dar suporte à cobrança mensal BYOL ou Pagamento Conforme o Uso. |  
| VHD (disco rígido virtual) compatível com Azure | As VMs devem ser criadas em Windows ou Linux.<ul> <li>Para obter mais informações sobre como criar um VHD Linux, consulte [Distribuições do Linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre como criar um VHD Windows, consulte [Criar um VHD compatível com o Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Próximas etapas
*   Visite a página [ do Azure Marketplace e do AppSource Publisher Guide ](./marketplace-publishers-guide.md).  

