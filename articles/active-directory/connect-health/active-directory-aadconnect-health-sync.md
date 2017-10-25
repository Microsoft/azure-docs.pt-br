---
title: "Uso do Azure AD Connect Health com sincronização | Microsoft Docs"
description: "Esta é a página Azure AD Connect Health que discutirá como monitorar a sincronização do Azure Connect AD."
services: active-directory
documentationcenter: 
author: karavar
manager: femila
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b06338cb62cc458e7b097db36023f0746d4e969
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>Monitorar a sincronização do Azure AD Connect com o Azure AD Connect Health
A documentação a seguir é específica do monitoramento da sincronização do Azure AD Connect (Sync) com o Azure AD Connect Health.  Para saber mais sobre como monitorar o AD FS com o Azure AD Connect Health, consulte [Usando o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md). Além disso, para obter informações sobre como monitorar os Serviços de Domínio do Active Directory com o Azure AD Connect Health, confira [Usar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md).

![Azure AD Connect Health para sincronização](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Alertas do Azure AD Connect Health para sincronização
A seção Alertas do Azure AD Connect Health para sincronização fornece a lista de alertas ativos. Cada alerta inclui informações relevantes, etapas de resolução e links para documentação relacionada. Ao selecionar um alerta ativo ou resolvido, você verá uma nova folha com informações adicionais, bem como etapas a que serem seguidas para resolver o alerta e links para documentação adicional. Você também pode exibir dados históricos sobre alertas que foram resolvidas no passado.

Ao selecionar um alerta, você receberá informações adicionais, bem como etapas que você pode seguir para resolver o alerta e links para documentação adicional.

![Erro de sincronização do Azure AD Connect](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Avaliação limitada de alertas
Se o Azure AD Connect não estiver usando a configuração padrão (por exemplo, se a Filtragem de Atributos for alterada da configuração padrão para uma configuração personalizada), o agente do Azure AD Connect Health não carregará os eventos de erro relacionados ao Azure AD Connect.

Isso limita a avaliação de alertas pelo serviço. Você verá uma faixa que indica essa condição no Portal do Azure em seu serviço.

![Azure AD Connect Health para sincronização](./media/active-directory-aadconnect-health-sync/banner.png)

Você pode alterar isso clicando em "Configurações" e permitindo que o agente do Azure AD Connect Health carregue todos os logs de erro.

![Azure AD Connect Health para sincronização](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>Detalhes da sincronização
Os administradores frequentemente querem saber sobre o tempo necessário para sincronizar alterações ao Azure AD e a quantidade de alterações que ocorrem. Esse recurso fornece uma maneira fácil de visualizá-lo usando os gráficos abaixo:   

* Latência de operações de sincronização
* Tendência de alteração de objeto

### <a name="sync-latency"></a>Latência de Sincronização
Esse recurso oferece uma tendência gráfica de latência das operações de sincronização (importação, exportação etc.) para conectores.  Isso oferece uma maneira rápida e fácil de entender não apenas a latência de suas operações (maior se você tiver um grande conjunto de alterações), mas também uma maneira de detectar anomalias na latência que possam exigir mais investigação.

![Latência de Sincronização](./media/active-directory-aadconnect-health-sync/synclatency02.png)

Por padrão, somente a latência da operação 'Exportar' para o conector do AD do Azure é mostrada.  Para ver mais operações no conector ou para exibir as operações em outros conectores, clique com o botão direito do mouse no gráfico, selecione Editar gráfico ou clique no botão "Editar gráfico da latência" e escolha a operação e conectores específicos.

### <a name="sync-object-changes"></a>Alterações de objeto de sincronização
Esse recurso oferece uma tendência gráfica do número de alterações que estão sendo avaliadas e exportadas para o AD do Azure.  Hoje, é difícil tentar coletar essas informações de logs de sincronização.  O gráfico oferece não só uma maneira mais simples de monitorar o número de alterações que estão ocorrendo em seu ambiente, como também uma exibição visual das falhas ocorridas.

![Latência de Sincronização](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>Relatório de erros de sincronização no nível do objeto (Visualização)
Esse recurso fornece um relatório sobre os erros de sincronização que podem ocorrer quando os dados de identidade são sincronizados entre o Windows Server AD e o Azure AD usando o Azure AD Connect.

* O relatório abrange erros registrados pelo cliente de sincronização (Azure AD Connect versão 1.1.281.0 ou superior)
* Ele inclui os erros que ocorreram na última operação de sincronização no mecanismo de sincronização. ("Exportar" no Azure AD Connector.)
* O agente do Azure AD Connect Health para sincronização deve ter conectividade de saída para os pontos de extremidade necessários para o relatório incluir os dados mais recentes.
* O relatório é **atualizado a cada 30 minutos** usando os dados carregados pelo agente do Azure AD Connect Health para sincronização. Ele fornece os principais recursos abaixo

  * Categorização de erros
  * Lista de objetos com erro por categoria
  * Todos os dados sobre os erros em um só lugar
  * Comparação lado a lado de objetos com erro devido a um conflito
  * Baixar o relatório de erros como um CVS (em breve)

### <a name="categorization-of-errors"></a>Categorização de erros
O relatório categoriza os erros de sincronização existentes nas seguintes categorias:

| Categoria | Descrição |
| --- | --- |
| Duplicar atributo |Erros quando o Azure AD Connect tenta criar ou atualizar objetos com valores duplicados de um ou mais atributos no Azure AD que deve ser exclusivo em um Locatário, como proxyAddresses, UserPrincipalName. |
| Incompatibilidade de dados |Erros quando a correspondência flexível não corresponde a objetos que resultam em erros de sincronização. |
| Falha na validação de dados |Erros devido a dados inválidos, como caracteres sem suporte em atributos essenciais como UserPrincipalName, formate os erros que falharam na validação antes de serem gravados no Azure AD. |
| Atributo grande |Erros quando um ou mais atributos são maiores do que o tamanho, o comprimento ou a contagem permitidos. |
| Outros |Todos os outros erros que não se encaixam nas categorias acima. Com base nos comentários, essa categoria será dividida em subcategorias. |

![Resumo do relatório de erro de sincronização](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![Categorias de relatório de erro de sincronização](./media/active-directory-aadconnect-health-sync/errorreport02.png)

### <a name="list-of-objects-with-error-per-category"></a>Lista de objetos com erro por categoria
O detalhamento de cada categoria fornecerá a lista de objetos que têm o erro nessa categoria.
![Lista de relatórios de erros de sincronização](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>Detalhes do Erro
Os dados a seguir estão disponíveis no modo de exibição detalhado para cada erro

* Identificadores para o *Objeto do AD* envolvidos
* Identificadores para o *Objeto do Azure AD* envolvidos (conforme aplicável)
* Descrição do erro e como corrigir
* Artigos relacionados

![Detalhes do relatório de erro de sincronização](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>Baixar o relatório de erros como um CSV
Ao selecionar o botão "Exportar", você pode baixar um arquivo CSV com todos os detalhes sobre todos os erros.

## <a name="related-links"></a>Links relacionados
* [Solucionando problemas de erros durante a sincronização](../connect/active-directory-aadconnect-troubleshoot-sync-errors.md)
* [Duplicar a resiliência do atributo](../connect/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operações de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Usar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [Perguntas frequentes do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Histórico de versão do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)