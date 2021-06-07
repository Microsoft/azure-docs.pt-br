---
title: Configurar um cliente NFS para Azure NetApp Files | Microsoft Docs
description: Descreve como configurar clientes NFS para usar com o Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2020
ms.author: b-juche
ms.openlocfilehash: c1cdeaa41dda11f2ab520cf8d31ddb2116587082
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94409562"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Configurar um cliente NFS para o Azure NetApp Files

A configuração de cliente NFS descrita neste artigo faz parte da configuração quando você [configura a criptografia Kerberos do nfsv 4.1](configure-kerberos-encryption.md) ou [cria um volume de protocolo duplo](create-volumes-dual-protocol.md). Uma ampla variedade de distribuições do Linux está disponível para uso com o Azure NetApp Files. Este artigo descreve as configurações para dois dos ambientes mais comumente usados: RHEL 8 e Ubuntu 18, 4. 

Independentemente do tipo de Linux que você usa, as seguintes configurações são necessárias:
* Configure um cliente NTP para evitar problemas com distorção de tempo.
* Configurar entradas DNS do cliente Linux para resolução de nomes.  
    Essa configuração deve incluir o registro "A" (encaminhar) e o registro de PTR (reverso). 
* Para ingresso no domínio, crie uma conta de computador para o cliente Linux no Active Directory de destino (que é criado durante o comando de junção de realm). 
    > [!NOTE] 
    > A `$SERVICEACCOUNT` variável usada nos comandos abaixo deve ser uma conta de usuário com permissões ou delegação para criar uma conta de computador na unidade organizacional de destino.

## <a name="rhel-8-configuration"></a>Configuração do RHEL 8 

Esta seção descreve as configurações do RHEL necessárias para a criptografia Kerberos do NFSv 4.1 e para o protocolo dual.  

Os exemplos nesta seção usam o seguinte nome de domínio e endereço IP:  

* Nome de domínio: `contoso.com`
* IP privado: `10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>Configuração do RHEL 8 se você estiver usando a criptografia Kerberos do NFSv 4.1

1. Configure `/etc/resolv.conf` com o servidor DNS apropriado.  

    Por exemplo:  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. Adicione o registro de cliente NFS no servidor DNS para a zona de pesquisa inversa e direta de DNS.

3. Para verificar o DNS, use os seguintes comandos do cliente NFS:   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. Instalar pacotes:   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  Configure o cliente NTP.  

    O RHEL 8 usa chrony por padrão. Seguindo as diretrizes de configuração no [uso do `Chrony` conjunto para configurar o NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

6.  Ingresse no domínio de Active Directory:  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    Por exemplo: 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`
    
    Certifique-se de que `default_realm` está definido para o realm fornecido no `/etc/krb5.conf` .  Caso contrário, adicione-o na `[libdefaults]` seção no arquivo, conforme mostrado no exemplo a seguir:
    
    `default_realm = CONTOSO.COM`

7. Reinicie todos os serviços NFS:  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    A reinicialização impede a condição de erro `“mount.nfs: an incorrect mount option was specified”` durante a montagem Kerberos.

8. Execute o `kinit` comando com a conta de usuário para obter tíquetes: 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    Por exemplo:   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>Configuração do RHEL 8 se você estiver usando o protocolo duplo

As etapas a seguir são opcionais. Você precisará executar as etapas somente se usar o mapeamento de usuário no cliente NFS: 

