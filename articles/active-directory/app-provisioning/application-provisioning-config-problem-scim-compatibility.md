---
title: Problemas conhecidos com o sistema de conformidade do protocolo SCIM (gerenciamento de identidade entre domínios) 2,0-Azure AD
description: Como solucionar problemas de compatibilidade de protocolo comuns enfrentados ao adicionar um aplicativo inexistente na galeria que dá suporte a SCIM 2.0 ao Microsoft Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 08/05/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: d13629b4cb05995b9652e862f769a0ffcae30a8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256891"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Problemas conhecidos e as resoluções em conformidade com o protocolo SCIM 2.0 do serviço de Provisionamento de Usuário do Microsoft Azure Active Directory

O Microsoft Azure Active Directory (Azure AD) pode provisionar automaticamente usuários e grupos para qualquer aplicativo ou sistema que seja administrado por um serviço Web com a interface definida no [Sistema para especificação do protocolo de Gerenciamento de Identidade Entre Domínios (SCIM) 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Suporte do Azure AD para o protocolo SCIM 2.0 é descrito em [Usando o Sistema para Gerenciamento de Identidade Entre Domínios (SCIM) para provisionar automaticamente usuários e grupos do Microsoft Azure Active Directory para aplicativos](use-scim-to-provision-users-and-groups.md), que lista o partes específicas do protocolo que implementa a fim de provisionar automaticamente usuários e grupos do Azure AD para aplicativos que dão suporte a SCIM 2.0.

Este artigo descreve os problemas atuais e anteriores com a aderência ao serviço de provisionamento do usuário do Azure Active Directory ao protocolo SCIM 2.0 e como trabalhar nesses problemas.

## <a name="understanding-the-provisioning-job"></a>Noções básicas sobre o trabalho de provisionamento
O serviço de provisionamento usa o conceito de um trabalho para operar em um aplicativo. A jobID pode ser encontrada na [barra de progresso](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar). Todos os novos aplicativos de provisionamento são criados com um jobID começando com "SCIM". O trabalho scim representa o estado atual do serviço. Os trabalhos mais antigos têm a ID "customappsso". Esse trabalho representa o estado do serviço em 2018. 

Se você estiver usando um aplicativo na Galeria, o trabalho geralmente conterá o nome do aplicativo (por exemplo, zoom do floco de neve, databricks, etc.). Você pode ignorar esta documentação ao usar um aplicativo da galeria. Isso se aplica principalmente a aplicativos que não são da galeria com jobID SCIM ou customAppSSO.

## <a name="scim-20-compliance-issues-and-status"></a>Problemas de conformidade de SCIM 2.0 e o status
Na tabela a seguir, qualquer item marcado como Fixed significa que o comportamento adequado pode ser encontrado no trabalho SCIM. Trabalhamos para garantir a compatibilidade com versões anteriores para as alterações que fizemos. No entanto, não recomendamos a implementação de comportamento antigo. É recomendável usar o novo comportamento para novas implementações e atualizar as implementações existentes.

> [!NOTE]
> Para as alterações feitas em 2018, você pode reverter para o comportamento customappsso. Para as alterações feitas desde 2018, você pode usar as URLs para reverter para o comportamento mais antigo. Trabalhamos para garantir a compatibilidade com versões anteriores para as alterações que fizemos, permitindo que você reverta para o jobID antigo ou usando um sinalizador. No entanto, como mencionado anteriormente, não recomendamos a implementação de um comportamento antigo. É recomendável usar o novo comportamento para novas implementações e atualizar as implementações existentes.

| **Problemas de conformidade SCIM 2.0** |  **Fixado?** | **Corrigir a data**  |  **Compatibilidade com versões anteriores** |
|---|---|---|
| O Microsoft Azure Active Directory requer "/ scim" para estar na raiz da URL do ponto de extremidade do SCIM do aplicativo  | Sim  |  18 de dezembro de 2018 | fazer downgrade para customappSSO |
| Os atributos de extensão usam ponto de notação "." antes de nomes de atributo, em vez de notação de dois pontos “:” |  Sim  | 18 de dezembro de 2018  | fazer downgrade para customappSSO |
| As solicitações de patch para atributos com vários valores contêm a sintaxe de filtro de caminho inválido | Sim  |  18 de dezembro de 2018  | fazer downgrade para customappSSO |
| As solicitações de criação de grupo contêm um URI de esquema inválido | Sim  |  18 de dezembro de 2018  |  fazer downgrade para customappSSO |
| Atualizar o comportamento do PATCH para garantir a conformidade (por exemplo, ativo como booliano e remoções de associação de grupo adequadas) | Não | TBD| usar sinalizador de visualização |

## <a name="flags-to-alter-the-scim-behavior"></a>Sinalizadores para alterar o comportamento de SCIM
Use os sinalizadores abaixo na URL do locatário do seu aplicativo para alterar o comportamento padrão do cliente do SCIM.

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.jpg" alt-text="SCIM sinalizadores para o comportamento posterior.":::

* Use a URL a seguir para atualizar o comportamento do PATCH e garantir a conformidade do SCIM (por exemplo, ativo como booliano e remoções de associação de grupo adequadas). Esse comportamento está disponível no momento apenas ao usar o sinalizador, mas se tornará o comportamento padrão nos próximos meses. Observe que esse sinalizador de visualização atualmente não funciona com o provisionamento sob demanda. 
  * **URL (compatível com scim):** AzureAdScimPatch062020
  * **Referências de RFC SCIM:** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2
  * **Comportamento**
  ```json
   PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
   {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "remove",
            "path": "members[value eq \"16b083c0-f1e8-4544-b6ee-27a28dc98761\"]"
        }
    ]
   }

    PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "members",
            "value": [
                {
                    "value": "10263a6910a84ef9a581dd9b8dcc0eae"
                }
            ]
        }
    ]
    } 

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].value",
            "value": "someone@contoso.com"
        },
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].primary",
            "value": true
        },
        {
            "op": "replace",
            "value": {
                "active": false,
                "userName": "someone"
            }
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "active",
            "value": false
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department",
            "value": "Tech Infrastructure"
        }
    ]
    }
   
  ```

  * **URL de downgrade:** Depois que o novo comportamento em conformidade com o SCIM se tornar o padrão no aplicativo inexistente na Galeria, você poderá usar a seguinte URL para reverter para o comportamento antigo em conformidade não SCIM: AzureAdScimPatch2017
  


## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>Atualizando do trabalho customappsso mais antigo para o trabalho SCIM
Seguir as etapas abaixo excluirá seu trabalho customappsso existente e criará um novo trabalho do SCIM. 
 
1. Entre no portal do Azure em: https://portal.azure.com.
2. Na seção **Azure Active Directory > Aplicativos Enterprise** do portal do Azure, localize e selecione o seu aplicativo de SCIM.
3. Na seção **Propriedades** do seu aplicativo existente do SCIM, copie a **ID de objeto**.
4. Em uma nova janela do navegador da web, vá para https://developer.microsoft.com/graph/graph-explorer e entre como o administrador de locatário do Microsoft Azure Active Directory onde seu aplicativo é adicionado.
5. No Explorador do Graph, execute o comando a seguir para localizar a ID do seu trabalho de provisionamento. Substitua "[object-id]" pelo serviço de ID da entidade (ID de objeto) copiado da terceira etapa.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Obter trabalhos](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Obter trabalhos") 


6. Nos resultados, copie a cadeia de caracteres completa de "ID" que começa com "customappsso" ou "scim".
7. Execute o comando a seguir para recuperar a configuração de mapeamento de atributo, para que você possa fazer um backup. Use o mesmo [object-id] como antes e substitua [job-id] pela ID de trabalho de provisionamento copiada da última etapa.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Obter esquema](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Obter esquema") 

8. Copie a saída JSON da última etapa e salve-a em um arquivo de texto. O JSON contém qualquer mapeamento de atributo personalizado que você adicionou ao seu aplicativo antigo e deve ter aproximadamente algumas milhares de linhas de JSON.
9. Execute o comando a seguir para excluir o trabalho de provisionamento:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Execute o comando a seguir para criar um novo trabalho de provisionamento que tenha as correções mais recentes do serviço.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Nos resultados da última etapa, copie a cadeia de caracteres completa de "ID" que começa com "scim". Opcionalmente, reaplique os mapeamentos de atributo antigos executando o comando a seguir, substituindo [New-Job-ID] pela nova ID de trabalho copiada e inserindo a saída JSON da etapa #7 como o corpo da solicitação.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Volte para a primeira janela do navegador da web e selecione a guia **Provisionamento** para o seu aplicativo.
13. Verifique a configuração e, em seguida, inicie o trabalho de provisionamento. 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>Fazendo downgrade do trabalho SCIM para o trabalho customappsso (não recomendado)
 Permitimos que você faça o downgrade de volta para o comportamento antigo, mas não o recomende, pois o customappsso não se beneficia de algumas das atualizações que fazemos, e talvez não tenha suporte para sempre. 

1. Entre no portal do Azure em: https://portal.azure.com.
2. a seção **Azure Active Directory > aplicativos empresariais > Criar aplicativo** do portal do Azure, crie um novo aplicativo **inexistente na galeria**.
3. Na seção **propriedades** do seu novo aplicativo personalizado, copie a **ID de objeto**.
4. Em uma nova janela do navegador da web, vá para https://developer.microsoft.com/graph/graph-explorer e entre como o administrador de locatário do Microsoft Azure Active Directory onde seu aplicativo é adicionado.
5. No Graph Explorer, execute o comando a seguir para inicializar a configuração de provisionamento para seu aplicativo.
   Substitua "[object-id]" pelo serviço de ID da entidade (ID de objeto) copiado da terceira etapa.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Volte para a primeira janela do navegador da web e selecione a guia **Provisionamento** para o seu aplicativo.
7. Conclua o configuração de provisionamento de usuário conforme faria normalmente.


## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o provisionamento e desprovisionamento para aplicativos SaaS](user-provisioning.md)
