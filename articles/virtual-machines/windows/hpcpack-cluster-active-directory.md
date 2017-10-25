---
title: Cluster HPC Pack com o Azure Active Directory | Microsoft Docs
description: Saiba como integrar um cluster HPC Pack 2016 no Azure ao Azure Active Directory
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/14/2016
ms.author: danlep
ms.openlocfilehash: c5a06a9c810349b1bcce01c7f73563941a5af0ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Gerenciar um cluster HPC Pack no Azure usando o Azure Active Directory
O [Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) dá suporte à integração com o [Azure AD](../../active-directory/index.md) (Azure Active Directory) para administradores que implantam um cluster HPC Pack no Azure.



Siga as etapas neste artigo para as seguintes tarefas de alto nível: 
* Integrar manualmente o cluster HPC Pack ao locatário do Azure AD
* Gerenciar e agendar trabalhos no cluster HPC Pack no Azure 

A integração de uma solução de cluster HPC Pack ao Azure AD segue as etapas padrão para integrar outros aplicativos e serviços. Este artigo pressupõe que você esteja familiarizado com o gerenciamento básico de usuários no Azure AD. Para obter mais informações e detalhes, confira a [documentação do Azure Active Directory](../../active-directory/index.md) e a seção a seguir.

## <a name="benefits-of-integration"></a>Vantagens da integração


O Azure AD (Azure Active Directory) é um serviço de gerenciamento de identidades e multilocatário baseado em nuvem diretórios que fornece acesso SSO (logon único) a soluções de nuvem.

A integração de um cluster HPC Pack ao Azure AD pode ajudá-lo a atingir as seguintes metas:

