Na janela do terminal local, adicione um remoto do Azure ao repositório Git local.

```bash
git remote add azure <URI from previous step>
```

Envie por push para o Azure remoto para implantar seu aplicativo com o comando a seguir. Quando uma senha for solicitada, verifique se você inseriu a senha que criou em [Configurar um usuário de implantação](#configure-a-deployment-user), não a senha usada para fazer logon no Portal do Azure.

```bash
git push azure master
```

O comando anterior exibe informações semelhantes ao seguinte exemplo:
