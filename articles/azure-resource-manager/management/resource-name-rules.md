---
title: Restrições de nomenclatura de recursos
description: Mostra as regras e restrições de nomenclatura de recursos do Azure.
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: dbe19dae4509e1dd6aa95763e4d034c339bb0531
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220846"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Regras de nomenclatura e restrições para recursos do Azure

Este artigo resume as regras de nomenclatura e as restrições para recursos do Azure. Para obter recomendações sobre como dar nome aos recursos, veja [Convenções de nomenclatura e de marcação recomendadas](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

Este artigo lista os recursos por namespace do provedor de recursos. Para obter uma lista de como os provedores de recursos correspondem aos serviços do Azure, consulte [provedores de recursos para serviços do Azure](azure-services-resource-providers.md).

Os nomes de recursos não diferenciam maiúsculas de minúsculas, a menos que sejam especificamente indicados na coluna de caracteres válidos.

Nas tabelas a seguir, o termo alfanumérico refere-se a:

* **a** a **z** (letras minúsculas)
* **A** a **Z** (letras maiúsculas)
* **0** a **9** (números)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | servers | grupo de recursos | 3-63 | Letras minúsculas e números.<br><br>Comece com uma letra minúscula. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | serviço | global | 1-50 | Alfanuméricos e hifens.<br><br>Comece com letra e termine com caractere alfanumérico. |
> | service / apis | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / apis / issues | api | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / apis / issues / attachments | problema | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / apis / issues / comments | problema | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / apis / operations | api | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / apis / operations / tags | operação | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / apis / releases | api | 1-80 | Caracteres alfanuméricos, sublinhados e hifens.<br><br>Comece e termine com caracteres alfanuméricos ou sublinhados. |
> | service / apis / schemas | api | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / apis / tagDescriptions | api | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / apis / tags | api | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / api-version-sets | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / authorizationServers | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / backends | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / certificates | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / diagnostics | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / groups | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / groups / users | group | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / identityProviders | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / loggers | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / notifications | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / notifications / recipientEmails | notificação | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / openidConnectProviders | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / policies | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / products | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / products / apis | product | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / products / groups | product | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / products / tags | product | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / properties | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / subscriptions | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / tags | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / templates | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |
> | service / users | serviço | 1–256 | Não é possível usar:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | configurationStores | grupo de recursos | 5 a 50 | Caracteres alfanuméricos, sublinhados e hifens. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | locks | escopo da atribuição | 1-90 | Caracteres alfanuméricos, pontos, sublinhados, hifens e parênteses.<br><br>Não pode terminar com ponto. |
> | policyAssignments | escopo da atribuição | Nome de exibição 1–128<br><br>1-64 nome do recurso<br><br>1-24 nome do recurso no escopo do grupo de gerenciamento | O nome para exibição pode conter qualquer caractere.<br><br>O nome do recurso não pode incluir `%` e não pode terminar com ponto ou espaço. |
> | policyDefinitions | escopo da definição | Nome de exibição 1–128<br><br>1-64 nome do recurso | O nome para exibição pode conter qualquer caractere.<br><br>O nome do recurso não pode incluir `%` e não pode terminar com ponto ou espaço. |
> | policySetDefinitions | escopo da definição | Nome de exibição 1–128<br><br>1-64 nome do recurso<br><br>1-24 nome do recurso no escopo do grupo de gerenciamento | O nome para exibição pode conter qualquer caractere.<br><br>O nome do recurso não pode incluir `%` e não pode terminar com ponto ou espaço.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | automationaccounts | região de & do grupo de recursos <br>(Veja a observação abaixo) | 6–50 | Caracteres alfanuméricos e hifens.<br><br>Comece com letra e termine com caractere alfanumérico. |
> | automationAccounts / certificates | conta de automação | 1–128 | Não é possível usar:<br> `<>*%&:\?.+/` <br><br>Não pode terminar com espaço.  |
> | automationAccounts / connections | conta de automação | 1–128 | Não é possível usar:<br> `<>*%&:\?.+/` <br><br>Não pode terminar com espaço. |
> | automationAccounts / credentials | conta de automação | 1–128 | Não é possível usar:<br> `<>*%&:\?.+/` <br><br>Não pode terminar com espaço. |
> | automationAccounts / runbooks | conta de automação | 1-63 | Caracteres alfanuméricos, sublinhados e hifens.<br><br>Comece com letra.  |
> | automationAccounts / schedules | conta de automação | 1–128 | Não é possível usar:<br> `<>*%&:\?.+/` <br><br>Não pode terminar com espaço. |
> | automationAccounts / variables | conta de automação | 1–128 | Não é possível usar:<br> `<>*%&:\?.+/` <br><br>Não pode terminar com espaço. |
> | automationAccounts / watchers | conta de automação | 1-63 |  Caracteres alfanuméricos, sublinhados e hifens.<br><br>Comece com letra. |
> | automationAccounts / webhooks | conta de automação | 1–128 | Não é possível usar:<br> `<>*%&:\?.+/` <br><br>Não pode terminar com espaço. |

> [!NOTE]
> Os nomes da conta de Automação são exclusivos por região e grupo de recursos. Os nomes das contas de Automação excluídas não estarão disponíveis imediatamente.

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | batchAccounts | Região | 3-24 | Letras minúsculas e números. |
> | batchAccounts / applications | conta do lote | 1-64 | Caracteres alfanuméricos, sublinhados e hifens. |
> | batchAccounts / certificates | conta do lote | 5–45 | Caracteres alfanuméricos, sublinhados e hifens. |
> | batchAccounts / pools | conta do lote | 1-64 | Caracteres alfanuméricos, sublinhados e hifens. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | blockchainMembers | global | 2–20 | Letras minúsculas e números.<br><br>Comece com uma letra minúscula. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | botServices | global | 2-64 |  Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. |
> | botServices / channels | serviço de bot | 2-64 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. |
> | botServices / Connections | serviço de bot | 2-64 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. |
> | enterpriseChannels | grupo de recursos | 2-64 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | Redis | global | 1-63 | Caracteres alfanuméricos e hifens.<br><br>Comece e termine com caractere alfanumérico. Hifens consecutivos não são permitidos. |
> | Redis / firewallRules | Redis | 1–256 | Caracteres alfanuméricos |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | perfis | grupo de recursos | 1–260 | Caracteres alfanuméricos e hifens.<br><br>Comece e termine com caractere alfanumérico. |
> | profiles/endpoints | global | 1-50 | Caracteres alfanuméricos e hifens.<br><br>Comece e termine com caractere alfanumérico. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | certificateOrders | grupo de recursos | 3–30 | Caracteres alfanuméricos. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | accounts | grupo de recursos | 2-64 | Caracteres alfanuméricos e hifens.<br><br>Comece e termine com caractere alfanumérico. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | availabilitySets | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | diskEncryptionSets | grupo de recursos | 1-80 | Caracteres alfanuméricos e sublinhados. |
> | disks | grupo de recursos | 1-80 | Caracteres alfanuméricos e sublinhados. |
> | galleries | grupo de recursos | 1-80 | Caracteres alfanuméricos e pontos.<br><br>Comece e termine com caractere alfanumérico. |
> | galleries / applications | gallery | 1-80 | Caracteres alfanuméricos, hifens e pontos.<br><br>Comece e termine com caractere alfanumérico. |
> | galleries / applications/versions | aplicativo | Inteiro de 32 bits | Números e pontos. |
> | galleries/images | gallery | 1-80 | Alfanuméricos, sublinhados, hifens e pontos.<br><br>Comece e termine com caractere alfanumérico. |
> | galleries/images/versions | image | Inteiro de 32 bits | Números e pontos. |
> | images | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | snapshots | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | virtualMachines | grupo de recursos | 1–15 (Windows)<br>1–64 (Linux)<br><br>Veja a observação abaixo. | Não é possível usar espaço ou estes caracteres:<br> `\/"'[]:|<>+=;,?*@&_`<br><br>As VMs do Windows não podem incluir o período ou terminar com hífen.<br><br>As VMs do Linux não podem terminar com um ponto ou hífen. |
> | virtualMachineScaleSets | grupo de recursos | 1–15 (Windows)<br>1–64 (Linux)<br><br>Veja a observação abaixo. | Não é possível usar espaço ou estes caracteres:<br> `\/"'[]:|<>+=;,?*@&`<br><br>Não pode começar com sublinhado. Não pode terminar com ponto ou hífen. |

> [!NOTE]
> As máquinas virtuais do Azure têm dois nomes distintos: nome do recurso e nome do host. Quando você cria uma máquina virtual no portal, o mesmo valor é usado para ambos os nomes. As restrições na tabela anterior são para o nome do host. O nome real do recurso pode ter até 64 caracteres.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | containerGroups | grupo de recursos | 1-63 | Letras minúsculas, números e hifens.<br><br>Não pode iniciar ou terminar com hífen. Hifens consecutivos não são permitidos. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | registries | global | 5 a 50 | Caracteres alfanuméricos. |
> | registries / buildTasks | Registro | 5 a 50 | Caracteres alfanuméricos. |
> | registries / buildTasks/steps | tarefa de build | 5 a 50 | Caracteres alfanuméricos. |
> | registries/replications | Registro | 5 a 50 | Caracteres alfanuméricos. |
> | registries / scopeMaps | Registro | 5 a 50 | Caracteres alfanuméricos, hifens e sublinhados. |
> | registries/tasks | Registro | 5 a 50 | Caracteres alfanuméricos, hifens e sublinhados. |
> | registries/tokens | Registro | 5 a 50 | Caracteres alfanuméricos, hifens e sublinhados. |
> | registries/webhooks | Registro | 5 a 50 | Caracteres alfanuméricos. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | managedClusters | grupo de recursos | 1-63 | Caracteres alfanuméricos, sublinhados e hifens.<br><br>Comece e termine com caractere alfanumérico. |
> | openShiftManagedClusters | grupo de recursos | 1–30 | Caracteres alfanuméricos. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | hubs | grupo de recursos | 1-64 | Caracteres alfanuméricos.<br><br>Comece com letra.  |
> | hubs / authorizationPolicies | hub | 1-50 | Caracteres alfanuméricos, sublinhados e pontos.<br><br>Comece e termine com caractere alfanumérico. |
> | hubs / connectors | hub | 1–128 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com letra. |
> | hubs / connectors/mappings | conector | 1–128 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com letra. |
> | hubs / interactions | hub | 1–128 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com letra. |
> | hubs / kpi | hub | 1–512 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com letra. |
> | hubs / links | hub | 1–512 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com letra. |
> | hubs / predictions | hub | 1–512 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com letra. |
> | hubs / profiles | hub | 1–128 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com letra. |
> | hubs / relationshipLinks | hub | 1–512 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com letra. |
> | hubs / relationships | hub | 1–512 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com letra. |
> | hubs / roleAssignments | hub | 1–128 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com letra. |
> | hubs / views | hub | 1–512 | Caracteres alfanuméricos e sublinhados.<br><br>Comece com letra. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | associations | grupo de recursos | 1–180 | Não é possível usar:<br>`%&\\?/`<br><br>Não pode terminar com ponto ou espaço. |
> | resourceProviders | grupo de recursos | 3–64 | Não é possível usar:<br>`%&\\?/`<br><br>Não pode terminar com ponto ou espaço. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | jobs | grupo de recursos | 3-24 | Caracteres alfanuméricos, hifens, sublinhados e pontos. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | workspaces | grupo de recursos | 3–30 | Caracteres alfanuméricos, sublinhados e hifens |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | factories | global | 3-63 | Caracteres alfanuméricos e hifens.<br><br>Comece e termine com caractere alfanumérico. |
> | factories / dataflows | fábrica | 1–260 | Não é possível usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com caractere alfanumérico. |
> | factories / datasets | fábrica | 1–260 | Não é possível usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com caractere alfanumérico. |
> | factories / integrationRuntimes | fábrica | 3-63 | Caracteres alfanuméricos e hifens.<br><br>Comece e termine com caractere alfanumérico. |
> | factories / linkedservices | fábrica | 1–260 | Não é possível usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com caractere alfanumérico. |
> | factories / pipelines | fábrica | 1–260 | Não é possível usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com caractere alfanumérico. |
> | factories / triggers | fábrica | 1–260 | Não é possível usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com caractere alfanumérico. |
> | factories / triggers / rerunTriggers | gatilho | 1–260 | Não é possível usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com caractere alfanumérico. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | Letras minúsculas e números. |
> | accounts / computePolicies | account | 3–60 | Caracteres alfanuméricos, hifens e sublinhados. |
> | accounts / dataLakeStoreAccounts | account | 3-24 | Letras minúsculas e números. |
> | accounts / firewallRules | account | 3-50 | Caracteres alfanuméricos, hifens e sublinhados. |
> | accounts / storageAccounts | account | 3–60 | Caracteres alfanuméricos, hifens e sublinhados. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | Letras minúsculas e números. |
> | accounts / firewallRules | account | 3-50 | Caracteres alfanuméricos, hifens e sublinhados. |
> | accounts / virtualNetworkRules | account | 3-50 | Caracteres alfanuméricos, hifens e sublinhados. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | services | grupo de recursos | 2–62 | Caracteres alfanuméricos, hifens, pontos e sublinhados.<br><br>Comece com caractere alfanumérico. |
> | services/projects | serviço | 2–57 | Caracteres alfanuméricos, hifens, pontos e sublinhados.<br><br>Comece com caractere alfanumérico. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | servers | global | 3-63 | Letras minúsculas, hifens e números.<br><br>Não pode iniciar ou terminar com hífen. |
> | servers / databases | servers | 1-63 | Caracteres alfanuméricos e hifens. |
> | servers / firewallRules | servers | 1–128 | Caracteres alfanuméricos, hifens e sublinhados. |
> | servers / virtualNetworkRules | servers | 1–128 | Caracteres alfanuméricos e hifens. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | servers | global | 3-63 | Letras minúsculas, hifens e números.<br><br>Não pode iniciar ou terminar com hífen. |
> | servers / databases | servers | 1-63 | Caracteres alfanuméricos e hifens. |
> | servers / firewallRules | servers | 1–128 | Caracteres alfanuméricos, hifens e sublinhados. |
> | servers / virtualNetworkRules | servers | 1–128 | Caracteres alfanuméricos e hifens. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | servers | global | 3-63 | Letras minúsculas, hifens e números.<br><br>Não pode iniciar ou terminar com hífen. |
> | servers / databases | servers | 1-63 | Caracteres alfanuméricos e hifens. |
> | servers / firewallRules | servers | 1–128 | Caracteres alfanuméricos, hifens e sublinhados. |
> | servers / virtualNetworkRules | servers | 1–128 | Caracteres alfanuméricos e hifens. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | IotHubs | global | 3-50 | Caracteres alfanuméricos e hifens.<br><br>Não pode terminar com hífen. |
> | IotHubs / certificates | Hub IoT | 1-64 | Caracteres alfanuméricos, hifens, pontos e sublinhados. |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | Caracteres alfanuméricos, hifens, pontos e sublinhados. |
> | provisioningServices | grupo de recursos | 3–64 | Caracteres alfanuméricos e hifens.<br><br>Termine com caractere alfanumérico. |
> | provisioningServices / certificates | provisioningServices | 1-64 | Caracteres alfanuméricos, hifens, pontos e sublinhados. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | labs | grupo de recursos | 1-50 | Caracteres alfanuméricos, sublinhados e hifens. |
> | labs / customimages | laboratório | 1-80 | Caracteres alfanuméricos, sublinhados, hifens e parênteses. |
> | labs / formulas | laboratório | 1-80 | Caracteres alfanuméricos, sublinhados, hifens e parênteses. |
> | labs / virtualmachines | laboratório | 1–15 (Windows)<br>1–64 (Linux) | Caracteres alfanuméricos e hifens.<br><br>Comece e termine com caractere alfanumérico. Não pode ser apenas números. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | databaseAccounts | global | 3–44 | Letras minúsculas, números e hifens.<br><br>Comece com letra minúscula ou número. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | domains | grupo de recursos | 3-50 | Caracteres alfanuméricos e hifens. |
> | domains / topics | domínio | 3-50 | Caracteres alfanuméricos e hifens. |
> | eventSubscriptions | grupo de recursos | 3–64 | Caracteres alfanuméricos e hifens. |
> | topics | grupo de recursos | 3-50 | Caracteres alfanuméricos e hifens. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | clusters | grupo de recursos | 6–50 | Caracteres alfanuméricos e hifens.<br><br>Comece com letra. Termine com letra ou número. |
> | namespaces | global | 6–50 | Caracteres alfanuméricos e hifens.<br><br>Comece com letra. Termine com letra ou número. |
> | namespaces / AuthorizationRules | namespace | 1-50 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Deve começar e terminar com letra ou número. |
> | namespaces / disasterRecoveryConfigs | namespace | 1-50 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Deve começar e terminar com letra ou número. |
> | namespaces / eventhubs | namespace | 1-50 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Deve começar e terminar com letra ou número. |
> | namespaces / eventhubs / authorizationRules | hub de eventos | 1-50 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Deve começar e terminar com letra ou número. |
> | namespaces / eventhubs / consumergroups | hub de eventos | 1-50 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Deve começar e terminar com letra ou número. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | clusters | global | 3–59 | Caracteres alfanuméricos e hifens<br><br>Deve começar e terminar com letra ou número. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | jobs | grupo de recursos | 2-64 | Caracteres alfanuméricos e hifens.<br><br>Comece com letra. |

## <a name="microsoftinsights"></a>Microsoft.insights

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | actionGroups | grupo de recursos | 1–260 | Não é possível usar:<br>`/&%\?` <br><br>Não pode terminar com espaço ou ponto.  |
> | components | grupo de recursos | 1–260 | Não é possível usar:<br>`%&\?/` <br><br>Não pode terminar com espaço ou ponto.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | IoTApps | global | 2–63 | Letras minúsculas, números e hifens.<br><br>Comece com letra minúscula ou número. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | vaults | global | 3-24 | Caracteres alfanuméricos e hifens.<br><br>Comece com letra. Termine com letra ou dígito. Não pode conter hifens consecutivos. |
> | vaults / secrets | Cofre | 1–127 | Caracteres alfanuméricos e hifens. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | clusters | global | 4–22 | Letras minúsculas e números.<br><br>Comece com letra. |
> | /clusters / databases | cluster | 1–260 | Caracteres alfanuméricos, hifens, espaços e pontos. |
> | /clusters / databases / dataConnections | Banco de Dados | 1–40 | Caracteres alfanuméricos, hifens, espaços e pontos. |
> | /clusters / databases / eventhubconnections | Banco de Dados | 1–40 | Caracteres alfanuméricos, hifens, espaços e pontos. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | integrationAccounts | grupo de recursos | 1-80 | Caracteres alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationAccounts / assemblies | conta de integração | 1-80 | Caracteres alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationAccounts / batchConfigurations | conta de integração | 1–20 | Caracteres alfanuméricos. |
> | integrationAccounts / certificates | conta de integração | 1-80 | Caracteres alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationAccounts / maps | conta de integração | 1-80 | Caracteres alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationAccounts / partners | conta de integração | 1-80 | Caracteres alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationAccounts / rosettanetprocessconfigurations | conta de integração | 1-80 | Caracteres alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationAccounts / schemas | conta de integração | 1-80 | Caracteres alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationAccounts / sessions | conta de integração | 1-80 | Caracteres alfanuméricos, hifens, sublinhados, pontos e parênteses. |
> | integrationServiceEnvironments | grupo de recursos | 1-80 | Caracteres alfanuméricos, hifens, pontos e sublinhados. |
> | integrationServiceEnvironments / managedApis | ambiente do serviço de integração | 1-80 | Caracteres alfanuméricos, hifens, pontos e sublinhados. |
> | workflows | grupo de recursos | 1-80 | Caracteres alfanuméricos, hifens, sublinhados, pontos e parênteses. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | commitmentPlans | grupo de recursos | 1–260 | Não é possível usar:<br>`<>*%&:?+/\\`<br><br>Não pode terminar com um espaço. |
> | webServices | grupo de recursos | 1–260 | Não é possível usar:<br>`<>*%&:?+/\\`<br><br>Não pode terminar com um espaço. |
> | workspaces | grupo de recursos | 1–260 | Não é possível usar:<br>`<>*%&:?+/\\`<br><br>Não pode terminar com um espaço. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | workspaces | grupo de recursos | 3–33 | Caracteres alfanuméricos e hifens. |
> | workspaces / computes | workspace | 2–16 | Caracteres alfanuméricos e hifens. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | userAssignedIdentities | grupo de recursos | 3–128 | Caracteres alfanuméricos, hifens e sublinhados<br><br>Comece com letra ou número. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | accounts | grupo de recursos | 1–98 (para nome do grupo de recursos e nome da conta) | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | mediaservices | grupo de recursos | 3-24 | Letras minúsculas e números. |
> | mediaservices / liveEvents | Serviço de mídia | 1–32 | Caracteres alfanuméricos e hifens.<br><br>Comece com caractere alfanumérico. |
> | mediaservices / liveEvents / liveOutputs | Evento ao vivo | 1–256 | Caracteres alfanuméricos e hifens.<br><br>Comece com caractere alfanumérico. |
> | mediaservices / streamingEndpoints | Serviço de mídia | 1-24 | Caracteres alfanuméricos e hifens.<br><br>Comece com caractere alfanumérico. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | applicationGateways | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | applicationSecurityGroups | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | azureFirewalls | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | bastionHosts | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | connections | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | dnsZones | grupo de recursos | Caracteres 1–63<br><br>2 a 34 rótulos<br><br>Cada rótulo é um conjunto de caracteres separados por um ponto. Por exemplo, **contoso.com** tem 2 rótulos. | Cada rótulo pode conter caracteres alfanuméricos, sublinhados e hifens.<br><br>Cada rótulo é separado por um ponto. |
> | expressRouteCircuits | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | firewallPolicies | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | firewallPolicies / ruleGroups | Política de firewall | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | frontDoors | global | 5–64 | Caracteres alfanuméricos e hifens.<br><br>Comece e termine com caractere alfanumérico. |
> | frontdoorWebApplicationFirewallPolicies | grupo de recursos | 1–128 | Caracteres alfanuméricos.<br><br>Comece com letra. |
> | loadBalancers | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | loadBalancers / inboundNatRules | balanceador de carga | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | virtualNetworkGateways | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | networkInterfaces | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | networkSecurityGroups | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | networkSecurityGroups / securityRules | grupo de segurança de rede | 1-80 |  Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | networkWatchers | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | privateDnsZones | grupo de recursos | Caracteres 1–63<br><br>2 a 34 rótulos<br><br>Cada rótulo é um conjunto de caracteres separados por um ponto. Por exemplo, **contoso.com** tem 2 rótulos. | Cada rótulo pode conter caracteres alfanuméricos, sublinhados e hifens.<br><br>Cada rótulo é separado por um ponto. |
> | privateDnsZones / virtualNetworkLinks | zona DNS privada | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | publicIPAddresses | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | publicIPPrefixes | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | routeFilters | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | routeFilters / routeFilterRules | filtro de rota | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | routeTables | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | routeTables / routes | route table | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | serviceEndpointPolicies | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | trafficmanagerprofiles | global | 1-63 | Caracteres alfanuméricos, hifens e pontos.<br><br>Comece e termine com caractere alfanumérico. |
> | virtualNetworkGateways | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | virtualNetworks | grupo de recursos | 2-64 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | virtualnetworks / subnets | rede virtual | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | virtualNetworks / virtualNetworkPeerings | rede virtual | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | virtualWans | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | vpnGateways | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | vpnGateways / vpnConnections | gateway de VPN | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |
> | vpnSites | grupo de recursos | 1-80 | Caracteres alfanuméricos, sublinhados, pontos e hifens.<br><br>Comece com caractere alfanumérico. Termine com caractere alfanumérico ou sublinhado. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | namespaces | global | 6–50 | Caracteres alfanuméricos e hifens<br><br>Comece com letra. Termine com caractere alfanumérico. |
> | namespaces / AuthorizationRules | namespace | 1–256 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece com caractere alfanumérico. |
> | namespaces / notificationHubs | namespace | 1–260 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece com caractere alfanumérico. |
> | namespaces / notificationHubs / AuthorizationRules | hub de notificação | 1–256 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece com caractere alfanumérico. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | clusters | grupo de recursos | 4–63 | Caracteres alfanuméricos e hifens.<br><br>Comece e termine com caractere alfanumérico. |
> | workspaces | global | 4–63 | Caracteres alfanuméricos e hifens.<br><br>Comece e termine com caractere alfanumérico. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | solutions | workspace | N/D | Para soluções criadas pela Microsoft, o nome deve estar no padrão:<br>`SolutionType(WorkspaceName)`<br><br>Para soluções criadas por terceiros, o nome deve estar no padrão:<br>`SolutionType[WorkspaceName]`<br><br>Por exemplo, um nome válido é:<br>`AntiMalware(contoso-IT)`<br><br>O tipo de solução diferencia maiúsculas de minúsculas. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | dashboards | grupo de recursos | 3–160 | Caracteres alfanuméricos e hifens.<br><br>Para usar caracteres restritos, adicione uma marca denominada **hidden-title** com o nome do painel que você deseja usar. O portal exibe esse nome ao mostrar o painel. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | Caracteres alfanuméricos e hifens.<br><br>Não pode começar com hífen. Não é possível usar hifens consecutivos. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | capacities | region | 3-63 | Letras minúsculas ou números<br><br>Comece com uma letra minúscula. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | vaults | grupo de recursos | 2–50 | Caracteres alfanuméricos e hifens.<br><br>Comece com letra. |
> | vaults / backupPolicies | cofre | 3–150 | Caracteres alfanuméricos e hifens.<br><br>Comece com letra. Não pode terminar com hífen. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | namespaces | global | 6–50 | Caracteres alfanuméricos e hifens.<br><br>Comece com uma letra. Termine com uma letra ou número. |
> | namespaces / AuthorizationRules | namespace | 1-50 |  Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com caractere alfanumérico. |
> | namespaces / HybridConnections | namespace | 1–260 | Caracteres alfanuméricos, pontos, hifens, sublinhados e barras.<br><br>Comece e termine com caractere alfanumérico. |
> | namespaces / HybridConnections/authorizationRules | conexão híbrida | 1-50 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com caractere alfanumérico. |
> | namespaces / WcfRelays | namespace | 1–260 | Caracteres alfanuméricos, pontos, hifens, sublinhados e barras.<br><br>Comece e termine com caractere alfanumérico. |
> | namespaces / WcfRelays / authorizationRules | Retransmissão de WCF | 1-50 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com caractere alfanumérico. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | deployments | grupo de recursos | 1-64 | Caracteres alfanuméricos, sublinhados, parênteses, hifens e pontos. |
> | resourcegroups | subscription | 1-90 | Caracteres alfanuméricos, sublinhados, parênteses, hifens, pontos e caracteres unicode que correspondem à [documentação regex](/rest/api/resources/resourcegroups/createorupdate).<br><br>Não pode terminar com ponto. |
> | tagNames | recurso | 1–512 | Não é possível usar:<br>`<>%&\?/` |
> | tagNames / tagValues | nome da tag | 1–256 | Todos os caracteres. |
> | templateSpecs | grupo de recursos | 1-90 | Caracteres alfanuméricos, sublinhados, parênteses, hifens e pontos. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | namespaces | global | 6–50 | Caracteres alfanuméricos e hifens.<br><br>Comece com uma letra. Termine com uma letra ou número.<br><br>Para mais informações, veja [Criar namespace](/rest/api/servicebus/create-namespace). |
> | namespaces / AuthorizationRules | namespace | 1-50 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com caractere alfanumérico. |
> | namespaces / disasterRecoveryConfigs | global | 6–50 | Caracteres alfanuméricos e hifens.<br><br>Comece com letra. Termine com caractere alfanumérico. |
> | namespaces / migrationConfigurations | namespace |  | Sempre deve ser **$default**. |
> | namespaces / queues | namespace | 1–260 | Caracteres alfanuméricos, pontos, hifens, sublinhados e barras.<br><br>Comece e termine com caractere alfanumérico. |
> | namespaces / queues / authorizationRules | fila | 1-50 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com caractere alfanumérico. |
> | namespaces / topics | namespace | 1–260 | Caracteres alfanuméricos, pontos, hifens, sublinhados e barras.<br><br>Comece e termine com caractere alfanumérico. |
> | namespaces / topics / authorizationRules | topic | 1-50 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com caractere alfanumérico. |
> | namespaces / topics / subscriptions | topic | 1-50 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com caractere alfanumérico. |
> | namespaces / topics / subscriptions / rules | subscription | 1-50 | Caracteres alfanuméricos, pontos, hifens e sublinhados.<br><br>Comece e termine com caractere alfanumérico. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | clusters | region | 4–23 | Letras minúsculas, números e hifens.<br><br>Comece com uma letra minúscula. Termine com letra minúscula ou número. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | signalR | global | 3-63 | Caracteres alfanuméricos e hifens.<br><br>Comece com letra. Termine com letra ou número.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | managedInstances | global | 1-63 | Letras minúsculas, números e hifens.<br><br>Não pode iniciar ou terminar com hífen. <br><br> Não pode ter nenhum caractere especial, como `@` . |
> | servers | global | 1-63 | Letras minúsculas, números e hifens.<br><br>Não pode iniciar ou terminar com hífen. |
> | servers / administrators | Servidor |  | Deve ser `ActiveDirectory`. |
> | servers / databases | Servidor | 1–128 | Não é possível usar:<br>`<>*%&:\/?`<br><br>Não pode terminar com ponto ou espaço. |
> | servers / databases / syncGroups | Banco de Dados | 1-150 | Caracteres alfanuméricos, hifens e sublinhados. |
> | servers / elasticPools | Servidor | 1–128 | Não é possível usar:<br>`<>*%&:\/?`<br><br>Não pode terminar com ponto ou espaço. |
> | servers / failoverGroups | global | 1-63 | Letras minúsculas, números e hifens.<br><br>Não pode iniciar ou terminar com hífen. |
> | servers / firewallRules | Servidor | 1–128 | Não é possível usar:<br>`<>*%&:;\/?`<br><br>Não pode terminar com ponto. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | storageAccounts | global | 3-24 | Letras minúsculas e números. |
> | storageAccounts / blobServices | do Azure |  | Deve ser `default`. |
> | storageAccounts / blobServices / containers | do Azure | 3-63 | Letras minúsculas, números e hifens.<br><br>Comece com letra minúscula ou número. Não é possível usar hifens consecutivos. |
> | storageAccounts / fileServices | do Azure |  | Deve ser `default`. |
> | storageAccounts / fileServices / shares | do Azure | 3-63 | Letras minúsculas, números e hifens.<br><br>Não pode iniciar ou terminar com hífen. Não é possível usar hifens consecutivos. |
> | storageAccounts / managementPolicies | do Azure |  | Deve ser `default`. |
> | blob | contêiner | 1-1024 | Qualquer caractere de URL, diferencia maiúsculas de minúsculas |
> | fila | do Azure | 3-63 | Letras minúsculas, números e hifens.<br><br>Não pode iniciar ou terminar com hífen. Não é possível usar hifens consecutivos. |
> | tabela | do Azure | 3-63 | Caracteres alfanuméricos.<br><br>Comece com letra. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | storageSyncServices | grupo de recursos | 1–260 | Caracteres alfanuméricos, espaços, pontos, hifens e sublinhados.<br><br>Não pode terminar com ponto ou espaço. |
> | storageSyncServices / syncGroups | serviço de sincronização de dados | 1–260 | Caracteres alfanuméricos, espaços, pontos, hifens e sublinhados.<br><br>Não pode terminar com ponto ou espaço. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | managers | grupo de recursos | 2–50 | Caracteres alfanuméricos e hifens.<br><br>Comece com letra. Termine com caractere alfanumérico. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | streamingjobs | grupo de recursos | 3-63 | Caracteres alfanuméricos, hifens e sublinhados. |
> | streamingjobs / functions | trabalho de streaming | 3-63 | Caracteres alfanuméricos, hifens e sublinhados. |
> | streamingjobs / inputs | trabalho de streaming | 3-63 | Caracteres alfanuméricos, hifens e sublinhados. |
> | streamingjobs / outputs | trabalho de streaming | 3-63 | Caracteres alfanuméricos, hifens e sublinhados. |
> | streamingjobs / transformations | trabalho de streaming | 3-63 | Caracteres alfanuméricos, hifens e sublinhados. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | environments | grupo de recursos | 1-90 | Não é possível usar:<br>`'<>%&:\?/#` |
> | environments / accessPolicies | environment | 1-90 | Não é possível usar:<br> `'<>%&:\?/#` |
> | environments / eventSources | environment | 1-90 | Não é possível usar:<br>`'<>%&:\?/#` |
> | environments / referenceDataSets | environment | 3-63 | Caracteres alfanuméricos |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | certificates | grupo de recursos | 1–260 | Não é possível usar:<br>`/` <br><br>Não pode terminar com espaço ou ponto.  | 
> | serverfarms | grupo de recursos | 1–40 | Caracteres alfanuméricos e hifens. |
> | sites | global | 2–60 | Contém caracteres alfanuméricos e hifens.<br><br>Não pode iniciar ou terminar com hífen. |
> | sites / slots | site | 2–59 | Caracteres alfanuméricos e hifens. |

> [!NOTE]
> O Azure Functions tem as mesmas regras de nomenclatura e restrições que Microsoft.Web/sites.

## <a name="next-steps"></a>Próximas etapas

Para obter recomendações sobre como dar nome aos recursos, veja [Pronto: Convenções de nomenclatura e de marcação recomendadas](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
