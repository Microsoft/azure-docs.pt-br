---
title: 'Sincronização de objeto único Azure AD Connect '
description: Saiba como sincronizar um objeto de Active Directory ao Azure AD para solução de problemas.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 373cebee4e7f95062791d8bc68bfee7d845e1465
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726033"
---
# <a name="azure-ad-connect-single-object-sync"></a>Sincronização de objeto único Azure AD Connect 

O Azure AD Connect ferramenta de sincronização de objeto único é um cmdlet do PowerShell que pode ser usado para sincronizar um objeto individual de Active Directory para Azure Active Directory. O relatório gerado pode ser usado para investigar e solucionar problemas de sincronização por objeto. 

> [!NOTE]
> A ferramenta dá suporte à sincronização de Active Directory para Azure Active Directory. Ele não oferece suporte à sincronização de Azure Active Directory para Active Directory. 
>
> A ferramenta dá suporte à sincronização de adicionar e atualizar uma modificação de objeto. Ele não dá suporte à sincronização de uma exclusão de modificação de objeto. 

## <a name="how-it-works"></a>Como ele funciona
A ferramenta de sincronização de objeto único requer um Active Directory nome distinto como entrada para localizar o conector de origem e a partição para importação. Ele exporta as alterações para Azure Active Directory. A ferramenta gera uma saída JSON semelhante ao tipo de recurso **provisioningObjectSummary** . 

A ferramenta de sincronização de objeto único executa as seguintes etapas: 

 1. Determine se o domínio (origem) do objeto (conector Active Directory e a partição) está no escopo de sincronização. 
 2. Determine se o domínio (destino) do objeto (conector Azure Active Directory e a partição) está no escopo de sincronização. 
 3. Determine se a unidade organizacional do objeto está no escopo de sincronização. 
 4. Determine se o objeto pode ser acessado usando credenciais de conta de conector. 
 5. Determine se o tipo do objeto está no escopo de sincronização. 
 6. Determine se o objeto está no escopo de sincronização se a filtragem de grupo estiver habilitada. 
 7. Importar objeto de Active Directory para Active Directory espaço do conector. 
 8. Importar objeto de Azure Active Directory para Azure Active Directory espaço do conector. 
 9. Sincronizar objeto do espaço do conector de Active Directory. 
 10. Exportar objeto do espaço do conector de Azure Active Directory para Azure Active Directory. 

Além da saída JSON, a ferramenta gera um relatório HTML que tem todos os detalhes da operação de sincronização. O relatório HTML está localizado em **C:\ProgramData\AADConnect\ADSyncObjectDiagnostics\ ADSyncSingleObjectSyncResult- <date> . htm**. Este relatório HTML pode ser compartilhado com a equipe de suporte para fazer mais soluções de problemas, se necessário. 

O relatório HTML tem o seguinte: 

|Tab|Descrição|
|-----|-----|
|Etapas|Descreve as etapas executadas para sincronizar um objeto. Cada etapa contém detalhes para a solução de problemas. As etapas de importação, sincronização e exportação contêm informações adicionais sobre o atributo, como nome, com valores múltiplos, tipo, valor, agregação de valor, exclusão de valor, operação, regra de sincronização, tipo de mapeamento e fonte de dados.| 
|Solução de problemas & recomendação|Fornece o código de erro e o motivo. As informações de erro estarão disponíveis somente se ocorrer uma falha.| 
|Propriedades Modificadas|Mostra o valor antigo e o novo valor. Se não houver nenhum valor antigo ou se o novo valor for excluído, essa célula ficará em branco. Para atributos com valores de multivalor, ele mostra a contagem. O nome do atributo é um link para a guia etapas: exportar objeto do Azure Active Directory espaço do conector para Azure Active Directory: as informações de atributo que contêm detalhes adicionais do atributo, como nome, têm valores múltiplos, tipo, valor, valor agregado, exclusão de valor, operação, regra de sincronização, tipo de mapeamento e fonte de dados.| 
|Resumo|Fornece uma visão geral do que aconteceu e identificadores para o objeto nos sistemas de origem e de destino.| 

## <a name="prerequisites"></a>Pré-requisitos 

Para usar a ferramenta de sincronização de objeto único, você precisará usar a versão de 2021 de março do Azure AD Connect ou posterior. 

### <a name="run-the-single-object-sync-tool"></a>Executar a ferramenta de sincronização de objeto único 

Para executar a ferramenta de sincronização de objeto único, execute as seguintes etapas: 

 1. Abra uma nova sessão do Windows PowerShell no servidor do Azure AD Connect com a opção Executar como administrador. 

 2. Defina a [política de execução](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy) como RemoteSigned ou irrestrito. 

 3. Desabilite o Agendador de sincronização depois de verificar se nenhuma operação de sincronização está em execução. 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. Importar o módulo de diagnóstico AdSync 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. Invocar o cmdlet de sincronização de objeto único. 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. Habilite novamente o Agendador de sincronização. 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|Parâmetros de entrada de sincronização de objeto único|Descrição| 
|-----|----|
|DistinguishedName|Esse é um parâmetro de cadeia de caracteres necessário. </br></br>Esse é o nome distinto do objeto de Active Directory que precisa de sincronização e solução de problemas.| 
|StagingMode|Esse é um parâmetro de opção opcional.</br></br>Esse parâmetro pode ser usado para impedir a exportação das alterações para Azure Active Directory.</br></br>**Observação**: o cmdlet confirmará a operação de sincronização. </br></br>**Observação**: Azure ad Connect servidor de preparo não irá exportar as alterações para Azure Active Directory.|
|NoHtmlReport|Esse é um parâmetro de opção opcional.</br></br>Esse parâmetro pode ser usado para impedir a geração do relatório HTML. 

## <a name="single-object-sync-throttling"></a>Limitação de sincronização de objeto único 

A ferramenta de sincronização de objeto **único destina-** se a investigar e solucionar problemas de sincronização por objeto. **Não** se destina a substituir o ciclo de sincronização executado pelo Agendador. A importação de Azure Active Directory e exportação para Azure Active Directory estão sujeitas a limites de limitação. Tente novamente após 5 minutos, se você atingir o limite de limitação. 

## <a name="next-steps"></a>Próximas etapas
- [Solucionando problemas de sincronização de objeto](tshoot-connect-objectsync.md)
- [Solucionar problemas de objeto não sincronizando](tshoot-connect-object-not-syncing.md)
- [Solução de problemas de objetos e atributos de Azure AD Connect de ponta a ponta](https://docs.microsoft.com/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)
