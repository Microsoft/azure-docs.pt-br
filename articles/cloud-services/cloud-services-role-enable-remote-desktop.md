---
title: "Habilitar Área de Trabalho Remota em um Serviço de Nuvem do Azure | Microsoft Docs"
description: "Como configurar seu aplicativo de serviço de nuvem do Azure para permitir conexões de área de trabalho remota"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: d3110ee8-6526-4585-aba5-d0bc9a713e9b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 413e72e9a39fcde84f56bfc61a6bc72dbadf1c97
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Habilitar a conexão de Área de Trabalho Remota para uma função nos Serviços de Nuvem do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Portal clássico do Azure](cloud-services-role-enable-remote-desktop.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)

Você pode habilitar uma conexão de Área de Trabalho Remota em sua função durante o desenvolvimento, incluindo os módulos de Área de Trabalho Remota em sua definição de serviço, ou você pode optar por habilitar a Área de Trabalho Remota por meio da Extensão de Área de Trabalho Remota. A abordagem preferida é usar a extensão de Área de Trabalho Remota, pois você poderá habilitar a Área de Trabalho Remota mesmo depois que o aplicativo for implantado, sem precisar reimplantar o aplicativo.

## <a name="configure-remote-desktop-from-the-azure-classic-portal"></a>Configurar a Área de Trabalho Remota do portal clássico do Azure
O portal clássico do Azure usa a abordagem da Extensão da Área de Trabalho Remota para que você possa habilitar a Área de Trabalho Remota, mesmo depois da implantação do aplicativo. A página **Configurar** do seu Serviço de Nuvem permite habilitar a Área de Trabalho Remota, alterar a conta do administrador local usada para conexão às máquinas virtuais, o certificado usado na autenticação e definir a data de validade.

1. Clique em **Serviços de Nuvem**, no nome do serviço de nuvem e depois em **Configurar**.
2. Clique no botão **Remover** na parte inferior.

    ![Serviços de nuvem remotos](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)

   > [!WARNING]
   > Todas as instâncias de função serão reiniciadas quando você ativa área de trabalho remota pela primeira vez e clica em OK (marca de seleção). Para evitar a reinicialização, o certificado usado para criptografar a senha deve estar instalado na função. Para evitar uma reinicialização, [carregue um certificado para o serviço de nuvem](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) e retorne a esta caixa de diálogo.

3. Em **Funções**, selecione a função que você deseja atualizar ou selecione **Tudo** para todas as funções.
4. Faça algumas das seguintes alterações:

   * Para habilitar a área de trabalho remota, marque a caixa de seleção de **Habilitar Área de Trabalho Remota** . Para desabilitar a área de trabalho remota, desmarque a caixa de seleção.
   * Crie uma conta para usar nas conexões de área de trabalho remota para as instâncias de função.
   * Atualize a senha da conta existente.
   * Selecione um certificado carregado para usar para autenticação (carregue o certificado usando **Carregar** na página **Certificados**) ou crie um novo certificado.
   * Altere a data de validade para a configuração da área de trabalho remota.

5. Ao concluir as atualizações da configuração, clique em **OK** (marca de seleção).

## <a name="remote-into-role-instances"></a>Remoto em instâncias de função
Depois que a Área de Trabalho Remota estiver habilitada nas funções, você poderá conectar-se remotamente a uma instância de função por meio de várias ferramentas.

Para conectar-se a uma instância de função do portal clássico do Azure:

1. Clique em **Instâncias** para abrir a página **Instâncias**.
2. Selecione uma instância de função com a área de trabalho remota configurada.
3. Clique em **Conectar**e siga as instruções para abrir a área de trabalho.
4. Clique em **Abrir** e em **Conectar** para iniciar a conexão de Área de Trabalho Remota.

### <a name="use-visual-studio-to-remote-into-a-role-instance"></a>Use o Visual Studio para conectar-se remotamente a uma instância de função
No Visual Studio, Gerenciador de Servidores:

1. Expanda o nó **Azure** > **Serviços de Nuvem** > **[nome do serviço de nuvem]**.
2. Expanda **Preparo** ou **Produção**.
3. Expanda a função individual.
4. Clique em uma das instâncias de função, clique em **Conectar-se usando a Área de Trabalho Remota...**e insira o nome de usuário e senha.

![Área de trabalho remota do Gerenciador de Servidores](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)

### <a name="use-powershell-to-get-the-rdp-file"></a>Use o PowerShell para obter o arquivo RDP
Você pode usar o cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) para recuperar o arquivo RDP. Em seguida, você pode usar o arquivo RDP com a conexão de área de trabalho remota para acessar o serviço de nuvem.

### <a name="programmatically-download-the-rdp-file-through-the-service-management-rest-api"></a>Baixar programaticamente o arquivo RDP por meio da API REST do gerenciamento de serviços
Você pode usar a operação REST [Baixar arquivo RDP](https://msdn.microsoft.com/library/jj157183.aspx) para baixar o arquivo RDP.

## <a name="to-configure-remote-desktop-in-the-service-definition-file"></a>Para configurar a Área de Trabalho Remota no novo arquivo de definição de serviço
Esse método permite habilitar a Área de Trabalho Remota para o aplicativo durante o desenvolvimento. Essa abordagem requer que senhas criptografadas sejam armazenadas em seu arquivo de configuração de serviço e todas as atualizações na configuração de Área de Trabalho Remota exigiriam uma reimplantação do aplicativo. Se você quiser evitar essas desvantagens, use a abordagem com base em extensão da Área de Trabalho Remota descrita acima.  

Você pode usar o Visual Studio para [habilitar uma conexão de Área de Trabalho Remota](../vs-azure-tools-remote-desktop-roles.md) usando a abordagem de arquivo de definição de serviço.  
As etapas a seguir descrevem as alterações necessárias para os arquivos de modelo de serviço habilitarem a Área de Trabalho Remota. O Visual Studio fará automaticamente essas alterações durante a publicação.

### <a name="set-up-the-connection-in-the-service-model"></a>Configurar a conexão no modelo de serviço
Use o elemento **Imports** para importar o módulo **RemoteAccess** e o módulo **RemoteForwarder** para o arquivo [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef).

O arquivo de definição de serviço deve ser semelhante ao exemplo a seguir com o elemento `<Imports>` adicionado.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
O arquivo [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) deve ser semelhante ao exemplo a seguir, observe os elementos `<ConfigurationSettings>` e `<Certificates>`. O certificado especificado deve ser [carregado no serviço de nuvem](cloud-services-how-to-create-deploy.md#how-to-upload-a-certificate-for-a-cloud-service).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## <a name="additional-resources"></a>Recursos adicionais
[Como configurar os Serviços de Nuvem](cloud-services-how-to-configure.md)
[Perguntas frequentes sobre os serviços de nuvem — Área de Trabalho Remota](cloud-services-faq.md)
