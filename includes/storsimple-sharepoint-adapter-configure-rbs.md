---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f84fe995e65d2b67aaaf4ff9acc4a6a44ce607dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632992"
---
> [!NOTE]
> Ao fazer alterações na configuração do Adaptador StorSimple para SharePoint RBS, você deve estar conectado com uma conta de usuário que pertença ao grupo Administradores do Domínio. Além disso, você deve acessar a página de configuração em um navegador em execução no mesmo host que a Administração Central.
> 
> 

#### <a name="to-configure-rbs"></a>Para configurar o RBS
1. Abra a página Administração Central do SharePoint e navegue até **Configurações do Sistema**. 
2. Na seção **Azure StorSimple**, clique em **Configurar Adaptador StorSimple**.
   
    ![Configurar o Adaptador StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Na página **Configurar Adaptador StorSimple** :
   
   1. Verifique se a caixa de seleção **Habilitar edição de caminho** está marcada.
   2. Na caixa de texto, digite o caminho UNC (Convenção de Nomenclatura Universal) do armazenamento de BLOB.
      
      > [!NOTE]
      > O volume de armazenamento de BLOB deve ser hospedado em um volume iSCSI configurado no dispositivo StorSimple.

   3. Clique no botão **Habilitar** abaixo de cada banco de dados de conteúdo que deseja configurar para armazenamento remoto.
      
      > [!NOTE]
      > O armazenamento de BLOB deve ser compartilhado e poder ser acessado por todos os servidores WFE (Front-End da Web) e a conta do usuário que está configurada para o farm de servidores do SharePoint deve ter acesso ao compartilhamento.
      
      ![Habilitar o provedor RBS](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Quando você habilita ou desabilita o RBS, a mensagem a seguir é exibida.
      
      ![Configurar o Adaptador StorSimple para Habilitado/Desabilitado](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Clique no botão **Atualizar** para aplicar a configuração. Quando você clica no botão **Atualizar** , o status da configuração do RBS é atualizado em todos os servidores WFE e todo o farm é habilitado para RBS. A seguinte mensagem é exibida.
      
      ![Mensagem de configuração do adaptador](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Se você estiver configurando um RBS para um farm do SharePoint com um número muito grande de bancos de dados (maior que 200), o tempo limite da página da Web da Administração Central do SharePoint poderá se esgotar. Se isso ocorrer, atualize a página. Isso não afeta o processo de configuração.

4. Verifique a configuração:
   
   1. Entre no site de Administração Central do SharePoint e navegue até a página **Configurar Adaptador StorSimple** .
   2. Verifique os detalhes da configuração para garantir que eles correspondam às configurações que você inseriu. 
5. Verifique se o RBS funciona corretamente:
   
   1. Carregue um documento no SharePoint. 
   2. Navegue até o caminho UNC que você configurou. Verifique se a estrutura do diretório do RBS foi criada e se ela contém o objeto carregado.
6. (Opcional) Você pode usar o cmdlet do PowerShell `Migrate()` do Microsoft RBS incluído com o SharePoint para migrar o conteúdo BLOB existente para o dispositivo StorSimple. Para saber mais, confira [Migrar conteúdo para dentro ou fora do RBS no SharePoint 2013][6] ou [Migrar conteúdo para dentro ou fora do RBS (SharePoint Foundation 2010)][7].
7. (Opcional) Em instalações de teste, você pode verificar se os BLOBs foram movidos para fora do banco de dados de conteúdo, como se segue: 
   
   1. Inicie o SQL Management Studio.
   2. Execute a consulta ListBlobsInDB_2010.sql ou ListBlobsInDB_2013.sql como se segue.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Se o RBS foi configurado corretamente, um valor NULL deverá aparecer na coluna SizeOfContentInDB para qualquer objeto que foi carregado e externalizado com êxito com o RBS.
8. (Opcional) Depois de configurar o RBS e mover todo o conteúdo BLOB para o dispositivo StorSimple, você pode mover o banco de dados de conteúdo para o dispositivo. Caso opte por mover o banco de dados de conteúdo, é recomendável configurar o armazenamento de banco de dados de conteúdo no dispositivo como um volume primário. Em seguida, use as práticas recomendadas estabelecidas do SQL Server para migrar o banco de dados de conteúdo para o dispositivo StorSimple. 
   
   > [!NOTE]
   > Mover o banco de dados de conteúdo para o dispositivo só é permitido no StorSimple série 8000 (não há suporte para essa ação na série 5000 ou 7000).
   
   Se você armazenar BLOBs e o banco de dados de conteúdo em volumes separados no dispositivo StorSimple, é recomendável configurá-los no mesmo contêiner de volume. Isso garante que os respectivos backups sejam feitos juntos.
   
   > [!WARNING]
   > Se você não tiver habilitado o RBS, não é recomendável mover o banco de dados de conteúdo para o dispositivo StorSimple. Essa é uma configuração não testada.
   
9. Vá para a etapa seguinte: [Configurar a coleta de lixo](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
