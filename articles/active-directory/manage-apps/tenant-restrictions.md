---
title: Usar restrições de locatário para gerenciar o acesso aos aplicativos SaaS – Azure AD
description: Como usar restrições de locatário para gerenciar quais usuários podem acessar aplicativos com base em seu locatário do Azure AD.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 4/6/2021
ms.author: iangithinji
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 941bf61f3387abf19be58bdd4d8861ab123e244f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376572"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Usar restrições de locatário para gerenciar o acesso aos aplicativos de nuvem de SaaS

Organizações de grande porte que priorizam a segurança desejam mudar para serviços de nuvem como o Microsoft 365, mas precisam ter a certeza de que seus usuários poderão acessar somente os recursos aprovados. Tradicionalmente, as empresas restringem endereços IP ou nomes de domínio quando desejam gerenciar o acesso. Essa abordagem falha em um mundo em que os aplicativos de SaaS (software como serviço) são hospedados em uma nuvem pública e são executados em nomes de domínio compartilhados como [outlook.office.com](https://outlook.office.com/) e [login.microsoftonline.com](https://login.microsoftonline.com/). Bloquear esses endereços impediria totalmente que os usuários acessassem o Outlook na web, em vez de simplesmente restringi-los a identidades e recursos aprovados.

A solução do Azure AD (Azure Active Directory) para esse desafio é um recurso chamado restrições de locatário. Com as restrições de locatário, as organizações podem controlar o acesso a aplicativos de nuvem de SaaS com base no locatário do Azure AD que os aplicativos usam para o logon único. Por exemplo, é possível permitir o acesso aos aplicativos do Microsoft 365 de sua organização e bloquear o acesso às instâncias de outras organizações desses mesmos aplicativos.  

Com as restrições de locatário, as organizações podem especificar a lista de locatários que os usuários delas têm permissão para acessar. O Azure AD então apenas concede acesso para esses locatários permitidos.

Este artigo se concentra em restrições de locatário para Microsoft 365, mas o recurso protege todos os aplicativos que enviam o usuário ao Azure AD para o logon único. Se você usar aplicativos SaaS com um locatário do Azure AD diferente do locatário usado pelo seu Microsoft 365, verifique se todos os locatários necessários são permitidos (por exemplo, em cenários de colaboração B2B). Para obter mais informações sobre os aplicativos de nuvem de SaaS, consulte o [Marketplace do Active Directory](https://azuremarketplace.microsoft.com/marketplace/apps).

Além disso, o recurso de restrições de locatário agora dá suporte ao [bloqueio do uso de todos os aplicativos MSA (aplicativos de consumidor da Microsoft)](#blocking-consumer-applications-public-preview), como o OneDrive, o Hotmail e o Xbox.com.  Isso usa um cabeçalho separado para o ponto de extremidade `login.live.com` e é detalhado ao final do documento.

## <a name="how-it-works"></a>Como ele funciona

A solução geral inclui os seguintes componentes:

1. **Azure AD**: se o cabeçalho `Restrict-Access-To-Tenants: <permitted tenant list>` estiver presente, o Azure AD emitirá tokens de segurança apenas para os locatários permitidos.

2. **Infraestrutura de servidor proxy local**: Essa infraestrutura é um dispositivo proxy capaz de realizar inspeção de protocolo TLS. Você precisa configurar o proxy para inserir o cabeçalho que contém a lista de locatários permitidos no tráfego destinado ao Azure AD.

3. **Software cliente**: Para dar suporte às restrições de locatário, o software cliente precisa solicitar tokens diretamente do Azure AD, de modo que a infraestrutura de proxy possa interceptar o tráfego. No momento, tanto os aplicativos do Microsoft 365 baseados em navegador quanto os clientes do Office que usam autenticação moderna (como o OAuth 2.0) são compatíveis com restrições de locatário.

4. **Autenticação moderna**: os serviços de nuvem devem usar a autenticação moderna para usar restrições de locatário e bloquear o acesso a todos os locatários não permitidos. Configure os serviços de nuvem do Microsoft 365 para usar protocolos de autenticação moderna por padrão. Para obter as informações mais recentes sobre o suporte do Microsoft 365 para autenticação moderna, leia [Autenticação moderna do Office 365 atualizada](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

O diagrama a seguir ilustra o fluxo do tráfego de alto nível. As restrições de locatário requerem a inspeção de TLS apenas no tráfego para o Azure AD, não para os serviços de nuvem do Microsoft 365. Essa distinção é importante porque o volume de tráfego de autenticação para o Azure AD normalmente é muito menor do que o volume de tráfego para aplicativos de SaaS como o Exchange Online e o SharePoint Online.

![Fluxo de tráfego de restrições de locatário – diagrama](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configurar as restrições de locatário

Há duas etapas para começar a usar as restrições de locatário. Primeiro, verifique se seus clientes podem se conectar aos endereços à direita. Segundo, configure sua infraestrutura de proxy.

### <a name="urls-and-ip-addresses"></a>URLs e endereços IP

Para usar restrições de locatário, seus clientes devem ser capazes de se conectar às seguintes URLs do Azure AD para autenticação: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/) e [login.windows.net](https://login.windows.net/). Além disso, para acessar o Office 365, seus clientes também precisam ser capazes de se conectar aos FQDNs (nomes de domínio totalmente qualificados), URLs e endereços IP definidos em [URLs e intervalos de endereços IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Requisitos e configuração de proxy

A configuração a seguir é necessária para habilitar as restrições de locatário por meio de sua infraestrutura de proxy. Essa diretriz é genérica, você deverá consultar a documentação do seu fornecedor de proxy para encontrar as etapas de implementação específicas.

#### <a name="prerequisites"></a>Pré-requisitos

- O proxy deve ser capaz de realizar a interceptação de TLS, a inserção de cabeçalho HTTP e a filtragem de destinos usando FQDNs/URLs.

- Os clientes devem confiar na cadeia de certificados apresentada pelo proxy para comunicações TLS. Por exemplo, se forem usados certificados de uma [PKI (infraestrutura de chave pública)](/windows/desktop/seccertenroll/public-key-infrastructure) interna, o certificado da autoridade de certificado raiz de emissão interno deverá ser confiável.

- As licenças do Azure AD Premium 1 são necessárias para usar as restrições de locatário.

#### <a name="configuration"></a>Configuração

Para cada solicitação de saída para login.microsoftonline.com, login.microsoft.com e login.windows.net, insira dois cabeçalhos HTTP: *Restrict-Access-To-Tenants* e *Restrict-Access-Context*.

> [!NOTE]
> Não inclua subdomínios em `*.login.microsoftonline.com` na sua configuração de proxy. Isso incluirá device.login.microsoftonline.com e interferirá na autenticação de certificado do cliente, que é usada nos cenários de registro de dispositivos e acesso condicional baseado no dispositivo. Configure o servidor proxy para excluir device.login.microsoftonline.com da interrupção e inspeção de TLS e da injeção de cabeçalho.

Os cabeçalhos devem incluir os seguintes elementos:

- Para *Restrict-Access-To-Tenants*, use um valor de \<permitted tenant list\>, que representa uma lista separada por vírgulas dos locatários que poderão ser acessados pelos usuários. Qualquer domínio registrado com um locatário pode ser usado para identificar o locatário nessa lista, além do próprio ID de diretório. Como exemplo das três maneiras de descrever um locatário, o par nome/valor que permite a Contoso, a Fabrikam e a Microsoft é semelhante ao seguinte: `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`

- Para *Restrict-Access-Context*, use um valor de uma ID de diretório, declarando qual locatário está configurando as restrições de locatário. Por exemplo, para declarar a Contoso como o locatário que define a política de restrições de locatário, o par nome/valor é semelhante ao seguinte: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`.  Você **deve** usar sua própria ID de diretório nesse local para obter os logs para essas autenticações.

> [!TIP]
> Você pode encontrar a ID de diretório no [portal do Azure Active Directory](https://aad.portal.azure.com/). Entre como administrador, selecione **Azure Active Directory** e selecione **Propriedades**. 
>
> Para validar que uma ID de diretório ou nome de domínio se referem ao mesmo locatário, use essa ID ou o domínio no lugar de <tenant> nesta URL: `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration`.  Quando os resultados são iguais com o domínio e com o ID, isso significa que eles se referem ao mesmo locatário. 

Para impedir que os usuários insiram seu próprio cabeçalho HTTP com locatários não aprovados, o proxy precisará substituir o cabeçalho *Restrict-Access-To-Tenants* se ele já estiver presente na solicitação recebida.

Os clientes devem ser forçados a usar o proxy para todas as solicitações para login.microsoftonline.com, login.microsoft.com e login.windows.net. Por exemplo, se os arquivos PAC forem usados para direcionar os clientes para usar o proxy, os usuários finais não deverão ser capazes de editar ou desabilitar os arquivos PAC.

## <a name="the-user-experience"></a>A experiência do usuário

Esta seção descreve a experiência para usuários finais e administradores.

### <a name="end-user-experience"></a>Experiência do usuário final

Um usuário de exemplo está na rede da Contoso, mas está tentando acessar a instância da Fabrikam de um aplicativo de SaaS compartilhado como o Outlook online. Se a Fabrikam for um locatário não permitido para a instância da Contoso, o usuário verá uma mensagem de negação de acesso dizendo que você está tentando acessar um recurso que pertence a uma organização não aprovada pelo seu departamento de TI.

![Mensagem de erro de restrições de locatário, de abril de 2021](./media/tenant-restrictions/error-message.png)

### <a name="admin-experience"></a>Experiência de admin

Embora a configuração de restrições de locatário seja feita na infraestrutura de proxy corporativa, os administradores podem acessar os relatórios de restrições de locatário diretamente no portal do Azure. Para visualizar os relatórios:

1. Entre no [Portal do Azure Active Directory](https://aad.portal.azure.com/). O painel **centro de administração do Azure Active Directory** aparece.

2. No painel esquerdo, selecione **Azure Active Directory**. A página de visão geral do Azure Active Directory aparece.

3. Na página Visão geral, selecione **Restrições de locatário**.

O administrador do locatário especificado como o locatário Restricted-Access-Context pode usar esse relatório para ver as entradas bloqueadas por causa da política de restrições de locatário, incluindo a identidade usada e a ID de diretório de destino. As entradas serão incluídas se o locatário que define a restrição for o locatário do usuário ou o locatário do recurso para a entrada.

O relatório pode conter informações limitadas, como a ID do diretório de destino, no momento em que um usuário que está em um locatário diferente do locatário de contexto de acesso restrito entra. Nesse caso, as informações de identificação do usuário, como nome e nome UPN, são mascaradas para proteger os dados do usuário em outros locatários ("{PII removidas}@domain.com" ou 00000000-0000-0000-0000-000000000000 no lugar de nomes de usuários e IDs de objeto, conforme apropriado). 

Como outros relatórios no Portal do Azure, você pode usar filtros para especificar o escopo do relatório. Você pode filtrar por um status, usuário, aplicativo, cliente ou intervalo de tempo específico. Se você selecionar o botão **Colunas**, poderá optar por exibir dados com qualquer combinação dos seguintes campos:

- **Usuário** – Este campo pode ter dados pessoais removidos. Nesse caso, ele será definido como `00000000-0000-0000-0000-000000000000`. 
- **Aplicativo**
- **Status**
- **Data**
- **Data (UTC)** – em que UTC é o Tempo Universal Coordenado
- **Endereço IP**
- **Cliente**
- **Nome de usuário** – Este campo pode ter dados pessoais removidos. Nesse caso, ele será definido como `{PII Removed}@domain.com`
- **Localidade**
- **ID do locatário de destino**

## <a name="microsoft-365-support"></a>Suporte do Microsoft 365

Os aplicativos do Microsoft 365 devem atender a dois critérios para dar suporte total às restrições de locatário:

1. O cliente usado dá suporte à autenticação moderna.
2. A autenticação moderna está habilitada como o protocolo de autenticação padrão para o serviço de nuvem.

Consulte [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Autenticação moderna do Office 365 atualizada) para obter as informações mais recentes sobre quais clientes do Office atualmente dão suporte à autenticação moderna. Essa página também inclui links para instruções para habilitar a autenticação moderna nos locatários do Exchange Online e Skype for Business Online. O SharePoint Online já habilita autenticação moderna por padrão.

No momento, os aplicativos baseados em navegador do Microsoft 365 (o portal do Office, o Yammer, os sites do SharePoint, o Outlook na Web, entre outros) dão suporte a restrições de locatário. Thick clients (Outlook, Skype for Business, Word, Excel, PowerPoint e mais) podem impor restrições de locatário somente ao usarem a autenticação moderna.  

Clientes Skype for Business e Outlook que dão suporte à autenticação moderna ainda podem usar protocolos herdados em locatários em que a autenticação moderna não está habilitada, ignorando efetivamente as restrições de locatário. As restrições de locatário podem bloquear aplicativos que usam protocolos herdados se eles contatarem login.microsoftonline.com, login.microsoft.com ou login.windows.net durante a autenticação.

Para o Outlook no Windows, os clientes podem optar por implementar restrições que impedem que os usuários finais adicionem contas de email não aprovadas aos seus perfis. Por exemplo, consulte a configuração da política de grupo [Prevent adding non-default Exchange accounts](https://gpsearch.azurewebsites.net/default.aspx?ref=1) (Impedir a adição de contas do Exchange não padrão).

## <a name="testing"></a>Testando

Se você quiser experimentar as restrições de locatário antes de implementá-las em toda a organização, há duas opções: uma abordagem baseada em host usando uma ferramenta como o Fiddler ou uma distribuição em etapas das configurações de proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler para uma abordagem baseada em host

O Fiddler é um proxy de depuração da Web gratuito que pode ser usado para capturar e modificar o tráfego HTTP/HTTPS, incluindo a inserção de cabeçalhos HTTP. Para configurar o Fiddler para testar as restrições de locatário, execute as seguintes etapas:

1. [Baixe e instale o Fiddler](https://www.telerik.com/fiddler).

2. Configure o Fiddler para descriptografar o tráfego HTTPS, de acordo com a [documentação de ajuda do Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Configure o Fiddler para inserir os cabeçalhos *Restrict-Access-To-Tenants* e *Restrict-Access-Context* usando regras personalizadas:

   1. Na ferramenta Depurador da Web Fiddler Web, selecione o menu **Regras** e selecione **Personalizar Regras...** para abrir o arquivo CustomRules.

   2. Adicione as seguintes linhas no início da função `OnBeforeRequest`. Substitua \<List of tenant identifiers\> por um domínio registrado com seu locatário (por exemplo, `contoso.onmicrosoft.com`). Substitua \<directory ID\> pelo identificador de GUID do Azure AD do locatário.  Você **deve** incluir o identificador GUID correto para que os logs apareçam em seu locatário. 

   ```JScript.NET
    // Allows access to the listed tenants.
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<List of tenant identifiers>";
          oSession.oRequest["Restrict-Access-Context"] = "<Your directory ID>";
      }

    // Blocks access to consumer apps
      if (
          oSession.HostnameIs("login.live.com")
      )
      {
          oSession.oRequest["sec-Restrict-Tenant-Access-Policy"] = "restrict-msa";
      }
   ```

   Se você precisar permitir vários locatários, use uma vírgula para separar os nomes de locatário. Por exemplo:

   `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Salve e feche o arquivo CustomRules.

Depois de configurar o Fiddler, você pode capturar o tráfego indo para o menu **Arquivo** e selecionando **Capturar Tráfego**.

### <a name="staged-rollout-of-proxy-settings"></a>Distribuição em etapas as configurações de proxy

Dependendo dos recursos da sua infraestrutura de proxy, você poderá liberar em etapas a distribuição de configurações para seus usuários. Aqui estão algumas opções de alto nível para consideração:

1. Use arquivos PAC para apontar os usuários de teste para uma infraestrutura de proxy de teste, enquanto os usuários normais continuam a usar a infraestrutura do proxy de produção.
2. Alguns servidores proxy podem dar suporte a configurações diferentes usando grupos.

Para obter detalhes específicos, veja a documentação do servidor proxy.

## <a name="blocking-consumer-applications-public-preview"></a>Bloqueando aplicativos de consumidor (versão prévia pública)

Os aplicativos da Microsoft que dão suporte a contas de consumidor e contas organizacionais, como [OneDrive](https://onedrive.live.com/) ou [Microsoft Learn](/learn/), podem, às vezes, ser hospedados na mesma URL.  Isso significa que os usuários que precisam acessar essa URL para fins de trabalho também têm acesso a ela para uso pessoal, o que pode não ser permitido sob suas diretrizes operacionais.

Algumas organizações tentam corrigir isso bloqueando `login.live.com` para bloquear a autenticação de contas pessoais.  Isso tem várias desvantagens:

1. O bloqueio de `login.live.com` impede o uso de contas pessoais em cenários de convidado B2B, que podem ser intrusos em visitantes e colaboração.
1. [O Autopilot requer o uso de `login.live.com`](/mem/autopilot/networking-requirements) para implantação. Os cenários do Intune e do Autopilot podem falhar quando `login.live.com` está bloqueado.
1. A telemetria organizacional e as atualizações do Windows que dependem do serviço login.live.com para as IDs de dispositivo [deixarão de funcionar](/windows/deployment/update/windows-update-troubleshooting#feature-updates-are-not-being-offered-while-other-updates-are).

### <a name="configuration-for-consumer-apps"></a>Configuração para aplicativos de consumidor

Embora o cabeçalho `Restrict-Access-To-Tenants` funcione como uma lista de permitidos, o bloco MSA (conta Microsoft) funciona como um sinal de negação, informando à plataforma da conta Microsoft para não permitir que os usuários entrem em aplicativos de consumidor. Para enviar esse sinal, o cabeçalho `sec-Restrict-Tenant-Access-Policy` é injetado no tráfego que está visitando `login.live.com` usando o mesmo proxy ou firewall corporativo descrito [acima](#proxy-configuration-and-requirements). O valor do cabeçalho deve ser `restrict-msa`. Quando o cabeçalho estiver presente e um aplicativo de consumidor estiver tentando conectar um usuário diretamente, essa entrada será bloqueada.

Neste momento, a autenticação para aplicativos de consumidor não aparece nos [logs de administração](#admin-experience), pois o login.live.com é hospedado separadamente do Azure AD.

### <a name="what-the-header-does-and-does-not-block"></a>O que o cabeçalho bloqueia e o que ele não bloqueia

A política `restrict-msa` bloqueia o uso de aplicativos de consumidor, mas permite vários outros tipos de tráfego e autenticação:

1. Tráfego sem usuário para dispositivos.  Isso inclui o tráfego para Autopilot, Windows Update e telemetria organizacional.
1. Autenticação B2B de contas de consumidor. Usuários com contas Microsoft que são [convidados para colaborar com um locatário](../external-identities/redemption-experience.md#invitation-redemption-flow) autenticam-se no login.live.com para acessar um locatário de recursos.
    1. Esse acesso é controlado usando o cabeçalho `Restrict-Access-To-Tenants` para permitir ou negar o acesso a esse locatário de recursos.
1. Autenticação de "passagem", usada por muitos aplicativos do Azure, bem como o Office.com, em que os aplicativos usam o Azure AD para conectar usuários consumidores em um contexto de consumidor.
    1. Esse acesso também é controlado usando o cabeçalho `Restrict-Access-To-Tenants` para permitir ou negar o acesso ao locatário especial de "passagem" (`f8cdef31-a31e-4b4a-93e4-5f571e91255a`).  Se esse locatário não aparecer na lista `Restrict-Access-To-Tenants` de domínios permitidos, as contas de consumidor serão bloqueadas pelo Azure AD para impedir a entrada nesses aplicativos.

## <a name="next-steps"></a>Próximas etapas

- Leia sobre a [Updated Office 365 modern authentication](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Autenticação moderna do Office 365 atualizada)
- Examine as [URLs e intervalos de endereços IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
