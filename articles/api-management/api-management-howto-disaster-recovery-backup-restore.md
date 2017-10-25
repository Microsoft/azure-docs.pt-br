---
title: "Implementar a recuperação de desastre usando backup e restauração no Gerenciamento de API do Azure | Microsoft Docs"
description: "Saiba como usar o backup e restauração para executar a recuperação de desastres no Gerenciamento de API no Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 6f10be3c-f796-4a6c-bacd-7931b6aa82af
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 07c0265490cfae733133b6e0c938f90f9b392da4
ms.contentlocale: pt-br
ms.lasthandoff: 08/11/2017

---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Como implementar a recuperação de desastre usando o backup de serviço e restaurar no Gerenciamento de API no Azure
Ao escolher publicar e gerenciar suas APIs através do Gerenciamento de API do Azure, você está tirando proveito de muitos recursos de tolerância e infraestrutura que você precisaria criar, implementar e gerenciar. A plataforma Azure atenua grande parte das falhas potenciais a uma fração do custo.

Para se recuperar de problemas de disponibilidade que afetam a região onde o serviço de Gerenciamento de API está hospedado, você deverá estar pronto para reconstituir seu serviço em uma região diferente a qualquer momento. Dependendo de suas metas de disponibilidade e objetivo de tempo de recuperação, você pode querer reservar um serviço de backup em uma ou mais regiões e tentar manter suas configurações e conteúdos na sincronização com o serviço ativo. O backup do serviço e o recurso de restauração fornece o bloco de criação necessário para implementação de sua estratégia de recuperação de desastre.

Este guia mostra como autenticar solicitações do Gerenciador de Recursos do Azure e como fazer backup e restaurar suas instâncias de serviço de Gerenciamento de API.

> [!NOTE]
> O processo de backup e restauração de uma instância do serviço Gerenciamento de API para recuperação de desastres também pode ser usado para replicar as instâncias de serviço Gerenciamento de API para cenários como preparação.
>
> Observe que cada backup expira após 30 dias. Se você tentar restaurar um backup após o período de expiração de 30 dias, a restauração falhará com uma mensagem `Cannot restore: backup expired`.
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Autenticação de solicitações do gerenciador de recursos do Azure
> [!IMPORTANT]
> A API REST para backup e restauração usa o Gerenciador de Recursos do Azure e tem um mecanismo de autenticação diferentes das APIs REST para gerenciar suas entidades de Gerenciamento de API. As etapas desta seção descrevem como autenticar solicitações do Gerenciador de Recursos do Azure. Para mais informações, consulte [Autenticação de solicitações do Gerenciador de Recursos do Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx).
>
>

Todas as tarefas que você faz em recursos usando o Gerenciador de Recursos do Azure devem ser autenticadas com o Active Directory do Azure usando as etapas a seguir.

* Adicione um aplicativo no locatário do Active Directory do Azure.
* Defina permissões para o aplicativo que você adicionou.
* Obtenha o token para autenticar solicitações ao Gerenciador de Recursos do Azure.