* Remover o controlador de domínio do Active Directory tradicional do cluster HPC Pack. Isso poderá ajudar a reduzir os custos de manutenção do cluster se isso não for necessário para sua empresa, bem como acelerar o processo de implantação.
* Aproveitar os seguintes benefícios oferecidos pelo Azure AD:
    *   Logon único 
    *   Usar uma identidade do AD local para o cluster HPC Pack no Azure 

    ![Ambiente do Azure Active Directory](./media/hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>Pré-requisitos
* **Cluster HPC Pack 2016 implantado em máquinas virtuais do Azure** - para obter as etapas, confira [Implantar um cluster HPC Pack 2016 no Azure](hpcpack-2016-cluster.md). Você precisa do nome DNS do nó principal e as credenciais de um administrador de cluster para concluir as etapas neste artigo.

  > [!NOTE]
  > Não há suporte à integração do Azure Active Directory nas versões do HPC Pack anteriores ao HPC Pack 2016.



* **Computador cliente** - você precisa de um computador cliente Windows ou Windows Server para executar utilitários clientes do HPC Pack. Se você quiser usar a API REST ou o portal da Web do Pacote HPC para enviar trabalhos, poderá usar qualquer computador cliente de sua escolha.

* **Utilitários clientes do HPC Pack** - instale os utilitários clientes do HPC Pack no computador cliente, usando o pacote de instalação gratuita disponível no Centro de Download da Microsoft.


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>Etapa 1: registrar o servidor de cluster HPC no locatário do Azure AD
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Clique em **Active Directory** no menu à esquerda e clique no diretório desejado em sua assinatura. Você deve ter permissão para acessar recursos no diretório.
3. Clique em **Usuários** e verifique se há contas de usuário já criadas ou configuradas.
4. Clique em **Aplicativos** > **Adicionar** e clique em **Adicionar um aplicativo que minha organização está desenvolvendo**. Insira as seguintes informações no assistente:
    * **Nome** - HPCPackClusterServer
    * **Tipo** - selecione **Aplicativo Web e/ou API Web**
    * **URL de logon** - a URL base para o exemplo, que, por padrão, é `https://hpcserver`
    * **URI da ID do aplicativo** - `https://<Directory_name>/<application_name>`. Substitua `<Directory_name`> pelo nome completo do locatário do Azure AD, por exemplo, `hpclocal.onmicrosoft.com` e substitua `<application_name>` pelo nome escolhido anteriormente.

5. Depois que o aplicativo for adicionado, clique em **Configurar**. Configure as seguintes propriedades:
    * Selecione **Sim** para **O aplicativo é multilocatário**
    * Selecione **Sim** para **Atribuição de usuário necessária para acessar o aplicativo**.

6. Clique em **Salvar**. Quando o salvamento for concluído, clique em **Gerenciar Manifesto**. Essa ação baixa o arquivo de JavaScript Object Notation (JSON). Edite o manifesto baixado localizando a configuração `appRoles` e adicionando a seguinte função de aplicativo:
    ```json
    "appRoles": [
        {
        "allowedMemberTypes": [
            "User",
            "Application"
        ],
        "displayName": "HpcAdminMirror",
        "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
        "isEnabled": true,
        "description": "HpcAdminMirror",
        "value": "HpcAdminMirror"
        },
        {
        "allowedMemberTypes": [
            "User",
            "Application"
        ],
        "description": "HpcUsers",
        "displayName": "HpcUsers",
        "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
        "isEnabled": true,
        "value": "HpcUsers"
        }
    ],
    ```
7. Salve o arquivo. No portal, clique em **Gerenciar Manifesto** > **Carregar Manifesto**. Em seguida, você pode carregar o manifesto editado.
8. Clique em **Usuários**, selecione um usuário e clique em **Atribuir**. Atribua uma das funções disponíveis (HpcUsers ou HpcAdminMirror) ao usuário. Repita essa etapa com outros usuários no diretório. Para obter informações sobre os usuários de cluster, confira [Gerenciar usuários de cluster](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx).

   > [!NOTE] 
   > Para gerenciar usuários, é recomendável usar a folha de visualização do Azure Active Directory no [portal do Azure](https://portal.azure.com).
   >


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>Etapa 2: registrar o cliente de cluster HPC no locatário do Azure AD

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Clique em **Active Directory** no menu à esquerda e clique no diretório desejado em sua assinatura. Você deve ter permissão para acessar recursos no diretório.
3. Clique em **Aplicativos** > **Adicionar** e clique em **Adicionar um aplicativo que minha organização está desenvolvendo**. Insira as seguintes informações no assistente:

    * **Nome** - HPCPackClusterClient
    * **Tipo** – selecione **Aplicativo Cliente Nativo**
    * **URI de redirecionamento** - `http://hpcclient`

4. Depois que o aplicativo for adicionado, clique em **Configurar**. Copie o valor de **ID do cliente** e salve-o. Você precisará dele mais tarde ao configurar o aplicativo.

5. Em **Permissões para outros aplicativos**, clique em **Adicionar aplicativo**. Pesquisar e adicionar o aplicativo HpcPackClusterServer (criado na Etapa 1).

6. No menu suspenso **Permissões Delegadas**, selecione **Acessar HpcClusterServer**. Em seguida, clique em **Salvar**.


## <a name="step-3-configure-the-hpc-cluster"></a>Etapa 3: configurar o cluster HPC

1. Conecte-se ao nó principal do HPC Pack 2016 no Azure.

2. Inicie o HPC PowerShell.

3. Execute o comando a seguir:

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    onde

    * `AADTenant` especifica o nome do locatário do Azure AD, como `hpclocal.onmicrosoft.com`
    * `AADClientAppId` especifica a ID do cliente para o aplicativo criado na Etapa 2.

4. Reinicie o serviço HpcSchedulerStateful.

    Em um cluster com vários nós principais, você pode executar os seguintes comandos do PowerShell no nó principal para alternar a réplica principal para o serviço HpcSchedulerStateful:

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName “fabric:/HpcApplication/SchedulerStatefulService”

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>Etapa 4: gerenciar e enviar trabalhos do cliente

Para instalar os utilitários do Pacote HPC no computador cliente, baixe os arquivos de instalação do Pacote HPC 2016 (instalação completa) pelo Centro de Download da Microsoft. Quando você começar a instalação, escolha a opção de instalação dos **utilitários cliente do Pacote HPC**.

Para preparar o computador cliente, instale o certificado usado durante a [instalação do cluster HPC](hpcpack-2016-cluster.md) no computador cliente. Use procedimentos padrão de gerenciamento de certificados do Windows para instalar o certificado público no repositório **Certificados – usuário atual** > **Autoridades de Certificação Raiz Confiáveis**. 

Agora você pode executar os comandos do HPC Pack ou usar a GUI do Gerenciador de trabalhos do HPC Pack para enviar e gerenciar trabalhos de cluster usando a conta do Azure AD. Para obter as opções de envio de trabalhos, consulte [Enviar trabalhos HPC de um computador local para um cluster HPC Pack implantado no Azure](hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster).

> [!NOTE]
> Quando você tenta se conectar ao cluster HPC Pack no Azure pela primeira vez, uma janela pop-up é exibida. Insira suas credenciais do Azure AD para fazer logon. O token é armazenado em cache. Conexões posteriores ao cluster no Azure usam o token em cache, a menos que a autenticação seja alterada ou o cache seja limpo.
>
  
Por exemplo, após concluir as etapas anteriores, você pode consultar trabalhos de um cliente local da seguinte maneira:

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Cmdlets úteis para envio de trabalhos com a integração do Azure AD 

### <a name="manage-the-local-token-cache"></a>Gerenciar o cache de token local

O HPC Pack 2016 fornece dois novos cmdlets do PowerShell HPC para gerenciar o cache de token local. Esses cmdlets são úteis para envio de trabalhos de forma não interativa. Veja os exemplos a seguir:

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Definir as credenciais para enviar trabalhos usando a conta do Azure AD 

Às vezes, é recomendável executar o trabalho no usuário do cluster HPC (para um cluster HPC ingressado em domínio, execute como um usuário de domínio; para um cluster HPC não ingressado em domínio, execute como um usuário local no nó de cabeçalho).

1. Use os seguintes comandos para definir as credenciais:

    ```powershell
    $localUser = “<username>”

    $localUserPassword=”<password>”

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. Depois, envie o trabalho da maneira a seguir. O trabalho/tarefa é executado em $localUser nos nós de computação.

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   Se `–Credential` não for especificado com `Submit-HpcJob`, o trabalho ou a tarefa será executado em um usuário mapeado local como a conta do Azure AD. (O cluster HPC cria um usuário local com o mesmo nome que a conta do Azure AD para executar a tarefa.)
    
3. Defina dados estendidos para a conta do Azure AD. Isso é útil ao executar um trabalho MPI em nós do Linux usando a conta do Azure AD.

   * Definir dados estendidos para a própria conta do Azure AD

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * Definir dados estendidos e executar como usuário do cluster HPC
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```

