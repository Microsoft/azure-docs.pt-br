---
title: Atualizar um cluster do Azure Service Fabric | Microsoft Docs
description: Atualize o código e/ou configuração do Service Fabric que executa um cluster do Service Fabric, incluindo a definição do modo de atualização do cluster, a atualização de certificados, a adição de portas do aplicativo, a aplicação de patches no sistema operacional etc. O que você pode esperar após a execução das atualizações?
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 234bff5049babf0c4b1d036b40201720b2736228
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714672"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Atualizar a versão do Service Fabric de um cluster

Para qualquer sistema moderno, oferecer a possibilidade de atualização é fundamental para o sucesso duradouro de seu produto. Um cluster do Azure Service Fabric é um recurso cujo proprietário é você, mas que é parcialmente gerenciado pela Microsoft. Este artigo descreve como atualizar a versão do Service Fabric em execução no seu cluster do Azure.

Você pode definir o cluster para receber atualizações automáticas do Fabric, assim que elas forem liberadas pela Microsoft ou pode optar por selecionar uma versão com suporte do Fabric na qual deseja que o cluster se encontre.

Você pode fazer isso definindo a configuração do cluster "upgradeMode" no portal ou usando o Gerenciador de Recursos no momento da criação ou posteriormente em um cluster ativo 

> [!NOTE]
> Certifique-se de manter o cluster sempre executando uma versão do Fabric com suporte. Quando anunciamos o lançamento de uma nova versão do Service Fabric, a versão anterior é programada para encerrar seu tempo de vida após um mínimo de 60 dias a partir da data desse anúncio. As novas versões são anunciadas [no blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Então, a nova versão está disponível para escolha. 
> 
> 

14 dias antes da expiração da versão do cluster em execução, um evento de integridade é gerado, colocando seu cluster em um estado de integridade de aviso. O cluster permanecerá em um estado de aviso até você atualize para uma versão do Fabric com suporte.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Definir o modo de atualização no portal do Azure
Você pode definir o cluster para manual ou automático ao criá-lo.

![Create_Manualmode][Create_Manualmode]

Você pode definir o cluster como automático ou manual quando estiver em um cluster ativo, usando a experiência de gerenciamento. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Atualizando para uma nova versão em um cluster que está definido para o modo Manual por meio do portal.
Para atualizar para uma nova versão, tudo o que você precisa fazer é selecionar a versão disponível no menu suspenso e salvar. A atualização do Fabric é inicializada automaticamente. As políticas de integridade do cluster (uma combinação de integridade do nó e da integridade de todos os aplicativos executados no cluster) são atendidas durante a atualização.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Role para baixo deste documento para ler mais sobre como definir as políticas de integridade personalizadas. 

Depois de corrigir os problemas que resultaram na reversão, você precisará iniciar a atualização novamente, seguindo as mesmas etapas de antes.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Definir o modo de atualização usando o modelo do Resource Manager
Adicione a configuração "upgradeMode" à definição de recursos Microsoft.ServiceFabric/clusters e defina "clusterCodeVersion" para uma das versões do Fabric com suporte, conforme mostrado abaixo e, em seguida, implante o modelo. Os valores válidos para "upgradeMode" são "Manual" ou "Automático"

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Atualizando para uma nova versão em um cluster que está definido para o modo Manual por meio do modelo do Resource Manager.
Quando o cluster está no modo Manual, para atualizar para uma nova versão, altere "clusterCodeVersion" para uma versão com suporte e implante-o. A implantação do modelo ativa a atualização do Fabric, que é inicializada automaticamente. As políticas de integridade do cluster (uma combinação de integridade do nó e da integridade de todos os aplicativos executados no cluster) são atendidas durante a atualização.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida.  

Depois de corrigir os problemas que resultaram na reversão, você precisará iniciar a atualização novamente, seguindo as mesmas etapas de antes.

## <a name="set-custom-health-polices-for-upgrades"></a>Políticas de integridade personalizadas do conjunto para atualizações
Você pode especificar as políticas de integridade personalizadas para atualização do Fabric. Se você tiver configurado o cluster para atualizações automáticas do Fabric, essas políticas serão aplicadas à [Fase 1 das atualizações automáticas do Fabric](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Se você tiver configurado o cluster para atualizações manuais do Fabric, essas políticas serão aplicadas cada vez que você selecionar uma nova versão ao disparar o sistema para acionar a atualização do Fabric em seu cluster. Se você não substituir as políticas, os padrões serão usados.

Você pode especificar as políticas de integridade personalizados ou examinar as configurações atuais na folha de "atualização de malha", selecionando as configurações avançadas de atualização. Examine a imagem a seguir com detalhes. 

![Gerenciar políticas de integridade personalizadas][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Liste todas as versões disponíveis para todos os ambientes para determinada assinatura
Execute o comando a seguir e obtenha uma saída semelhante a esta.

"supportExpiryUtc" informa o quando determinada versão está expirando ou expirou. A versão mais recente não tem uma data válida, ela tem um valor de "9999-12-31T23:59:59.9999999", que significa apenas que a data de vencimento ainda não foi definida.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

## <a name="next-steps"></a>Próximas etapas
* Saiba como personalizar algumas das [configurações de malha do cluster do Service Fabric](service-fabric-cluster-fabric-settings.md)
* Saiba como [reduzir e escalar horizontalmente seu cluster](service-fabric-cluster-scale-up-down.md)
* Saiba mais sobre [atualizações de aplicativo](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
