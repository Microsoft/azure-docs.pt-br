---
title: Implantar o Azure Blockchain Workbench
description: Como implantar o Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 5f488811e57ee20cb25db56b2d9e04202b17ffb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60869423"
---
# <a name="deploy-azure-blockchain-workbench"></a>Implantar o Azure Blockchain Workbench

O Azure Blockchain Workbench é implantado usando um modelo de solução no Azure Marketplace. O modelo simplifica a implantação de componentes necessários para criar aplicativos de blockchain. Uma vez implantado, o Blockchain Workbench fornece acesso aos aplicativos de cliente para criar e gerenciar usuários e aplicativos de blockchain.

Para obter mais informações sobre os componentes do Blockchain Workbench, consulte [Arquitetura do Azure Blockchain Workbench](architecture.md).

## <a name="prepare-for-deployment"></a>Preparar para a implantação

O Workbench Blockchain permite que você implante uma razão blockchain juntamente com um conjunto de serviços do Microsoft Azure relevante geralmente usado para criar um aplicativo baseado em blockchain. Implantar o Blockchain Workbench resulta nos seguintes serviços do Azure que estão sendo provisionados dentro de um grupo de recursos em sua assinatura do Azure.

* 1 Tópico de Grade de Eventos do Azure
* 1 Namespace do barramento de serviço
* 1 Application Insights
* 1 Banco de Dados SQL (Standard S0)
* 2 Serviços de Aplicativos (Standard)
* 2 Cofres de Chave do Microsoft Azure
* 2 Contas de Armazenamento do Microsoft Azure (Standard LRS)
* 2 Conjuntos de escala de Máquina Virtual (para nós de validação e de trabalho)
* 2 redes virtuais (incluindo o balanceador de carga, o grupo de segurança de rede e o endereço IP público para cada rede virtual)
* Opcional: Azure Monitor

A seguir está um exemplo de implantação criado no grupo de recursos do **myblockchain**.

![Exemplo de implantação](media/deploy/example-deployment.png)

O custo do Blockchain Workbench é uma agregação do custo de serviços subjacentes do Azure. Informações sobre preços para serviços do Azure podem ser calculadas usando a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/).

> [!IMPORTANT]
> Se você estiver usando uma assinatura com limites de serviço baixos, como uma assinatura de camada gratuita do Azure, a implantação poderá falhar devido a cota insuficiente de núcleos da VM. Antes da implantação, verifique sua cota usando as diretrizes do artigo [cotas de vCPU de máquina virtual](../../virtual-machines/windows/quotas.md). A seleção de VM padrão exige 6 núcleos VM. Mudar para uma VM de tamanho menor como *Standard DS1 v2* reduz o número de núcleos para 4.

## <a name="prerequisites"></a>Pré-requisitos

