---
title: Comparar linhas de base com o monitoramento de integridade de arquivo na central de segurança do Azure | Microsoft Docs
description: Saiba como comparar linhas de base com o monitoramento de integridade de arquivo na central de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: v-mohabe
ms.openlocfilehash: afc03baa71f17deb0b923f483fde214a86c5e9b4
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296478"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Comparar linhas de base usando o monitoramento de integridade de arquivo (FIM)

O monitoramento de integridade de arquivo (FIM) informa quando ocorrem alterações em áreas confidenciais em seus recursos, para que você possa investigar e resolver atividades não autorizadas. O FIM monitora arquivos do Windows, registros do Windows e arquivos do Linux.

Este tópico explica como habilitar o FIM nos arquivos e registros. Para obter mais informações sobre o FIM, consulte [monitoramento de integridade de arquivo na central de segurança do Azure](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Por que usar o FIM?

O sistema operacional, os aplicativos e as configurações associadas controlam o comportamento e o estado de segurança de seus recursos. Portanto, os invasores direcionam os arquivos que controlam seus recursos, a fim de overtake o sistema operacional de um recurso e/ou executar atividades sem serem detectados.

Na verdade, muitos padrões de conformidade regulatória, como PCI-DSS & ISO 17799, exigem a implementação de controles FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Habilitar verificações de registro recursivas internas

Os padrões do hive do registro do FIM fornecem uma maneira conveniente de monitorar alterações recursivas em áreas de segurança comuns.  Por exemplo, um adversário pode configurar um script para ser executado no contexto de LOCAL_SYSTEM Configurando uma execução na inicialização ou no desligamento.  Para monitorar as alterações desse tipo, habilite a verificação interna.  

![Registro](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Verificações recursivas se aplicam somente a hives de segurança recomendados e não a caminhos de registro personalizados.  

## <a name="adding-a-custom-registry-check"></a>Adicionando uma verificação personalizada do registro

As linhas de base do FIM começam identificando as características de um estado válido conhecido para o sistema operacional e o aplicativo de suporte.  Para este exemplo, nos concentraremos nas configurações de política de senha para o Windows Server 2008 e superior.


|Nome da política                 | Configuração do registro|
|---------------------------------------|-------------|
|Controlador de domínio: Recusar alterações de senha da conta do computador| MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RefusePasswordChange|
|Membro do domínio: Criptografar digitalmente ou assinar dados de canal seguro (sempre)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireSignOrSeal|
|Membro do domínio: Criptografar digitalmente os dados do canal seguro (quando possível)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\SealSecureChannel|
|Membro do domínio: Assinar digitalmente os dados do canal seguro (quando possível)|MACHINE\System\CurrentControlSet\Services   \Netlogon\Parameters\SignSecureChannel|
|Membro do domínio: Desabilitar alterações de senha da conta do computador|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|Membro do domínio: Duração máxima da senha da conta da máquina|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\MaximumPasswordAge|
|Membro do domínio: Exigir chave de sessão forte (Windows 2000 ou posterior)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireStrongKey|
|Segurança de rede: Restringir NTLM:  Autenticação NTLM neste domínio|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RestrictNTLMInDomain|
|Segurança de rede: Restringir NTLM: Adicionar exceções de servidor neste domínio|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DCAllowedNTLMServers|
|Segurança de rede: Restringir NTLM: Auditar a autenticação NTLM neste domínio|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Para saber mais sobre as configurações de registro com suporte em várias versões de sistema operacional, consulte a [planilha de referência de configurações de política de grupo](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250).

*Para configurar o FIM para monitorar linhas de base de registro:*

1. Na janela **adicionar registro do Windows para controle de alterações** , na caixa de texto **chave do registro do Windows** , insira a chave do registro.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Habilitar FIM em um registro](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Controlando alterações em arquivos do Windows

1. Na janela **Adicionar arquivo do Windows para controle de alterações** , na caixa de texto **Inserir caminho** , insira a pasta que contém os arquivos que você deseja acompanhar. No exemplo na figura a seguir, o **aplicativo Web contoso** reside no D:\ unidade dentro da estrutura de pastas **ContosWebApp** .  
1. Crie uma entrada de arquivo do Windows personalizada fornecendo um nome para a classe de configuração, habilitando a recursão e especificando a pasta superior com um sufixo curinga (*).

    ![Habilitar FIM em um arquivo](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Recuperando dados de alteração

Os dados de monitoramento de integridade de arquivo residem no conjunto de tabelas Log Analytics/ConfigurationChange do Azure.  

 1. Defina um intervalo de tempo para recuperar um resumo das alterações por recurso.
No exemplo a seguir, recuperamos todas as alterações nos últimos quatorze dias nas categorias de registro e de arquivos:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Para exibir detalhes das alterações no registro:

    1. Remover **arquivos** da cláusula **Where** , 
    1. Remova a linha de resumo e substitua-a por uma cláusula de ordenação:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Os relatórios podem ser exportados para CSV para arquivamento e/ou canal para um relatório Power BI.  

![Dados do FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)