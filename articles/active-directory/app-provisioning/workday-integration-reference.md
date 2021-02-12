---
title: Referência de integração de Azure Active Directory e workday
description: Aprofundamento técnico no provisionamento orientado a workday-HR
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/18/2021
ms.author: chmutali
ms.openlocfilehash: f260bca196839a091ae7d12be6d5f85912bf92db
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255977"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Como Azure Active Directory provisionamento se integra ao workday

[Azure Active Directory serviço de provisionamento de usuário](../app-provisioning/user-provisioning.md) se integra ao [workday HCM](https://www.workday.com) para gerenciar o ciclo de vida de identidade dos usuários. O Azure Active Directory oferece três integrações predefinidas: 

* [Workday no local Active Directory provisionamento de usuário](../saas-apps/workday-inbound-tutorial.md)
* [WORKDAY para Azure Active Directory provisionamento de usuário](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Write-back do Workday](../saas-apps/workday-writeback-tutorial.md)

Este artigo explica como a integração funciona e como você pode personalizar o comportamento de provisionamento para diferentes cenários de RH. 

## <a name="establishing-connectivity"></a>Estabelecendo conectividade 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>Restringindo o acesso à API do workday aos pontos de extremidade do Azure AD
O serviço de provisionamento do Azure AD usa a autenticação básica para se conectar aos pontos de extremidade da API dos serviços Web da workday.  

Para proteger ainda mais a conectividade entre o serviço de provisionamento do Azure AD e o workday, você pode restringir o acesso para que o usuário do sistema de integração designado acesse apenas as APIs do workday de intervalos de IP permitidos do Azure AD. Entre em contato com o administrador do WORKDAY para concluir a configuração a seguir em seu locatário do workday. 

1. Baixe os [intervalos de IP mais recentes](https://www.microsoft.com/download/details.aspx?id=56519) para a nuvem pública do Azure. 
1. Abra o arquivo e procure a marca **AzureActiveDirectory** 

   >[!div class="mx-imgBorder"] 
   >![Intervalo de IPS do Azure AD](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Copie todos os intervalos de endereços IP listados no elemento *addressPrefixes* e use o intervalo para criar sua lista de endereços IP.
1. Faça logon no portal de administração do workday. 
1. Acesse a tarefa **manter intervalos de IP** para criar um novo intervalo de IP para data centers do Azure. Especifique os intervalos de IP (usando a notação CIDR) como uma lista separada por vírgulas.  
1. Acesse a tarefa **gerenciar políticas de autenticação** para criar uma nova política de autenticação. Na política de autenticação, use a lista de permissões de autenticação para especificar o intervalo de IP do Azure AD e o grupo de segurança que terá permissão de acesso desse intervalo de IP. Salve as alterações. 
1. Acesse a tarefa **ativar todas as alterações de política de autenticação pendente** para confirmar as alterações.

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>Limitando o acesso aos dados de trabalho no workday usando grupos de segurança restritos

As etapas padrão para [Configurar o usuário do sistema de integração do workday](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday) concedem acesso para recuperar todos os usuários em seu locatário do workday. Em determinados cenários de integração, talvez você queira limitar o acesso, para que os usuários que pertencem apenas a determinadas organizações de supervisão sejam retornados pela chamada à API Get_Workers e processados pelo conector do Azure AD do workday. 

Você pode atender esse requisito trabalhando com o administrador do workday e configurando grupos de segurança de sistema de integração restrito. Para obter mais informações sobre como isso é feito, consulte [Este artigo da Comunidade do workday](https://community.workday.com/forums/customer-questions/620393) (*credenciais de logon da Comunidade do workday são necessárias para acessar este artigo*)

Essa estratégia de limitar o acesso usando ISSG restritos (grupos de segurança do sistema de integração) é particularmente útil nos seguintes cenários: 
* **Cenário de distribuição em fases**: você tem um locatário grande do workday e planeja executar uma distribuição em fases do WORKDAY para o provisionamento automatizado do Azure AD. Nesse cenário, em vez de excluir usuários que não estão no escopo da fase atual com filtros de escopo do Azure AD, é recomendável configurar ISSG restritos para que somente os trabalhadores no escopo fiquem visíveis para o Azure AD.
* **Cenário de vários trabalhos de provisionamento**: você tem um grande locatário do workday e vários domínios do AD, cada um com suporte para uma unidade de negócios/divisão/empresa diferente. Para dar suporte a essa topologia, você gostaria de executar vários trabalhos do WORKDAY para o provisionamento do Azure AD com cada trabalho provisionando um conjunto específico de trabalhadores. Nesse cenário, em vez de usar filtros de escopo do Azure AD para excluir dados de trabalho, é recomendável configurar ISSG restritos para que somente os dados relevantes do trabalhador fiquem visíveis para o Azure AD. 

### <a name="workday-test-connection-query"></a>Consulta de conexão de teste do workday

Para testar a conectividade com o workday, o Azure AD envia o seguinte *Get_Workers* solicitação de serviços Web do workday. 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>Como funciona a sincronização completa

A **sincronização completa** no contexto do provisionamento controlado por workday refere-se ao processo de busca de todas as identidades do workday e à determinação de quais regras de provisionamento serão aplicadas a cada objeto de trabalho. A sincronização completa ocorre quando você ativa o provisionamento pela primeira vez e também quando você *reinicia o provisionamento* do portal do Azure ou usando as APIs do Graph. 

O Azure AD envia o seguinte *Get_Workers* solicitação de serviços Web do WORKDAY para recuperar dados de trabalho. A consulta pesquisa o log de transações do WORKDAY para todas as entradas de trabalho com data de efetivação a partir do tempo correspondente à execução da sincronização completa. 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
O nó *Response_Group* é usado para especificar quais atributos de trabalho serão obtidos do workday. Para obter uma descrição de cada sinalizador no nó de *Response_Group* , consulte a documentação da [API de Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType)do workday. 

Determinados valores de sinalizador especificados no nó *Response_Group* são calculados com base nos atributos configurados no aplicativo de provisionamento do Azure AD do workday. Consulte a seção sobre as *entidades com suporte* para os critérios usados para definir os valores de sinalizador. 

A resposta *Get_Workers* do WORKDAY para a consulta acima inclui o número de registros de trabalho e a contagem de páginas.

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
Para recuperar a próxima página do conjunto de resultados, a próxima consulta de *Get_Workers* especifica o número da página como um parâmetro na *Response_Filter*.

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
O serviço de provisionamento do Azure AD processa cada página e itera todos os trabalhadores efetivos durante a sincronização completa. Para cada entrada de trabalho importada do workday:
* A [expressão XPath](workday-attribute-reference.md) é aplicada para recuperar valores de atributo do workday.
* O mapeamento de atributo e as regras de correspondência são aplicados e 
* O serviço determina qual operação deve ser executada no destino (Azure AD/AD). 

Quando o processamento for concluído, ele salvará o carimbo de data/hora associado ao início da sincronização completa como uma marca d' água. Esta marca d' água serve como ponto de partida para o ciclo de sincronização incremental. 

## <a name="how-incremental-sync-works"></a>Como funciona a sincronização incremental

Após a sincronização completa, o serviço de provisionamento do Azure AD mantém `LastExecutionTimestamp` e usa-o para criar consultas Delta para recuperar alterações incrementais. Durante a sincronização incremental, o Azure AD envia os seguintes tipos de consultas para o workday: 

* [Consulta de atualizações manuais](#query-for-manual-updates)
* [Consultar atualizações efetivas e terminações](#query-for-effective-dated-updates-and-terminations)
* [Consulta para contratações em data futuras](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>Consulta de atualizações manuais

Os seguintes *Get_Workers* solicitam consultas para atualizações manuais que ocorreram entre a última execução e o tempo de execução atual. 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>Consultar atualizações efetivas e terminações

Os seguintes *Get_Workers* solicitam consultas para atualizações com data efetiva que ocorreram entre a última execução e o tempo de execução atual. 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>Consulta para contratações em data futuras

Se qualquer uma das consultas acima retornar uma contratação no futuro, a seguinte *Get_Workers* solicitação será usada para buscar informações sobre uma nova contratação no futuro. O atributo *wid* da nova contratação é usado para executar a pesquisa e a data de efetivação é definida como a data e a hora de contratação. 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="retrieving-worker-data-attributes"></a>Recuperando atributos de dados de trabalho

A API *Get_Workers* pode retornar conjuntos de dados diferentes associados a um trabalho. Dependendo das expressões de [API XPath](workday-attribute-reference.md) configuradas no esquema de provisionamento, o serviço de provisionamento do Azure ad determina quais conjuntos de dados recuperar do workday. Da mesma forma, os sinalizadores de *Response_Group* são definidos na solicitação *Get_Workers* . 

A tabela a seguir fornece orientação sobre como mapear a configuração a ser usada para recuperar um conjunto de dados específico. 

| \# | Entidade workday                       | Incluído por padrão | Padrão XPATH a ser especificado no mapeamento para buscar entidades não padrão             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | Dados pessoais                        | Sim                 | WD: Worker \_ Data/WD: \_ dados pessoais                                             |
| 2  | Dados de emprego                      | Sim                 | WD: Worker \_ Data/WD: dados de emprego \_                                           |
| 3  | Dados de trabalho adicionais                  | Sim                 | WD: Worker \_ Data/WD: empregos de trabalho \_ /WD: \_ trabalho de dados do trabalho de trabalho \_ \[ @wd:Primary \_ = 0\]|
| 4  | Dados da organização                    | Sim                 | WD: Worker \_ Data/WD: dados da organização \_                                         |
| 5  | Dados da cadeia de gerenciamento                | Sim                 | WD: Worker \_ Data/WD: \_ dados da cadeia de gerenciamento \_                                    |
| 6  | Organização de supervisão             | Sim                 | SUPERVISÃO                                                                 |
| 7  | Empresa                              | Sim                 | CORPORATIVA                                                                     |
| 8  | Unidade de negócios                        | Não                  | ' unidade de negócios \_ '                                                              |
| 9  | Hierarquia de unidade de negócios              | Não                  | ' hierarquia de unidades de negócios \_ \_ '                                                   |
| 10 | Hierarquia da empresa                    | Não                  | ' hierarquia da empresa \_ '                                                          |
| 11 | Centro de Custo                          | Não                  | ' centro de custo \_ '                                                                |
| 12 | Hierarquia do centro de custo                | Não                  | ' hierarquia do centro de custo \_ \_ '                                                     |
| 13 | Fundo                                 | Não                  | FUNDO                                                                        |
| 14 | Hierarquia de fundos                       | Não                  | ' hierarquia de fundos \_ '                                                             |
| 15 | Gift                                 | Não                  | Gift                                                                        |
| 16 | Hierarquia de presentes                       | Não                  | ' hierarquia de presentes \_ '                                                             |
| 17 | Conceder                                | Não                  | CONCEDER                                                                       |
| 18 | Hierarquia de concessão                      | Não                  | ' conceder \_ hierarquia '                                                            |
| 19 | Hierarquia de sites de negócios              | Não                  | ' hierarquia de site da empresa \_ \_ '                                                   |
| 20 | Organização da matriz                  | Não                  | TABELA                                                                      |
| 21 | Grupo de pagamento                            | Não                  | ' grupo de pagamento \_ '                                                                  |
| 22 | Programas                             | Não                  | SUPLEMENTA                                                                    |
| 23 | Hierarquia do programa                    | Não                  | ' hierarquia de programas \_ '                                                          |
| 24 | Região                               | Não                  | ' hierarquia de região \_ '                                                           |
| 25 | Hierarquia de localização                   | Não                  | ' hierarquia de localização \_ '                                                         |
| 26 | Dados de provisionamento de conta            | Não                  | WD: Worker \_ Data/WD: dados de provisionamento de conta \_ \_                                |
| 27 | Dados de verificação em segundo plano                | Não                  | WD: Worker \_ Data/WD: \_ dados de verificação em segundo plano \_                                    |
| 28 | Dados de qualificação do benefício             | Não                  | WD: Worker \_ Data/WD: \_ dados de qualificação do benefício \_                                 |
| 29 | Dados de registro do benefício              | Não                  | WD: Worker \_ Data/WD: \_ dados de registro do benefício \_                                  |
| 30 | Dados de carreira                          | Não                  | WD: Worker \_ Data/WD: dados de carreira \_                                               |
| 31 | Dados de compensação                    | Não                  | WD: Worker \_ Data/WD: dados de compensação \_                                         |
| 32 | Dados de autoridade de imposto de trabalho contingente | Não                  | WD: Worker \_ Data/WD: \_ dados de \_ \_ tipo de \_ formulário da autoridade de \_ imposto do trabalhador \_ contingente       |
| 33 | Dados do item de desenvolvimento                | Não                  | WD: Worker \_ Data/WD: \_ dados do item de desenvolvimento \_                                    |
| 34 | Dados de contratos de funcionários              | Não                  | WD: Worker \_ Data/WD: \_ dados de contratos de funcionários \_                                  |
| 35 | Dados de revisão do funcionário                 | Não                  | WD: Worker \_ Data/WD: \_ dados de revisão do funcionário \_                                     |
| 36 | Dados recebidos de comentários               | Não                  | WD: Worker \_ Data/WD: comentários \_ recebidos \_ dados                                   |
| 37 | Dados de meta do trabalhador                     | Não                  | WD: Worker \_ Data/WD: \_ dados de meta do trabalhador \_                                         |
| 38 | Dados de foto                           | Não                  | WD: Worker \_ Data/WD: dados de foto \_                                                |
| 39 | Dados de qualificação                   | Não                  | WD: Worker \_ Data/WD: dados de qualificação \_                                        |
| 40 | Dados de pessoas relacionadas                 | Não                  | WD: Worker \_ Data/WD: \_ dados de pessoas relacionadas \_                                     |
| 41 | Dados da função                            | Não                  | WD: Worker \_ Data/WD: dados da função \_                                                 |
| 42 | Dados de habilidade                           | Não                  | WD: Worker \_ Data/WD: dados de habilidade \_                                                |
| 43 | Dados do perfil de sucessão              | Não                  | WD: Worker \_ Data/WD: dados do \_ perfil de sucesso \_                                  |
| 44 | Dados de avaliação de talento               | Não                  | WD: Worker \_ Data/WD: \_ dados de avaliação de talento \_                                   |
| 45 | Dados da conta de usuário                    | Não                  | WD: Worker \_ Data/WD: \_ dados da conta de usuário \_                                        |
| 46 | Dados de documentos de trabalho                 | Não                  | WD: Worker \_ Data/WD: \_ dados de documentos do trabalhador \_                                     |

>[!NOTE]
>Cada entidade workday listada na tabela é protegida por uma **política de segurança de domínio** no workday. Se não for possível recuperar qualquer atributo associado à entidade depois de definir o XPATH correto, verifique com o administrador do WORKDAY para garantir que a política de segurança de domínio apropriada esteja configurada para o usuário do sistema de integração associado ao aplicativo de provisionamento. Por exemplo, para recuperar *dados de habilidades*, é necessário *obter* acesso nos dados de trabalho do domínio do workday *: habilidades e experiência*. 

Aqui estão alguns exemplos de como você pode estender a integração do WORKDAY para atender a requisitos específicos. 

**Exemplo 1**

Digamos que você deseja recuperar os seguintes conjuntos de dados do workday e usá-los em suas regras de provisionamento:

* Centro de custo
* Hierarquia do centro de custo
* Grupo de pagamento

Os conjuntos de dados acima não são incluídos por padrão. Para recuperar esses conjuntos de dados:
1. Faça logon no portal do Azure e abra o seu WORKDAY para o aplicativo de provisionamento de usuário do AD/Azure AD. 
1. Na folha de provisionamento, edite os mapeamentos e abra a lista de atributos do workday na seção avançado. 
1. Adicione as seguintes definições de atributos e marque-as como "obrigatória". Esses atributos não serão mapeados para nenhum atributo no AD ou no Azure AD. Eles servem apenas como sinais para o conector para recuperar o centro de custo, a hierarquia do centro de custo e as informações do grupo de pagamento. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Nome do atributo | Expressão de API XPATH |
     >|---|---|
     >| CostCenterHierarchyFlag  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER_HIERARCHY ']/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER ']/WD: Organization_Data/WD: Organization_Code/Text () |
     >| PayGroupFlag  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' PAY_GROUP ']/WD: Organization_Data/WD: Organization_Reference_ID/Text () |

1. Depois que o centro de custo e o conjunto de dados do grupo de pagamento estiverem disponíveis na resposta *Get_Workers* , você poderá usar os valores XPath abaixo para recuperar o nome do centro de custo, o código do centro de custo e o grupo de pagamento. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Nome do atributo | Expressão de API XPATH |
     >|---|---|
     >| CostCenterName  | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' centro de custo ']/WD: Organization_Name/Text () |
     >| CostCenterCode | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' centro de custo ']/WD: Organization_Code/Text () |
     >| PayGroup | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' grupo de pagamento ']/WD: Organization_Name/Text () |

**Exemplo 2**

Digamos que você deseja recuperar as certificações associadas a um usuário. Essas informações estão disponíveis como parte do conjunto de *dados de qualificação* . Para obter esse conjunto de dados como parte da resposta *Get_Workers* , use o XPath a seguir: 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**Exemplo 3**

Digamos que você deseja recuperar grupos de *provisionamento* atribuídos a um trabalho. Essas informações estão disponíveis como parte do conjunto de *dados de provisionamento de conta* . Para obter esse conjunto de dados como parte da resposta *Get_Workers* , use o XPath a seguir: 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="next-steps"></a>Próximas etapas

* [Saiba como configurar o WORKDAY para Active Directory provisionamento](../saas-apps/workday-inbound-tutorial.md)
* [Saiba como configurar o Write-back para o workday](../saas-apps/workday-writeback-tutorial.md)
* [Saiba mais sobre os Atributos do Workday com suporte para o provisionamento de entrada](workday-attribute-reference.md)

