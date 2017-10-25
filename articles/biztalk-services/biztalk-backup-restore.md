---
title: "Criar e restaurar um backup nos Serviços BizTalk | Microsoft Docs"
description: "Os serviços BizTalk incluem backup e restauração. Saiba como criar e restaurar um backup e determinar do que é feito backup. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 59f91173-4683-48df-abd5-41262bfce6df
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: c55d1ab124441c42101b4ad60924a9ea28231408
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="biztalk-services-backup-and-restore"></a>Serviços BizTalk: backup e restauração

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Os Serviços BizTalk do Azure incluem recursos de backup e restauração. Este tópico descreve como fazer backup e restaurar os Serviços BizTalk usando o portal clássico do Azure.

Você também pode fazer backup dos Serviços do BizTalk usando os [API REST dos Serviços do BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584). 

> [!NOTE]
> NÃO se faz backup das conexões híbridas, independentemente da Edição. Você deve recriar suas conexões híbridas.


## <a name="before-you-begin"></a>Antes de começar
* É possível que o backup e a restauração não estejam disponíveis para todas as edições. Consulte [Serviços BizTalk: gráfico de edições](biztalk-editions-feature-chart.md).
* Usando o portal clássico do Azure, você pode criar um backup sob demanda ou criar um backup agendado. 
* O conteúdo de backup pode ser restaurado para o mesmo Serviço do BizTalk ou para um novo Serviço do BizTalk. Para restaurar o Serviço do BizTalk usando o mesmo nome, o Serviço do BizTalk existente precisa ser excluído e o nome precisa estar disponível. Após excluir um Serviço do BizTalk, pode levar mais tempo do que o desejado para que o mesmo nome esteja disponível. Se você não puder esperar para o mesmo nome esteja disponível, faça a restauração para um novo Serviço do BizTalk.
* Os Serviços do BizTalk podem ser restaurados para a mesma edição ou para uma edição superior. Não há suporte para a restauração dos Serviços do BizTalk para uma edição inferior, de quando foi feito um backup.
  
    Por exemplo, um backup usando a Basic Edition pode ser restaurado para a Premium Edition. Um backup usando a Premium Edition não pode ser restaurado para a Standard Edition.
* O backup dos números de controle de EDI são feitos para manter a continuidade dos números de controle. Se as mensagens forem processadas depois do último backup, a restauração desse conteúdo de backup pode provocar a duplicação dos números de controle.
* Se um lote tiver mensagens ativas, processe o lote **antes** de executar um backup. Ao criar um backup (conforme necessário ou agendado), as mensagens em lotes nunca são armazenadas. 
  
    **Se um backup for feito com mensagens ativas em um lote, o backup dessas mensagens não será feito e, portanto, elas serão perdidas.**
* Opcional: no Portal dos Serviços BizTalk, interrompa todas as operações de gerenciamento.

## <a name="create-a-backup"></a>Criar um backup
Um backup pode ser obtido a qualquer momento e é totalmente controlado por você. Esta seção lista as etapas para criar backups usando o portal clássico do Azure, incluindo:

