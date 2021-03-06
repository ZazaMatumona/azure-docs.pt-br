---
title: 'Início Rápido: Criar um aplicativo Java no Serviço de Aplicativo do Azure'
description: Implante seu primeiro Olá, Mundo em Java no Serviço de Aplicativo do Azure em minutos. O Plug-in de Aplicativo Web do Azure para Maven torna-o conveniente para implantar aplicativos Java.
keywords: azure, serviço de aplicativo, aplicativo Web, windows, linux, java, maven, início rápido
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 274228ea5aa9ac9de9725176c8b6221ee9e9542e
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182673"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Início Rápido: Criar um aplicativo Java no Serviço de Aplicativo do Azure

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches.  Este início rápido mostra como usar a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) com o [Plug-in de Aplicativo Web do Azure para Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) para implantar um arquivo WAR (Arquivo Web) Java.

> [!NOTE]
> Neste artigo, estamos trabalhando apenas com aplicativos Java empacotados em arquivos WAR. O plug-in também oferece suporte a aplicativos Web JAR, visite [Implantar um arquivo JAR do Java SE para o Serviço de Aplicativo no Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para experimentá-lo.

> [!NOTE]
> A mesma coisa também pode ser feita usando IDEs populares, como o IntelliJ e o Eclipse. Confira nossos documentos semelhantes em [Início Rápido: Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) ou [Início Rápido: Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).
>
![Aplicativo de exemplo em execução no Serviço de Aplicativo do Azure](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Criar um aplicativo Java

Execute o seguinte comando do Maven no prompt do Cloud Shell para criar um aplicativo chamado `helloworld`:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Em seguida, altere o diretório de trabalho para a pasta do projeto:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Configurar o plug-in do Maven

O processo de implantação no Serviço de Aplicativo do Azure pode extrair suas credenciais do Azure automaticamente por meio da CLI do Azure. O plug-in do Maven conectará você ao OAuth ou ao logon do dispositivo se a CLI do Azure não estiver instalada localmente. Verifique os detalhes em [Autenticação com plug-ins do Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication), se necessário.

Execute o comando do Maven abaixo para configurar a implantação
```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

::: zone pivot="platform-windows" 
Você precisará selecionar 
* **Sistema Operacional (Padrão: `linux`)**
* **Versão do Java (Padrão: `1.8`)**
* **Contêiner da Web (Padrão: `tomcat 8.5`)** 
 
Tenha cuidado para inserir **`2`** a fim de escolher o sistema operacional **Windows** na primeira etapa. Mantenha as outras configurações com o padrão clicando em **ENTER**. Por fim, clique em **`Y`** no aviso **Confirmar (S/N)** para concluir a configuração.

Um processo de exemplo é semelhante a este:

```console
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
```
::: zone-end
::: zone pivot="platform-linux"  

Você precisará selecionar 
* **Sistema Operacional (Padrão: `linux`)**
* **Versão do Java (Padrão: `Java 8`)**
* **Contêiner da Web (Padrão: `Tomcat 8.5`)** 

Todas as configurações podem ser deixadas com o padrão pressionando **ENTER**. Por fim, clique em **`Y`** no aviso **Confirmar (S/N)** para concluir a configuração.
Um processo de exemplo é semelhante a este:

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. Java 11
2. Java 8 [*]
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. TOMCAT 8.5 [*]
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```
::: zone-end

Você poderá modificar as configurações do Serviço de Aplicativo diretamente em seu `pom.xml`, se necessário. Algumas configurações comuns estão listadas abaixo:

 Propriedade | Obrigatório | Descrição | Versão
---|---|---|---
`<schemaVersion>` | false | Especifique a versão do esquema de configuração. Os valores suportados são: `v1`, `v2`. | 1.5.2
`<resourceGroup>` | true | Grupo de recursos do Azure para seu aplicativo Web. | 0.1.0+
`<appName>` | true | O nome do seu aplicativo Web. | 0.1.0+
`<region>` | true | Especifica a região onde seu aplicativo Web será hospedado; o valor padrão é **westeurope**. Todas as regiões válidas na seção [Regiões com suporte](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<pricingTier>` | false | O tipo de preço do seu aplicativo Web. O valor padrão é **P1V2**.| 0.1.0+
`<runtime>` | true | A configuração do ambiente de runtime. Você pode ver os detalhes [aqui](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<deployment>` | true | A configuração de implantação. Você pode ver os detalhes [aqui](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+

Tenha cuidado com os valores de `<appName>` e `<resourceGroup>`(`helloworld-1590394316693` e `helloworld-1590394316693-rg` de acordo com a demonstração), que serão usados posteriormente.

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Implantar o aplicativo

O processo de implantação no Serviço de Aplicativo do Azure usa credenciais de conta da CLI do Azure. [Entre com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar.

```azurecli
az login
```
Em seguida, você poderá implantar seu aplicativo Java no Azure usando o seguinte comando:

```bash
mvn package azure-webapp:deploy
```

Depois que a implantação for concluída, seu aplicativo estará pronto em `http://<appName>.azurewebsites.net/`(`http://helloworld-1590394316693.azurewebsites.net` na demonstração). Abra a URL com o navegador da Web local para ver

![Aplicativo de exemplo em execução no Serviço de Aplicativo do Azure](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Parabéns!** Você implantou seu primeiro aplicativo Java no Serviço de Aplicativo.

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Limpar os recursos

Nas etapas anteriores, você criou os recursos do Azure em um grupo de recursos. Se você acha que não precisará desses recursos no futuro, exclua o grupo de recursos por meio do portal ou executando o seguinte comando no Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Esse comando pode demorar um pouco para ser executado.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Conectar-se ao Banco de Dados SQL do Azure com Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Conectar-se ao BD do Azure para MySQL com Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Conectar-se ao BD do Azure para PostgreSQL com Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Recursos do Azure para desenvolvedores Java](/java/azure/)

> [!div class="nextstepaction"]
> [Configurar o aplicativo Java](configure-language-java.md)

> [!div class="nextstepaction"]
> [CI/CD com Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Mapear domínio personalizado](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre os plug-ins do Maven para o Azure](https://github.com/microsoft/azure-maven-plugins)
