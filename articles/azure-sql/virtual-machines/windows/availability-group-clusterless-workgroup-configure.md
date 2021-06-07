---
title: Configurar um grupo de disponibilidade de grupo de trabalho independente de domínio
description: Saiba como configurar um grupo de disponibilidade de Always On de Active Directory de domínio independente de domínios em uma máquina virtual SQL Server no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 0f194101720481f71434709c467d0e3130a0f1f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359448"
---
# <a name="configure-a-workgroup-availability-group"></a>Configurar um grupo de disponibilidade de grupo de trabalho 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo explica as etapas necessárias para criar um cluster independente de domínio do Active Directory com um grupo de disponibilidade Always On, que também é conhecido como um cluster de grupo de trabalho. Este artigo se concentra nas etapas que são relevantes para preparar e configurar o grupo de trabalho e o grupo de disponibilidade e passa pelas etapas abordadas em outros artigos, como a maneira de criar o cluster ou implantar o grupo de disponibilidade. 


## <a name="prerequisites"></a>Pré-requisitos

Para configurar um grupo de disponibilidade de grupo de trabalho, você precisará do seguinte:
- Pelo menos duas máquinas virtuais do Windows Server 2016 (ou superior) que executam o SQL Server 2016 (ou superior), implantadas no mesmo conjunto de disponibilidade ou em zonas de disponibilidade diferentes, usando endereços IP estáticos. 
- Uma rede local com no mínimo 4 endereços IP livres na sub-rede. 
- Uma conta em cada computador no grupo de administradores que também tenha direitos de sysadmin dentro do SQL Server. 
- Abrir portas: TCP 1433, TCP 5022 e TCP 59999. 

Para referência, os seguintes parâmetros são usados neste artigo, mas podem ser modificados conforme necessário: 

| **Nome** | **Parâmetro** |
| :------ | :---------------------------------- |
| **Node1**   | AGNode1 (10.0.0.4) |
| **Node2**   | AGNode2 (10.0.0.5) |
| **Nome do cluster** | AGWGAG (10.0.0.6) |
| **Ouvinte** | AGListener (10.0.0.7) | 
| **Sufixo DNS** | ag.wgcluster.example.com | 
| **Nome do grupo de trabalho** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-a-dns-suffix"></a>Definir um sufixo DNS 

Nesta etapa, configure o sufixo DNS para ambos os servidores. Por exemplo, `ag.wgcluster.example.com`. Isso permite que você use o nome do objeto ao qual você deseja se conectar como um endereço totalmente qualificado em sua rede, como `AGNode1.ag.wgcluster.example.com`. 

Para configurar o sufixo DNS, siga estas etapas:

