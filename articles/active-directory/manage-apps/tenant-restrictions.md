---
title: Usar restrições de locatário para gerenciar o acesso aos aplicativos SaaS – Azure AD
description: Como usar restrições de locatário para gerenciar quais usuários podem acessar aplicativos com base em seu locatário do Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: kenwith
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: f605b2bb48855d70ea305dcda194b26da71ee9ec
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99252467"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Usar restrições de locatário para gerenciar o acesso aos aplicativos de nuvem de SaaS

Grandes organizações que enfatizam a segurança desejam migrar para serviços de nuvem como Microsoft 365, mas precisam saber que seus usuários só podem acessar os recursos aprovados. Tradicionalmente, as empresas restringem endereços IP ou nomes de domínio quando desejam gerenciar o acesso. Essa abordagem falha em um mundo em que os aplicativos de SaaS (software como serviço) são hospedados em uma nuvem pública e são executados em nomes de domínio compartilhados como [outlook.office.com](https://outlook.office.com/) e [login.microsoftonline.com](https://login.microsoftonline.com/). Bloquear esses endereços impediria totalmente que os usuários acessassem o Outlook na web, em vez de simplesmente restringi-los a identidades e recursos aprovados.

A solução do Azure AD (Azure Active Directory) para esse desafio é um recurso chamado restrições de locatário. Com as restrições de locatário, as organizações podem controlar o acesso a aplicativos de nuvem de SaaS com base no locatário do Azure AD que os aplicativos usam para o logon único. Por exemplo, talvez você queira permitir o acesso aos aplicativos Microsoft 365 da sua organização, impedindo o acesso a instâncias de outras organizações desses mesmos aplicativos.  

Com as restrições de locatário, as organizações podem especificar a lista de locatários que os usuários delas têm permissão para acessar. O Azure AD então apenas concede acesso para esses locatários permitidos.

Este artigo se concentra em restrições de locatário para Microsoft 365, mas o recurso deve funcionar com qualquer aplicativo de nuvem SaaS que usa protocolos de autenticação modernos com o Azure AD para logon único. Se você usar aplicativos SaaS com um locatário do Azure AD diferente do locatário usado pelo Microsoft 365, certifique-se de que todos os locatários necessários sejam permitidos. Para obter mais informações sobre os aplicativos de nuvem de SaaS, consulte o [Marketplace do Active Directory](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory).

## <a name="how-it-works"></a>Como ele funciona

A solução geral inclui os seguintes componentes:

1. **Azure ad**: se o `Restrict-Access-To-Tenants: <permitted tenant list>` cabeçalho estiver presente, o Azure ad emitirá apenas tokens de segurança para os locatários permitidos.

2. **Infraestrutura de servidor proxy local**: Essa infraestrutura é um dispositivo proxy capaz de realizar inspeção de protocolo TLS. Você precisa configurar o proxy para inserir o cabeçalho que contém a lista de locatários permitidos no tráfego destinado ao Azure AD.

3. **Software cliente**: Para dar suporte às restrições de locatário, o software cliente precisa solicitar tokens diretamente do Azure AD, de modo que a infraestrutura de proxy possa interceptar o tráfego. Atualmente, os aplicativos Microsoft 365 baseados em navegador dão suporte a restrições de locatário, como clientes do Office que usam autenticação moderna (como o OAuth 2,0).

4. **Autenticação moderna**: os serviços de nuvem devem usar a autenticação moderna para usar restrições de locatário e bloquear o acesso a todos os locatários não permitidos. Você deve configurar Microsoft 365 serviços de nuvem para usar protocolos de autenticação modernos por padrão. Para obter as informações mais recentes sobre Microsoft 365 suporte para autenticação moderna, leia [autenticação moderna do Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

O diagrama a seguir ilustra o fluxo do tráfego de alto nível. Restrições de locatário requer a inspeção TLS somente no tráfego para o Azure AD, não para os serviços de nuvem Microsoft 365. Essa distinção é importante porque o volume de tráfego de autenticação para o Azure AD normalmente é muito menor do que o volume de tráfego para aplicativos de SaaS como o Exchange Online e o SharePoint Online.

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

- Azure AD Premium 1 licenças são necessárias para o uso de restrições de locatário. 

#### <a name="configuration"></a>Configuração

Para cada solicitação de entrada para login.microsoftonline.com, login.microsoft.com e login.windows.net, insira dois cabeçalhos HTTP: *Restrict-Access-To-Tenants* e *Restrict-Access-Context*.

> [!NOTE]
> Ao configurar a interceptação de SSL e a injeção de cabeçalho, certifique-se de que o tráfego para https://device.login.microsoftonline.com é excluído. Essa URL é usada para a autenticação do dispositivo e a execução de interrupção e inspeção do TLS pode interferir na autenticação de certificado do cliente, o que pode causar problemas com o registro de dispositivo e o acesso condicional baseado no dispositivo.



Os cabeçalhos devem incluir os seguintes elementos:

- Para *restringir o acesso-para-locatários*, use um valor de \<permitted tenant list\> , que é uma lista separada por vírgulas de locatários que você deseja permitir que os usuários acessem. Qualquer domínio registrado com um locatário pode ser usado para identificar o locatário nessa lista, bem como a própria ID de diretório. Para obter um exemplo de todas as três maneiras de descrever um locatário, o par nome/valor para permitir que contoso, fabrikam e Microsoft se pareça com: `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`

- Para *Restrict-Access-Context*, use um valor de uma ID de diretório, declarando qual locatário está configurando as restrições de locatário. Por exemplo, para declarar contoso como o locatário que define a política de restrições de locatário, o par nome/valor é semelhante a: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` .  Você **deve** usar sua própria ID de diretório nesse local.

> [!TIP]
> Você pode encontrar a ID de diretório no [portal do Azure Active Directory](https://aad.portal.azure.com/). Entre como administrador, selecione **Azure Active Directory** e selecione **Propriedades**. 
>
> Para validar que uma ID de diretório ou nome de domínio se referem ao mesmo locatário, use essa ID ou o domínio no lugar desta <tenant> URL: `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration` .  Se os resultados com o domínio e a ID forem os mesmos, eles se referirão ao mesmo locatário. 

Para impedir que os usuários insiram seu próprio cabeçalho HTTP com locatários não aprovados, o proxy precisará substituir o cabeçalho *Restrict-Access-To-Tenants* se ele já estiver presente na solicitação recebida.

Os clientes devem ser forçados a usar o proxy para todas as solicitações para login.microsoftonline.com, login.microsoft.com e login.windows.net. Por exemplo, se os arquivos PAC forem usados para direcionar os clientes para usar o proxy, os usuários finais não deverão ser capazes de editar ou desabilitar os arquivos PAC.

> [!NOTE]
> Não inclua subdomínios em *. login.microsoftonline.com na sua configuração de proxy. Isso incluirá device.login.microsoftonline.com e poderá interferir na autenticação de certificado do cliente, que é usada no registro de dispositivos e nos cenários de acesso condicional com base no dispositivo. Configure o servidor proxy para excluir device.login.microsoftonline.com do TLS break-and-inspecionar e injeção de cabeçalho.

## <a name="the-user-experience"></a>A experiência do usuário

Esta seção descreve a experiência para usuários finais e administradores.

### <a name="end-user-experience"></a>Experiência do usuário final

Um usuário de exemplo está na rede da Contoso, mas está tentando acessar a instância da Fabrikam de um aplicativo de SaaS compartilhado como o Outlook online. Se a Fabrikam for um locatário não permitido para a instância da Contoso, o usuário verá uma mensagem de negação de acesso dizendo que você está tentando acessar um recurso que pertence a uma organização não aprovada pelo seu departamento de TI.

### <a name="admin-experience"></a>Experiência de admin

Embora a configuração de restrições de locatário seja feita na infraestrutura de proxy corporativa, os administradores podem acessar os relatórios de restrições de locatário diretamente no portal do Azure. Para visualizar os relatórios:

1. Entre no [Portal do Azure Active Directory](https://aad.portal.azure.com/). O painel **centro de administração do Azure Active Directory** aparece.

2. No painel esquerdo, selecione **Azure Active Directory**. A página de visão geral do Azure Active Directory aparece.

3. Na página Visão geral, selecione **restrições de locatário**.

O administrador do locatário especificado como o locatário Restricted-Access-Context pode usar esse relatório para ver as entradas bloqueadas por causa da política de restrições de locatário, incluindo a identidade usada e a ID de diretório de destino. As entradas serão incluídas se o locatário que define a restrição for o locatário do usuário ou o locatário do recurso para a entrada.

> [!NOTE]
> O relatório pode conter informações limitadas, como a ID do diretório de destino, no momento em que um usuário que está em um locatário diferente do locatário de contexto de acesso restrito entra. Nesse caso, as informações de identificação do usuário, como nome e nome UPN, são mascaradas para proteger os dados do usuário em outros locatários (" 00000000-0000-0000-0000-00000000@domain.com ") 

Como outros relatórios no Portal do Azure, você pode usar filtros para especificar o escopo do relatório. Você pode filtrar por um status, usuário, aplicativo, cliente ou intervalo de tempo específico. Se você selecionar o botão **Colunas**, poderá optar por exibir dados com qualquer combinação dos seguintes campos:

- **Usuário**
- **Aplicativo**
- **Status**
- **Data**
- **Data (UTC)** (em que UTC é o Tempo Universal Coordenado)
- **Método de autenticação MFA** (método de autenticação multifator)
- **Detalhe de autenticação MFA** (detalhe de autenticação multifator)
- **Resultado do MFA**
- **Endereço IP**
- **Cliente**
- **Nome de usuário**
- **Localidade**
- **ID do locatário de destino**

## <a name="microsoft-365-support"></a>Suporte do Microsoft 365

Microsoft 365 aplicativos devem atender a dois critérios para oferecer suporte total às restrições de locatário:

1. O cliente usado dá suporte à autenticação moderna.
2. A autenticação moderna está habilitada como o protocolo de autenticação padrão para o serviço de nuvem.

Consulte [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Autenticação moderna do Office 365 atualizada) para obter as informações mais recentes sobre quais clientes do Office atualmente dão suporte à autenticação moderna. Essa página também inclui links para instruções para habilitar a autenticação moderna nos locatários do Exchange Online e Skype for Business Online. O SharePoint Online já habilita autenticação moderna por padrão.

Microsoft 365 aplicativos baseados em navegador (o portal do Office, o Yammer, os sites do SharePoint, o Outlook na Web e muito mais) dão suporte atualmente a restrições de locatário. Thick clients (Outlook, Skype for Business, Word, Excel, PowerPoint e mais) podem impor restrições de locatário somente ao usarem a autenticação moderna.  

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

   2. Adicione as seguintes linhas no início da função `OnBeforeRequest`. Substituir \<tenant domain\> por um domínio registrado com seu locatário (por exemplo, `contoso.onmicrosoft.com` ). Substitua \<directory ID\> pelo identificador de GUID do Azure AD do locatário.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
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

## <a name="next-steps"></a>Próximas etapas

- Leia sobre a [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Autenticação moderna do Office 365 atualizada)
- Examine as [URLs e intervalos de endereços IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
