---
title: Registro em log do HSM Gerenciado do Azure
description: Este tutorial vai ajudar você a começar a usar o registro em log do HSM Gerenciado.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: mbaldwin
ms.openlocfilehash: 0d5749894fd277ff6a2f77e3db9721e6989d72ac
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109230"
---
# <a name="managed-hsm-logging"></a>Registro em log do HSM Gerenciado 

Depois de criar um ou mais HSMs Gerenciados, você provavelmente desejará monitorar como e quando eles serão acessados e por quem. Isso pode ser feito habilitando o registro em log, que salva as informações em uma conta de armazenamento do Azure fornecida por você. Um novo contêiner chamado **insights-logs-auditevent** é criado automaticamente para a conta de armazenamento especificada. Você pode usar essa mesma conta de armazenamento para coletar logs de vários HSMs Gerenciados.

Você pode acessar suas informações de registro em log 10 minutos (no máximo) após a operação do HSM Gerenciado. Na maioria dos casos, será mais rápido do que isso.  Cabe a você gerenciar os logs em sua conta de armazenamento:

* use os métodos de controle de acesso padrão do Azure para proteger os logs, restringindo quem pode acessá-los.
* Exclua os logs que você não deseja manter em sua conta de armazenamento.

Este tutorial vai ajudar você a começar a usar o registro em log do HSM Gerenciado. Você criará uma conta de armazenamento, habilitará o registro em log e interpretará as informações de log coletadas.  

> [!NOTE]
> Este tutorial não inclui instruções sobre como criar chaves ou HSMs Gerenciados. Este artigo fornece instruções da CLI do Azure para atualizar o log de diagnósticos.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste artigo, você precisará ter os seguintes itens:

* Uma assinatura do Microsoft Azure. Se você não tiver uma, pode se inscrever e fazer uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* A CLI do Azure versão 2.12.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
* Um HSM Gerenciado na sua assinatura. Confira [Início Rápido: Provisione e ative um HSM Gerenciado usando a CLI do Azure](quick-create-cli.md) para provisionar e ativar um HSM Gerenciado.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Conecte-se à sua assinatura do Azure

A primeira etapa para configurar o registro em log das chaves é apontar a CLI do Azure para o HSM Gerenciado que você deseja registrar em log.

```azurecli-interactive
az login
```

Para saber mais sobre as opções de logon por meio da CLI, veja [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli)

Talvez seja necessário especificar a assinatura que você usou para criar o HSM Gerenciado. Insira o seguinte comando para ver as assinaturas da sua conta:

## <a name="identify-the-managed-hsm-and-storage-account"></a>Identificar o HSM Gerenciado e a conta de armazenamento

```azurecli-interactive
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name ContosoMHSMLogs --query id -o tsv)
```

## <a name="enable-logging"></a>Habilitar o registro em log

Para habilitar o registro em log do HSM Gerenciado, use o comando de **criar configurações de diagnóstico do AZ Monitor**, juntamente com as variáveis que criamos para a nova conta de armazenamento e o HSM Gerenciado. Também definiremos o sinalizador **-Enabled** como **$true** e a categoria como **AuditEvent** (a única categoria para registro em log do HSM Gerenciado):

Essa saída confirma que o registro em log está habilitado para o HSM Gerenciado e que as informações serão salvas na conta de armazenamento.

Opcionalmente, você pode definir uma política de retenção para os logs, de modo que os logs mais antigos sejam automaticamente excluídos. Por exemplo, defina a política de retenção definindo o sinalizador **-RetentionEnabled** como **$true** e defina o parâmetro **-RetentionInDays** como **90** para que os logs de mais de 90 dias sejam automaticamente excluídos.

```azurecli-interactive
az monitor diagnostic-settings create --name ContosoMHSM-Diagnostics --resource $hsmresource --logs '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource
```

O que é registrado em log:

* Todas as solicitações à API REST autenticadas, incluindo solicitações que falharam devido a permissões de acesso, erros do sistema ou solicitações inválidas.
* Operações de plano gerenciado no próprio HSM Gerenciado, incluindo criação, exclusão e atualização de atributos, como marcas.
* Operações relacionadas ao Domínio de Segurança, como inicializar e baixar, inicializar recuperação, carregar
* Operações completas de backup, restauração e restauração seletiva do HSM
* Operações de gerenciamento de função, como criar/exibir/excluir atribuições de função e criar/exibir/excluir definições de função personalizadas
* Operações em chaves, incluindo:
  * Criar, modificar ou excluir as chaves.
  * Assinar, verificar, criptografar, descriptografar, encapsular, desencapsular e listar as chaves.
  * Backup, restauração e limpeza de chave
* Solicitações não autenticadas que resultam em uma resposta 401. Por exemplo, solicitações que não têm um token de portador estão malformadas ou expiradas ou têm um token inválido.  

## <a name="access-your-logs"></a>Acessar seus logs

Os logs do HSM Gerenciado são armazenados no contêiner **insights-logs-auditevent** da conta de armazenamento que você forneceu. Para exibir os logs, você precisa baixar blobs. Para obter informações sobre o Armazenamento do Azure, confira [Criar, baixar e listar blobs com a CLI do Azure](../../storage/blobs/storage-quickstart-blobs-cli.md).

Os blobs individuais são armazenados como texto e formatados como um JSON. Vamos examinar um exemplo de entrada de log. O exemplo a seguir mostra a entrada de log quando uma solicitação para criar um backup completo é enviada ao HSM Gerenciado.

```json
[
  {
    "TenantId": "766eaf62-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "time": "2020-08-31T19:52:39.763Z",
    "resourceId": "/SUBSCRIPTIONS/A1BA9AAA-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/MANAGEDHSMS/CONTOSOMHSM",
    "operationName": "BackupCreate",
    "operationVersion": "7.0",
    "category": "AuditEvent",
    "resultType": "Success",
    "properties": {
        "PoolType": "M-HSM",
        "sku_Family": "B",
        "sku_Name": "Standard_B1"
    },
    "durationMs": 488,
    "callerIpAddress": "X.X.X.X",
    "identity": "{\"claim\":{\"appid\":\"04b07795-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"http_schemas_microsoft_com_identity\":{\"claims\":{\"objectidentifier\":\"b1c52bf0-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"}},\"http_schemas_xmlsoap_org_ws_2005_05_identity\":{\"claims\":{\"upn\":\"admin@contoso.com\"}}}}",
    "clientInfo": "azsdk-python-core/1.7.0 Python/3.8.2 (Linux-4.19.84-microsoft-standard-x86_64-with-glibc2.29) azsdk-python-azure-keyvault/7.2",
    "correlationId": "8806614c-ebc3-11ea-9e9b-00155db778ad",
    "subnetId": "(unknown)",
    "httpStatusCode": 202,
    "PoolName": "mhsmdemo",
    "requestUri": "https://ContosoMHSM.managedhsm.azure.net/backup",
    "resourceGroup": "ContosoResourceGroup",
    "resourceProvider": "MICROSOFT.KEYVAULT",
    "resource": "ContosoMHSM",
    "resourceType": "managedHSMs"
  }
]
```



## <a name="use-azure-monitor-logs"></a>Usar os logs do Azure Monitor

Você pode usar a solução do Key Vault nos logs do Azure Monitor para examinar os logs do **AuditEvent** do HSM Gerenciado. Nos logs do Azure Monitor, você usa consultas de log para analisar dados e obter as informações necessárias.

Para obter mais informações, incluindo como configurar isso, confira [Azure Key Vault no Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Aprender sobre as [melhores práticas](best-practices.md) para provisionar e usar um HSM Gerenciado
- Aprender sobre [como fazer backup e restaurar](backup-restore.md) um HSM Gerenciado
