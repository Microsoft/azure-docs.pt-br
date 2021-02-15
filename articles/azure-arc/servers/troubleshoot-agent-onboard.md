---
title: Solucionar problemas de conexão do agente de servidores habilitados para Arc do Azure
description: Este artigo informa como solucionar problemas com o agente do computador conectado que surge com os servidores habilitados para Arc do Azure ao tentar se conectar ao serviço.
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 36feb6a65ec52d99dfd664ae54cb099ea6a7e239
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900671"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>Solucionar problemas de conexão do agente do computador conectado

Este artigo fornece informações sobre como solucionar problemas que podem ocorrer durante a tentativa de configurar o agente de máquina conectado de servidores habilitados para o Azure ARC para Windows ou Linux. Os métodos de instalação interativa e em escala ao configurar a conexão com o serviço são incluídos. Para obter informações gerais, consulte [visão geral de servidores habilitados para Arc](./overview.md).

## <a name="agent-verbose-log"></a>Log detalhado do agente

Antes de seguir as etapas de solução de problemas descritas posteriormente neste artigo, as informações mínimas necessárias são o log detalhado. Ele contém a saída dos comandos da ferramenta **azcmagent** , quando o argumento Verbose (-v) é usado. Os arquivos de log são gravados no `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` para Windows e Linux para `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Veja a seguir um exemplo do comando para habilitar o log detalhado com o agente do computador conectado para Windows ao executar uma instalação interativa.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Veja a seguir um exemplo do comando para habilitar o log detalhado com o agente do computador conectado para Windows ao executar uma instalação em escala usando uma entidade de serviço.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

Veja a seguir um exemplo do comando para habilitar o log detalhado com o agente do computador conectado para Linux ao executar uma instalação interativa.

>[!NOTE]
>Você deve ter permissões de acesso à *raiz* em computadores Linux para executar o **azcmagent**.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Veja a seguir um exemplo do comando para habilitar o log detalhado com o agente do computador conectado para Linux ao executar uma instalação em escala usando uma entidade de serviço.

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Problemas de conexão do agente com o serviço

A tabela a seguir lista alguns dos erros conhecidos e as sugestões sobre como solucioná-los e resolvê-los.

|Mensagem |Erro do |Causa provável |Solução |
|--------|------|---------------|---------|
|Falha ao adquirir o fluxo do dispositivo do token de autorização |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Não é possível alcançar o `login.windows.net` ponto de extremidade | Verifique a conectividade com o ponto de extremidade. |
|Falha ao adquirir o fluxo do dispositivo do token de autorização |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |O proxy ou o firewall está bloqueando o acesso ao `login.windows.net` ponto de extremidade. | Verifique a conectividade com o ponto de extremidade e se ele não está bloqueado por um firewall ou servidor proxy. |
|Falha ao adquirir o fluxo do dispositivo do token de autorização  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | Política de Grupo Configuração do computador do objeto *\ Modelos Administrativos \ perfis do usuário \ Sistema \ excluir perfis de usuário mais antigos que um número especificado de dias durante a reinicialização do sistema* está habilitado. | Verifique se o GPO está habilitado e direcionado para o computador afetado. Consulte a nota de rodapé <sup>[1](#footnote1)</sup> para obter mais detalhes. |
|Falha ao adquirir o token de autorização do SPN |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |O proxy ou o firewall está bloqueando o acesso ao `login.windows.net` ponto de extremidade. |Verifique a conectividade com o ponto de extremidade e se ele não está bloqueado por um firewall ou servidor proxy. |
|Falha ao adquirir o token de autorização do SPN |`Invalid client secret is provided` |Segredo de entidade de serviço incorreto ou inválido. |Verifique o segredo da entidade de serviço. |
| Falha ao adquirir o token de autorização do SPN |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Entidade de serviço e/ou ID de locatário incorreta. |Verifique a entidade de serviço e/ou a ID do locatário.|
|Obter resposta de recurso ARM |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Credenciais erradas e/ou permissões |Verifique se você ou a entidade de serviço é membro da função de **integração do computador conectado do Azure** . |
|Falha ao AzcmagentConnect recurso ARM |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Os provedores de recursos do Azure não estão registrados. |Registre os [provedores de recursos](./agent-overview.md#register-azure-resource-providers). |
|Falha ao AzcmagentConnect recurso ARM |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |O servidor proxy ou o firewall está bloqueando o acesso ao `management.azure.com` ponto de extremidade. |Verifique a conectividade com o ponto de extremidade e se ele não está bloqueado por um firewall ou servidor proxy. |

<a name="footnote1"></a><sup>1</sup> Se esse GPO estiver habilitado e se aplicar a computadores com o agente do computador conectado, ele excluirá o perfil do usuário associado à conta interna especificada para o serviço *himds* . Como resultado, ele também exclui o certificado de autenticação usado para se comunicar com o serviço armazenado em cache no repositório de certificados local por 30 dias. Antes do limite de 30 dias, é feita uma tentativa de renovar o certificado. Para resolver esse problema, siga as etapas para [cancelar o registro do computador](manage-agent.md#unregister-machine) e registrá-lo novamente com o serviço em execução `azcmagent connect` .

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar seu problema aqui ou não conseguir resolvê-lo, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Microsoft Q&a](/answers/topics/azure-arc.html).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Suporte do Azure conecta a Comunidade do Azure a respostas, suporte e especialistas.

* Registrar um incidente de suporte do Azure. Acesse o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**.
