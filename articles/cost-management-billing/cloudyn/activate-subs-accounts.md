---
title: Ativar assinaturas e contas do Azure
description: Habilitar o acesso usando APIs do Azure Resource Manager para contas novas e existentes e resolver problemas comuns de conta.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: vitavor
ms.custom: secdec18
ROBOTS: NOINDEX
ms.openlocfilehash: b1b7ea7467be107bd1af9daf0869c77ff0b94c70
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537331"
---
# <a name="activate-azure-subscriptions-and-accounts-with-cloudyn"></a>Ativar assinaturas e contas do Azure com o Cloudyn

Adicionar ou atualizar suas credenciais do Azure Resource Manager permite que o Cloudyn descubra todas as contas e assinaturas no seu locatário do Azure. Se você também tem a extensão do Diagnóstico do Azure habilitada em suas máquinas virtuais, o Cloudyn pode coletar métricas estendidas como CPU e memória. Este artigo descreve como habilitar o acesso usando APIs do Azure Resource Manager para contas novas e existentes. Ele também descreve como resolver problemas comuns de conta.

O Cloudyn não pode acessar a maioria dos dados de sua assinatura do Azure quando ela _não está ativa_ . Você deve editar contas _não ativas_ para que o Cloudyn possa acessá-las.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="required-azure-permissions"></a>Permissões necessárias do Azure

Permissões específicas são necessárias para concluir os procedimentos neste artigo. Você ou o administrador de locatário deve ter as seguintes permissões:

- Permissão para registrar o aplicativo CloudynCollector com seu locatário do Azure AD.
- A capacidade de atribuir o aplicativo em uma função em suas assinaturas do Azure.

