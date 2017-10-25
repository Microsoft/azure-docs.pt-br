---
title: "Guia estratégico de prova de conceito do Azure Active Directory: blocos de construção | Microsoft Docs"
description: "Explorar e implementar rapidamente os cenários de Identidade e Gerenciamento de Acesso"
services: active-directory
keywords: azure active directory, cartilha, Prova de Conceito, PoC
documentationcenter: 
author: dstefanMSFT
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: dstefan
ms.openlocfilehash: bdbdebe069b3150bed4aa26f1f6e677a66f75f32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Guia estratégico de prova de conceito do Azure Active Directory: blocos de construção

## <a name="catalog-of-roles"></a>Catálogo de funções

| Função | Descrição | Responsabilidade da PoC (prova de conceito) |
| --- | --- | --- |
| **Arquitetura de Identidade / equipe de desenvolvimento** | Essa equipe geralmente é a que projeta a solução, implementa protótipos, aprovações de unidades e, finalmente, entrega para operações | Ela fornece os ambientes e é quem avalia os diferentes cenários a partir da perspectiva de capacidade de Gerenciamento |
| **Equipe de Operações de Identidade Local** | Gerencia as diferentes fontes de identidade locais: Florestas do Active Directory, Diretórios LDAP, Sistemas de RH e Provedores de Identidade do WS-Federation . | Fornece acesso aos recursos locais necessários para os cenários PoC.<br/>Devem interferir o mínimo possível|
| **Técnicos Proprietários do Aplicativo** | Técnicos proprietários dos diferentes serviços e aplicativos de nuvem que serão integrados ao Azure AD | Fornecer detalhes sobre aplicativos SaaS (potencialmente instâncias para teste) |
| **Administrador Global do AD do Azure** | Gerencia a configuração do Azure AD | Fornece credenciais para configurar o serviço de sincronização. Geralmente, a mesma Arquitetura de Identidade da equipe durante a prova de conceito, mas separada durante a fase de operações|
| **Equipe do Banco de Dados** | Proprietários da infraestrutura do Banco de Dados | Fornece acesso ao ambiente SQL (ADFS ou Azure AD Connect) para preparações de cenário específico.<br/>Devem interferir o mínimo possível |
| **Equipe de Rede** | Proprietários da infraestrutura de Rede | Fornece acesso exigido no nível da rede aos servidores de sincronização para acessar fontes de dados e serviços de nuvem (regras de firewall, portas abertas, regras etc.) corretamente |
| **Equipe de segurança** | Define a estratégia de segurança, analisa os relatórios de segurança de várias fontes e acompanha nas descobertas. | Fornecer cenários de avaliação de segurança de destino |

## <a name="common-prerequisites-for-all-building-blocks"></a>Pré-requisitos comuns a todos os blocos de construção

A seguir, estão alguns pré-requisitos necessários para qualquer prova de conceito com o Azure AD Premium.

