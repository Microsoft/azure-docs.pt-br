---
title: "Relatórios sobre o provisionamento automático de conta de usuário do Azure Active Directory para aplicativos SaaS | Microsoft Docs"
description: "Saiba como verificar o status dos trabalhos de provisionamento de automático de conta de usuário e como solucionar problemas com o provisionamento de usuários individuais."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: asmalser-msft
ms.openlocfilehash: 86b9a3d93745045904c6038583b9bc6ebac5667e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Tutorial: Relatórios sobre o provisionamento automático de conta de usuário


O Azure Active Directory inclui um [serviço de provisionamento de conta de usuário](active-directory-saas-app-provisioning.md) que ajuda a automatizar o provisionamento e o desprovisionamento de contas de usuário em aplicativos SaaS e outros sistemas para fins de gerenciamento de ciclo de vida da identidade de ponta a ponta. O Azure AD dá suporte a conectores de provisionamento de usuário pré-integrados para todos os aplicativos e sistemas na seção "Em destaque" da [Galeria de aplicativos do Azure AD](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Este artigo descreve como verificar o status de trabalhos de provisionamento depois de eles terem sido configurados e como solucionar problemas com o provisionamento de usuários e grupos individuais.

## <a name="overview"></a>Visão geral

Conectores de provisionamento são basicamente configurados e definidos usando o [Portal de gerenciamento do Azure](https://portal.azure.com), seguindo a [documentação fornecida](active-directory-saas-tutorial-list.md) do aplicativo em que o provisionamento de conta de usuário é desejado. Depois de estarem configurados e em execução, os trabalhos de provisionamento para um aplicativo podem ser informados usando um dos dois métodos:

* **Portal de gerenciamento do Azure** – este artigo descreve principalmente a recuperação de informações de relatório do [Portal de gerenciamento do Azure](https://portal.azure.com), que fornece tanto um relatório resumido do provisionamento quanto logs de auditoria de provisionamento detalhados para um determinado aplicativo.

* **API de auditoria** – o Azure Active Directory também fornece uma API de auditoria que permite a recuperação programática dos logs de auditoria de provisionamento detalhados. Consulte a [referência da API de auditoria do Azure Active Directory](active-directory-reporting-api-audit-reference.md) para a documentação específica para usar essa API. Embora este artigo não cubra especificamente como usar a API, ele detalha os tipos de eventos de provisionamento que são registrados no log de auditoria.

### <a name="definitions"></a>Definições

Este artigo usa os seguintes termos, definidos a seguir:

* **Sistema de origem** – o repositório de usuários do qual o serviço de provisionamento do Azure AD faz a sincronização. O Azure Active Directory é o sistema de origem para a maioria dos conectores de provisionamento previamente integrados, no entanto, há algumas exceções (exemplo: Sincronização de Entrada do Workday).

* **Sistema de destino** – o repositório de usuários ao qual o serviço de provisionamento do Azure AD faz a sincronização. Geralmente é um aplicativo de SaaS (exemplos: Salesforce, ServiceNow, Google Apps, Dropbox for Business), mas, em alguns casos, pode ser um sistema local, como o Active Directory (exemplo: sincronização de entrada do Workday com o Active Directory).


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>Como obter relatórios de provisionamento no portal de gerenciamento do Azure

Para obter informações de relatório de provisionamento para um determinado aplicativo, primeiro inicie o [portal de gerenciamento do Azure](https://portal.azure.com) e acesse o Aplicativo Empresarial para o qual o provisionamento está configurado. Por exemplo, se você estiver provisionando usuários para o LinkedIn Elevate, o caminho de navegação para os detalhes do aplicativo será:

**Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos > LinkedIn Elevate**

Aqui, você pode acessar tanto o relatório de resumo de provisionamento quanto os logs de auditoria de provisionamento, ambos descritos abaixo.


### <a name="provisioning-summary-report"></a>Relatório de resumo de provisionamento

O relatório de resumo de provisionamento fica visível na guia **Provisionamento** para determinado aplicativo. Ele está localizado na seção de Detalhes de Sincronização sob **Configurações** e fornece as seguintes informações:

* O número total de usuários e grupos que foram sincronizados e estão atualmente no escopo para provisionamento entre o sistema de origem e o sistema de destino.

* A última vez em que a sincronização foi executada. As sincronizações normalmente ocorrem a cada 20 a 40 minutos, após uma sincronização ser concluída.

* Se uma sincronização completa inicial foi ou não concluída.

* Se o processo de provisionamento foi colocado em quarentena ou não e qual o motivo do status de quarentena (por exemplo, falha na comunicação com o sistema de destino devido a credenciais de administrador inválidas)

O relatório de resumo de provisionamento deve ser o primeiro local em que os administradores procuram para verificar a integridade operacional do trabalho de provisionamento.

 ![Relatório resumido](./media/active-directory-saas-provisioning-reporting/summary_report.PNG)

### <a name="provisioning-audit-logs"></a>Logs de auditoria de provisionamento
Todas as atividades realizadas pelo serviço de provisionamento são registradas nos logs de auditoria do Azure AD, que podem ser exibidos na guia **Logs de auditoria** na categoria **Provisionamento da Conta**. Alguns dos tipos de eventos de atividade incluídos no log são:

* **Eventos de importação** – um evento de "importação" é registrado sempre que o serviço de provisionamento do Azure AD recupera informações sobre um usuário individual ou um grupo de um sistema de origem ou de destino. Durante a sincronização, os usuários são recuperados do sistema de origem primeiro, sendo os resultados registrados como eventos de "importação". As IDs correspondentes dos usuários recuperados então são consultadas em comparação ao sistema de destino para verificar se elas existem, sendo os resultados também registrados como eventos de "importação". Esses eventos registram todos os atributos de usuário mapeados e os valores que foram vistos pelo serviço de provisionamento do Azure AD no momento do evento. 

* **Eventos de regra de sincronização** – esses eventos relatam os resultados das regras de mapeamento de atributo e quaisquer filtros de escopo configurados depois de os dados de usuário terem sido importados e avaliados dos sistemas de origem e destino. Por exemplo, se um usuário em um sistema de origem for considerado no escopo para provisionamento e considerado como inexistente no sistema de destino, esse evento registrará que o usuário será provisionado no sistema de destino. 

* **Eventos de exportação** – um evento de "exportação" é registrado sempre que o serviço de provisionamento do Azure AD grava um objeto de grupo ou conta de usuário em um sistema de destino. Esses eventos registram todos os atributos de usuário e os valores que foram gravados pelo serviço de provisionamento do Azure AD no momento do evento. Se tiver ocorrido um erro ao gravar o objeto de grupo ou conta de usuário no sistema de destino, ele será exibido aqui.

* **Eventos de processamento de caução** – cauções de processo ocorrerem quando o serviço de provisionamento encontra uma falha ao tentar uma operação e começa a tentar realizar a operação novamente em um intervalo de retirada. Um evento "caução" é registrado sempre que uma operação de provisionamento é desativada.

Ao examinar eventos de provisionamento para um usuário individual, os eventos geralmente ocorrem nesta ordem:

1. Evento de importação: o usuário é recuperado do sistema de origem.

2. Evento de importação: sistema de destino é consultado para verificar a existência do usuário recuperado.

3. Evento de regra de sincronização: os dados do usuário de sistemas de origem e destino são avaliados com relação aos filtros de escopo e às regras de mapeamento do atributo configurado para determinar qual ação, caso haja, deve ser executada.

4. Evento de exportação: se o evento de regra de sincronização tiver determinado que uma ação deve ser executada (por exemplo, Adicionar, Atualizar, Excluir), os resultados da ação serão registrados em um evento de exportação.

![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-provisioning-reporting/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Como pesquisar eventos de provisionamento para um usuário específico

O caso de uso mais comum para os logs de auditoria de provisionamento é verificar o status de provisionamento de uma conta de usuário individual. Para pesquisar os últimos eventos de provisionamento para um usuário específico:

1. Acesse a seção **Logs de auditoria**.

2. No menu **Categoria**, selecione **Provisionamento de Conta**.

3. No menu **Intervalo de Datas**, selecione o intervalo de datas que você deseja pesquisar.

4. Na barra **Pesquisar**, insira a ID de usuário do usuário que você deseja pesquisar. O formato do valor de ID deve corresponder àquele que você selecionou como a ID primária correspondente na configuração de mapeamento de atributo (por exemplo, userPrincipalName ou número de ID do funcionário). O valor de ID necessário estará visível na coluna de Destinos.

5. Pressione Enter para pesquisar. Os eventos de provisionamento mais recentes serão retornados primeiro.

6. Se forem retornados eventos, observe os tipos de atividade e se foram bem-sucedidas ou falharam. Se nenhum resultado for retornado, isso significará o usuário não existe ou não ainda foi detectado pelo processo de provisionamento, caso uma sincronização completa ainda não tenha sido concluída.

7. Clique em eventos individuais para exibir detalhes estendidos, incluindo todas as propriedades de usuário que foram recuperadas, avaliadas ou gravadas como parte do evento.


### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Dicas para exibir os logs de auditoria de provisionamento

Para melhor legibilidade no portal do Azure, selecione o botão **Colunas** e escolha estas colunas:

* **Data** – mostra a data em que o evento ocorreu.
* **Destinos** – mostra a ID de usuário e o nome do aplicativo que são as entidades do evento.
* **Atividade** – o tipo de atividade, como descrito anteriormente.
* **Status** – se o evento foi bem-sucedido ou não.
* **Motivo do status** – um resumo do que aconteceu no evento de provisionamento.


## <a name="troubleshooting"></a>Solucionar problemas

O relatório de resumo de provisionamento e os logs de auditoria desempenham um papel importante ao ajudarem os administradores a solucionar vários problemas de provisionamento de conta de usuário.

Para obter diretrizes baseada em cenário sobre como solucionar problemas de provisionamento automático de usuário, consulte [Problemas ao configurar e provisionar usuários para um aplicativo](active-directory-application-provisioning-content-map.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
