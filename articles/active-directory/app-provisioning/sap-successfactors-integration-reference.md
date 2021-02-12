---
title: Referência de integração do Azure Active Directory e do SAP SuccessFactors
description: Aprofundamento técnico no provisionamento direcionado do SAP SuccessFactors-HR
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: ed97600ca1802629f81f93f4f51c92ad4b1c9bd1
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256214"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Como Azure Active Directory provisionamento se integra ao SAP SuccessFactors 

[Azure Active Directory serviço de provisionamento de usuário](../app-provisioning/user-provisioning.md) integra-se ao [SAP SuccessFactors Employee central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) para gerenciar o ciclo de vida de identidade dos usuários. O Azure Active Directory oferece três integrações predefinidas: 

* [SuccessFactors no local Active Directory provisionamento de usuário](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors para Azure Active Directory provisionamento de usuário](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)
* [Write-back SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)

Este artigo explica como a integração funciona e como você pode personalizar o comportamento de provisionamento para diferentes cenários de RH. 

## <a name="establishing-connectivity"></a>Estabelecendo conectividade 
O serviço de provisionamento do Azure AD usa a autenticação básica para se conectar aos pontos de extremidade da API do OData central do funcionário. Ao configurar o aplicativo de provisionamento SuccessFactors, use o parâmetro *URL do locatário* na seção *credenciais de administrador* para configurar a [API Data Center URL](https://apps.support.sap.com/sap/support/knowledge/en/2215682). 

Para proteger ainda mais a conectividade entre o serviço de provisionamento do Azure AD e o SuccessFactors, você pode adicionar os intervalos de IP do Azure AD na lista de permissões de IP do SuccessFactors usando as etapas descritas abaixo:

1. Baixar os [intervalos de IP mais recentes](https://www.microsoft.com/download/details.aspx?id=56519) para a nuvem pública do Azure 
1. Abra o arquivo e procure a marca **AzureActiveDirectory** 

   >[!div class="mx-imgBorder"] 
   >![Intervalo de IPS do Azure AD](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Copie todos os intervalos de endereços IP listados no elemento *addressPrefixes* e use o intervalo para criar sua lista de restrições de endereço IP.
1. Traduza os valores de CIDR em intervalos de IP.  
1. Faça logon no portal de administração do SuccessFactors para adicionar intervalos de IP à lista de permissões. Consulte a [Nota de suporte SAP 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200). Agora você pode [Inserir intervalos de IP](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) nesta ferramenta. 

## <a name="supported-entities"></a>Entidades com suporte
Para cada usuário no SuccessFactors, o serviço de provisionamento do Azure AD recupera as entidades a seguir. Cada entidade é expandida usando a API OData *$Expand* parâmetro de consulta. Consulte a coluna de *regra de recuperação* abaixo. Algumas entidades são expandidas por padrão, enquanto algumas entidades são expandidas somente se um atributo específico estiver presente no mapeamento. 

| \# | Entidade SuccessFactors                  | Nó do OData     | Regra de recuperação |
|----|----------------------------------------|------------------------------|------------------|
| 1  | Perpessoa                              | *nó raiz*                  | Sempre           |
| 2  | Entre pessoas                            | personalInfoNav              | Sempre           |
| 3  | Por telefone                               | phoneNav                     | Sempre           |
| 4  | Enviar por email                               | emailNav                     | Sempre           |
| 5  | EmpEmployment                          | employmentNav                | Sempre           |
| 6  | Usuário                                   | employmentNav/userNav        | Sempre           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | Sempre           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | Sempre           |
| 9  | Gerente do usuário                         | employmentNav/userNav/gerente/empInfo | Sempre  |
| 10 | FOCompany                              | employmentNav/jobInfoNav/companyNav | Somente se `company` ou o `companyId` atributo for mapeado |
| 11 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | Somente se `department` ou o `departmentId` atributo for mapeado |
| 12 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | Somente se `businessUnit` ou o `businessUnitId` atributo for mapeado |
| 13 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | Somente se `costCenter` ou o `costCenterId` atributo for mapeado |
| 14 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | Somente se `division` ou o `divisionId` atributo for mapeado |
| 15 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | Somente se `jobCode` ou o `jobCodeId` atributo for mapeado |
| 16 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | Somente se o `payGrade` atributo for mapeado |
| 17 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | Somente se o `location` atributo for mapeado |
| 18 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | Se o mapeamento contiver um dos seguintes atributos: `officeLocationAddress,  officeLocationCity, officeLocationZipCode` |
| 19 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | Somente se o `eventReason` atributo for mapeado |
| 20 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | Somente se `assignmentType` for mapeado |
| 21 | Lista de seleção de empregos                | employmentNav/jobInfoNav/employmentTypeNav | Somente se `employmentType` for mapeado |
| 22 | Lista de seleção de EmployeeClass                 | employmentNav/jobInfoNav/employeeClassNav | Somente se `employeeClass` for mapeado |
| 23 | Lista de seleção de EmplStatus                    | employmentNav/jobInfoNav/emplStatusNav | Somente se `emplStatus` for mapeado |
| 24 | Lista de seleção de assignmenttype                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | Somente se `assignmentType` for mapeado |

## <a name="how-full-sync-works"></a>Como funciona a sincronização completa
Com base no mapeamento de atributo, durante a sincronização completa, o serviço de provisionamento do Azure AD envia a seguinte consulta de API OData "GET" para buscar dados efetivos de todos os usuários ativos. 

> [!div class="mx-tdCol2BreakAll"]
>| Parâmetro | Descrição |
>| ----------|-------------|
>| Host de API OData | Anexa https à URL do *locatário*. Exemplo: `https://api4.successfactors.com` |
>| Ponto de extremidade da API OData | `/odata/v2/PerPerson` |
>| Parâmetro de consulta de $format OData | `json` |
>| Parâmetro de consulta de $filter OData | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| Parâmetro de consulta de $expand OData | Esse valor de parâmetro depende dos atributos mapeados. Exemplo: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| Parâmetro de consulta OData customPageSize | `100` |

> [!NOTE]
> Durante a primeira sincronização completa inicial, o serviço de provisionamento do Azure AD não obtém dados de trabalho inativos/encerrados.

Para cada usuário do SuccessFactors, o serviço de provisionamento procura uma conta no destino (Azure AD/local Active Directory) usando o atributo correspondente definido no mapeamento. Por exemplo: se *personIdExternal* for mapeado para *EmployeeID* e for definido como o atributo correspondente, o serviço de provisionamento usará o valor *personIdExternal* para pesquisar o usuário com o filtro *EmployeeID* . Se uma correspondência de usuário for encontrada, ela atualizará os atributos de destino. Se nenhuma correspondência for encontrada, ela criará uma nova entrada no destino. 

Para validar os dados retornados pelo ponto de extremidade da API OData para um específico `personIdExternal` , atualize o `SuccessFactorsAPIEndpoint` na consulta de API abaixo com sua API Data Center servidor URL e use uma ferramenta como o [postmaster](https://www.postman.com/downloads/) para invocar a consulta. 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>Como funciona a sincronização incremental

Após a sincronização completa, o serviço de provisionamento do Azure AD mantém `LastExecutionTimestamp` e usa-o para criar consultas Delta para recuperar alterações incrementais. Os atributos de carimbo de data/hora presentes em cada entidade SuccessFactors, como `lastModifiedDateTime` ,, `startDate` `endDate` e `latestTerminationDate` , são avaliados para ver se a alteração está entre o `LastExecutionTimestamp` e o `CurrentExecutionTime` . Em caso afirmativo, a alteração da entrada será considerada efetiva e processada para sincronização. 

## <a name="reading-attribute-data"></a>Lendo dados de atributo

Quando o serviço de provisionamento do Azure AD consulta SuccessFactors, ele recupera um conjunto de resultados JSON. O conjunto de resultados JSON inclui um número de atributos armazenados no funcionário central. Por padrão, o esquema de provisionamento é configurado para recuperar apenas um subconjunto desses atributos. 

Para recuperar atributos adicionais, siga as etapas listadas abaixo:
    
1. Navegue até **aplicativos empresariais**  ->  **SuccessFactors**  ->  **provisionamento**  ->  de aplicativo **Editar**  ->  **página de mapeamento de atributo de** provisionamento.
1. Role para baixo e clique em **Mostrar opções avançadas**.
1. Clique em **Editar lista de atributos do SuccessFactors**. 

   > [!NOTE] 
   > Se a opção **Editar lista de atributos para o SuccessFactors** não aparecer no portal do Azure, use a URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* para acessar a página. 

1. A coluna **expressão de API** nessa exibição exibe as expressões JSONPath usadas pelo conector.

   >[!div class="mx-imgBorder"] 
   >![Expressão de API](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  

1. Você pode editar um valor JSONPath existente ou adicionar um novo atributo com uma expressão JSONPath válida ao esquema. 

A próxima seção fornece uma lista de cenários comuns para editar os valores de JSONPath. 

## <a name="handling-different-hr-scenarios"></a>Lidando com cenários de RH diferentes

JSONPath é uma linguagem de consulta para JSON semelhante ao XPath for XML. Como o XPath, o JSONPath permite a extração e a filtragem de dados de uma carga JSON.

Usando a transformação JSONPath, você pode personalizar o comportamento do aplicativo de provisionamento do Azure AD para recuperar atributos personalizados e manipular cenários como recontratar, conversão de trabalho e atribuição global. 

Esta seção aborda como você pode personalizar o aplicativo de provisionamento para os seguintes cenários de RH: 
* [Recuperando atributos adicionais](#retrieving-additional-attributes)
* [Recuperando atributos personalizados](#retrieving-custom-attributes)
* [Manipulando cenário de conversão de trabalho](#handling-worker-conversion-scenario)
* [Manipulando o cenário de recontratação](#handling-rehire-scenario)
* [Tratando do cenário de atribuição global](#handling-global-assignment-scenario)
* [Manipulando o cenário de trabalhos simultâneos](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>Recuperando atributos adicionais

O esquema padrão do aplicativo de provisionamento SuccessFactors do Azure AD é fornecido com mais de [90 atributos predefinidos](sap-successfactors-attribute-reference.md). Para adicionar mais atributos SuccessFactors ao esquema de provisionamento, use as etapas listadas abaixo: 

1. Use a consulta OData abaixo para recuperar dados de um usuário de teste válido da central de funcionários. 

   ```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
   ```

1. Determinar a entidade do funcionário central associada ao atributo
   * Se o atributo fizer parte da entidade *EmpEmployment* , procure o atributo em nó *employmentNav* . 
   * Se o atributo fizer parte da entidade do *usuário* , procure o atributo em nó *employmentNav/userNav* .
   * Se o atributo fizer parte da entidade *EmpJob* , procure o atributo no nó *employmentNav/jobInfoNav* . 
1. Construa o caminho JSON associado ao atributo e adicione esse novo atributo à lista de atributos SuccessFactors. 
   * Exemplo 1: digamos que você queira adicionar o atributo *okToRehire*, que faz parte da entidade *employmentNav* , em seguida, use o JSONPath  `$.employmentNav.results[0].okToRehire`
   * Exemplo 2: digamos que você queira adicionar o *fuso horário* do atributo, que faz parte da entidade *userNav* , em seguida, use o JSONPath `$.employmentNav.results[0].userNav.timeZone`
   * Exemplo 3: digamos que você queira adicionar o atributo *flsaStatus*, que faz parte da entidade *jobInfoNav* , em seguida, use o JSONPath `$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
1. Salve o esquema. 
1. Reinicie o provisionamento.

### <a name="retrieving-custom-attributes"></a>Recuperando atributos personalizados

Por padrão, os seguintes atributos personalizados são predefinidos no aplicativo de provisionamento SuccessFactors do Azure AD: 
* *custom01-custom15* da entidade User (userNav)
* *customString1-customString15* da entidade EmpEmployment (employmentNav) chamada *empNavCustomString1-empNavCustomString15*
* *customString1-customString15* da entidade EmpJobInfo (jobInfoNav) chamada *empJobNavCustomString1-empNavJobCustomString15*

Digamos, em sua instância central de funcionários, o atributo *customString35* em *EmpJobInfo* armazena a descrição do local. Você deseja fluir esse valor para Active Directory atributo *physicalDeliveryOfficeName* . Para configurar o mapeamento de atributos para esse cenário, use as etapas fornecidas abaixo: 

1. Edite a lista de atributos SuccessFactors para adicionar um novo atributo chamado *empJobNavCustomString35*.
1. Defina a expressão da API JSONPath para este atributo como: `$.employmentNav.results[0].jobInfoNav.results[0].customString35`
1. Salve e recarregue a alteração de mapeamento no portal do Azure.  
1. Na folha atributo-mapeamento, mapeie *empJobNavCustomString35* para *physicalDeliveryOfficeName*.
1. Salve o mapeamento.

Estendendo este cenário: 
* Se você quiser mapear o atributo *custom35* da entidade de *usuário* , use o JSONPath `$.employmentNav.results[0].userNav.custom35`
* Se você quiser mapear o atributo *customString35* da entidade *EmpEmployment* , use o JSONPath `$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>Manipulando cenário de conversão de trabalho

A conversão de trabalho é o processo de converter um funcionário em tempo integral existente para um prestador de atendimento ou um prestador de tempo integral. Nesse cenário, a central de funcionários adiciona uma nova entidade *EmpEmployment* junto com uma nova entidade de *usuário* para a mesma entidade *Person* . A entidade de *usuário* aninhada na entidade *EmpEmployment* anterior está definida como NULL. Para lidar com esse cenário para que os novos dados de emprego apareçam quando ocorre uma conversão, você pode atualizar em massa o esquema do aplicativo de provisionamento usando as etapas listadas abaixo:  

1. Abra a folha de mapeamento de atributo do seu aplicativo de provisionamento SuccessFactors. 
1. Role para baixo e clique em **Mostrar opções avançadas**.
1. Clique no link **revisar seu esquema aqui** para abrir o editor de esquema. 

   >![Captura de tela mostra o link examinar seu esquema aqui, que abre o editor de esquema.](media/sap-successfactors-integration-reference/review-schema.png#lightbox)

1. Clique no link **baixar** para salvar uma cópia do esquema antes de editar. 

   >![Captura de tela mostra o editor de esquema com download selecione para salvar uma cópia do esquema.](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
1. No editor de esquema, pressione a tecla Ctrl-H para abrir o controle localizar-substituir.
1. Na caixa de texto Localizar, copie e cole o valor `$.employmentNav.results[0]`
1. Na caixa de texto substituir, copie e cole o valor `$.employmentNav.results[?(@.userNav != null)]` . Observe o espaço em branco ao redor do `!=` operador, que é importante para o processamento bem-sucedido da expressão JSONPath. 
   >![localizar-substituir-conversão](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
1. Clique na opção "substituir tudo" para atualizar o esquema. 
1. Salve o esquema. 
1. O processo acima atualiza todas as expressões JSONPath da seguinte maneira: 
   * JSONPath antigo: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Novo JSONPath: `$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
1. Reinicie o provisionamento. 

### <a name="handling-rehire-scenario"></a>Manipulando o cenário de recontratação

Geralmente, há duas opções para processar recontratações: 
* Opção 1: criar um novo perfil de pessoa na central do funcionário
* Opção 2: reutilizar o perfil de pessoa existente na central do funcionário

Se o processo de RH usar a opção 1, nenhuma alteração será necessária para o esquema de provisionamento. Se o processo de RH usar a opção 2, a central de funcionários adicionará uma nova entidade *EmpEmployment* junto com uma nova entidade de *usuário* para a mesma entidade *Person* . Ao contrário do cenário de conversão, a entidade de *usuário* na entidade *EmpEmployment* anterior não está definida como NULL. 

Para lidar com esse cenário de recontratação (opção 2), para que os dados de emprego mais recentes sejam exibidos para perfis de recontratação, você pode atualizar em massa o esquema do aplicativo de provisionamento usando as etapas listadas abaixo:  

1. Abra a folha de mapeamento de atributo do seu aplicativo de provisionamento SuccessFactors. 
1. Role para baixo e clique em **Mostrar opções avançadas**.
1. Clique no link **revisar seu esquema aqui** para abrir o editor de esquema.   
1. Clique no link **baixar** para salvar uma cópia do esquema antes de editar.   
1. No editor de esquema, pressione a tecla Ctrl-H para abrir o controle localizar-substituir.
1. Na caixa de texto Localizar, copie e cole o valor `$.employmentNav.results[0]`
1. Na caixa de texto substituir, copie e cole o valor `$.employmentNav.results[-1:]` . Essa expressão JSONPath retorna o registro de *EmpEmployment* mais recente.   
1. Clique na opção "substituir tudo" para atualizar o esquema. 
1. Salve o esquema. 
1. O processo acima atualiza todas as expressões JSONPath da seguinte maneira: 
   * JSONPath antigo: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Novo JSONPath: `$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
1. Reinicie o provisionamento. 

Essa alteração de esquema também dá suporte ao cenário de conversão de trabalho. 

### <a name="handling-global-assignment-scenario"></a>Tratando do cenário de atribuição global

Quando um usuário na central do funcionário é processado para atribuição global, o SuccessFactors adiciona uma nova entidade *EmpEmployment* e define o *ASSIGNMENTCLASS* como "Ga". Ele também cria uma nova entidade de *usuário* . Portanto, o usuário agora tem:
* Uma   +  entidade de *usuário* EmpEmployment que corresponde à atribuição inicial com *assignmentClass* definido como "St" e 
* Outra   +  entidade de *usuário* EmpEmployment que corresponde à atribuição global com *assignmentClass* definido como "Ga"

Para buscar atributos que pertencem à atribuição padrão e ao perfil de usuário de atribuição global, use as etapas listadas abaixo: 

1. Abra a folha de mapeamento de atributo do seu aplicativo de provisionamento SuccessFactors. 
1. Role para baixo e clique em **Mostrar opções avançadas**.
1. Clique no link **revisar seu esquema aqui** para abrir o editor de esquema.   
1. Clique no link **baixar** para salvar uma cópia do esquema antes de editar.   
1. No editor de esquema, pressione a tecla Ctrl-H para abrir o controle localizar-substituir.
1. Na caixa de texto Localizar, copie e cole o valor `$.employmentNav.results[0]`
1. Na caixa de texto substituir, copie e cole o valor `$.employmentNav.results[?(@.assignmentClass == 'ST')]` . 
1. Clique na opção "substituir tudo" para atualizar o esquema. 
1. Salve o esquema. 
1. O processo acima atualiza todas as expressões JSONPath da seguinte maneira: 
   * JSONPath antigo: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Novo JSONPath: `$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
1. Recarregue a folha de mapeamento de atributo do aplicativo. 
1. Role para baixo e clique em **Mostrar opções avançadas**.
1. Clique em **Editar lista de atributos do SuccessFactors**.
1. Adicione novos atributos para buscar dados de atribuição global. Por exemplo: se você quiser buscar o nome do departamento associado a um perfil de atribuição global, poderá adicionar o atributo *globalAssignmentDepartment* com a expressão JSONPath definida como `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` . 
1. Agora você pode fluir ambos os valores de departamento para Active Directory atributos ou fluir seletivamente um valor usando o mapeamento de expressão. Exemplo: a expressão abaixo define o valor do atributo de *Departamento* do AD como *globalAssignmentDepartment* se presente, caso contrário, ele define o valor para *Department* associado à atribuição padrão. 
   * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`

1. Salve o mapeamento. 
1. Reinicie o provisionamento. 

### <a name="handling-concurrent-jobs-scenario"></a>Manipulando o cenário de trabalhos simultâneos

Quando um usuário na central do funcionário tem trabalhos simultâneos/múltiplos, há duas entidades *EmpEmployment* e *usuário* com *assignmentClass* definido como "St". Para buscar atributos que pertencem a ambos os trabalhos, use as etapas listadas abaixo: 

1. Abra a folha de mapeamento de atributo do seu aplicativo de provisionamento SuccessFactors. 
1. Role para baixo e clique em **Mostrar opções avançadas**.
1. Clique em **Editar lista de atributos do SuccessFactors**.
1. Digamos que você deseja efetuar pull do departamento associado ao trabalho 1 e ao trabalho 2. O *Departamento* de atributos predefinido já busca o valor do departamento para o primeiro trabalho. Você pode definir um novo atributo chamado *secondJobDepartment* e definir a expressão JSONPath como `$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
1. Agora você pode fluir ambos os valores de departamento para Active Directory atributos ou fluir seletivamente um valor usando o mapeamento de expressão. 
1. Salve o mapeamento. 
1. Reinicie o provisionamento. 

## <a name="writeback-scenarios"></a>Cenários de write-back

Esta seção aborda diferentes cenários de write-back. Ele recomenda abordagens de configuração com base em como o email e o número de telefone são configurados no SuccessFactors.

### <a name="supported-scenarios-for-phone-and-email-write-back"></a>Cenários com suporte para telefone e Write-back de email 

| \# | Requisito de cenário | Email primário <br> valor do sinalizador | Telefone comercial <br> valor do sinalizador primário | Telefone celular <br> valor do sinalizador primário | Telefone comercial <br> mapping | Telefone celular <br> mapping |
|--|--|--|--|--|--|--|
| 1 | * Definir somente email comercial como primário. <br> * Não defina números de telefone. | true | true | false | \[Não definido\] | \[Não definido\] | 
| 2 | * No SuccessFactors, o email comercial e o telefone comercial são os principais <br> * Sempre flua o número de telefone do Azure AD para telefone comercial e móvel para telefone celular. | true | true | false | telephoneNumber | Serviço Móvel | 
| 3 | * No SuccessFactors, o email comercial e o telefone celular são primários <br> * Sempre flua o número de telefone do Azure AD para telefone comercial e móvel para telefone celular | true | false | verdadeiro |  telephoneNumber | Serviço Móvel | 
| 4 | * No email comercial do SuccessFactors é o principal <br> * No Azure AD, verifique se o número de telefone de trabalho está presente, se presente, verifique se o número de celular também está presente, marque número de telefone comercial como primário somente se o número de celular não estiver presente. | verdadeiro | Usar mapeamento de expressão: `IIF(IsPresent([telephoneNumber]), IIF(IsPresent([mobile]),"false", "true"), "false")` | Usar mapeamento de expressão: `IIF(IsPresent([mobile]),"false", "true")` | telephoneNumber | Serviço Móvel | 
| 5 | * No SuccessFactors Business email e o telefone comercial é o principal. <br> * No Azure AD, se o Mobile estiver disponível, defina-o como o telefone comercial, caso contrário, use telephoneNumber. | true | true | false | `IIF(IsPresent([mobile]), [mobile], [telephoneNumber])` | \[Não definido\] | 

* Se não houver mapeamento para o número de telefone no mapeamento de atributo write-back, somente o email será incluído no write-back.
* Durante o novo contratação de integração na central de funcionários, o email comercial e o número de telefone podem não estar disponíveis. Se a configuração de email comercial e telefone comercial como primário for obrigatória durante a integração, você poderá definir um valor fictício para telefone comercial e email durante a criação de nova contratação, que será eventualmente atualizada pelo aplicativo de write-back.
 
### <a name="unsupported-scenarios-for-phone-and-email-write-back"></a>Cenários sem suporte para telefone e Write-back de email

* Na central de funcionários, durante a integração, o email pessoal e o telefone pessoal são definidos como primário. O aplicativo write-back não pode mudar essa configuração e definir email comercial e telefone comercial como primário.
* Na central de funcionários, o telefone comercial é definido como primário. O aplicativo write-back não pode alterar isso e definir o telefone celular como primário.
* O aplicativo write-back não pode ler as configurações do sinalizador primário atual e usar os mesmos valores para a operação de gravação. Os valores de sinalizador configurados no mapeamento de atributo serão sempre usados. 

## <a name="next-steps"></a>Próximas etapas

* [Saiba como configurar o SuccessFactors para Active Directory provisionamento](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Saiba como configurar o Write-back para SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [Saiba mais sobre os Atributos do SuccessFactors com suporte para o provisionamento de entrada](sap-successfactors-attribute-reference.md)










