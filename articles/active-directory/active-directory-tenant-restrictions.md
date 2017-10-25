---
title: "Gerenciar o acesso a aplicativos de nuvem restringindo locatários – Azure | Microsoft Docs"
description: "Como usar Restrições de Locatário para gerenciar quais usuários podem acessar aplicativos com base em seu locatário do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: kgremban
ms.openlocfilehash: 7288f8fa173f8018570cd17aa7274f56a4eead41
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Usar Restrições de Locatário para gerenciar o acesso aos aplicativos de nuvem de SaaS

Organizações de grande porte que enfatizam a segurança desejam mudar para serviços de nuvem como o Office 365, mas precisam saber que seus usuários poderão acessar somente os recursos aprovados. Tradicionalmente, as empresas restringem endereços IP ou nomes de domínio quando desejam gerenciar o acesso. Essa abordagem falha em um mundo em que os aplicativos de SaaS são hospedados em uma nuvem pública e são executados em nomes de domínio compartilhados como outlook.office.com e login.microsoftonline.com. Bloquear esses endereços impediria totalmente que os usuários acessassem o Outlook na web, em vez de simplesmente restringi-los a identidades e recursos aprovados.

A solução do Azure Active Directory para esse desafio é um recurso chamado Restrições de Locatário. As Restrições de Locatário permitem que as organizações controlem o acesso a aplicativos de nuvem de SaaS, com base no locatário do Azure AD que os aplicativos usam para o logon único. Por exemplo, você pode desejar permitir o acesso aos aplicativos do Office 365 da sua organização enquanto impede o acesso a instâncias de outras organizações desses mesmos aplicativos.  

As Restrições de Locatário dão às organizações a capacidade de especificar a lista de locatários que seus usuários têm permissão para acessar. O Azure AD então apenas concede acesso para esses locatários permitidos.