[Backup sob demanda](#backupnow)

[Agendar um backup](#backupschedule)

#### <a name="backupnow"></a>Backup sob demanda
1. No portal clássico do Azure, selecione **Serviços BizTalk**e, em seguida, selecione o Serviço BizTalk do você quer fazer backup.
2. Na guia **Painel**, selecione **Backup** na parte inferior da página.
3. Insira um nome de backup. Por exemplo, digite *meuServiçoBizTalk*BU*Data*.
4. Escolha uma conta de armazenamento de blob e selecione a marca de seleção para iniciar o backup.

Quando o backup for concluído, um contêiner com o nome do backup inserido será criado na conta de armazenamento. Esse contêiner contém a configuração de backup do Serviço do BizTalk.

#### <a name="backupschedule"></a>Agendar um backup
1. No portal clássico do Azure, selecione **Serviços BizTalk**, selecione o nome do Serviço BizTalk que você deseja agendar para o backup, em seguida, selecione a guia **Configurar**.
2. Defina o **Status de Backup** para **Automático**. 
3. Selecione a **Conta de Armazenamento** para armazenar o backup, digite a **Frequência** para criar os backups e por quanto tempo manter os backups (**Dias de Retenção**):
   
    ![][AutomaticBU]
   
    **Observações**     
   
   * Em **Dias de retenção**, o período de retenção deve ser maior do que a frequência de backup.
   * Selecione **Sempre manter pelo menos um backup**, mesmo que o período de retenção tenha passado.
4. Selecione **Salvar**.

Quando um trabalho de backup agendado é executado, ele cria um contêiner (para armazenar os dados do backup) na conta de armazenamento especificada. O nome do contêiner é *Nome do Serviço BizTalk-data-hora*. 

Se o painel do Serviço do BizTalk mostrar um status **Falha** :

![Último status do backup agendado][BackupStatus] 

O link abre os Logs de Operação de Serviços de Gerenciamento para ajudar solucionar os problemas. Consulte [Serviços BizTalk: solução de problemas usando logs de operação](http://go.microsoft.com/fwlink/p/?LinkId=391211).

## <a name="restore"></a>Restore
Você pode fazer backups no portal clássico do Azure ou em [Restaurar API REST do Serviço do BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325582). Esta seção lista as etapas para restaurar usando o portal clássico.

#### <a name="before-restoring-a-backup"></a>Antes de restaurar um backup
* Os novos armazenamentos de acompanhamento, arquivamento e monitoramento podem ser especificados durante a restauração de um Serviço BizTalk.
* Os mesmos dados de tempo de execução do EDI são restaurados. O backup do tempo de execução do EDI restaura os números de controle. Os números de controle restaurados estão em sequência da hora do backup. Se as mensagens forem processadas depois do último backup, a restauração desse conteúdo de backup pode provocar a duplicação dos números de controle.

#### <a name="restore-a-backup"></a>Restaurar um backup
1. No portal clássico do Azure, selecione **Novo** > **Serviços de Aplicativos** > **Serviço BizTalk** > **Restaurar**:
   
    ![Restaurar um backup][Restore]
2. Na **URL de backup**, selecione o ícone da pasta e expanda a conta de armazenamento do Azure que armazena o backup de configuração do Serviço de BizTalk. Expanda o contêiner e no painel esquerdo, selecione o arquivo .txt de backup correspondente. 
   <br/><br/>
   Selecione **Abrir**.
3. Na página **Restaurar Serviço BizTalk**, insira um **Nome do Serviço BizTalk** e verifique a **URL do Domínio**, **Edição** e **Região** do Serviço BizTalk restaurado. **Criar uma nova instância de banco de dados do SQL** para o banco de dados de acompanhamento:
   
    ![][RestoreBizTalkService]
   
    Selecione a seta de avanço.
4. Verificar o nome do banco de dados SQL, insira o servidor físico onde será criado o banco de dados SQL, e um nome de usuário/senha para aquele servidor.

    Se você quiser configurar a edição do banco de dados SQL, tamanho e outras propriedades, selecione  **Definir Configurações Avançadas do Banco de Dados**. 

    Selecione a seta de avanço.

1. Crie uma nova conta de armazenamento ou especifique uma conta de armazenamento existente para o Serviço BizTalk.
2. Selecione a marca de seleção para começar a restauração.

Quando a restauração for concluída com êxito, um novo Serviço do BizTalk será listado em um estado suspenso na página Serviços do BizTalk no portal clássico do Azure.

### <a name="postrestore"></a>Depois de restaurar um backup
O Serviço BizTalk sempre é restaurado em um estado **Suspenso** . Neste estado, você pode fazer qualquer alteração de configuração antes que o novo ambiente esteja funcional, incluindo:

* Se você tiver criado aplicativos do Serviço BizTalk usando o SDK dos Serviços BizTalk do Azure, será necessário atualizar as credenciais de ACS (Controle de Acesso) nesses aplicativos para trabalhar com o ambiente restaurado.
* Você restaura um Serviço do BizTalk para replicar um ambiente de Serviço do BizTalk existente. Nesta situação, se houver contratos configurados no portal original de Serviços do BizTalk que usam uma pasta FTP de origem, é possível que precise atualizar os contratos no ambiente recentemente restaurado para usar uma pasta FTP de origem diferente. De outra forma, pode haver dois contratos diferentes tentando puxar a mesma mensagem.
* Se você fez a restauração para ter vários ambientes do Serviço do BizTalk, certifique-se de ter como destino o ambiente certo nos aplicativos do Visual Studio, cmdlets do PowerShell, APIs REST ou APIs de OM de Gerenciamento de Parceiros Comerciais.
* É uma boa prática configurar backups automáticos no ambiente do Serviço do BizTalk Service recém-restaurado.

Para iniciar o Serviço do BizTalk no portal clássico do Azure, selecione o Serviço de BizTalk e selecione **Retomar** na barra de tarefas. 

## <a name="what-gets-backed-up"></a>Do que é feito backup
Quando um backup é criado, os seguintes itens são copiados:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Itens do backup</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portal de Serviços BizTalk do Azure</strong></td>
</tr> 
<tr>
<td>Configuração e tempo de execução</td> 
<td>
<ul>
<li>Detalhes e perfil do parceiro</li>
<li>Contratos de parceiro</li>
<li>Assemblies personalizados implantados</li>
<li>Pontes implantadas</li>
<li>Certificados</li>
<li>Transformações implantadas</li>
<li>Pipelines</li>
<li>Modelos criados e salvos no Portal de Serviços BizTalk</li>
<li>Mapeamentos de X12 ST01 e GS01</li>
<li>Números de controle (EDI)</li>
<li>Valores de MIC de mensagens AS2</li>
</ul>
</td>
</tr> 

<tr>
<td colspan="2">
 <strong>Serviço BizTalk do Azure</strong></td>
</tr> 
<tr>
<td>Certificado SSL</td> 
<td>
<ul>
<li>Dados do certificado SSL</li>
<li>Senha do certificado SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Configurações do Serviço do BizTalk</td> 
<td>
<ul>
<li>Contagem de unidades da escala</li>
<li>Edição</li>
<li>Versão do produto</li>
<li>Região/Datacenter</li>
<li>O namespace e a chave do ACS (Serviço de Controle de Acesso)</li>
<li>Cadeia de conexão do banco de dados de acompanhamento</li>
<li>Cadeia de conexão da conta de armazenamento de arquivamento</li>
<li>Cadeia de conexão da conta de armazenamento de monitoramento</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Itens Adicionais</strong></td>
</tr> 
<tr>
<td>Banco de Dados de Acompanhamento</td> 
<td>Quando o Serviço do BizTalk é criado, os detalhes do Banco de Dados de Acompanhamento são inseridos, incluindo o Servidor do Banco de Dados SQL do Azure e o nome do Banco de Dados de Acompanhamento. O backup do Banco de Dados de Acompanhamento não é feito automaticamente.
<br/><br/>
<strong>Importante</strong><br/>
Se o Banco de Dados de Acompanhamento for excluído e o banco de dados precisar ser recuperado, deverá existir um backup anterior. Se não existir um backup, o Banco de Dados de Acompanhamento e seus dados não poderão ser recuperados. Nessa situação, crie um novo Banco de Dados de Acompanhamento com o mesmo nome do banco de dados. A Replicação Geográfica é recomendada.</td>
</tr> 
</table>

## <a name="next"></a>Avançar
Para criar os Serviços BizTalk do Azure no portal clássico do Azure, confira [Serviços BizTalk: provisionando com o portal clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280). Para começar a criar aplicativos, visite [Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Consulte também
* [Fazer o backup do Serviço BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Restaurar o Serviço BizTalk do backup](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [Serviços BizTalk: provisionamento usando o portal clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [Serviços BizTalk: gráfico do status do provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [Serviços BizTalk: guias Painel, Monitor e Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [Serviços BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [Serviços BizTalk: nome e chave do emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Como começar a usar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png

