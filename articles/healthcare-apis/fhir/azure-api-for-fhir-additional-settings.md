---
title: Configurações adicionais para a API do Azure para FHIR
description: Visão geral das configurações adicionais que você pode definir para a API do Azure para FHIR
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: matjazl
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: 23c4fb6be4e30e78faa1ce411037f828d6518f50
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017584"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>Configurações adicionais para a API do Azure para FHIR

Neste guia de instruções, examinaremos as configurações adicionais que talvez você queira definir em sua API do Azure para FHIR. Há páginas adicionais que detalham ainda mais detalhes.

## <a name="configure-database-settings"></a>Definir configurações de banco de dados

A API do Azure para o FHIR usa o banco de dados para armazenar os seus dados. O desempenho do banco de dados subjacente depende do número de unidades de solicitação (RU) selecionado durante o provisionamento do serviço ou nas configurações do banco de dados depois que o serviço tiver sido provisionado.

A taxa de transferência deve ser provisionada para garantir que recursos suficientes do sistema estejam disponíveis para o seu banco de dados em todos os momentos. A quantidade de RUs que você precisa para seu aplicativo depende das operações que você executa. As operações podem variar desde leituras simples e gravações até consultas mais complexas.

Para obter mais informações sobre como alterar as configurações padrão, consulte [definir configurações de banco de dados](configure-database.md).

## <a name="access-control"></a>Controle de acesso

A API do Azure para FHIR só permitirá que usuários autorizados acessem a API FHIR. Você pode configurar usuários autorizados por meio de dois mecanismos diferentes. A maneira primária e recomendada para configurar o controle de acesso é usar o [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/index.yml), que pode ser acessado por meio da folha de **controle de acesso (iam)** . O RBAC do Azure só funcionará se você quiser proteger o acesso ao plano de dados usando o locatário Azure Active Directory associado à sua assinatura. Se você quiser usar um locatário diferente, a API do Azure para FHIR oferece um mecanismo de controle de acesso do plano de dados FHIR local. As opções de configuração não são tão ricas quando se usa o mecanismo de RBAC local. Para obter detalhes, escolha uma das seguintes opções:

* [Azure RBAC para o plano de dados FHIR](configure-azure-rbac.md). Essa é a opção preferida quando você está usando o locatário de Azure Active Directory associado à sua assinatura.
* [Controle de acesso do plano de dados FHIR local](configure-local-rbac.md). Use esta opção somente quando você precisar usar um locatário de Azure Active Directory externo para o controle de acesso do plano de dados. 

## <a name="enable-diagnostic-logging"></a>Habilitar registro em log de diagnóstico
Talvez você queira habilitar o log de diagnósticos como parte da sua configuração para poder monitorar seu serviço e ter relatórios precisos para fins de conformidade. Para obter detalhes sobre como configurar o log de diagnóstico, consulte nosso [Guia de instruções](enable-diagnostic-logging.md) sobre como configurar o log de diagnósticos, juntamente com algumas consultas de exemplo. 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>Usar cabeçalhos personalizados para adicionar dados aos logs de auditoria
Na API do Azure para FHIR, talvez você queira incluir informações adicionais nos logs, provenientes do sistema de chamada. Para incluir essas informações, você pode usar cabeçalhos personalizados.

Você pode usar cabeçalhos personalizados para capturar vários tipos de informações. Por exemplo:

* Informações de identidade ou autorização
* Origem do chamador
* Organização de origem
* Detalhes do sistema do cliente (registro de integridade eletrônico, portal do paciente)

Para adicionar esses dados aos seus logs de auditoria, consulte o guia [usar cabeçalhos HTTP personalizados para adicionar dados a logs de auditoria](use-custom-headers.md) .

## <a name="next-steps"></a>Próximas etapas

Neste guia de instruções, você define configurações adicionais para a API do Azure para FHIR.

Em seguida, confira a série de tutoriais para criar um aplicativo Web que lê os dados do FHIR.

>[!div class="nextstepaction"]
>[Implantar aplicativo JavaScript](tutorial-web-app-fhir-server.md)