Em suas assinaturas do Azure, as contas devem ter `Microsoft.Authorization/*/Write` acesso para atribuir o aplicativo CloudynCollector. Esta ação deve ser concedida pela função [Proprietário](../../role-based-access-control/built-in-roles.md#owner) ou pela função [Administrador de Acesso do Usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator).

Se sua conta tiver a função **Colaborador** , você não tem a permissão adequada para atribuir o aplicativo. Você recebe um erro durante ao tentar atribuir o aplicativo CloudynCollector à sua assinatura do Azure.

### <a name="check-azure-active-directory-permissions"></a>Verificar as permissões do Azure Active Directory

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No portal do Azure, selecione **Azure Active Directory** .
3. No Azure Active Directory, selecione **Configurações de Usuário** .
4. Verifique a opção **Registros do Aplicativo** .
    - Se estiver definido como **Sim** , os usuários não administradores podem registrar aplicativos do AD. Essa configuração significa que qualquer usuário no locatário do Azure AD pode registrar um aplicativo.  
    ![selecionar Registros de aplicativo em Configurações do usuário](./media/activate-subs-accounts/app-register.png)
    - Se a opção **Registros do aplicativo** estiver definida como **Não** , apenas os usuários administrativos do locatário podem registrar aplicativos do Active Directory do Azure. Seu administrador de locatário deve registrar o aplicativo CloudynCollector.


## <a name="add-an-account-or-update-a-subscription"></a>Adicionar uma conta ou atualizar uma assinatura

Quando você adiciona uma assinatura a uma atualização de conta, você concede ao Cloudyn acesso a seus dados do Azure.

### <a name="add-a-new-account-subscription"></a>Adicionar uma nova conta (assinatura)

1. No portal do Cloudyn, clique no símbolo de engrenagem no canto superior direito e selecione **Contas do Cloud** .
2. Clique em **Adicionar nova conta** e a caixa **Adicionar nova conta** é exibida. Insira as informações necessárias.  
    ![insira as informações necessárias na caixa Adicionar nova conta](./media/activate-subs-accounts/add-new-account.png)

### <a name="update-a-subscription"></a>Atualizar uma assinatura

1. Se você quiser atualizar uma assinatura _não ativa_ que já existe no Cloudyn no Gerenciamento de Contas, clique no símbolo de lápis de edição à direita do _GUID de locatário_ pai. As assinaturas são agrupadas em um locatário pai, portanto, evite ativar assinaturas individualmente.
    ![selecione sua ID de locatário na caixa Redescobrir assinaturas](./media/activate-subs-accounts/existing-sub.png)
2. Se necessário, insira a ID de locatário. Se você não souber sua ID de locatário, use as seguintes etapas para localizá-lo:
    1. Entre no [portal do Azure](https://portal.azure.com).
    2. No portal do Azure, selecione **Azure Active Directory** .
    3. Para obter a ID de locatário, selecione **Propriedades** do seu locatário do Azure AD.
    4. Copie o GUID em ID de Diretório. Esse valor é a ID do locatário.
    Para saber mais, confira [Obter ID do locatário](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
3. Se necessário, selecione a ID de taxa. Se você não souber a ID de taxa, use as seguintes etapas para localizá-lo.
    1. No canto superior direito do portal do Azure, clique em suas informações de usuário e, em seguida, clique em **Exibir minha fatura** .
    2. Em **Conta de Cobrança** , clique em **Assinaturas** .
    3. Em **Minhas assinaturas** , selecione a assinatura.
    4. Sua ID de taxa é mostrada em **ID da oferta** . Copiar a ID de oferta para a assinatura.
4. Na caixa Adicionar nova conta (ou Editar assinatura), clique em **Salvar** (ou **Próxima** ). Você será redirecionado para o portal do Azure.
5. Entre no portal. Clique em **Aceitar** para autorizar o coletor do Cloudyn a acessar sua conta do Azure.

    Você será redirecionado para a página de gerenciamento das Contas do Cloudyn e sua assinatura será atualizada com o status de conta **ativa** . Um símbolo de marca de seleção verde na coluna do Gerenciador de Recursos deve ser exibida.

    Se você não vir um símbolo de marca de seleção verde para uma ou mais assinaturas, isso significa que você não tem permissões para criar o aplicativo de leitor (o CloudynCollector) para a assinatura. Um usuário com permissões mais altas para a assinatura precisa repetir esse processo.

Assista ao vídeo [Conectar-se ao Azure Resource Manager com o Cloudyn](https://youtu.be/oCIwvfBB6kk) que guia você pelo processo.

>[!VIDEO https://www.youtube.com/embed/oCIwvfBB6kk?ecver=1]

## <a name="resolve-common-indirect-enterprise-set-up-problems"></a>Como resolver problemas comuns de configuração de empresa indireta

Ao usar o portal do Cloudyn pela primeira vez, você poderá ver as seguintes mensagens se for um usuário do Contrato Enterprise ou Provedor de Solução de Nuvem (CSP):

- *A chave de API especificada não é uma chave de registro de nível superior* exibida no assistente **Configurar o Cloudyn** .
- *Inscrição direta – Não* exibido no portal do Contrato Enterprise.
- *Nenhum dado de uso encontrado nos últimos 30 dias. Entre em contato com o distribuidor para ter certeza de que o markup foi habilitado para sua conta do Azure* exibido no portal do Cloudyn.

As mensagens anteriores indicam que você adquiriu um Contrato Enterprise do Azure por meio de um revendedor ou CSP. Seu revendedor ou CSP precisa habilitar o _markup_ para que você possa exibir os dados da conta do Azure no Cloudyn.

Veja como corrigir os problemas:

1. Seu revendedor precisa habilitar _marcação_ para sua conta. Para obter instruções, consulte o [Guia de integração de cliente indireto](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).
2. Você gera a chave de Contrato Enterprise do Azure para usar com o Cloudyn.

Habilite a API de cobrança do Azure antes de gerar a chave de API do Contrato Enterprise do Azure para configurar o Cloudyn seguindo as instruções em:

- [Visão geral das APIs de Relatórios para clientes Enterprise](../manage/enterprise-api.md)
- [API de relatórios do Microsoft Azure Enterprise Portal](https://ea.azure.com/helpdocs/reportingAPI) em **Habilitando o acesso a dados para a API**

Você também precisa conceder permissões para administradores de departamento, proprietários de conta e administradores de empresa para _exibir encargos_ com a API de cobrança.

Somente um administrador de serviços do Azure pode habilitar o Cloudyn. As permissões de coadministrador são insuficientes. No entanto, você pode contornar o requisito do administrador. É possível solicitar que o administrador do Active Directory do Azure conceda permissão para autorizar o **CloudynAzureCollector** com um script do PowerShell. O script a seguir concede permissão para registrar a Entidade de Serviço do Azure Active Directory **CloudynAzureCollector** .


```powershell
#THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

#Tenant - enter your tenant ID or Name
$tenant = "<ReplaceWithYourTenantID>"

#Cloudyn Collector application ID
$appId = "83e638ef-7885-479f-bbe8-9150acccdb3d"

#URL to activate the consent screen
$url = "https://login.windows.net/"+$tenant+"/oauth2/authorize?api-version=1&response_type=code&client_id="+$appId+"&redirect_uri=http%3A%2F%2Flocalhost%3A8080%2FCloudynJava&prompt=consent"

#Choose your browser, the default is Internet Explorer

#Chrome
#[System.Diagnostics.Process]::Start("chrome.exe", "--incognito $url")

#Firefox
#[System.Diagnostics.Process]::Start("firefox.exe","-private-window $url" )

#IExplorer
[System.Diagnostics.Process]::Start("iexplore.exe","$url -private" )

```

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não concluiu o primeiro tutorial do Cloudyn, leia-o em [Examinar o uso e os custos](tutorial-review-usage.md).