O Azure Blockchain Workbench requer a configuração do Azure AD e os registros de aplicativos. É possível fazer as [configurações do Azure AD](#azure-ad-configuration) manualmente antes da implantação ou executar uma implantação pós-script. Se estiver reimplementando o Blockchain Workbench, consulte as [configurações do Azure AD](#azure-ad-configuration) para verificar a configuração do Azure AD.

> [!IMPORTANT]
> O Workbench não precisa ser implantado no mesmo locatário que está sendo utilizado para registrar um aplicativo do Azure AD. O Workbench deve ser implantado em um locatário no qual você tenha permissões suficientes para implantar recursos. Para obter mais informações sobre os locatários do Microsoft Azure Active Directory, consulte [Como obter um locatário do Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) e [Integrando aplicativos com o Microsoft Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).



## <a name="deploy-blockchain-workbench"></a>Implantar o Blockchain Workbench

Depois de concluir as etapas de pré-requisito, você estará pronto para implantar o Blockchain Workbench. As seções a seguir descrevem como implantar a estrutura.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione sua conta no canto superior direito e alterne para o locatário desejado do Azure AD em que deseja implantar o Azure Blockchain Workbench.
3. No painel esquerdo, selecione **Criar um recurso**. Pesquise por `Azure Blockchain Workbench` na barra de pesquisa **Pesquisar no Marketplace**. 

    ![Barra de pesquisa do Marketplace](media/deploy/marketplace-search-bar.png)

4. Selecione **Azure Blockchain Workbench**.

    ![Resultados de pesquisa do Marketplace](media/deploy/marketplace-search-results.png)

5. Selecione **Criar**.
6. Conclua as configurações básicas.

    ![Criar Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Configuração | DESCRIÇÃO  |
    |---------|--------------|
    | Prefixo de recursos | Identificador exclusivo curto para sua implantação. Esse valor é usado como base para nomear recursos. |
    | Nome de usuário da VM | O nome de usuário é usado como administrador para todas as máquinas virtuais (VM). |
    | Tipo de autenticação. | Selecione se deseja usar uma senha ou chave para se conectar a VMs. |
    | Senha | A senha é usada para se conectar a VMs. |
    | SSH | Use uma chave pública RSA no formato de única linha começando com **ssh-rsa** ou use o formato PEM de várias linha. É possível gerar chaves SSH usando `ssh-keygen` no Linux e OS X ou usando PuTTYGen no Windows. Para obter mais informações sobre como usar chaves SSH, veja [Como usar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Senha do banco de dados / Confirmar senha do banco de dados | Especifique a senha a ser usada para acessar o banco de dados criado como parte da implantação. |
    | Resultados da implantação | Especifique onde implantar os recursos Blockchain Workbench. Para melhor disponibilidade, isso deve corresponder à configuração do **Local**. |
    | Assinatura | Especifique a assinatura do Azure que você deseja usar para sua implantação. |
    | Grupos de recursos | Crie um novo grupo de recursos selecionando **Criar novo** e especifique um nome exclusivo para o grupo de recursos. |
    | Local padrão | Especifique a região em que você deseja implantar a estrutura. |

7. Selecione **OK** para concluir a seção de configuração básica.

8. Em **Configurações Avançadas**, escolha se você quer criar uma nova rede de blockchain ou usar uma rede de blockchain de prova de autoridade existente.

    Para **Criar novo**:

    A opção *criar novo* cria um conjunto de nós de PoA (prova de autoridade) do Ethereum dentro da assinatura de um único membro. 

    ![Configurações avançadas para nova rede de blockchain](media/deploy/advanced-blockchain-settings-new.png)

    | Configuração | DESCRIÇÃO  |
    |---------|--------------|
    | Monitoramento | Escolha se você deseja que o Azure Monitor monitore a sua rede do blockchain |
    | Configurações do Azure Active Directory | Escolha **Adicionar mais tarde**.</br>Observação: Se você escolher [pré-configurar o Microsoft Azure Active Directory](#azure-ad-configuration) ou estiver reimplantando, escolha *Adicionar Agora*. |
    | Seleção de VM | Escolha o tamanho de VM preferido para sua rede de blockchain. Escolha um tamanho menor de VM, como *Standard DS1 v2* se você estiver usando uma assinatura com limites de serviço baixo, como a camada gratuita do Azure. |

    Para **Usar existente**:

    A opção *usar existente* permite que você especifique uma rede de blockchain de PoA (Prova de Autoridade) do Ethereum. Os pontos de extremidade têm os seguintes requisitos.

   * O ponto de extremidade deve ser uma rede de blockchain de PoA (Prova de Autoridade) do Ethereum.
   * O ponto de extremidade deve estar publicamente acessível pela rede.
   * A rede de blockchain PoA deve ser configurada para definir o preço do gás como zero.

     > [!NOTE]
     > As contas do Blockchain Workbench não são financiadas. Se fundos forem necessários, as transações falharão.

     ![Configurações avançadas para rede de blockchain existente](media/deploy/advanced-blockchain-settings-existing.png)

     | Configuração | DESCRIÇÃO  |
     |---------|--------------|
     | Ponto de extremidade RPC do Ethereum | Fornecer o ponto de extremidade RPC de uma rede de blockchain de PoA existente. O ponto de extremidade começa com https:// ou http:// e termina com um número de porta. Por exemplo, `http<s>://<network-url>:<port>` |
     | Configurações do Azure Active Directory | Escolha **Adicionar mais tarde**.</br>Observação: Se você escolher [pré-configurar o Microsoft Azure Active Directory](#azure-ad-configuration) ou estiver reimplantando, escolha *Adicionar Agora*. |
     | Seleção de VM | Escolha o tamanho de VM preferido para sua rede de blockchain. |

9. Selecione **OK** para concluir as configurações avançadas.

10. Revise o resumo para verificar se os parâmetros são precisos.

    ![Resumo](media/deploy/blockchain-workbench-summary.png)

11. Selecione **Criar** para concordar com os termos e implantar o Azure Blockchain Workbench.

A implantação pode levar até 90 minutos. Você pode usar o Portal do Azure para monitorar o progresso. No grupo de recursos criado recentemente, selecione **Implantações > Visão geral** para ver o status dos artefatos implantados.

> [!IMPORTANT]
> Após a implantação, será necessário concluir as configurações do Active Directory. Se você escolheu **Adicionar mais tarde**, será necessário executar o [script de configuração do Azure AD](#azure-ad-configuration-script).  Se você escolheu **Adicionar agora**, deverá [configurar a URL de Resposta](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>URL da Web do Blockchain Workbench

Concluída a implantação do Blockchain Workbench, um novo grupo de recursos conterá seus recursos do Blockchain Workbench. Os serviços do Blockchain Workbench são acessados por meio de uma URL da Web. As etapas a seguir mostram como recuperar a URL da Web do framework implantado.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No painel de navegação à esquerda, selecione **Grupos de recursos**
3. Escolha o nome do grupo de recursos especificado ao implantar o Blockchain Workbench.
4. Selecione a coluna **TIPO** para classificar a lista alfabeticamente por tipo.
5. Há dois recursos com o tipo **Serviço de Aplicativo**. Selecione o recurso do tipo **Serviço de Aplicativo** *sem* o sufixo "-api".

    ![Lista de Serviços de Aplicativo](media/deploy/resource-group-list.png)

6. Na seção **Conceitos Básicos** do Serviço de Aplicativo, copie o valor da **URL**, que representa a URL da Web para o Blockchain Workbench implantado.

    ![Conceitos básicos do Serviço de Aplicativo](media/deploy/app-service.png)

Para associar um nome de domínio personalizado ao Blockchian Workbench, consulte [Configurando um nome de domínio personalizado para um aplicativo Web no Serviço de Aplicativo do Azure usando o Gerenciador de Tráfego](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="azure-ad-configuration-script"></a>Script de configuração do Azure AD

O Azure AD deverá ser configurado para concluir a implantação do Blockchain Workbench. Você usará um script do PowerShell para fazer a configuração.

1. Em um navegador, navegue até a [URL da Web do Blockchain Workbench](#blockchain-workbench-web-url).
2. Você verá instruções para configurar o Azure AD usando o Cloud Shell. Copie o comando e inicie o Cloud Shell.

    ![Inicializar o script do AAD](media/deploy/launch-aad-script.png)

3. Escolha o locatário do Azure AD no qual você implantou o Blockchain Workbench.
4. No Cloud Shell, cole e execute o comando.
5. Quando solicitado, insira o locatário do Azure AD que você quer usar para o Blockchain Workbench. Este será o locatário contendo os usuários do Blockchain Workbench.

    > [!IMPORTANT]
    > O usuário autenticado requer permissões para criar registros de aplicativos do Azure AD e conceder permissões de aplicativos delegados no locatário. Talvez seja necessário solicitar a um administrador do locatário que execute o script de configuração do Azure AD ou criar um novo locatário.

    ![Inserir o locatário do Azure AD](media/deploy/choose-tenant.png)

6. Você deverá autenticar-se no locatário do Azure AD usando um navegador. Abra a URL da Web em um navegador, insira o código e faça a autenticação.

    ![Autenticar com código](media/deploy/authenticate.png)

7. O script gera várias mensagens de status. Você receberá uma mensagem de status **ÊXITO**, se o locatário for provisionado com êxito.
8. Navegue para A URL do Blockchain Workbench. Você será solicitado a dar consentimento para conceder permissões de leitura para o diretório. Isso permitirá que os usuários no locatário tenham acesso ao aplicativo Web do Blockchain Workbench. Se você for o administrador do locatário, poderá escolher se dará consentimento para toda a organização. Essa opção aceita consentimento para todos os usuários no locatário. Caso contrário, cada usuário será solicitado a consentir no primeiro uso do aplicativo Web do Blockchain Workbench.
9. Selecione **Aceitar** para conceder.

     ![Consentimento para ler perfis de usuários](media/deploy/graph-permission-consent.png)

10. Após o consentimento, o aplicativo Web do Blockchain Workbench poderá ser utilizado.

## <a name="azure-ad-configuration"></a>Configuração do Azure AD

Se você optar por configurar ou verificar manualmente as configurações do Azure AD antes da implantação, conclua todas as etapas desta seção. Se preferir definir as configurações do Azure AD automaticamente, use o [script de configuração do Azure AD](#azure-ad-configuration-script) após implantar o Blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Registro do aplicativo de API do Blockchain Workbench

A implantação do Blockchain Workbench exige o registro de um aplicativo do Microsoft Azure Active Directory. Você precisa de um locatário do Microsoft Azure Active Directory (Azure AD) para registrar o aplicativo. Você pode usar um locatário existente ou criar um novo locatário. Se estiver usando um locatário do Azure AD existente, você precisará de permissões suficientes para registrar aplicativos, conceder permissões de API do Graph e permitir o acesso de convidado em um locatário do Azure AD. Se você não tiver permissões suficientes em um locatário existente do Azure AD, crie um novo locatário.


1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione sua conta no canto superior direito e alterne para o locatário desejado do Azure AD. O locatário deve ser o locatário do administrador da assinatura onde o Workbench é implantado e você tem permissões suficientes para registrar aplicativos.
3. No painel de navegação esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Registros do aplicativo** > **Novo registro de aplicativo**.

    ![Registro do aplicativo](media/deploy/app-registration.png)

4. Forneça um **Nome** e uma **URL de Logon** para o aplicativo. Você pode usar valores de espaço reservado, pois os valores são alterados durante a implantação. 

    ![Criar o Registro do aplicativo](media/deploy/app-registration-create.png)

    |Configuração  | Valor  |
    |---------|---------|
    |NOME | `Blockchain API` |
    |Tipo de aplicativo |Aplicativo Web / API|
    |URL de logon | `https://blockchainapi` |

5. Selecione **Criar** para registrar o aplicativo do Microsoft Azure Active Directory.

### <a name="modify-manifest"></a>Modificar manifesto

Em seguida, será necessário modificar o manifesto para usar as funções do aplicativo no Azure AD e especificar os administradores do Blockchain Workbench.  Para obter mais informações sobre manifestos de aplicativos, consulte [Manifesto de aplicativo do Microsoft Azure Active Directory](../../active-directory/develop/reference-app-manifest.md).

1. Para o aplicativo que você registrou, selecione **Manifesto** no painel de detalhes do aplicativo registrado.
2. Gere um GUID. Você pode gerar um GUID usando o comando do PowerShell [guid] :: NewGuid () ou cmdlet New-GUID. Outra opção é usar um site gerador de GUID.
3. Você atualizará a seção **appRoles** do manifesto. No painel de Editar manifesto, selecione **Editar** e substitua `"appRoles": []` pelo JSON fornecido. Certifique-se de substituir o valor do campo **id** pelo GUID gerado. 

    ![Editar manifesto](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > O valor de **Administrador** é necessário para identificar administradores do Blockchain Workbench.

4. No manifesto, altere também o valor de **Oauth2AllowImplicitFlow** para **verdadeiro**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

5. Selecione **Salvar** para salvar as alterações do manifesto.

### <a name="add-graph-api-required-permissions"></a>Adicionar permissões necessárias de API do Graph

O aplicativo de API precisa solicitar a permissão do usuário para acessar o diretório. Defina a seguinte permissão necessária para o aplicativo de API:

1. No registro do aplicativo de API de Blockchain, selecione **Configurações > Permissões necessárias > Selecionar uma API > Microsoft Graph**.

    ![Selecionar uma API](media/deploy/client-app-select-api.png)

    Clique em **Selecionar**.

2. Em **Habilitar Acesso**, em **Permissões Delegadas**, escolha **Ler os perfis básicos de todos os usuários**.

    ![Habilitar acesso](media/deploy/client-app-read-perms.png)

    Selecione **Salvar** e, em seguida, selecione **Concluído**.

3. Em **Permissões necessárias**, selecione **Conceder permissões** e, em seguida, selecione **Sim** na solicitação de verificação.

   ![Conceder permissões](media/deploy/client-app-grant-permissions.png)

   A concessão de permissão permite que o Blockchain Workbench acesse os usuários no diretório. A permissão de leitura é necessária para pesquisar e adicionar membros ao Blockchain Workbench.

### <a name="get-application-id"></a>Obter ID do aplicativo

As informações de locatário e ID do aplicativo são necessárias para a implantação. Coletar e armazenar as informações para uso durante a implantação.

1. Para o aplicativo que você registrou, selecione **Configurações** > **Propriedades**.
2. No painel **Propriedades**, copie e guarde os seguintes valores para uso posterior durante a implantação.

    ![Propriedades do aplicativo de API](media/deploy/app-properties.png)

    | Configurando para armazenar  | Usar na implantação |
    |------------------|-------------------|
    | ID do aplicativo | Configuração do Azure Active Directory > ID do aplicativo |

### <a name="get-tenant-domain-name"></a>Obter nome de domínio do locatário

Colete e guarde o nome de domínio do locatário do Active Directory em que os aplicativos são registrados. 

No painel de navegação esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Personalizar nomes de domínio**. Copie e guarde o nome de domínio.

![Nome de domínio](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Configurações do usuário convidado

Se houver usuários convidados no locatário do Azure AD, siga as etapas adicionais para garantir que a atribuição e o gerenciamento de usuários do Blockchain Workbench funcionem corretamente.

1. Alterne o locatário do Azure AD e selecione **Azure Active Directory > Configurações do usuário > Gerenciar configurações de colaboração externa**.
2. Defina **Permissões de usuário convidado são limitadas** para **Não**.
    ![Configuração de colaboração externa](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Configurar a URL de resposta

Após a implantação do Azure Blockchain Workbench, será necessário configurar a **URL de Resposta** do aplicativo cliente do Azure Active Directory da URL da Web do Blockchain Workbench implantado.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se que você está no locatário em que registrou o aplicativo cliente do Microsoft Azure Active Directory.
3. No painel de navegação esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Registros do Aplicativo**.
4. Selecione o aplicativo cliente do Microsoft Azure Active Directory registrado na seção de pré-requisitos.
5. Selecione **Configurações > URLs de resposta**.
6. Especifique a URL da Web principal da implantação do Azure Blockchain Workbench recuperada na seção **Obter a URL da Web do Azure Blockchain Workbench**. A URL de resposta é prefixada com `https://`. Por exemplo, `https://myblockchain2-7v75.azurewebsites.net`

    ![URLs de resposta](media/deploy/configure-reply-url.png)

7. Selecione **Salvar** para atualizar o registro do cliente.

## <a name="remove-a-deployment"></a>Remove uma implantação

Quando uma implantação não é mais necessária, você pode remover uma implantação, excluindo o grupo de recursos do Workbench Blockchain.

1. No portal do Azure, navegue até **Grupo de recursos** no painel de navegação esquerdo e selecione o grupo de recursos que você deseja excluir. 
2. Selecione **Excluir grupo de recursos**. Verifique a exclusão digitando o nome do grupo de recursos e selecione **Excluir**.

    ![Excluir grupo de recursos](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo de procedimentos, você implantou o Workbench do Azure Blockchain. Para saber como usar o aplicativo blockchain, continue no próximo artigo de instruções.

> [!div class="nextstepaction"]
> [Criar um aplicativo blockchain no Azure Blockchain Workbench](create-app.md)
