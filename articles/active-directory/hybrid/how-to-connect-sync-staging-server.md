---
title: 'Sincronização do Azure AD Connect: considerações e tarefas operacionais | Microsoft Docs'
description: Este tópico descreve as tarefas operacionais para a sincronização do Azure Connect AD e como preparar para operar esse componente.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48584fa4042cf53fa1084e519dca0e64f530ca59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090118"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: servidor de preparo e recuperação de desastre
Com um servidor no modo de preparo, você pode fazer alterações na configuração e visualizar as alterações antes de tornar o servidor ativo. Ele também permite executar sincronização e importação totais para verificar se todas as alterações são esperadas antes de você fazê-las em seu ambiente de produção.

## <a name="staging-mode"></a>Modo de preparo
O modo de teste pode ser usado para vários cenários, incluindo:

* Alta disponibilidade.
* Teste e implantação de novas alterações de configuração.
* Introdução de um novo servidor e encerramento do antigo.

Durante a instalação, você pode selecionar o servidor em **modo de preparo**. Essa ação ativa o servidor de importação e sincronização, mas não executa qualquer exportação. Um servidor no modo de preparo não executa a sincronização de senha ou o write-back de senha, mesmo que esses recursos sejam selecionados durante a instalação. Quando você desabilita o modo de preparo, o servidor inicia a exportação e habilita a sincronização de senha e o write-back de senha.

> [!NOTE]
> Suponha que você tenha um Azure AD Connect com o recurso de Sincronização de Hash de Senha habilitado. Quando você habilita o modo de preparo, o servidor para a sincronização de alterações de senha do AD local. Quando você desabilita o modo de preparo, o servidor retoma a sincronização de alterações de senha de onde ela parou. Se o servidor estiver no modo de preparo por um longo período de tempo, poderá levar um tempo para que ele sincronize todas as alterações de senha que ocorreram durante esse período.
>
>

Você ainda pode forçar uma exportação usando o Synchronization Service Manager.

Um servidor no modo de preparo continua a receber alterações do Active Directory e do Azure AD e pode assumir rapidamente as responsabilidades de outro servidor em caso de falha. Se você fizer alterações de configuração no servidor primário, será sua responsabilidade fazer as mesmas alterações no servidor em modo de preparo.

Para aqueles com conhecimento das tecnologias mais antigas de sincronização, o modo de preparo é diferente, pois o servidor tem seu próprio banco de dados SQL. Essa arquitetura permite que o servidor de modo de preparo esteja localizado em um datacenter diferente.

### <a name="verify-the-configuration-of-a-server"></a>Verifique a configuração de um servidor
Para aplicar esse método, siga estas etapas:

1. [Preparar](#prepare)
2. [Configuration](#configuration)
3. [Importar e sincronizar](#import-and-synchronize)
4. [Verificar](#verify)
5. [Servidor ativo do comutador](#switch-active-server)

#### <a name="prepare"></a>Preparar
1. Instale o Azure AD Connect, selecione **modo de preparo** e desmarque **Iniciar sincronização** na última página do assistente de instalação. Esse modo permite que você execute o mecanismo de sincronização manualmente.
   ![Captura de tela mostra a página pronto para configurar na caixa de diálogo Azure AD Connect.](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Saia e entre e, no menu Iniciar, selecione **Serviço de Sincronização**.

#### <a name="configuration"></a>Configuração
Se você tiver feito alterações personalizadas no servidor primário e deseja comparar a configuração com o servidor de preparo, use o [documentador de configuração do Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importar e sincronizar
1. Selecione **Conectores** e selecione o primeiro Conector com o tipo **Serviços de Domínio Active Directory**. Clique em **Executar**, selecione **Importação completa** e **OK**. Siga estas etapas para todos os Conectores desse tipo.
2. Selecione o Conector com o tipo **Active Directory do Azure (Microsoft)**. Clique em **Executar**, selecione **Importação completa** e **OK**.
3. Verifique se a guia Conectores ainda está selecionada. Para cada Conector com tipo **Active Directory Domain Services**, clique em **Executar**, selecione **Sincronização Delta** e **OK**.
4. Selecione o Conector com o tipo **Active Directory do Azure (Microsoft)**. Clique em **Executar**, selecione **Sincronização Delta** e **OK**.

Você agora preparou a exportação das alterações para o Azure AD e AD local (se estiver usando implantação híbrida do Exchange). As próximas etapas permitem que você inspecione o que está prestes a ser alterado antes de realmente começar a exportação para os diretórios.

#### <a name="verify"></a>Verificar
1. Inicie um prompt de comando e vá para `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Execute: `csexport "Name of Connector" %temp%\export.xml /f:x` o nome do Conector pode ser encontrado no Serviço de Sincronização. Ele tem um nome semelhante a "contoso.com – Azure AD" para o Azure AD.
3. Executar: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` Você tem um arquivo em %temp% chamado export.csv que pode ser examinado no Microsoft Excel. Esse arquivo contém todas as alterações que estão prestes a ser exportadas.
4. Faça as alterações necessárias na configuração ou nos dados e execute essas etapas novamente (importar, sincronizar e verificar) até o momento estimado para que as alterações a serem exportadas ocorram.

**Noções básicas sobre o arquivo export.csv** A maior parte do arquivo é autoexplicativa. Algumas siglas para entender o conteúdo:
* OMODT – Object Modification Type. Indica se a operação em nível de objeto é um Add, Update ou Delete.
* AMODT – Attribute Modification Type. Indica se a operação em um nível de atributo é um Add, Update ou Delete.

**Recuperar identificadores comuns** O arquivo export.csv contém todas as alterações que estão prestes a ser exportadas. Cada linha corresponde a uma alteração de um objeto no espaço do conector e o objeto é identificado pelo atributo DN. O atributo DN é um identificador exclusivo atribuído a um objeto no espaço do conector. Quando você tem muitas linhas/alterações em export.csv para analisar, pode ser difícil que, com base apenas no atributo DN, você possa descobrir para quais objetos as alterações são. Para simplificar o processo de análise das alterações, use o script csanalyzer.ps1 do PowerShell. O script recupera identificadores comuns (por exemplo, displayName, userPrincipalName) dos objetos. Para usar o script:
1. Copie o script do PowerShell da seção [CSAnalyzer](#appendix-csanalyzer) para um arquivo chamado `csanalyzer.ps1`.
2. Abra uma janela do PowerShell e procure a pasta em que você criou o script do PowerShell.
3. Execute: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Agora você tem um arquivo chamado **processedusers1.csv** que pode ser examinado no Microsoft Excel. Observe que o arquivo fornece um mapeamento de atributo DN para identificadores comuns (por exemplo, displayName e userPrincipalName). Atualmente, ele não inclui as alterações de atributo real que estão prestes a ser exportadas.

#### <a name="switch-active-server"></a>Servidor ativo do comutador
1. No servidor atualmente ativo, desligue o servidor (FIM/DirSync/Azure AD Sync) para que ele não exporte para o Azure AD ou defina-o no modo de preparação (Azure AD Connect).
2. Execute o assistente de instalação no servidor no **modo de preparo** e desabilite o **modo de preparo**.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Recuperação de desastre
Parte do design de implementação é planejar o que fazer em caso de desastre, quando você perde o servidor de sincronização. Há modelos diferentes para uso e qual deles usar depende de vários fatores, incluindo:

* Quão tolerável é para você não poder fazer alterações em objetos no Azure AD durante o tempo de inatividade?
* Se você usar a sincronização de senha, os usuários aceitarão que devem usar a senha antiga no Azure AD se a alterarem no local?
* Você tem uma dependência em operações em tempo real, como write-back de senha?

Dependendo das respostas a essas perguntas e da política da sua organização, uma das estratégias abaixo pode ser implementada:

* Recriar quando necessário.
* Ter um servidor em espera reserva, conhecido como **modo de preparo**.
* Usar máquinas virtuais.

Se não usar o banco de dados interno do SQL Express, você também deverá examinar a seção [Alta disponibilidade do SQL](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Recriar quando necessário
Uma estratégia viável é planejar para a recriação do servidor quando necessário. Geralmente, a instalação do mecanismo de sincronização, a importação e a sincronização inicial podem ser concluídas em algumas horas. Se não houver um servidor reserva disponível, é possível usar temporariamente um controlador de domínio para hospedar o mecanismo de sincronização.

O servidor do mecanismo de sincronização não armazena qualquer estado sobre os objetos para que o banco de dados possa ser reconstruído com os dados no Active Directory e no Azure AD. O atributo **sourceAnchor** é usado para unir os objetos do local e da nuvem. Se você recriar o servidor com objetos locais existentes e a nuvem, o mecanismo de sincronização fará a correspondência entre esses objetos novamente na reinstalação. As coisas que você precisa documentar e salvar são as alterações de configuração feitas no servidor, como regras de sincronização e de filtragem. Essas configurações personalizadas devem ser reaplicadas antes que você inicie a sincronização.

### <a name="have-a-spare-standby-server---staging-mode"></a>Ter um servidor em espera reserva - modo de preparo
Se você tiver um ambiente mais complexo, é recomendável ter um ou mais servidores em espera. Durante a instalação, você pode habilitar um servidor em **modo de preparo**.

Para obter mais informações, consulte [modo de preparo](#staging-mode).

### <a name="use-virtual-machines"></a>Usar máquinas virtuais
Um método comum e com suporte é a execução do mecanismo de sincronização em uma máquina virtual. Se o host tiver um problema, a imagem com o servidor do mecanismo de sincronização pode ser migrada para outro servidor.

### <a name="sql-high-availability"></a>Alta disponibilidade do SQL
Se você não estiver usando o SQL Server Express que vem com o Azure AD Connect, a alta disponibilidade do SQL Server também deverá ser considerada. As soluções de alta disponibilidade com suporte incluem o clustering de SQL e AOA (Grupos de Disponibilidade AlwaysOn). Soluções sem suporte incluem espelhamento.

Suporte para SQL AOA foi adicionado ao Azure AD Connect versão 1.1.524.0. Você deve habilitar o SQL AOA antes de instalar o Azure AD Connect. Durante a instalação, o Azure AD Connect detecta se a instância do SQL fornecida está habilitada para SQL AOA ou não. Se o SQL AOA estiver habilitado, o Azure AD Connect descobrirá melhor se o AOA SQL está configurado para usar replicação síncrona ou replicação assíncrona. Ao configurar o Ouvinte do Grupo de Disponibilidade, é recomendável definir a propriedade RegisterAllProvidersIP como 0. Isso ocorre porque o Azure AD Connect atualmente usa o SQL Native Client para conectar-se ao SQL, e o SQL Native Client não dá suporte ao uso da propriedade MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Apêndice: CSAnalyzer
Consulte a seção [Verificar](#verify) para saber como usar esse script.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as a CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Próximas etapas
**Tópicos de visão geral**  

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)  
* [Integração de suas identidades locais com o Active Directory do Azure](whatis-hybrid-identity.md)  