| Pré-requisito | Recursos |
| --- | --- |
| Locatário Azure AD definido com uma assinatura Azure válida | [Como obter um locatário do Active Directory do Azure](active-directory-howto-tenant.md)<br/>**Observação:** se você já tem um ambiente com licenças do Azure AD Premium, é possível obter uma assinatura de limite zero, navegando até https://aka.ms/accessaad <br/>Saiba mais em: https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ and https://technet.microsoft.com/library/dn832618.aspx |
| Domínios definidos e verificados | [Adicionar um nome de domínio personalizado ao Azure Active Directory](active-directory-domains-add-azure-portal.md)<br/>**Observação:** Algumas cargas de trabalho, como o Power BI podem ter provisionado um locatário do Azure AD nos bastidores. Para verificar se um determinado domínio está associado a um locatário, navegue até https://login.microsoftonline.com/{domain}/v2.0/.well-known/openid-configuration. Se você obtiver uma resposta bem-sucedida, então o domínio já está atribuído a um locatário e a tomada de controle pode ser necessária. Nesse caso, contate a Microsoft para obter mais orientações. Saiba mais sobre as opções de tomada de controle em: [O que é Inscrição de Autoatendimento do Azure?](active-directory-self-service-signup.md) |
| Avaliação do Azure AD Premium ou EMS habilitada | [Azure Active Directory Premium gratuito por um mês](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Você atribuiu licenças do Azure AD Premium ou EMS a usuários de PoC | [Licencie a si mesmo e seus usuários no Azure Active Directory](active-directory-licensing-get-started-azure-portal.md) |
| Credenciais de Administrador Global do Azure AD | [Atribuindo funções de administrador no Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md) |
| Opcional mas altamente recomendável: ambiente de laboratório paralelo como um fallback | [Pré-requisitos do Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>Sincronização de diretórios - PHS (sincronização de hash de senha) - nova instalação

Aproximar o tempo para Concluir: uma hora para menos de 1.000 usuários PoC

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Servidor para executar o Azure AD Connect | [Pré-requisitos do Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |
| Usuários Poc no mesmo domínio e parte de um grupo de segurança e UO | [Instalação personalizada do Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| Recursos do Azure AD Connect necessários para a prova de conceito são identificados | [Conectar o Active Directory com o Azure Active Directory - Configurar recursos de sincronização](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Você precisou de credenciais para ambientes de nuvem e locais  | [Azure AD Connect: Contas e permissões](./connect/active-directory-aadconnect-accounts-permissions.md) |

### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Baixar a versão mais recente do Azure AD Connect | [Baixe o Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) |
| Instalar o Azure Connect AD com o caminho mais simples: Expresso <br/>1. Filtrar para o destino OU para minimizar o tempo de Ciclo de Sincronização<br/>2. Escolher o conjunto de destino de usuários no grupo local.<br/>3. Implantar os recursos necessários para os outros temas de prova de conceito | [Azure AD Connect: Instalação personalizada: Filtragem de domínio/UO](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Azure AD Connect: Instalação personalizada: Filtragem baseada no grupo](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect: Integrando suas identidades locais com o Azure Active Directory: Configurar Recursos de Sincronização](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Abra a UI do Azure AD Connect e veja os perfis em execução concluídos (Importar, sincronizar e exportar) | [Sincronização do Azure AD Connect: Agendador](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| Abra o [portal de gerenciamento do Azure AD](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/), vá para a folha "Todos os Usuários", adicione a coluna "Origem da autoridade" e verá que os usuários aparecem corretamente marcados como sendo provenientes do "Windows Server AD" | [Portal de gerenciamento do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>Considerações

1. Examine as considerações de segurança de sincronização de hash de senha [aqui](./connect/active-directory-aadconnectsync-implement-password-synchronization.md).  Se a sincronização de hash de senha para usuários de produção piloto definitivamente não for uma opção, considere as seguintes alternativas:
   * Crie usuários de teste no domínio de produção. Certifique-se de não sincronizar nenhuma outra conta
   * Mover para um ambiente de teste de aceitação do usuário
2.  Se você quer buscar federação convém compreender que os custos associados a uma solução federada com Provedor de Identidade local excedem o POC e, calcule isso em relação aos benefícios que você está procurando:
    * Ele está no caminho crítico, então é necessário projetar para alta disponibilidade
    * É um serviço local que você precisa para planejamento de capacidade
    * É um serviço local que é necessário monitorar/manter/aplicar patch

Saiba mais: [Compreendendo a identidade do Office 365 e o Azure Active Directory - Identidade Federada](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>Identidade visual

Tempo estimado para Conclusão: 15 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Ativos (imagens, logotipos, etc.); Para melhor visualização certifique-se de que os ativos possuem os tamanhos recomendados. | [Adicionar identidade visual à sua página de entrada no Azure Active Directory](active-directory-branding-custom-signon-azure-portal.md) |
| Opcional: Se o ambiente tiver um servidor ADFS, acesse o servidor para personalizar o tema da Web | [Personalização de entrada do usuário AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| Computador cliente para executar a experiência de logon do usuário final |  |
| Opcional: Dispositivos móveis para validar experiência |  |

### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Vá para o Portal de Gerenciamento do Azure AD | [Portal de Gerenciamento do AD do Azure - Marca da Empresa](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| Carregue os ativos para a página de logon (logotipo Hero, logotipo pequeno, rótulos, etc.). Opcionalmente, se você tiver o AD FS alinhe os mesmos recursos com as páginas de logon do ADFS | [Adicionar identidade visual da empresa às páginas de entrada e do Painel de acesso:Elementos Personalizáveis ](active-directory-add-company-branding.md) |
| Aguarde alguns minutos para que a alteração tenha pleno efeito |  |
| Faça logon com as credenciais de usuário POC https://MyApps.microsoft.com |  |
| Confirme a aparência no navegador | [Adicionar identidade visual da empresa às páginas de entrada e do Painel de acesso](active-directory-add-company-branding.md) |
| Opcionalmente, confirme a aparência em outros dispositivos |  |

### <a name="considerations"></a>Considerações

Se a aparência antiga permanecer após a personalização, libere o cache de cliente do navegador e repita a operação.

## <a name="group-based-licensing"></a>Licenciamento baseado em grupo

Tempo estimado para Conclusão: 10 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Todos os usuários de prova de conceito fazem parte de um grupo de segurança (nuvem ou local) | [Criar um grupo e adicionar membros no Azure Active Directory](active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Vá para a folha de licenças no Portal de Gerenciamento do Azure AD | [Portal de Gerenciamento do Azure AD: Licenciamento](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| Atribua as licenças para o grupo de segurança com usuários de prova de conceito. | [Atribuir licenças a um grupo de usuários no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) |

### <a name="considerations"></a>Considerações

Em caso de qualquer problema, vá para [Cenários, limitações e problemas conhecidos com o uso de grupos para gerenciar o licenciamento no Azure Active Directory](active-directory-licensing-group-advanced.md)

## <a name="saas-federated-sso-configuration"></a>Configuração de SSO Federado SaaS

Tempo estimado para Conclusão: 60 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Ambiente de teste do aplicativo SaaS disponível. Neste guia, usamos o ServiceNow como um exemplo.<br/>É altamente recomendável usar uma instância de teste para minimizar fricção ao navegar na qualidade dos dados existentes e mapeamentos. | Vá para https://developer.servicenow.com/app.do#!/home para iniciar o processo de obtenção de uma instância de teste |
| Acesso de administrador para o console de gerenciamento do ServiceNow | [Tutorial: Integração do Active Directory do Azure com o ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Conjunto de destino de usuários para atribuir o aplicativo. Recomenda-se um grupo de segurança que contém os usuários de prova de conceito. <br/>Se não for possível criar o grupo, então atribua os usuários diretamente ao aplicativo para a prova de conceito | [Atribuir um usuário ou um grupo a um aplicativo empresarial no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |

### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Compartilhar o tutorial para todos os atores da Documentação da Microsoft  | [Tutorial: Integração do Active Directory do Azure com o ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Defina uma reunião de trabalho e siga as etapas do tutorial com cada ator. | [Tutorial: Integração do Active Directory do Azure com o ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Atribua o aplicativo para o grupo identificado nos pré-requisitos. Se a prova de conceito tiver acesso condicional no escopo, você poderá rever posteriormente e adicionar MFA e semelhantes. <br/>Observe que isso será iniciado no processo de provisionamento (se configurado) |  [Atribuir um usuário ou um grupo a um aplicativo empresarial no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) <br/>[Criar um grupo e adicionar membros no Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Use o Portal de Gerenciamento do AD do Azure para adicionar o aplicativo de ServiceNow da Galeria| [Portal de Gerenciamento do Azure AD: Aplicativos Empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[O que há de novo no gerenciamento de Aplicativos Empresariais no Azure Active Directory](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| Na folha "Logon único" do Aplicativo ServiceNow, habilite "Logon único baseado em SAML" |  |
| Preencha os campos "URL de Entrada" e "Identificador" com a URL de ServiceNow<br/>Marque a caixa para "Ativar novo certificado"<br/>e em Salvar configurações |  |
| Abra a folha "Configurar ServiceNow" na parte inferior do painel para exibir instruções personalizadas para a configuração do ServiceNow |  |
| Siga as instruções para configurar o ServiceNow |  |
| Na folha "Provisionamento" do Aplicativo ServiceNow habilite o provisionamento "Automático" | [Gerenciamento de conta de usuário para aplicativos da empresa no novo portal do Azure](active-directory-enterprise-apps-manage-provisioning.md) |
| Aguarde alguns minutos enquanto o provisionamento é concluído.  Enquanto isso, você pode verificar os relatórios de provisionamento |  |
| Faça logon em https://myapps.microsoft.com/ como um usuário de teste que possui acesso | [O que é o Painel de Acesso?](active-directory-saas-access-panel-introduction.md) |
| Clique no bloco do aplicativo que acabou de ser criado. Confirmar o acesso |  |
| Opcionalmente, você pode verificar os relatórios de uso do aplicativo. Observe que há alguma latência, portanto, é necessário esperar algum tempo para ver o tráfego nos relatórios. | [Relatórios de atividades de entrada no portal do Azure Active Directory: Uso de aplicativo gerenciado](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Políticas de retenção de relatório do Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Considerações

1. Acima, o [Tutorial](active-directory-saas-servicenow-tutorial.md) refere-se à antiga experiência de gerenciamento do Azure AD. Mas a prova de conceito baseia-se em experiência de [Início Rápido ](active-directory-enterprise-apps-whats-new-azure-portal.md#quick-start-get-going-with-your-new-application-right-away).
2. Se o aplicativo de destino não estiver presente na galeria, então é possível usar "Traga seu próprio aplicativo". Saiba mais em: [O que há de novo no gerenciamento de Aplicativos Empresariais no Azure Active Directory: Adicionar aplicativos personalizados de um local](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>Configuração do SSO de senha de SaaS

Tempo estimado para Conclusão: 15 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Ambiente de teste para aplicativos SaaS. Um exemplo de SSO de senha é HipChat e o Twitter. Para qualquer outro aplicativo, você precisa da URL exata da página com o formulário de entrada html. | [Twitter no Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[HipChat no Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| Testar contas para os aplicativos. | [Inscrever-se para Twitter](https://twitter.com/signup?lang=en)<br/>[Inscreva-se para avaliação gratuita: HipChat](https://www.hipchat.com/sign_up) |
| Conjunto de destino de usuários para atribuir o aplicativo. Recomenda-se um grupo de segurança que contém os usuários de prova de conceito. | [Atribuir um usuário ou um grupo a um aplicativo empresarial no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Acesso de administrador local em um computador para implantar a extensão do Painel de Acesso para o Internet Explorer, Chrome ou Firefox | [Extensão do Painel de Acesso do IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extensão do Painel de Acesso do Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extensão do Painel de Acesso do Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Instalar a extensão do navegador | [Extensão do Painel de Acesso do IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extensão do Painel de Acesso do Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extensão do Painel de Acesso do Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Configurar um Aplicativo da Galeria | [O que há de novo no gerenciamento de Aplicativos Empresariais no Azure Active Directory: A nova e aprimorada galeria de aplicativos](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Configurar SSO de Senha | [Gerenciamento de logon único para aplicativos empresariais no novo portal do Azure: Logon baseado em senha ](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Atribua o aplicativo para o grupo identificado nos Pré-requisitos | [Atribuir um usuário ou um grupo a um aplicativo empresarial no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Faça logon em https://myapps.microsoft.com/ como um usuário de teste que possui acesso |  |
| Clique no bloco do aplicativo que acabou de ser criado. | [O que é o Painel de Acesso?: SSO baseado em senha sem provisionamento de identidade](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Forneça a credencial de aplicativo | [O que é o Painel de Acesso?: SSO baseado em senha sem provisionamento de identidade](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Feche o navegador e repita o logon. Agora, o usuário deve ver acesso transparente ao aplicativo. |  |
| Opcionalmente, você pode verificar os relatórios de uso do aplicativo. Observe que há alguma latência, portanto, é necessário esperar algum tempo para ver o tráfego nos relatórios. | [Relatórios de atividades de entrada no portal do Azure Active Directory: Uso de aplicativo gerenciado](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Políticas de retenção de relatório do Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Considerações

Se o aplicativo de destino não estiver presente na galeria, então é possível usar "Traga seu próprio aplicativo". Saiba mais em: [O que há de novo no gerenciamento de Aplicativos Empresariais no Azure Active Directory: Adicionar aplicativos personalizados de um local](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Tenha em mente os seguintes requisitos:
   * O aplicativo deve ter uma URL de logon conhecida
   * A página de entrada deve conter um formulário HTML com um mais campos de texto do que as extensões do navegador podem preencher automaticamente. No mínimo, ele deve conter o nome de usuário e senha.

## <a name="saas-shared-accounts-configuration"></a>Configuração de Contas Compartilhadas SaaS

Tempo estimado para Conclusão: 30 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| A lista de aplicativos de destino e as URLs de entrada exatas antecipadamente. Por exemplo, você pode usar o Twitter. | [Twitter no Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Inscrever-se para Twitter](https://twitter.com/signup?lang=en) |
| Credencial compartilhada para esse aplicativo SaaS. | [Compartilhando contas usando o Azure AD](active-directory-sharing-accounts.md)<br/>[Transferência de senha automatizada do Azure AD para Facebook, Twitter e LinkedIn agora na versão prévia! - Blog de segurança e mobilidade corporativa] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/ ) |
| Credenciais para pelo menos dois membros da equipe que irá acessar a mesma conta. Eles devem ser parte de um grupo de segurança. | [Atribuir um usuário ou um grupo a um aplicativo empresarial no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Acesso de administrador local em um computador para implantar a extensão do Painel de Acesso para o Internet Explorer, Chrome ou Firefox | [Extensão do Painel de Acesso do IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extensão do Painel de Acesso do Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extensão do Painel de Acesso do Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Instalar a extensão do navegador | [Extensão do Painel de Acesso do IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extensão do Painel de Acesso do Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extensão do Painel de Acesso do Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Configurar um Aplicativo da Galeria | [O que há de novo no gerenciamento de Aplicativos Empresariais no Azure Active Directory: A nova e aprimorada galeria de aplicativos](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Configurar SSO de Senha | [Gerenciamento de logon único para aplicativos empresariais no novo portal do Azure: Logon baseado em senha ](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Atribua o aplicativo ao grupo identificado nos Pré-requisitos ao atribuir-lhes credenciais | [Atribuir um usuário ou um grupo a um aplicativo empresarial no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Efetue login como diferentes usuários que acessam o aplicativo como a **mesma conta compartilhada.**  |  |
| Opcionalmente, você pode verificar os relatórios de uso do aplicativo. Observe que há alguma latência, portanto, é necessário esperar algum tempo para ver o tráfego nos relatórios. | [Relatórios de atividades de entrada no portal do Azure Active Directory: Uso de aplicativo gerenciado](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Políticas de retenção de relatório do Azure Active Directory](active-directory-reporting-retention.md) |


### <a name="considerations"></a>Considerações

Se o aplicativo de destino não estiver presente na galeria, então é possível usar "Traga seu próprio aplicativo". Saiba mais em: [O que há de novo no gerenciamento de Aplicativos Empresariais no Azure Active Directory: Adicionar aplicativos personalizados de um local](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Tenha em mente os seguintes requisitos:
   * O aplicativo deve ter uma URL de logon conhecida
   * A página de entrada deve conter um formulário HTML com um mais campos de texto do que as extensões do navegador podem preencher automaticamente. No mínimo, ele deve conter o nome de usuário e senha.

## <a name="app-proxy-configuration"></a>Configuração de Proxy de Aplicativo

Tempo estimado para Conclusão: 20 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Uma assinatura premium ou básica do Microsoft Azure AD e um diretório do Azure AD do qual você seja administrador global | [Edições do Active Directory do Azure](active-directory-editions.md) |
| Um aplicativo web hospedado no local que você deseja configurar para acesso remoto |  |
| Um servidor executando o Windows Server 2012 R2 ou Windows 8.1 ou posterior em que você possa instalar o Conector do Proxy de Aplicativo | [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-understand-connectors.md) |
| Se houver um firewall no caminho, verifique se o ele está aberto para que o Conector possa fazer solicitações HTTPS (TCP) para o Proxy de Aplicativo | [Habilitar o Proxy de Aplicativo no Portal do Azure: Pré-requisitos do Proxy de Aplicativo](active-directory-application-proxy-enable.md#application-proxy-prerequisites) |
| Se sua organização usa servidores proxy para se conectar à Internet, confira a postagem do blog Trabalhar com servidores proxy locais existentes para obter detalhes sobre como configurá-los | [Trabalhar com servidores proxy locais existentes](application-proxy-working-with-proxy-servers.md) |


### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Instalar um conector no servidor | [Habilitar o Proxy de Aplicativo no Portal do Azure: Instalar e registrar o Conector](active-directory-application-proxy-enable.md#install-and-register-a-connector) |
| Publicar o aplicativo local no Azure AD como um aplicativo de Proxy de aplicativo | [Publicar aplicativos usando o Proxy de Aplicativo do AD do Azure](application-proxy-publish-azure-portal.md) |
| Atribuir usuários de teste | [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD: Adicionar um usuário de teste](application-proxy-publish-azure-portal.md#add-a-test-user) |
| Opcionalmente, configure uma experiência de logon único para seus usuários | [Fornecer logon único com o Proxy de Aplicativo do Azure AD](application-proxy-sso-azure-portal.md) |
| Testar o aplicativo entrando no portal do MyApps como usuário atribuído | https://myapps.microsoft.com |

### <a name="considerations"></a>Considerações

1. Embora sugerimos colocar o conector em sua rede corporativa, há casos em que você perceberá melhor desempenho colocando-o na nuvem. Saiba mais em: [Considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure Active Directory](application-proxy-network-topology-considerations.md)
2. Para obter mais detalhes de segurança e como isso fornece uma solução de acesso remoto particularmente segura, mantendo apenas conexões de saída, consulte: [Considerações de segurança para acessar aplicativos remotamente usando o Proxy de Aplicativo do Azure AD](application-proxy-security-considerations.md)

## <a name="generic-ldap-connector-configuration"></a>Configuração do Conector LDAP Genérico

Tempo estimado para Conclusão: 60 minutos

> [!IMPORTANT]
> Esta é uma configuração avançada que exige alguma familiaridade com o FIM/MIM. Se usada em produção, aconselhamos consulgar as perguntas sobre essa configuração através do [Suporte Premier](https://support.microsoft.com/premier).

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Azure AD Connect instalado e configurado | Bloco de construção: [Sincronização de Diretórios - PHS (sincronização de hash de senha)](#directory-synchronization--password-hash-sync-phs--new-installation) |
| Atendendo aos requisitos de instância ADLDS | [Referência técnica de Conector LDAP genérica: Visão geral do conector LDAP Genérica](./connect/active-directory-aadconnectsync-connector-genericldap.md#overview-of-the-generic-ldap-connector) |
| Lista de cargas de trabalho que os usuários estão usando e atributos associados a essas cargas de trabalho | [Sincronização do Azure AD Connect: Atributos sincronizados com o Active Directory do Azure](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Adicionar Conector LDAP Genérica | [Referência Técnica do Conector LDAP Genérica: Criar um novo Conector](./connect/active-directory-aadconnectsync-connector-genericldap.md#create-a-new-connector) |
| Criar perfis de execução para o conector criado (importação completa, importação Delta, sincronização completa, sincronização Delta, exportação) | [Criar um Perfil de Execução de Agente de Gerenciamento](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)<br/> [Usando conectores com o Sync Service Manager do Azure AD Connect](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| Execute o perfil de importação completa e verifique se há objetos no espaço conector | [Pesquisar um Objeto do Espaço Conector](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)<br/>[Usando conectores com o Sync Service Manager do Azure AD Connect: Pesquisar Espaço Conector ](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| Criar regras de sincronização para que os objetos no Metaverso tenham atributos necessários para cargas de trabalho | [Sincronização do Azure AD Connect: Práticas recomendadas para alterar a configuração padrão: Alterações nas Regras de Sincronização](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Sincronização do Azure AD Connect: Noções Básicas sobre Provisionamento Declarativo](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Azure AD Connect Sync: Noções básicas sobre Expressões de Provisionamento Declarativo](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| Inicie o ciclo de sincronização completa | [Sincronização do Azure AD Connect: Iniciar o agendador](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| Em caso de problemas execute a solução de problemas | [Solucionar problemas de um objeto que não está sincronizando com o Azure AD](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) |
| Verifique se o usuário LDAP pode entrar e acessar o aplicativo | https://myapps.microsoft.com |

### <a name="considerations"></a>Considerações

> [!IMPORTANT]
> Esta é uma configuração avançada que exige alguma familiaridade com o FIM/MIM. Se usada em produção, aconselhamos consulgar as perguntas sobre essa configuração através do [Suporte Premier](https://support.microsoft.com/premier).

## <a name="groups---delegated-ownership"></a>Grupos - Propriedade Delegada

Tempo estimado para Conclusão: 10 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Aplicativo de SaaS (SSO federado ou SSO de senha) foi já configurado | Bloco de construção: [Configuração de SSO Federado SaaS](#saas-federated-sso-configuration) |
| Grupo de Nuvem que é atribuído o acesso ao aplicativo #1 é identificado | Bloco de construção: [Configuração de SSO Federado SaaS](#saas-federated-sso-configuration) <br/>[Criar um grupo e adicionar membros no Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| As credenciais para o proprietário do grupo estão disponíveis | [Gerenciar o acesso a recursos com Grupos do Azure Active Directory](active-directory-manage-groups.md) |
| As credenciais para o operador de informações acessam os aplicativos foram identificados | [O que é o Painel de Acesso?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Identificar o grupo que recebeu acesso ao aplicativo e configurar o proprietário de determinado grupo| [Gerenciar as configurações para um grupo no Azure Active Directory ](active-directory-groups-settings-azure-portal.md) |
| Faça logon como o proprietário do grupo, consulte a associação no guia grupos do painel de acesso | [Página de Gerenciamento de Grupos do Azure Active Directory](https://account.activedirectory.windowsazure.com/r/#/groups) |
| Adicionar o operador de informações que você deseja testar |  |
| Faça logon como o operador de informações, confirme se o bloco está disponível | [O que é o Painel de Acesso?](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>Considerações

Se o aplicativo tiver o provisionamento habilitado, talvez seja necessário aguardar alguns minutos para o provisionamento ser concluído antes de acessar o aplicativo como o operador de informações.

## <a name="saas-and-identity-lifecycle"></a>Ciclo de vida de identidade e SaaS

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Aplicativo de SaaS (SSO federado ou SSO de senha) foi já configurado | Bloco de construção: [Configuração de SSO Federado SaaS](#saas-federated-sso-configuration) |
| Grupo de Nuvem que é atribuído o acesso ao aplicativo #1 é identificado | Bloco de construção: [Configuração de SSO Federado SaaS](#saas-federated-sso-configuration) <br/>[Criar um grupo e adicionar membros no Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| As credenciais para o operador de informações acessam os aplicativos foram identificados | [O que é o Painel de Acesso?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Remover o usuário do grupo ao qual o aplicativo é atribuído | [Gerenciar associação de grupo de usuários em seu locatário do Azure Active Directory](active-directory-groups-members-azure-portal.md) |
| Aguarde alguns minutos para desprovisionamento | [Provisionamento de Usuário de Aplicativo SaaS Automatizado do Azure AD: Como fazer o trabalho de provisionamento automatizado?](active-directory-saas-app-provisioning.md#how-does-automatic-provisioning-work) |
| Em uma sessão separada do navegador, faça logon como o operador de informações para o portal de aplicativos e confirme se o bloco está ausente | http://myapps.microsoft.com |


### <a name="considerations"></a>Considerações

Expandir o cenário de prova de conceito para cenários de ausência e/ou saídas. Se o usuário foi desabilitado no AD local ou removido não há mais possibilidade de fazer logon no aplicativo SaaS.

## <a name="self-service-access-to-application-management"></a>Acesso de autoatendimento ao Gerenciamento de Aplicativo

Tempo estimado para Conclusão: 10 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Identificar os usuários de prova de conceito que irão solicitar o acesso aos aplicativos, como parte do grupo de segurança | Bloco de construção: [Configuração de SSO Federado SaaS](#saas-federated-sso-configuration) |
| Aplicativo de Destino Implantado | Bloco de construção: [Configuração de SSO Federado SaaS](#saas-federated-sso-configuration) |

### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Vá para a folha Aplicativos Empresariais no Portal de Gerenciamento do Azure AD | [Portal de Gerenciamento do Azure AD: Aplicativos Empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| Configurar o Aplicativo de Pré-requisitos com serviço de autoatendimento | [O que há de novo no gerenciamento de Aplicativos Empresariais no Azure Active Directory: Configurar o acesso ao aplicativo de autoatendimento](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| Faça logon como o operador de informações para o portal de aplicativos | http://myapps.microsoft.com |
| Observe o botão "+Adicionar aplicativo" na parte superior da página. Use-o para obter acesso ao aplicativo |  |

### <a name="considerations"></a>Considerações

Os aplicativos escolhidos podem ter requisitos de provisionamento, portanto, ir imediatamente para o aplicativo poderá causar alguns erros. Se o aplicativo escolhido oferecer suporte para provisionamento com azure ad e estiver configurado, você poderá usá-lo como uma oportunidade para mostrar todo o fluxo funcionando de ponta a ponta. Consulte o bloco de construção da [Configuração de SSO Federado SaaS](#saas-federated-sso-configuration) para obter mais recomendações

## <a name="self-service-password-reset"></a>Redefinição de senha por autoatendimento

Tempo estimado para Conclusão: 15 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Tipos de atividades de gerenciamento de senha de autoatendimento no seu locatáro. | [Redefinição de senhas do Azure Active Directory para administradores de TI](active-directory-passwords.md) |
| Habilite write-back de senha para gerenciar senhas locais. Observe que isso exige versões do Azure AD Connect | [Pré-requisitos de Write-back de Senha](active-directory-passwords-writeback.md) |
| Identificar os usuários de prova de conceito que usam essa funcionalidade e certifique-se de que eles são membros de um grupo de segurança. Os usuários devem ser não administradores para demonstração total do recurso | [Personalizar: Gerenciamento de Senha do Azure AD: Restringir o acesso à redefinição de senha](active-directory-passwords-writeback.md) |


### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Navegue até o Portal de Gerenciamento do Azure AD: Redefinição de senha | [Portal de Gerenciamento do Azure AD: Redefinição de senha](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| Determina a política de redefinição de senha. Para fins de prova de conceito é possível usar chamada telefônica e perguntas e respostas. É recomendável que o registro seja exigido ao iniciar sessão no painel de acesso |  |
| Faça logoff e logon como um operador de informações |  |
| Forneça os dados de redefinição de senha de Autoatendimento, conforme configurado na etapa 2 | http://aka.ms/ssprsetup |
| Feche o navegador |  |
| Inicie o processo de logon como o operador de informações que você usou na etapa 4 |  |
| Redefinir a senha | [Atualizar sua própria senha: Redefinir a minha senha](active-directory-passwords-update-your-own-password.md) |
| Tente efetuar logon com sua nova senha no Azure AD, bem como para recursos locais |  |

### <a name="considerations"></a>Considerações

1. Se atualizar o Azure AD Connect irá causar fricção, considere usá-lo entre contas de nuvem ou torná-lo uma demonstração em um ambiente separado
2. Os administradores possuem uma política diferente e usar a conta do administrador para redefinir a senha pode prejudicar a prova de conceito e causar confusão. Certifique-se de que você usa uma conta de usuário comum para testar as operações de redefinição


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>Autenticação Multifator do Azure com Chamadas Telefônicas

Tempo estimado para Conclusão: 10 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Identificar os usuários de prova de conceito que usam MFA  |  |
| Telefone com bom sinal para o desafio MFA  | [O que é a Autenticação Multifator do Azure?](../multi-factor-authentication/multi-factor-authentication.md) |

### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Navegue até a folha "Usuários e grupos" no Portal de Gerenciamento do Azure AD | [Portal de Gerenciamento do Azure AD: Usuários e grupos](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| Selecione a folha "Todos os usuários" |  |
| Na barra superior selecione o botão "Autenticação Multifator" | URL Direta para o portal MFA do Azure: https://aka.ms/mfaportal |
| Nas configurações de "Usuário", selecione os usuários de prova de conceito e habilite-os para MFA | [Estados do usuário na Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) |
| Faça logon como usuário de prova de conceito e percorra o processo de verificação  |  |

### <a name="considerations"></a>Considerações

1. As etapas de prova de conceito neste bloco de construção configura o MFA explicitamente para um usuário em todos os logins. Há outras ferramentas, como Acesso Condicional e Proteção de Identidade que envolvem MFA em outros cenários de destino. Isso será algo a considerar quando passar de prova de conceito para produção.
2. As etapas de prova de conceito neste bloco de construção são explicitamente usando Chamadas Telefônicas como o método MFA para experiência. Ao fazer a transição de prova de conceito para produção, é recomendável o uso de aplicativos como o [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) como seu segundo fator, sempre que possível.
Saiba mais em: [Publicação Especial DRAFT NIST 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>Acesso Condicional com MFA para Aplicativos SaaS

Tempo estimado para Conclusão: 10 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Identificar os usuários de prova de conceito para direcionar a política. Esses usuários devem estar em um grupo de segurança para definir o escopo de política de acesso condicional | [Configuração de SSO federado SaaS](#saas-federated-sso-configuration) |
| O aplicativo de SaaS foi já configurado |  |
| Usuários de prova de conceito já estão atribuídos ao aplicativo |  |
| As credenciais para o usuário de prova de conceito estão disponíveis |  |
| Usuário de prova de conceito está registrado para MFA. Usando um telefone com bom sinal | http://aka.ms/ssprsetup |
| Dispositivo na rede interna. Endereço IP configurado no intervalo de endereços internos | Localize do endereço IP em: https://www.bing.com/search?q=what%27s+my+ip |
| Dispositivo de rede externa (pode ser um telefone usando redes móveis de operadora) |  |

### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Vá para o Portal de Gerenciamento do Azure AD: Folha de acesso condicional | [Portal de Gerenciamento do Azure AD: Folha de acesso condicional](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| Criar política de Acesso Condicional:<br/>-Usuários de PoC de destino em "Usuários e grupos"<br/>-Aplicativo de PoC de destino em "Aplicativos de nuvem"<br/>-Todos os locais de destino exceto os confiáveis em "Condições"-> "Locais" **Observação:** IPs confiáveis são configurados no [Portal MFA](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)<br/>- Exigir autenticação multifator em "Conceder" | [Introdução ao acesso condicional no Azure Active Directory: Etapas de configuração de política](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| Aplicativo de acesso de dentro de rede corporativa | [Introdução ao acesso condicional no Azure Active Directory: Testa a polítca](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| Aplicativo de acesso de rede pública | [Introdução ao acesso condicional no Azure Active Directory: Testa a polítca](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>Considerações

Se você estiver usando a federação, você pode usar o Provedor de Identidade (IdP) local para comunicar o estado de rede corporativa interna/externa com declarações. É possível usar essa técnica sem a necessidade de gerenciar a lista de endereços IP que pode ser complexa para avaliar e gerenciar em grandes organizações. Nessa configuração é necessário considerar o cenário de "roaming de rede" (um usuário registrando na rede interna e, enquanto estiver registrado em locais como um café) e certificar-se de que compreende as implicações. Saiba mais em: [Protegendo os recursos da nuvem com Autenticação Multifator do Azure e AD FS: IPs confiáveis para usuários federados](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>PIM (Privileged Identity Management)

Tempo estimado para Conclusão: 15 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Identificar o administrador global que fará parte da Prova de Conceito para PIM | [Começar a usar o Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) |
| Identificar o administrador global que se tornará o Administrador de Segurança | [Começar a usar o Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)<br/> [Funções administrativas diferentes no PIM do Azure Active Directory](active-directory-privileged-identity-management-roles.md) |
| Opcional: Confirme se os administradores globais têm acesso ao email para exercer notificações por email no PIM | [O que é o Azure AD Privileged Identity Management?: Configure as configurações de ativação de função](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Faça logon em https://portal.azure.com como um administrador global (GA) e inicializa a folha de PIM. O Administrador Global que executa essa etapa é propagado como o administrador de segurança.  Vamos chamar isso de ator GA1 | [Usando o assistente de segurança noAzure AD Privileged Identity Management](active-directory-privileged-identity-management-security-wizard.md) |
| Identifique o administrador global e mova-o de permanente para qualificado. Esse deve ser um administrador separado do usado na etapa 1 para maior clareza. Vamos chamar isso de ator GA2 | [Azure AD Privileged Identity Management: como adicionar ou remover uma função de usuário](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[O que é o Azure AD Privileged Identity Management?: Configure as configurações de ativação de função](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| Agora, faça logon como GA2 em https://portal.azure.com e tente alterar "Configurações de Usuário". Observe que algumas opções estão esmaecidas. | |
| Em uma nova guia e na mesma sessão como a etapa 3, agora navegue até https://portal.azure.com e adicione a folha de PIM ao painel. | [Como ativar ou desativar funções no Azure AD Privileged Identity Management: Adicionar o aplicativo Privileged Identity Management](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| Solicitação de ativação para a função de Administrador Global | [Como ativar ou desativar funções no Azure AD Privileged Identity Management: Ativar uma função](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| Observe que se GA2 nunca se inscreveu para MFA, o registro para o Azure MFA será necessário |  |
| Volte à guia original na etapa 3 e clique no botão Atualizar no navegador. Observe que agora você tem acesso para alterar "Configurações de Usuário" | |
| Opcionalmente, se seus administradores globais tiverem email habilitado, você poderá verificar a caixa de entrada do GA1 e do GA2 e ver a notificação da função que está sendo ativada |  |
| 8 Verifique o histórico de auditoria e observe o relatório para confirmar se a elevação de GA2 é mostrada. | [O que é o Azure AD Privileged Identity Management?: Examinar a atividade da funçãol](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>Considerações

Esse recurso faz parte do Azure AD Premium P2 e/ou EMS E5

## <a name="discovering-risk-events"></a>Descoberta de Eventos de Risco

Tempo estimado para Conclusão: 20 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Dispositivo com Navegador Tor baixado e instalado | [Baixe o Navegador Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Acesso ao usuário de POC para fazer o login | [Guia estratégico do Azure Active Directory Identity Protection](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Abra o navegador Tor | [Baixe o Navegador Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Faça login em https://myapps.microsoft.com com a conta de usuário de POC | [Guia estratégico do Azure Active Directory Identity Protection: Simulação de Eventos de Risco](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| Aguarde de 5 a 7 minutos |  |
| Faça logon como um administrador global em https://portal.azure.com e abra a folha Identity Protection | https://aka.ms/aadipgetstarted |
| Abra a folha de eventos de risco. Você deve ver uma entrada em "Entradas de endereços IP anônimos"  | [Guia estratégico do Azure Active Directory Identity Protection: Simulação de Eventos de Risco](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>Considerações

Esse recurso faz parte do Azure AD Premium P2 e/ou EMS E5

## <a name="deploying-sign-in-risk-policies"></a>Implantação de políticas de risco de logon

Tempo estimado para Conclusão: 10 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Dispositivo com Navegador Tor baixado e instalado | [Baixe o Navegador Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Acesse como um usuário de POC para fazer o log em testes |  |
| O usuário de POC está registrado com MFA. Certifique-se de usar um telefone com bom sinal | Bloco de Construção: [Autenticação Multifator do Azure com Chamadas Telefônicas](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Faça logon como um administrador global em https://portal.azure.com e abra a folha Identity Protection | https://aka.ms/aadipgetstarted |
| Habilite uma política de risco de entrada conforme a seguir:<br/>-Atribuído a: usuário de POC<br/>-Condições: Risco de entrada médio ou superior (entrada de local anônimo é considerada como um nível de risco médio)<br/>-Controles: Exigir MFA | [Guia estratégico do Azure Active Directory Identity Protection: Risco de entrada](active-directory-identityprotection-playbook.md#sign-in-risk) |
| Abra o navegador Tor | [Baixe o Navegador Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Faça login em https://myapps.microsoft.com com a conta de usuário de PoC |  |
| Observe o desafio MFA | [Experiências de entrada com o Azure AD Identity Protection: Recuperação de entrada de risco](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>Considerações

Esse recurso faz parte do Azure AD Premium P2 e/ou EMS E5. Para saber mais sobre eventos de risco, consulte [Eventos de risco do Azure Active Directory](active-directory-reporting-risk-events.md)

## <a name="configuring-certificate-based-authentication"></a>Configurando autenticação baseada em certificado

Tempo estimado para conclusão: 20 minutos

### <a name="pre-requisites"></a>Pré-requisitos

| Pré-requisito | Recursos |
| --- | --- |
| Dispositivo com certificado do usuário provisionado (Windows, iOS ou Android) da Enterprise PKI | [Implantar Certificados do Usuário](https://msdn.microsoft.com/library/cc770857.aspx) |
| Domínio do Azure AD federado com ADFS | [Azure AD Connect e federação](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Visão geral dos Serviços de Certificados do Active Directory](https://technet.microsoft.com/library/hh831740.aspx)|
| Para dispositivos iOS tem o aplicativo Microsoft Authenticator instalado | [Introdução ao aplicativo Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>Etapas

| Etapa | Recursos |
| --- | --- |
| Habilitar "Autenticação de Certificado" no ADFS | [Configurar políticas de Autenticação: Configurar a autenticação primária global no Windows Server 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| Opcional: Habilitar a Autenticação de Certificado no Azure AD para clientes do Exchange Active Sync | [Introdução à autenticação baseada em certificado no Azure Active Directory](active-directory-certificate-based-authentication-get-started.md) |
| Navegue até o Painel de Acesso e autentique usando um Certificado do Usuário | https://myapps.microsoft.com |

### <a name="considerations"></a>Considerações

Para saber mais sobre advertências dessa implantação, consulte: [ADFS: Autenticação de Certificado com o Azure AD & Office 365](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)



> [!NOTE]
> A posse do certificado do usuário deve ser protegida. Por meio de gerenciamento de dispositivos ou por PIN, no caso de cartões inteligentes.



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