1. Conclua todas as etapas descritas na [configuração do RHEL 8 se você estiver usando a seção de criptografia Kerberos do nfsv 4.1](#rhel8_nfsv41_kerberos) .   

2. Adicione um registro DNS estático em seu arquivo/etc/hosts para usar o FQDN (nome de domínio totalmente qualificado) para seu anúncio, em vez de usar o endereço IP no arquivo de configuração SSSD:  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. Adicione uma seção extra para domínios para resolver identificadores do servidor LDAP do AD:    

    `[root@reddoc cbs]# cat /etc/sssd/sssd.conf`   
    `[sssd]`   
    `domains = contoso.com, contoso-ldap (new entry added for LDAP as id_provider)`   
    `config_file_version = 2`   
    `services = nss, pam, ssh, sudo (ensure nss is present in this list)`   
 
    `[domain/contoso-ldap] (Copy the following lines. Modify as per your domain name.)`   
    `auth_provider = krb5`   
    `chpass_provider = krb5`   
    `id_provider = ldap`   
    `ldap_search_base = dc=contoso,dc=com(your domain)`   
    `ldap_schema = rfc2307bis`   
    `ldap_sasl_mech = GSSAPI`   
    `ldap_user_object_class = user`   
    `ldap_group_object_class = group`   
    `ldap_user_home_directory = unixHomeDirectory`   
    `ldap_user_principal = userPrincipalName`   
    `ldap_account_expire_policy = ad`   
    `ldap_force_upper_case_realm = true`   
    `ldap_user_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_group_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_sasl_authid = REDDOC$ (ensure $ at the end you can get this from “klist -kte” command)`   
    `krb5_server = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `krb5_realm = CONTOSO.COM (domain name in caps)`   
    `krb5_kpasswd = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `use_fully_qualified_names = false`   
 
    `[domain/contoso.com]  (Do not edit or remove any of the following information. This information is automatically generated during the realm join process.)`   
    `ad_domain = contoso.com`   
    `krb5_realm = CONTOSO.COM`   
    `realmd_tags = manages-system joined-with-adcli`   
    `cache_credentials = True`   
    `id_provider = ad`   
    `krb5_store_password_if_offline = True`   
    `default_shell = /bin/bash`   
    `ldap_id_mapping = True`   
    `use_fully_qualified_names = True`   
    `fallback_homedir = /home/%u@%d`   
    `access_provider = ad`   

4. Verifique se o `/etc/nsswitch.conf` tem a `sss` entrada:   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. Reinicie o `sssd` serviço e limpe o cache:   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. Teste para garantir que o cliente esteja integrado ao servidor LDAP:   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Configuração do Ubuntu   

Esta seção descreve as configurações do Ubuntu necessárias para a criptografia Kerberos do NFSv 4.1 e para o protocolo dual. 

Os exemplos nesta seção usam o seguinte nome de domínio e endereço IP:  

* Nome de domínio: `contoso.com`
* IP privado: `10.6.1.4`

1. Configure `/etc/resolv.conf` com o servidor DNS apropriado:

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. Adicione o registro de cliente NFS no servidor DNS para a zona de pesquisa inversa e direta de DNS.
 
    Para verificar o DNS, use os seguintes comandos do cliente NFS:

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. Instalar pacotes:
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    Quando solicitado, insira `$DOMAIN.NAME` (usando letras maiúsculas, por exemplo, `CONTOSO.COM` ) como o realm padrão do Kerberos.

4. Reinicie o serviço `rpc-gssd.service` : 

    `sudo systemctl start rpc-gssd.service`

5. O Ubuntu 18, 4 usa chrony por padrão. Seguindo as diretrizes de configuração no [Ubuntu Bionic: usando o chrony para configurar o NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

6. Ingresse no domínio de Active Directory:   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    Por exemplo:    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. Execute `kinit` com o usuário para obter tíquetes: 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    Por exemplo:    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>Configuração do Ubuntu se você estiver usando o protocolo dual  

As etapas a seguir são opcionais.  Você precisará executar as etapas somente se quiser usar o mapeamento de usuário no cliente NFS:  

1. Execute o seguinte comando para atualizar os pacotes instalados:   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    O exemplo a seguir usa valores de exemplo. Quando o comando solicitar a entrada, você deverá fornecer a entrada com base em seu ambiente. 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. Execute o seguinte comando para reiniciar e habilitar o serviço:

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

O exemplo a seguir consulta o servidor LDAP do AD do cliente do Ubuntu LDAP para um usuário LDAP `‘hari1’` : 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   


## <a name="next-steps"></a>Próximas etapas  

* [Criar um volume NFS para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Criar um volume de protocolo duplo para Azure NetApp Files](create-volumes-dual-protocol.md)

