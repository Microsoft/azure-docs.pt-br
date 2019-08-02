---
title: Instalar o MySQL em uma VM OpenSUSE no Azure | Microsoft Docs
description: Saiba como instalar o MySQL em uma máquina virtual Linux OpenSUSE no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: baa52f4a17b06b6927013c88f37d4f2bc24744f3
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876014"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instalar MySQL em uma máquina virtual com o OpenSUSE Linux no Azure

[MySQL](https://www.mysql.com) é um banco de dados SQL de software livre popular. Este tutorial mostra como criar uma máquina virtual com o OpenSUSE Linux e instalar o MySQL.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, você precisará da CLI do Azure versão 2.0 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Criar uma máquina virtual que executa o OpenSUSE Linux

Em primeiro lugar, crie um grupo de recursos. Neste exemplo, o grupo de recursos é denominado *mySQSUSEResourceGroup* e ele é criado na região *Leste dos EUA*.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Crie a VM. Nesse exemplo, a VM é denominada *myVM* e o tamanho da VM é *Standard_D2s_v3*, mas você deve escolher o [tamanho da VM](sizes.md) que acredita ser mais adequado para sua carga de trabalho.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Também é necessário adicionar uma regra ao grupo de segurança de rede para permitir tráfego na porta 3306 para o MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Conectar-se à VM

Você usará SSH para conectar_se à VM. Neste exemplo, o endereço IP público da VM é *10.111.112.113*. É possível ver o endereço IP na saída ao criar a VM.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Atualizar a VM
 
Após estar conectado à VM, instale os patches e as atualizações do sistema. 
   
```bash
sudo zypper update
```

Siga as solicitações para atualizar a VM.

## <a name="install-mysql"></a>Instalar MySQL 


Instale o MySQL na VM através do SSH. Responda as instruções, conforme apropriado.

```bash
sudo zypper install mysql
```
 
Configure o MySQL para iniciar quando o sistema for inicializado. 

```bash
sudo systemctl enable mysql
```
Verifique se o MySQL está habilitado.

```bash
systemctl is-enabled mysql
```

Isso deverá retornar: habilitado.

Reinicie o servidor.

```bash
sudo reboot
```


## <a name="mysql-password"></a>Senha do MySQL

Após a instalação, a senha da raiz do MySQL é vazia por padrão. Execute o script de **instalação\_segura\_mysql** para proteger o MySQL. O script solicita que você altere a senha raiz do MySQL, remova as contas de usuários anônimos, desabilite as entradas de raiz remota, remova bancos de dados de teste e recarregue a tabela de privilégios. 

Depois que o servidor for reiniciado, ssh para a VM novamente.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Entrar no MySQL

Agora é possível entrar e inserir o prompt do MySQL.

```bash  
mysql -u root -p
```
Isso alterna-o para o prompt do MySQL onde é possível permitir instruções SQL para interagir com o banco de dados.

Agora, crie um novo usuário MySQL.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
O ponto e vírgula (;) no final da linha é crucial para finalizar o comando.


## <a name="create-a-database"></a>Criar um banco de dados


Crie um banco de dados e conceda as permissões do usuário `mysqluser`.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Nomes e senhas de usuário de banco de dados são usados apenas por scripts conectados ao banco de dados.  Nomes de conta de usuário do banco de dados não representam, necessariamente, contas de usuário reais no sistema.

Habilite a entrada de outro computador. Neste exemplo, o endereço IP do computador do qual permitir a entrada é *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Para sair do utilitário de administração de banco de dados MySQL, digite:

```    
quit
```


## <a name="next-steps"></a>Próximas etapas
Para obter detalhes sobre o MySQL, consulte a [Documentação do MySQL](https://dev.mysql.com/doc).