1. RDP no seu primeiro nó e abra Gerenciador do Servidor. 
1. Selecione **Servidor Local** e, em seguida, selecione o nome da sua máquina virtual em **Nome do computador**. 
1. Selecione **Alterar...** em **Para renomear este computador...** . 
1. Altere o nome do grupo de trabalho para algo significativo, como `AGWORKGROUP`: 

   ![Alterar nome do grupo de trabalho](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. Selecione **Mais...** para abrir a caixa de diálogo **Sufixo DNS e Nome do Computador NetBIOS**. 
1. Digite o nome do seu sufixo DNS em **Sufixo DNS primário deste computador**, como `ag.wgcluster.example.com` e, em seguida, selecione **OK**: 

   ![Captura de tela mostra o sufixo D N S e a caixa de diálogo nome do computador NetBIOS onde você pode inserir o valor.](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. Confirme se o **Nome completo do computador** agora está mostrando o sufixo DNS e, em seguida, selecione **OK** para salvar as alterações: 

   ![Captura de tela mostra onde ver o nome completo do computador.](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. Reinicialize o servidor quando solicitado a fazê-lo. 
1. Repita essas etapas em outros nós que serão usados para o grupo de disponibilidade. 

## <a name="edit-a-host-file"></a>Editar um arquivo de host

Como não há nenhum diretório ativo, não há como autenticar conexões de janela. Dessa forma, atribua confiança editando o arquivo de host com um editor de texto. 

Para editar o arquivo de host, siga estas etapas:

1. RDP em sua máquina virtual. 
1. Use o **Explorador de Arquivos** para acessar `c:\windows\system32\drivers\etc`. 
1. Clique com o botão direito do mouse no arquivo de **hosts** e abra o arquivo com o **Bloco de notas** (ou qualquer outro editor de texto).
1. No final do arquivo, adicione uma entrada para cada nó, para o grupo de disponibilidade e para o ouvinte na forma de `IP Address, DNS Suffix #comment`, como: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Adicionar entradas para o endereço IP, o cluster e o ouvinte ao arquivo de host](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>Definir permissões

Como não há Active Directory para gerenciar permissões, você precisa permitir manualmente que uma conta de administrador local não interna crie o cluster. 

Para fazer isso, execute o seguinte cmdlet do PowerShell em uma sessão administrativa do PowerShell em cada nó: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Criar o cluster de failover

Nesta etapa, você criará o cluster de failover. Se você não estiver familiarizado com essas etapas, poderá segui-las no [tutorial de cluster de failover](failover-cluster-instance-storage-spaces-direct-manually-configure.md).

Diferenças notáveis entre o tutorial e o que deve ser feito para um cluster de grupo de trabalho:
- Desmarque **Armazenamento** e **Espaços de Armazenamento Diretos** ao executar a validação do cluster. 
- Ao adicionar os nós ao cluster, adicione o nome totalmente qualificado, como:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Desmarque **Adicione todo o armazenamento qualificado ao cluster**. 

Depois que o cluster tiver sido criado, atribua um endereço IP de cluster estático. Para fazer isso, siga estas etapas:

1. Em um dos nós, abra **Gerenciador de Cluster de Failover**, selecione o cluster, clique com o botão direito do mouse no **Nome: \<ClusterNam>** em **Recursos Principais de Cluster** e, em seguida, selecione **Propriedades**. 

   ![Iniciar propriedades do nome do cluster](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. Selecione o endereço IP em **Endereços IP** e selecione **Editar**. 
1. Selecione **Usar Estático**, forneça o endereço IP do cluster e, em seguida, selecione **OK**: 

   ![Forneça um endereço IP estático para o cluster](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. Verifique se as configurações estão corretas e, em seguida, selecione **OK** para salvá-las:

   ![Verificar propriedades do cluster](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Criar uma testemunha de nuvem 

Nesta etapa, configure uma testemunha de compartilhamento em nuvem. Se você não estiver familiarizado com as etapas, consulte [implantar uma testemunha de nuvem para um cluster de failover](/windows-server/failover-clustering/deploy-cloud-witness). 

## <a name="enable-the-availability-group-feature"></a>Habilitar o recurso de grupo de disponibilidade 

Nesta etapa, habilite o recurso de grupo de disponibilidade. Se você não estiver familiarizado com as etapas, confira o [tutorial de grupo de disponibilidade](availability-group-manually-configure-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificates"></a>Criar chaves e certificados

Nesta etapa, crie certificados que um logon do SQL use no ponto de extremidade criptografado. Crie uma pasta em cada nó para manter os backups de certificado, como `c:\certs`. 

Para configurar o primeiro nó, siga estas etapas: 

1. Abra o **SQL Server Management Studio** e conecte-se ao primeiro nó, como `AGNode1`. 
1. Abra a janela **Nova Consulta** e execute a seguinte instrução T-SQL (Transact-SQL) após atualizar para uma senha complexa e segura:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Em seguida, crie o ponto de extremidade de HADR e use o certificado para autenticação executando esta instrução T-SQL (Transact-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Use o **Explorador de Arquivos** para acessar a localização de arquivo em que o certificado se encontra, como `c:\certs`. 
1. Faça uma cópia manual do certificado do primeiro nó, como `AGNode1Cert.crt`, e transfira-o para a mesma localização no segundo nó. 

Para configurar o segundo nó, siga estas etapas: 

1. Conecte-se ao segundo nó com o **SQL Server Management Studio**, como `AGNode2`. 
1. Em uma janela **Nova Consulta**, execute a seguinte instrução T-SQL (Transact-SQL) após atualizar para uma senha complexa e segura: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Em seguida, crie o ponto de extremidade de HADR e use o certificado para autenticação executando esta instrução T-SQL (Transact-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Use o **Explorador de Arquivos** para acessar a localização de arquivo em que o certificado se encontra, como `c:\certs`. 
1. Faça uma cópia manual do certificado do segundo nó, como `AGNode2Cert.crt`, e transfira-o para a mesma localização no primeiro nó. 

Se houver outros nós no cluster, repita essas etapas, modificando os respectivos nomes de certificado. 

## <a name="create-logins"></a>Criar logons

A autenticação de certificado é usada para sincronizar dados entre os nós. Para permitir isso, crie um logon para o outro nó, crie um usuário para o logon, crie um certificado para que o logon use o certificado de backup e, em seguida, conceda a conexão no ponto de extremidade de espelhamento. 

Para fazer isso, primeiro execute a seguinte consulta T-SQL (Transact-SQL) no primeiro nó, como `AGNode1`: 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Em seguida, execute a seguinte consulta T-SQL (Transact-SQL) no segundo nó, como `AGNode2`: 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Se houver outros nós no cluster, repita essas etapas, modificando os respectivos nomes de certificado e usuário. 

## <a name="configure-an-availability-group"></a>Configurar um conjunto de disponibilidade

Nesta etapa, configure seu grupo de disponibilidade e adicione bancos de dados a ele. Não crie um ouvinte neste momento. Se você não estiver familiarizado com as etapas, confira o [tutorial de grupo de disponibilidade](availability-group-manually-configure-tutorial.md#create-the-availability-group). Inicie um failover e um failback para verificar se tudo está funcionando como deveria. 

   > [!NOTE]
   > Se houver uma falha durante o processo de sincronização, talvez seja necessário conceder direitos sysadmin ao `NT AUTHORITY\SYSTEM` para criar recursos de cluster no primeiro nó, como `AGNode1` temporariamente. 

## <a name="configure-a-load-balancer"></a>Configurar um balanceador de carga

Nesta etapa final, configure o balanceador de carga usando o [portal do Azure](availability-group-load-balancer-portal-configure.md) ou o [PowerShell](availability-group-listener-powershell-configure.md).


## <a name="next-steps"></a>Próximas etapas

Você também pode usar a [CLI do AZ em VM do SQL](./availability-group-az-commandline-configure.md) para configurar um grupo de disponibilidade.