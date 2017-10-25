---
title: "Relatórios de atividade de entrada no portal do Azure Active Directory | Microsoft Docs"
description: "Introdução aos relatórios de atividades de entrada no portal do Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: b9e61950654ba427b09dd608d354589a0804aaa5
ms.contentlocale: pt-br
ms.lasthandoff: 07/20/2017

---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividades de entrada no portal do Azure Active Directory

Com os relatórios do Azure Active Directory (Azure AD) no [portal do Azure](https://portal.azure.com) você obtém todas as informações de que precisa para determinar como seu ambiente está se comportando.

A arquitetura de relatório no Azure Active Directory consiste nos seguintes componentes:

- **Atividade** 
    - **Atividades de entrada** – informações sobre o uso de aplicativos gerenciados e de atividades de entrada do usuário
    - **Logs de auditoria** – informações de atividade do sistema sobre o gerenciamento de usuários e de grupos, sobre os aplicativos gerenciados e sobre as atividades de diretório.
- **Segurança** 
    - **Entradas arriscadas** - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. Para obter mais detalhes, veja Entradas de risco.
    - **Usuários sinalizados para riscos** - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida. Para obter mais detalhes, consulte Usuários sinalizados para risco.

Este tópico fornece uma visão geral das atividades de entrada.

## <a name="pre-requisite"></a>Pré-requisito

### <a name="who-can-access-the-data"></a>Quem pode acessar os dados?
* Usuários na função de Administrador de segurança ou Leitor de segurança
* Administradores globais
* Qualquer usuário (não administradores) pode acessar suas próprias entradas 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Qual licença do Azure AD você precisa para acessar a atividade de entrada?
* Seu locatário deve ter uma licença do Azure AD Premium associada a ele para ver o relatório de atividade de entrada


## <a name="signs-in-activities"></a>Atividades de entrada

Com as informações fornecidas pelo relatório de entrada de usuário, você encontra respostas para perguntas como:

* O que é o padrão de entrada de um usuário?
* Quantos usuários entraram em uma semana?
* Qual é o status dessas entradas?

O seu primeiro ponto de entrada para todos os dados de atividades de entrada é **Entradas** na seção Atividade do **Azure Active**.


![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/61.png "Atividade de entrada")


Um log de auditoria tem um modo de exibição de lista padrão que mostra:

- o usuário relacionado
- o aplicativo no qual o usuário entrou
- o status de entrada
- a hora de entrada

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/41.png "Atividade de entrada")

Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/19.png "Atividade de entrada")

Isso permite a você exibir campos adicionais ou remover campos que já estão exibidos.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/42.png "Atividade de entrada")

Ao clicar em um item na exibição de lista, você obterá mais detalhes sobre ele.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/43.png "Atividade de entrada")


## <a name="filtering-sign-in-activities"></a>Filtragem de atividades de entrada

Para restringir os dados relatados a um nível que funciona para você, filtre os dados de entradas usando os seguintes campos:

- Intervalo de tempo
- Usuário
- Aplicativo
- Cliente
- Status de entrada

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/44.png "Atividade de entrada")


O filtro **intervalo de tempo** permite a você definir um período de tempo para os dados retornados.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 24 horas
- Personalizado

Quando você seleciona um período de tempo personalizado, pode configurar uma hora de início e uma hora de término.

O filtro **usuário** permite que você especifique o nome ou o UPN (nome principal do usuário) do usuário desejado.

O filtro **aplicativo** permite que você especifique o nome do aplicativo desejado.

O filtro **cliente** permite que você especifique informações sobre o dispositivo desejado.

O filtro **status de entrada** permite que você selecione um dos filtros abaixo:

- Todos
- Sucesso
- Failure


## <a name="sign-in-activities-shortcuts"></a>Atalhos de atividades de entrada

Além do Azure Active Directory, o portal do Azure fornece dois pontos de entrada adicionais para dados de atividade de entrada:

- Usuários e grupos
- Aplicativos empresariais


### <a name="users-and-groups-sign-ins-activities"></a>Atividades de entrada de usuários e grupos

Com as informações fornecidas pelo relatório de entrada de usuário, você encontra respostas para perguntas como:

- O que é o padrão de entrada de um usuário?
- Quantos usuários entraram em uma semana?
- Qual é o status dessas entradas?



O ponto de entrada para esses dados é o gráfico de entrada do usuário na seção **Visão geral** em **Usuários e grupos**.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/45.png "Atividade de entrada")

O gráfico de entrada do usuário mostra agregações semanais de entradas para todos os usuários em um determinado período de tempo. O padrão para o período é de 30 dias.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/46.png "Atividade de entrada")

Quando você clica em um dia no gráfico de entradas, obtém uma lista detalhada das atividades de entrada do dia.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/41.png "Atividade de entrada")

Cada linha na lista de atividades de entrada oferece as informações detalhadas sobre a entrada selecionada, como:

* Quem entrou?
* Qual era o UPN relacionado?
* Qual aplicativo era o destino da entrada?
* Qual é o endereço IP da entrada?
* Qual era o status da entrada?

A opção **entradas** fornece uma visão geral completa de todas as entradas de usuário.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/51.png "Atividade de entrada")



## <a name="usage-of-managed-applications"></a>Uso de aplicativos gerenciados

Com uma exibição centrada no aplicativo de seus dados de entrada, você pode responder a perguntas como:

* Quem está usando meus aplicativos?
* Quais são os três principais aplicativos em sua organização?
* Recentemente, eu implantei um aplicativo. Como ele está se saindo?

Seu ponto de entrada para esses dados é composto pelos três principais aplicativos em sua organização no relatório dos 30 últimos dias, presente na seção **Visão geral**, em **Aplicativos empresariais**.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/64.png "Atividade de entrada")

As agregações semanais ao gráfico de uso do aplicativo de entradas para seus três principais aplicativos em um determinado período de tempo. O padrão para o período é de 30 dias.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/47.png "Atividade de entrada")

Se desejar, você pode definir o foco em um aplicativo específico.


![Relatórios](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Relatórios")

Quando você clica em um dia no gráfico de uso do aplicativo, pode obter uma lista detalhada das atividades de entrada.


![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/48.png "Atividade de entrada")


A opção **Entradas** oferece uma visão geral completa de todos os eventos de entrada para seus aplicativos.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins/49.png "Atividade de entrada")



## <a name="next-steps"></a>Próximas etapas

Se você quiser saber mais sobre os códigos de erro da atividade de entrada, confira o [Códigos de erro do relatório de atividade de entrada no portal do Azure Active Directory](active-directory-reporting-activity-sign-ins-errors.md).


