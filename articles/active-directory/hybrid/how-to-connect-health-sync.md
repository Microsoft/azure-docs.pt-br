---
title: Uso do Azure AD Connect Health com sincronização | Microsoft Docs
description: Esta é a página Azure AD Connect Health que discutirá como monitorar a sincronização do Azure Connect AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: e803614a02e76d179579a2258abd563b5c58e63a
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016976"
---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>Monitorar a sincronização do Azure AD Connect com o Azure AD Connect Health
A documentação a seguir é específica do monitoramento da sincronização do Azure AD Connect (Sync) com o Azure AD Connect Health.  Para saber mais sobre como monitorar o AD FS com o Azure AD Connect Health, consulte [Usando o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md). Além disso, para obter informações sobre como monitorar Active Directory Domain Services com Azure AD Connect Health consulte [usando Azure ad Connect Health com AD DS](how-to-connect-health-adds.md).

![Captura de tela da página Azure AD Connect Health para sincronização.](./media/how-to-connect-health-sync/syncsnapshot.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Alertas do Azure AD Connect Health para sincronização
A seção Alertas do Azure AD Connect Health para sincronização fornece a lista de alertas ativos. Cada alerta inclui informações relevantes, etapas de resolução e links para documentação relacionada. Ao selecionar um alerta ativo ou resolvido, você verá uma nova folha com informações adicionais, bem como etapas a que serem seguidas para resolver o alerta e links para documentação adicional. Você também pode exibir dados históricos sobre alertas que foram resolvidas no passado.

Ao selecionar um alerta, você receberá informações adicionais, bem como etapas que você pode seguir para resolver o alerta e links para documentação adicional.

![Erro de sincronização do Azure AD Connect](./media/how-to-connect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Avaliação limitada de alertas
Se o Azure AD Connect não estiver usando a configuração padrão (por exemplo, se a Filtragem de Atributos for alterada da configuração padrão para uma configuração personalizada), o agente do Azure AD Connect Health não carregará os eventos de erro relacionados ao Azure AD Connect.

Isso limita a avaliação de alertas pelo serviço. Você verá uma faixa que indica essa condição no Portal do Azure em seu serviço.

![Captura de tela da faixa de alerta que informa que a avaliação do alerta é limitada. Atualize suas configurações para habilitar todos os alertas.](./media/how-to-connect-health-sync/banner.png)

Você pode alterar isso clicando em "Configurações" e permitindo que o agente do Azure AD Connect Health carregue todos os logs de erro.

![Captura de tela da opção de configurações chamada out e a seção Settings com a opção Save e a opção ON chamada out.](./media/how-to-connect-health-sync/banner2.png)

## <a name="sync-insight"></a>Detalhes da sincronização
Os administradores frequentemente querem saber sobre o tempo necessário para sincronizar alterações ao Azure AD e a quantidade de alterações que ocorrem. Esse recurso fornece uma maneira fácil de visualizá-lo usando os grafos abaixo:   

* Latência de operações de sincronização
* Tendência de alteração de objeto

### <a name="sync-latency"></a>Latência de Sincronização
Esse recurso oferece uma tendência gráfica de latência das operações de sincronização (importação, exportação etc.) para conectores.  Isso oferece uma maneira rápida e fácil de entender não apenas a latência de suas operações (maior se você tiver um grande conjunto de alterações), mas também uma maneira de detectar anomalias na latência que possam exigir mais investigação.

![Captura de tela da latência do perfil de execução do grafo dos últimos 3 dias.](./media/how-to-connect-health-sync/synclatency02.png)

Por padrão, somente a latência da operação 'Exportar' para o conector do AD do Azure é mostrada.  Para ver mais operações no conector ou para exibir as operações em outros conectores, clique com o botão direito do mouse no gráfico, selecione Editar gráfico ou clique no botão "Editar gráfico da latência" e escolha a operação e conectores específicos.

### <a name="sync-object-changes"></a>Alterações de objeto de sincronização
Esse recurso oferece uma tendência gráfica do número de alterações que estão sendo avaliadas e exportadas para o AD do Azure.  Hoje, é difícil tentar coletar essas informações de logs de sincronização.  O gráfico oferece não só uma maneira mais simples de monitorar o número de alterações que estão ocorrendo em seu ambiente, como também uma exibição visual das falhas ocorridas.

![Captura de tela do grafo exportar estatísticas para o Azure AD dos últimos 3 dias.](./media/how-to-connect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report"></a>Relatório de erros de sincronização no nível do objeto
Esse recurso fornece um relatório sobre os erros de sincronização que podem ocorrer quando os dados de identidade são sincronizados entre o Windows Server AD e o Azure AD usando o Azure AD Connect.

* O relatório abrange erros registrados pelo cliente de sincronização (Azure AD Connect versão 1.1.281.0 ou superior)
* Ele inclui os erros que ocorreram na última operação de sincronização no mecanismo de sincronização. ("Exportar" no Azure AD Connector.)
* O agente do Azure AD Connect Health para sincronização deve ter conectividade de saída para os pontos de extremidade necessários para o relatório incluir os dados mais recentes.
* O relatório é **atualizado após a cada 30 minutos** usando os dados carregados pelo agente Azure ad Connect Health para sincronização. Ele fornece os seguintes recursos principais

  * Categorização de erros
  * Lista de objetos com erro por categoria
  * Todos os dados sobre os erros em um só lugar
  * Comparação lado a lado de objetos com erro devido a um conflito
  * Baixar o relatório de erros como um CSV

### <a name="categorization-of-errors"></a>Categorização de erros
O relatório categoriza os erros de sincronização existentes nas seguintes categorias:

| Categoria | Descrição |
| --- | --- |
| Duplicar atributo |Erros quando o Azure AD Connect tenta criar ou atualizar objetos com valores duplicados de um ou mais atributos no Azure AD que deve ser exclusivo em um Locatário, como proxyAddresses, UserPrincipalName. |
| Incompatibilidade de dados |Erros quando a correspondência flexível não corresponde a objetos que resultam em erros de sincronização. |
| Falha na validação de dados |Erros devido a dados inválidos, como caracteres sem suporte em atributos essenciais como UserPrincipalName, formate os erros que falharam na validação antes de serem gravados no Azure AD. |
| Alteração de Domínio Federado | Erros quando as contas usam um domínio federado diferente. |
| Atributo grande |Erros quando um ou mais atributos são maiores do que o tamanho, o comprimento ou a contagem permitidos. |
| Outro |Todos os outros erros que não se encaixam nas categorias acima. Com base nos comentários, essa categoria será dividida em subcategorias. |

![Resumo do relatório de erro de sincronização](./media/how-to-connect-health-sync/errorreport01.png)
![Categorias de relatório de erro de sincronização](./media/how-to-connect-health-sync/SyncErrorByTypes.PNG)

### <a name="list-of-objects-with-error-per-category"></a>Lista de objetos com erro por categoria
O detalhamento de cada categoria fornecerá a lista de objetos que têm o erro nessa categoria.
![Lista de relatórios de erros de sincronização](./media/how-to-connect-health-sync/errorreport03.png)

### <a name="error-details"></a>Detalhes do Erro
Os dados a seguir estão disponíveis no modo de exibição detalhado para cada erro

* Atributo conflitante realçado
* Identificadores para o *Objeto do AD* envolvidos
* Identificadores para o *Objeto do Azure AD* envolvidos (conforme aplicável)
* Descrição do erro e como corrigir

![Detalhes do relatório de erro de sincronização](./media/how-to-connect-health-sync/duplicateAttributeSyncError.png)

### <a name="download-the-error-report-as-csv"></a>Baixar o relatório de erros como um CSV
Ao selecionar o botão "Exportar", você pode baixar um arquivo CSV com todos os detalhes sobre todos os erros.

### <a name="diagnose-and-remediate-sync-errors"></a>Diagnosticar e corrigir erros de sincronização 
Para o cenário de erro específico da sincronização de atributos duplicados que envolve a atualização de Source Anchor do usuário, você pode corrigi-los diretamente do portal. Leia mais sobre [Diagnosticar e corrigir erros de sincronização de atributos duplicados](how-to-connect-health-diagnose-sync-errors.md)

## <a name="related-links"></a>Links relacionados
* [Solucionando erros durante a sincronização](tshoot-connect-sync-errors.md)
* [Duplicar resiliência de atributo](how-to-connect-syncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalação do Agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operações de Azure AD Connect Health](how-to-connect-health-operations.md)
* [Usando o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md)
* [Usar o Azure AD Connect Health com o AD DS](how-to-connect-health-adds.md)
* [Perguntas frequentes do Azure AD Connect Health](reference-connect-health-faq.md)
* [Histórico de versão do Azure AD Connect Health](reference-connect-health-version-history.md)