Este artigo se concentra em Restrições de Locatário para o Office 365, mas o recurso deve funcionar com qualquer aplicativo de nuvem de SaaS que usa protocolos de autenticação moderna com o Azure AD para logon único. Se você usar aplicativos de SaaS com um locatário do Azure AD diferente do usado pelo Office 365, certifique-se de que todos os locatários necessários sejam permitidos. Para obter mais informações sobre os aplicativos de nuvem de SaaS, consulte o [Marketplace do Active Directory](https://azure.microsoft.com/en-us/marketplace/active-directory/).

## <a name="how-it-works"></a>Como ele funciona

A solução geral inclui os seguintes componentes: 

1. **Azure AD** – se o `Restrict-Access-To-Tenants: <permitted tenant list>` estiver presente, o Azure AD emitirá apenas tokens de segurança para os locatários permitidos. 

2. **Infraestrutura de servidor proxy local** – um dispositivo proxy capaz de realizar a inspeção de SSL, configurado para inserir o cabeçalho que contém a lista de locatários permitidos no tráfego destinado ao Azure AD. 

3. **Software cliente** – para dar suporte às Restrições de Locatário, o software cliente deve solicitar tokens diretamente do Azure AD, para que o tráfego possa ser interceptado pela infraestrutura de proxy. As Restrições de Locatário atualmente têm suporte pelos aplicativos do Office 365 baseados no navegador e pelos clientes do Office quando a autenticação moderna (como OAuth 2.0) é usada. 

4. **Autenticação Moderna** – os serviços de nuvem devem usar a autenticação moderna para usar Restrições de Locatário e bloquear o acesso a todos os locatários não permitidos. Os serviços de nuvem do Office 365 devem ser configurados para usar protocolos de autenticação moderna por padrão. Para obter as informações mais recentes sobre o suporte do Office 365 para autenticação moderna, leia [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Autenticação moderna do Office 365 atualizada).

O diagrama a seguir ilustra o fluxo do tráfego de alto nível. A inspeção SSL é necessária apenas no tráfego para o Azure AD, não para os serviços de nuvem do Office 365. Essa distinção é importante porque o volume de tráfego de autenticação para o Azure AD normalmente é muito menor do que o volume de tráfego para aplicativos de SaaS como o Exchange Online e o SharePoint Online.

![Fluxo de tráfego de Restrições de Locatário – diagrama](./media/active-directory-tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configurar as Restrições de Locatário

Há duas etapas para começar a usar as Restrições de Locatário. A primeira etapa é garantir que seus clientes podem se conectar aos endereços à direita. A segunda é configurar sua infraestrutura de proxy.

### <a name="urls-and-ip-addresses"></a>URLs e endereços IP

Para usar Restrições de Locatário, seus clientes devem ser capazes de se conectar às seguintes URLs do Azure AD para autenticação: login.microsoftonline.com, login.microsoft.com e login.windows.net. Além disso, para acessar o Office 365, seus clientes também devem ser capazes de se conectar aos FQDNs/URLs e endereços IP definidos em [URLs e intervalos de endereços IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Requisitos e configuração de proxy

A configuração a seguir é necessária para habilitar as Restrições de Locatário por meio de sua infraestrutura de proxy. Essa diretriz é genérica, você deverá consultar a documentação do seu fornecedor de proxy para encontrar as etapas de implementação específicas.

#### <a name="prerequisites"></a>Pré-requisitos

- O proxy deve ser capaz de realizar a interceptação de SSL, a inserção de cabeçalho HTTP e a filtragem de destinos usando FQDNs/URLs. 

- Os clientes devem confiar na cadeia de certificados apresentada pelo proxy para comunicações SSL. Por exemplo, se forem usados certificados de uma PKI interna, o certificado da autoridade de certificado raiz de emissão interno deverá ser confiável.

- Esse recurso está incluído nas assinaturas do Office 365, mas se você desejar usar Restrições de Locatário para controlar o acesso a outros aplicativos de SaaS, são necessárias licenças do Azure AD Premium 1.

#### <a name="configuration"></a>Configuração

Para cada solicitação de entrada para login.microsoftonline.com, login.microsoft.com e login.windows.net, insira dois cabeçalhos HTTP: *Restrict-Access-To-Tenants* e *Restrict-Access-Context*.

Os cabeçalhos devem incluir os seguintes elementos: 
- Para *Restrict-Access-To-Tenants*, um valor de \<permitted tenant list\>, que é uma lista separada por vírgulas dos locatários para permitir que os usuários acessem. Qualquer domínio que é registrado com um locatário pode ser usado para identificar o locatário nessa lista. Por exemplo, para permitir o acesso aos locatários Contoso e Fabrikam, o par nome-valor é semelhante a: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- Para *Restrict-Access-Context*, um valor de uma única ID de diretório, declarando qual locatário está configurando as Restrições de Locatário. Por exemplo, para declarar Contoso como o locatário que define a política de Restrições de Locatário, o par nome-valor é semelhante a: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Você pode encontrar a ID de diretório no [Portal do Azure](https://portal.azure.com). Entre como administrador, selecione **Azure Active Directory** e selecione **Propriedades**.

Para impedir que os usuários insiram seu próprio cabeçalho HTTP com locatários não aprovados, o proxy precisará substituir o cabeçalho Restrict-Access-To-Tenants se ele já estiver presente na solicitação recebida. 

Os clientes devem ser forçados a usar o proxy para todas as solicitações para login.microsoftonline.com, login.microsoft.com e login.windows.net. Por exemplo, se os arquivos PAC forem usados para direcionar os clientes para usar o proxy, os usuários finais não deverão ser capazes de editar ou desabilitar os arquivos PAC.

## <a name="the-user-experience"></a>A experiência do usuário

Esta seção mostra a experiência para usuários finais e administradores.

### <a name="end-user-experience"></a>Experiência do usuário final

Um usuário de exemplo está na rede da Contoso, mas está tentando acessar a instância da Fabrikam de um aplicativo de SaaS compartilhado como o Outlook online. Se a Contoso for um locatário não permitido para essa instância, o usuário verá a seguinte página:

![Página de acesso negado para usuários em locatários não permitido](./media/active-directory-tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>Experiência de admin

Embora a configuração de Restrições de Locatário seja feita na infraestrutura de proxy corporativa, os administradores podem acessar os relatórios de Restrições de Locatários diretamente no Portal do Azure. Para exibir os relatórios, vá para a página de Visão Geral do Azure Active Directory e procure em “Outros recursos”.

O administrador do locatário especificado como o locatário Restricted-Access-Context pode usar esse relatório para ver todas as entradas bloqueadas por causa da política de Restrições de Locatário, incluindo a identidade usada e a ID de diretório de destino.

![Use o Portal do Azure para exibir as tentativas de entrada restritas](./media/active-directory-tenant-restrictions/portal-report.png)

Como outros relatórios no Portal do Azure, você pode usar filtros para especificar o escopo do relatório. Você pode filtrar um usuário, aplicativo, cliente ou intervalo de tempo específico.

## <a name="office-365-support"></a>Suporte ao Office 365

Os aplicativos do Office 365 devem atender a dois critérios para dar suporte total às Restrições de Locatário:

1. O cliente usado dá suporte à autenticação moderna
2. A autenticação moderna está habilitada como o protocolo de autenticação padrão para o serviço de nuvem.

Consulte [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Autenticação moderna do Office 365 atualizada) para obter as informações mais recentes sobre quais clientes do Office atualmente dão suporte à autenticação moderna. Essa página também inclui links para instruções para habilitar a autenticação moderna nos locatários do Exchange Online e Skype for Business Online. A autenticação moderna já está habilitada por padrão no SharePoint Online.

As Restrições de Locatário no momento têm suporte pelos aplicativos baseados em navegador do Office 365 (o Portal do Office, Yammer, sites do SharePoint, Outlook na Web etc.). Para clientes espessos (Outlook, Skype for Business, Word, Excel, PowerPoint etc.) As Restrições de Locatário podem ser aplicadas somente quando a autenticação moderna é usada.  

Os clientes do Outlook e Skype for Business que dão suporte à autenticação moderna ainda podem usar protocolos herdados em locatários em que a autenticação moderna não está habilitada, ignorando efetivamente as Restrições de Locatário. Para o Outlook no Windows, os clientes podem optar por implementar restrições que impedem que os usuários finais adicionem contas de email não aprovadas aos seus perfis. Por exemplo, consulte a configuração da política de grupo [Prevent adding non-default Exchange accounts](http://gpsearch.azurewebsites.net/default.aspx?ref=1) (Impedir a adição de contas do Exchange não padrão). No momento, não há suporte completo para Restrições de Locatário para o Outlook em plataformas não Windows e para Skype for Business em nenhuma plataforma.

## <a name="testing"></a>Testando

Se você quiser experimentar as Restrições de Locatário antes de implementá-las em toda a organização, há duas opções: uma abordagem baseada em host usando uma ferramenta como o Fiddler ou uma distribuição em etapas das configurações de proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler para uma abordagem baseada em host

O Fiddler é um proxy de depuração da Web gratuito que pode ser usado para capturar e modificar o tráfego HTTP/HTTPS, incluindo a inserção de cabeçalhos HTTP. Para configurar o Fiddler para testar as Restrições de Locatário, execute as seguintes etapas:

1.  [Baixe e instale o Fiddler](http://www.telerik.com/fiddler).
2.  Configure o Fiddler para descriptografar o tráfego HTTPS, de acordo com a [documentação de ajuda do Fiddler](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).
3.  Configure o Fiddler para inserir os cabeçalhos *Restrict-Access-To-Tenants* e *Restrict-Access-Context* usando regras personalizadas:
  1. Na ferramenta Depurador da Web Fiddler Web, selecione o menu **Regras** e selecione **Personalizar Regras...** para abrir o arquivo CustomRules.
  2. Adicione as seguintes linhas no início da função *OnBeforeRequest*. Substitua \<tenant domain\> por um domínio registrado com seu locatário, por exemplo, contoso.onmicrosoft.com. Substitua \<directory ID\> pelo identificador de GUID do Azure AD do locatário.

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
  ```

  Se você precisar permitir vários locatários, use uma vírgula para separar os nomes de locatário. Por exemplo:

  ```
  oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
  ```

4. Salve e feche o arquivo CustomRules.

Depois de configurar o Fiddler, você pode capturar o tráfego indo para o menu **Arquivo** e selecionando **Capturar Tráfego**.

### <a name="staged-rollout-of-proxy-settings"></a>Distribuição em etapas as configurações de proxy

Dependendo dos recursos da sua infraestrutura de proxy, você poderá liberar em etapas a distribuição de configurações para seus usuários. Aqui estão algumas opções de alto nível para consideração:

1.  Use arquivos PAC para apontar os usuários de teste para uma infraestrutura de proxy de teste, enquanto os usuários normais continuam a usar a infraestrutura do proxy de produção.
2.  Alguns servidores proxy podem dar suporte a configurações diferentes usando grupos.

Consulte a documentação do servidor proxy para obter detalhes específicos.

## <a name="next-steps"></a>Próximas etapas

- Leia sobre a [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Autenticação moderna do Office 365 atualizada)

- Examine as [URLs e intervalos de endereços IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