A primeira etapa é criar um aplicativo do Active Directory do Azure. Faça logon no [Portal Clássico do Azure](http://manage.windowsazure.com/) usando a assinatura que contém sua instância de serviço Gerenciamento de API e navegue até a guia **Aplicativos** para o Active Directory do Azure padrão.

> [!NOTE]
> Se o diretório padrão do Active Directory do Azure não é visível para sua conta, contate o administrador da assinatura do Azure para conceder as permissões necessárias para sua conta.

![Criar aplicativo do Active Directory do Azure][api-management-add-aad-application]

Clique em **Adicionar**, **Adicionar um aplicativo que minha organização está desenvolvendo** e escolha **Aplicativo cliente nativo**. Insira um nome descritivo e clique na seta Avançar. Insira uma URL de espaço reservado como `http://resources` para o **URI de redirecionamento**, que é um campo obrigatório, mas o valor não é usado posteriormente. Clique na caixa de seleção para salvar o aplicativo.

Quando o aplicativo estiver salvo, clique em **Configurar**, role para baixo até a seção **Permissões para outros aplicativos** e clique em **Adicionar aplicativo**.

![Adicionar permissões][api-management-aad-permissions-add]

Selecione **Windows** **API de gerenciamento de serviços do Azure** e clique na caixa de seleção para adicionar o aplicativo.

![Adicionar permissões][api-management-aad-permissions]

Clique em **Permissões delegadas** ao lado do aplicativo recém-adicionado **API de gerenciamento de serviços** do **Microsoft Azure**, marque a caixa **Acessar o gerenciamento de serviço do Azure (visualização)** e clique em **Salvar**.

![Adicionar permissões][api-management-aad-delegated-permissions]

Antes de chamar as APIs que geram o backup e o restauram, é necessário obter um token. O exemplo a seguir usa o pacote do nuget [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) para recuperar o token.

```c#
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Substitua `{tentand id}`, `{application id}` e `{redirect uri}` usando as instruções a seguir.

Substitua `{tenant id}` com a ID de locatário do aplicativo do Active Directory do Azure que você acabou de criar. Você pode acessar a ID clicando em **Exibir pontos de extremidade**.

![Pontos de extremidade][api-management-aad-default-directory]

![Pontos de extremidade][api-management-endpoint]

Substitua `{application id}` e `{redirect uri}` usando a **ID do cliente** e a URL da seção **URIs de Redirecionamento** da guia **Configurar** do aplicativo Azure Active Directory.

![Recursos][api-management-aad-resources]

Depois que os valores são especificados, o exemplo de código deve retornar um token semelhante ao exemplo a seguir.

![Token][api-management-arm-token]

Antes de chamar as operações de backup e restauração descritas nas seções a seguir, defina o cabeçalho de solicitação de autorização para a chamada de REST.

```c#
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

## <a name="step1"> </a>Fazer backup de um serviço de Gerenciamento de API
Para fazer backup de um serviço de Gerenciamento de API, execute a seguinte solicitação HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

onde:

* `subscriptionId` - ID da assinatura contendo o serviço de Gerenciamento de API do qual você está tentando fazer backup
* `resourceGroupName` - uma cadeia de caracteres na forma de “Api-Default-{service-region}”, em que `service-region` identifica a região do Azure onde o serviço de Gerenciamento de API que você está tentando fazer backup é hospedado, por exemplo, `North-Central-US`
* `serviceName` - o nome do serviço de Gerenciamento de API que você está fazendo um backup em específico, no momento de sua criação
* `api-version` - substitua por `2014-02-14`

No corpo da solicitação, especifique o nome da conta de armazenamento de destino do Azure, a chave de acesso, o nome do contêiner Blob e o nome de backup:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Defina o valor do cabeçalho de solicitação `Content-Type` para `application/json`.

O backup é uma operação longa de execução que pode levar vários minutos para concluir.  Se a solicitação foi bem sucedida e o processo de backup foi iniciado, você receberá um código de status de resposta `202 Accepted` com um cabeçalho `Location`.  Faça solicitações “GET” para a URL no cabeçalho do `Location` para descobrir o status da operação. Enquanto o backup está em andamento, você continuará a receber um código de status “202 Aceito”. Um código de resposta de `200 OK` indicará a conclusão com sucesso da operação de backup.

Observe as restrições a seguir ao fazer uma solicitação de backup.

* O **contêiner** especificado no corpo solicitado **tem que existir**.
* Enquanto o backup está em andamento, você **não deve tentar quaisquer operações de gerenciamento de serviço** , como atualização ou downgrade de SKU, alteração do nome do domínio, etc.
* A restauração de um **backup é garantida somente por 30 dias** desde o momento de sua criação.
* Os **dados de uso** utilizados para a criação de relatórios de análise **não estão incluídos** no backup. Use o [API REST de Gerenciamento de API do Azure][Azure API Management REST API] para recuperar periodicamente os relatórios analíticos por questões de segurança.
* A frequência com que você executa os backups de serviço afetarão seu objetivo do ponto de recuperação. Para minimizar, aconselhamos a implementação de backups regular, bem como a execução de backups sob demanda depois de fazer alterações importantes para seu serviço de Gerenciamento de API.
* As **alterações** feitas à configuração de serviço (por exemplo, APIs, políticas, aparência do portal do desenvolvedor) enquanto a operação de backup está em andamento **podem não ser incluídas no backup e, portanto, serão perdidas**.

## <a name="step2"> </a>Restaurar um serviço de Gerenciamento de API
Para restaurar um serviço de Gerenciamento de API de um backup criado anteriormente faz a seguinte solicitação HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

onde:

* `subscriptionId` - ID da assinatura contendo o serviço de Gerenciamento de API que você está restaurando um backup em
* `resourceGroupName` - uma cadeia de caracteres na forma de “Api-Default-{service-region}”, em que `service-region` identifica a região do Azure onde o serviço de Gerenciamento de API que você está restaurando é armazenado, por exemplo, `North-Central-US`
* `serviceName` - o nome do serviço de Gerenciamento de API para o qual está sendo realizada a restauração, especificado no momento de sua criação
* `api-version` - substitua por `2014-02-14`

No corpo da solicitação, especifique o local de arquivo de backup, por exemplo, o nome da conta de armazenamento do Azure, chave de acesso, nome do contêiner Blob e nome de backup:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Defina o valor do cabeçalho de solicitação `Content-Type` para `application/json`.

Restaure uma operação longa de execução que pode levar até 30 minutos ou mais para concluir.  Se a solicitação foi bem sucedida e o processo de restauração foi iniciado, você receberá um código de status de resposta `202 Accepted` com um cabeçalho do `Location`.  Faça solicitações “GET” para a URL no cabeçalho do `Location` para descobrir o status da operação. Enquanto a restauração está em andamento, você continuará a receber o código de status “202 Aceito”. Um código de resposta `200 OK` indicará a conclusão com sucesso da operação de restauração.

> [!IMPORTANT]
> **O SKU** do serviço que está sendo restaurado **tem que corresponder** ao SKU do serviço de backup sendo restaurado.
>
> As **alterações** feitas na configuração do serviço (por exemplo, APIs, políticas, aparência do portal do desenvolvedor) enquanto restauram a operação que está em andamento **podem ser substituídas**.
>
>

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes blogs da Microsoft para duas diferentes orientações passo a passo do processo de backup/restauração.

* [Replicar contas de Gerenciamento de API do Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
  * Agradecemos a Gisela por sua contribuição neste artigo.
* [Gerenciamento de API do Azure: Fazendo backup e restaurando a configuração](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  * A abordagem detalhada por Stuart não coincide com as diretrizes oficiais, mas é muito interessante.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png

