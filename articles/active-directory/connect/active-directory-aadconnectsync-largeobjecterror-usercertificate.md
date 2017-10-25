---
title: "Sincronização do Azure AD Connect: Tratamento de erros LargeObject causados pelo atributo userCertificate | Microsoft Docs"
description: "Este tópico fornece as etapas de correção de erros LargeObject causados pelo atributo userCertificate."
services: active-directory
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 2a5418ff61e07793fceca5a8207c1c5aa18847b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Sincronização do Azure AD Connect: Tratamento de erros LargeObject causados pelo atributo userCertificate

O Azure AD impõe um limite máximo de **15** valores de certificado no atributo **userCertificate**. Se o Azure AD Connect exportar um objeto com mais de 15 valores para o Azure AD, o Azure AD retornará um erro **LargeObject** com a mensagem:

>*“O objeto provisionado é muito grande. Restrinja o número de valores de atributo nesse objeto. A operação será repetida no próximo ciclo de sincronização...”*

O erro LargeObject pode ser causado por outros atributos do AD. Para confirmar se ele realmente é causado pelo atributo userCertificate, você precisa verificar o objeto no AD local ou na [Pesquisa de Metaverso do Synchronization Service Manager](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Para obter a lista de objetos em seu locatário com erros LargeObject, use um dos seguintes métodos:

 * Caso seu locatário esteja habilitado para o Azure AD Connect Health para sincronização, consulte o [Relatório de Erros de Sincronização](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync#object-level-synchronization-error-report-preview) fornecido.
 
 * O email de notificação para erros de sincronização de diretório enviado ao final de cada ciclo de sincronização tem a lista de objetos com erros LargeObject. 
 * A [guia Operações do Synchronization Service Manager](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) exibirá a lista de objetos com erros LargeObject se você clicar na última operação Exportar para o Azure AD.
 
## <a name="mitigation-options"></a>Opções de mitigação
Até que o erro LargeObject seja resolvido, outras alterações de atributo no mesmo objeto não podem ser exportadas para o Azure AD. Para resolver o erro, considere as seguintes opções:

 * Atualize o Azure AD Connect para o build 1.1.524.0 ou posterior. No build 1.1.524.0 do Azure AD Connect, as regras de sincronização iniciais foram atualizadas para não exportar os atributos userCertificate e userSMIMECertificate se os atributos tiverem mais de 15 valores. Para obter detalhes sobre como atualizar o Azure AD Connect, consulte o artigo [Azure AD Connect: atualizar de uma versão anterior para a versão mais recente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

 * Implemente uma **regra de sincronização de saída** no Azure AD Connect que exporta um **valor nulo em vez dos valores reais de objetos com mais de 15 valores de certificado**. Essa opção será adequada se você não precisar que nenhum dos valores de certificado seja exportado para o Azure AD para objetos com mais de 15 valores. Para obter detalhes sobre como implementar essa regra de sincronização, consulte a próxima seção [Implementando uma regra de sincronização para limitar a exportação do atributo userCertificate](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Reduza o número de valores de certificado no objeto do AD local (15 ou menos) removendo os valores que não estão mais sendo usados por sua organização. Isso será adequado se a sobrecarga do atributo for causada por certificados expirados ou não utilizados. Use o [script do PowerShell disponível aqui](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) para ajudar a encontrar, fazer backup e excluir certificados expirados no AD local. Antes de excluir os certificados, é recomendável verificar com os administradores de Infraestrutura de Chave Pública de sua organização.

 * Configure o Azure AD Connect para excluir o atributo userCertificate de ser exportado para o Azure AD. Em geral, não recomendamos essa opção, pois o atributo pode ser usado pelo Microsoft Online Services para habilitar cenários específicos. Especificamente:
    * O atributo userCertificate no objeto User é usado por clientes do Exchange Online e do Outlook para criptografia e autenticação de mensagens. Para saber mais sobre esse recurso, consulte o artigo [S/MIME para criptografia e autenticação de mensagens](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * O atributo userCertificate no objeto Computer é usado pelo Azure AD para permitir que os dispositivos ingressados no domínio local do Windows 10 se conectem ao Azure AD. Para saber mais sobre esse recurso, consulte o artigo [Conectar dispositivos ingressados no domínio ao Azure AD para experiências com o Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementando a regra de sincronização para limitar a exportação do atributo userCertificate
Para resolver o erro LargeObject causado pelo atributo userCertificate, implemente uma regra de sincronização de saída no Azure AD Connect que exporta um **valor nulo em vez dos valores reais de objetos com mais de 15 valores de certificado**. Esta seção descreve as etapas necessárias para implementar a regra de sincronização em objetos **User**. As etapas podem ser adaptadas para objetos **Contact** e **Computer**.

> [!IMPORTANT]
> A exportação de um valor nulo remove os valores de certificado anteriormente exportados com êxito para o Azure AD.

As etapas podem ser resumidas como:

1. Desabilitar o agendador de sincronização e verificar se não há nenhuma sincronização em andamento.
3. Encontrar a regra de sincronização de saída existente para o atributo userCertificate.
4. Criar a regra de sincronização de saída necessária.
5. Verificar a nova regra de sincronização em um objeto existente com o erro LargeObject.
6. Aplicar a nova regra de sincronização aos objetos restantes com o erro LargeObject.
7. Verificar se não há alterações inesperadas com exportação pendente para o Azure AD.
8. Exportar as alterações para o Azure AD.
9. Habilitar o agendador de sincronização novamente.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Etapa 1. Desabilitar o agendador de sincronização e verificar se não há nenhuma sincronização em andamento
Garanta que não há nenhuma sincronização em andamento durante a implementação de uma nova regra de sincronização, a fim de evitar a exportação de alterações indesejadas para o Azure AD. Para desabilitar o agendador de sincronização interno:
1. Inicie uma sessão do PowerShell no servidor do Azure AD Connect.

2. Desabilite a sincronização agendada executando o cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> As etapas anteriores só são aplicáveis às versões mais novas (1.1.xxx.x) do Azure AD Connect com o agendador interno. Se você estiver usando versões mais antigas (1.0.xxx.x) do Azure AD Connect que usam o Agendador de Tarefas do Windows ou estiver usando seu próprio agendador personalizado (caso incomum) para disparar a sincronização periódica, precisará desabilitá-las de acordo.

3. Inicie o **Synchronization Service Manager** acessando INICIAR → Serviço de Sincronização.

4. Acesse a guia **Operações** e confirme se não há nenhuma operação cujo status é *“Em andamento”.*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Etapa 2. Encontrar a regra de sincronização de saída existente para o atributo userCertificate
Deve haver uma regra de sincronização existente que é habilitada e configurada para exportar o atributo userCertificate para objetos User para o Azure AD. Localize essa regra de sincronização para descobrir sua configuração de **precedência** e **filtro de escopo**:

1. Inicie o **Editor de Regras de Sincronização** acessando INICIAR → Editor de Regras de Sincronização.

2. Configure os filtros de pesquisa com os seguintes valores:

    | Atributo | Valor |
    | --- | --- |
    | Direção |**Saída** |
    | Tipo de Objeto de MV |**Pessoa** |
    | Conector |*nome de seu conector do Azure AD* |
    | Tipo de Objeto de Conector |**user** |
    | Atributos de MV |**userCertificate** |

3. Se você estiver usando regras de sincronização OOB (integradas) para o conector do Azure AD para exportar o atributo userCertificate de objetos User, deverá retornar à regra *“Saída para AAD – Exchange Online do Usuário”*.
4. Anote o valor de **precedência** dessa regra de sincronização.
5. Selecione a regra de sincronização e clique em **Editar**.
6. Na caixa de diálogo pop-up *“Editar Confirmação de Regra Reservada”*, clique em **Não**. (Não se preocupe; não vamos fazer nenhuma alteração nessa regra de sincronização).
7. Na tela de edição, selecione a guia **Filtro de escopo**.
8. Anote a configuração de filtro de escopo. Se você estiver usando a regra de sincronização OOB, deverá haver exatamente **um grupo de filtro de escopo que contém duas cláusulas**, incluindo:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Usuário |
    | cloudMastered | NOTEQUAL | True  |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Etapa 3. Criar a regra de sincronização de saída necessária
A nova regra de sincronização deve ter o mesmo **filtro de escopo** e a mesma **precedência mais alta** da regra de sincronização existente. Isso garante que a nova regra de sincronização se aplica ao mesmo conjunto de objetos da regra de sincronização existente e substitui a regra de sincronização existente do atributo userCertificate. Para criar a regra de sincronização:
1. No Editor de Regras de Sincronização, clique no botão **Adicionar nova regra**.
2. Na **guia Descrição**, forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | --- | --- | --- |
    | Nome | *Fornecer um nome* | Por exemplo, *“Saída para AAD – Substituição personalizado para userCertificate”* |
    | Descrição | *Fornecer uma descrição* | Por exemplo, *“Se o atributo userCertificate tiver mais de 15 valores, exportar NULL”.* |
    | Sistema Conectado | *Selecionar o Azure AD Connector* |
    | Tipo de Objeto do Sistema Conectado | **user** | |
    | Tipo de Objeto de Metaverso | **person** | |
    | Tipo de link | **Join** | |
    | Precedência | *Escolher um número entre 1 a 99* | O número escolhido não deve ser usado por nenhuma regra de sincronização existente e tem um valor mais baixo (e, portanto, uma precedência mais alta) que a regra de sincronização existente. |

3. Acesse a guia **Filtro de escopo** e implemente o mesmo filtro de escopo usado pela regra de sincronização existente.
4. Ignore a guia **Regras de junção**.
5. Acesse a guia **Transformações** para adicionar uma nova transformação usando a seguinte configuração:

    | Atributo | Valor |
    | --- | --- |
    | Tipo de Fluxo |**Expressão** |
    | Atributo de Destino |**userCertificate** |
    | Atributo de Origem |*Use a seguinte expressão*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Clique no botão **Adicionar** para criar a regra de sincronização.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Etapa 4. Verificar a nova regra de sincronização em um objeto existente com o erro LargeObject
Isso serve para verificar se a regra de sincronização criada está funcionando corretamente em um objeto existente do AD com o erro LargeObject antes de aplicá-la a outros objetos:
1. Acesse a guia **Operações** no Synchronization Service Manager.
2. Selecione a operação mais recente Exportar para o Azure AD e clique em um dos objetos com erros LargeObject.
3.  Na tela pop-up de Propriedades de Objeto do Espaço Conector, clique no botão **Visualizar**.
4. Na tela pop-up Visualização, selecione **Sincronização total** e clique em **Confirmar Visualização**.
5. Feche a tela Visualização e a tela Propriedades de Objeto do Espaço Conector.
6. Acesse a guia **Conectores** no Synchronization Service Manager.
7. Clique com o botão direito do mouse em **Azure AD** Connector e selecione **Executar...**
8. No pop-up Executar Conector, selecione a etapa **Exportar** e clique em **OK**.
9. Aguarde até que a etapa Exportar para o Azure AD seja concluída e confirme se não há mais nenhum erro LargeObject nesse objeto específico.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Etapa 5. Aplicar a nova regra de sincronização aos objetos restantes com o erro LargeObject
Depois que a regra de sincronização for adicionada, você precisará executar uma etapa de sincronização completa no AD Connector:
1. Acesse a guia **Conectores** no Synchronization Service Manager.
2. Clique com o botão direito do mouse em **AD** Connector e selecione **Executar...**
3. No pop-up Executar Conector, selecione a etapa **Sincronização Completa** e clique em **OK**.
4. Aguarde até que a etapa Sincronização Completa seja concluída.
5. Repita as etapas acima para os AD Connectors restantes caso você tenha mais de um AD Connector. Normalmente, vários conectores serão necessários se você tiver vários diretórios locais.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Etapa 6. Verificar se não há alterações inesperadas com exportação pendente para o Azure AD
1. Acesse a guia **Conectores** no Synchronization Service Manager.
2. Clique com o botão direito do mouse no **Azure AD** Connector e selecione **Pesquisar Espaço Conector**.
3. No pop-up Pesquisar Espaço Conector:
    1. Defina o Escopo como **Exportação Pendente**.
    2. Marque todas as 3 caixas de seleção, incluindo **Adicionar**, **Modificar** e **Excluir**.
    3. Clique no botão **Pesquisar** para retornar todos os objetos com alterações com exportação pendente para o Azure AD.
    4. Verifique se não há alterações inesperadas. Para examinar as alterações de determinado objeto, clique duas vezes nele.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Etapa 7. Exportar as alterações para o Azure AD
Para exportar as alterações para o Azure AD:
1. Acesse a guia **Conectores** no Synchronization Service Manager.
2. Clique com o botão direito do mouse em **Azure AD** Connector e selecione **Executar...**
4. No pop-up Executar Conector, selecione a etapa **Exportar** e clique em **OK**.
5. Aguarde até que a etapa Exportar para o Azure AD seja concluída e confirme se não há mais erros LargeObject.

### <a name="step-8-re-enable-sync-scheduler"></a>Etapa 8. Habilitar o agendador de sincronização novamente
Agora que o problema foi resolvido, habilite o agendador de sincronização interno novamente:
1. Inicie uma sessão do PowerShell.
2. Habilite a sincronização agendada novamente executando o cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> As etapas anteriores só são aplicáveis às versões mais novas (1.1.xxx.x) do Azure AD Connect com o agendador interno. Se você estiver usando versões mais antigas (1.0.xxx.x) do Azure AD Connect que usam o Agendador de Tarefas do Windows ou estiver usando seu próprio agendador personalizado (caso incomum) para disparar a sincronização periódica, precisará desabilitá-las de acordo.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

