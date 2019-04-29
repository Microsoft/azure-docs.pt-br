---
title: Matriz de módulo e versão Ansible do Azure | Microsoft Docs
description: Matriz de módulo e versão Ansible do Azure
keywords: ansible, funções, matriz, versão, azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 3f5bda36368ee1fe1f37527422c6072c3ffda177
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763317"
---
# <a name="ansible-module-and-version-matrix"></a>Matriz de módulo e versão Ansible do Azure

O Ansible inclui um conjunto de módulos para uso em provisionar e configurar recursos do Azure. Esses recursos incluem máquinas virtuais, conjuntos de dimensionamento, serviços e serviços de contêiner de rede. Este artigo lista vários módulos de Ansible do Azure e as versões do Ansible no qual eles são fornecidos.

## <a name="ansible-modules-for-azure"></a>Módulos de Ansible do Azure

Os seguintes módulos podem ser executados diretamente em hosts remotos ou por meio de Guias estratégicos.

Esses módulos estão disponíveis com a versão oficial do Ansible e das seguintes funções de guia estratégico da Microsoft.

| Módulo Ansible do Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Função Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Computação**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_availabilityset_facts              | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_deployment                         | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_functionapp                        | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_functionapp_facts                  | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_image                              | -            | Sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_resource                           | -            | -                           | Sim          | sim          | sim          | Sim          |
| azure_rm_resource_facts                     | -            | -                           | Sim          | sim          | sim          | Sim          |
| azure_rm_resourcegroup                      | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_resourcegroup_facts                | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_virtualmachine                     | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Sim          | sim          | Sim          |
| azure_rm_virtualmachineextension           | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_virtualmachineimage_facts          | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_virtualmachinescaleset            | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_virtualmachinescaleset_facts      | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | Sim          | Sim          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | Sim          | Sim          |
| **Rede**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Sim          | sim          | Sim          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Sim          | Sim          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | Sim          | Sim          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Sim          | Sim          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | Sim          | Sim          |
| azure_rm_dnsrecordset                       | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_dnsrecordset_facts                 | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_dnszone                            | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_dnszone_facts                      | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_loadbalancer                       | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_loadbalancer_facts                 | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_networkinterface                   | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_networkinterface_facts             | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_publicipaddress                    | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_publicipaddress_facts              | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_route                              | -            | -                           | -            | Sim          | sim          | Sim          |
| azure_rm_routetable                         | -            | -                           | -            | Sim          | sim          | Sim          |
| azure_rm_routetable_facts                   | -            | -                           | -            | Sim          | sim          | Sim          |
| azure_rm_securitygroup                      | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_subnet                             | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Sim          | sim          | Sim          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | Sim          | sim          | Sim          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Sim          | sim          | Sim          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | Sim          | sim          | Sim          |
| azure_rm_virtualnetwork                     | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_virtualnetwork_facts               | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Sim          | Sim          |
| **Armazenamento**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_manageddisk_facts                  | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_storageaccount                     | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_storageaccount_facts               | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_storageblob                        | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| **Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Sim          | sim          | Sim          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Sim          | sim          | Sim          |
| azure_rm_webapp                             | -            | -                         | -          | Sim          | sim          | Sim          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Sim          | sim          | Sim          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Sim          | Sim          |
| **Contêineres**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | Sim          | sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_aks                                | -            | -                           | Sim          | sim          | sim          | Sim          |
| azure_rm_aks_facts                          | -            | -                           | Sim          | sim          | sim          | Sim          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_containerinstance                  | -            | Sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_containerregistry                  | -            | Sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | Sim          | sim          | Sim          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Sim          | Sim          |
| **Bancos de dados**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mysqldatabase                      | -            | Sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Sim          | sim          | Sim          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mysqlserver                        | -            | Sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Sim          | sim          | Sim          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_postgresqldatabase                 | -            | Sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Sim          | sim          | Sim          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_postgresqlserver                   | -            | Sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Sim          | sim          | Sim          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_sqldatabase                        | -            | Sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Sim          | sim          | Sim          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_sqlserver                          | -            | Sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_sqlserver_facts                    | -            | Sim                         | sim          | sim          | sim          | Sim          |
| **Analytics**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Sim          | Sim          |
| **Integração**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Sim          | Sim          |
| **Segurança**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | Sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | Sim          | Sim          |
| azure_rm_keyvaultkey                        | -            | Sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_keyvaultsecret                     | -            | Sim                         | sim          | sim          | sim          | Sim          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | Sim          | Sim          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | Sim          | Sim          |
| **Azure Monitor**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | Sim          | sim          | Sim          |
| azure_rm_autoscale_facts            | -            | -                         | -          | Sim          | sim          | Sim          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | Sim          | Sim          |

## <a name="introduction-to-playbook-role-for-azure"></a>Introdução à função de guia estratégico do Azure

O [função de manual azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) inclui todos os módulos do Azure mais recente. As atualizações e correções de bug são feitas de forma mais rápida que na versão oficial do Ansible. Se você usar o Ansible para fins de provisionamento de recursos do Azure, será recomendado para instalar o `azure_preview_module` função de manual.

O `azure_preview_module` função de manual é liberada a cada três semanas.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre funções de manual, consulte [criar manuais reutilizáveis](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 