---
title: Instalar pacotes de idiomas em VMs do Windows 10 na área de trabalho virtual do Windows – Azure
description: Como instalar pacotes de idiomas para VMs de várias sessões do Windows 10 na área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 08/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: de495d18220500e5aa5653e89776c2634d5b1c85
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719131"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Adicionar pacotes de idiomas a uma imagem de várias sessões do Windows 10

A área de trabalho virtual do Windows é um serviço que os usuários podem implantar a qualquer momento, em qualquer lugar. É por isso que é importante que os usuários possam personalizar qual idioma suas imagens de várias sessões do Windows 10 Enterprise são exibidas.

Há duas maneiras de acomodar as necessidades de idioma de seus usuários:

- Crie pools de hosts dedicados com uma imagem personalizada para cada idioma.
- Ter usuários com requisitos diferentes de idioma e localização no mesmo pool de hosts, mas personalizar suas imagens para garantir que eles possam selecionar a linguagem de que precisam.

O último método é muito mais eficiente e econômico. No entanto, cabe a você decidir qual método melhor atende às suas necessidades. Este artigo mostrará como personalizar idiomas para suas imagens.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes itens para personalizar suas imagens de várias sessões do Windows 10 Enterprise para adicionar vários idiomas:

- Uma VM (máquina virtual) do Azure com Windows 10 Enterprise Multi-Session, versão 1903 ou posterior

- A linguagem ISO e o FOD (recurso sob demanda) do disco 1 da versão do sistema operacional usada pela imagem. Você pode baixá-las aqui: 
     
     - Idioma ISO:
        - [ISO do pacote de idiomas do Windows 10, versão 1903 ou 1909](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, versão 2004 do pacote de idiomas ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - ISO FOD disco 1:
        - [Windows 10, versão 1903 ou 1909 FOD disco 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, versão 2004 FOD disco 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)

- Um compartilhamento de arquivos do Azure ou um compartilhamento de arquivos em uma máquina virtual do servidor de arquivos do Windows

>[!NOTE]
>O compartilhamento de arquivos (repositório) deve ser acessível na VM do Azure que você planeja usar para criar a imagem personalizada.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Criar um repositório de conteúdo para pacotes de idiomas e recursos sob demanda

Para criar o repositório de conteúdo para pacotes de idiomas e FODs:

1. Em uma VM do Azure, baixe as imagens ISO e FODs do Windows 10 Multilanguage para Windows 10 Enterprise Multi-Session, versão 1903, 1909 e 2004 dos links em [pré-requisitos](#prerequisites).

2. Abra e monte os arquivos ISO na VM.

3. Vá para o pacote de idiomas ISO e copie o conteúdo das pastas **LocalExperiencePacks** e **x64 \\ Langpacks** e cole o conteúdo no compartilhamento de arquivos.

4. Vá para o **arquivo ISO fod**, copie todo o seu conteúdo e cole-o no compartilhamento de arquivos.

     >[!NOTE]
     > Se você estiver trabalhando com armazenamento limitado, copie apenas os arquivos para os idiomas que você conhece que os usuários precisam. Você pode contar os arquivos examinando os códigos de idioma em seus nomes de arquivo. Por exemplo, o arquivo francês tem o código "fr-FR" em seu nome. Para obter uma lista completa de códigos de idioma para todos os idiomas disponíveis, consulte [pacotes de idiomas disponíveis para Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

     >[!IMPORTANT]
     > Alguns idiomas exigem fontes adicionais incluídas em pacotes satélite que seguem diferentes convenções de nomenclatura. Por exemplo, nomes de arquivo de fonte em Japonês incluem "Jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![Um exemplo dos pacotes de idiomas japoneses com a marca de idioma "Jpan" em seus nomes de arquivo.](media/language-pack-example.png)

5. Defina as permissões no compartilhamento de repositório de conteúdo de idioma para que você tenha acesso de leitura da VM que você usará para criar a imagem personalizada.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Criar uma imagem personalizada de várias sessões do Windows 10 Enterprise manualmente

Para criar uma imagem personalizada de várias sessões do Windows 10 Enterprise manualmente:

1. Implante uma VM do Azure, em seguida, vá para a galeria do Azure e selecione a versão atual do Windows 10 Enterprise Multi-Session que você está usando.
2. Depois de implantar a VM, conecte-se a ela usando o RDP como um administrador local.
3. Verifique se sua VM tem todas as atualizações mais recentes do Windows. Baixe as atualizações e reinicie a VM, se necessário.
4. Conecte-se ao pacote de idiomas e ao repositório de compartilhamento de arquivos FOD e monte-o em uma unidade de letra (por exemplo, unidade E).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Criar automaticamente uma imagem personalizada de várias sessões do Windows 10 Enterprise

Se você preferir instalar idiomas por meio de um processo automatizado, poderá configurar um script no PowerShell. Você pode usar o exemplo de script a seguir para instalar os pacotes de idiomas espanhol (Espanha), francês (França) e chinês (PRC) para o Windows 10 Enterprise Multi-Session, versão 2004. O script integra o Language Interface Pack e todos os pacotes satélite necessários na imagem. No entanto, você também pode modificar esse script para instalar outros idiomas. Apenas certifique-se de executar o script de uma sessão do PowerShell com privilégios elevados ou não funcionará.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

>[!IMPORTANT]
>As versões 1903 e 1909 do Windows 10 Enterprise não exigem o `Microsoft-Windows-Client-Language-Pack_x64_<language-code>.cab` arquivo de pacote.

O script pode demorar um pouco dependendo do número de idiomas que você precisa instalar.

Após a conclusão da execução do script, verifique se os pacotes de idiomas foram instalados corretamente acessando configurações de **início**  >  **Settings**  >  **hora &** idioma do idioma  >  **Language**. Se os arquivos de idioma estiverem lá, você estará pronto.

Quando terminar, certifique-se de desconectar o compartilhamento.

## <a name="finish-customizing-your-image"></a>Concluir a personalização da imagem

Depois de instalar os pacotes de idiomas, você pode instalar qualquer outro software que deseja adicionar à imagem personalizada.

Quando tiver terminado de personalizar a imagem, você precisará executar a ferramenta de preparação do sistema (Sysprep).

Para executar o Sysprep:

1. Abra um prompt de comando com privilégios elevados e execute o seguinte comando para generalizar a imagem:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Desligue a VM e, em seguida, Capture-a em uma imagem gerenciada seguindo as instruções em [criar uma imagem gerenciada de uma VM generalizada no Azure](../virtual-machines/windows/capture-image-resource.md).

3. Agora você pode usar a imagem personalizada para implantar um pool de hosts da área de trabalho virtual do Windows. Para saber como implantar um pool de hosts, consulte [tutorial: criar um pool de hosts com o portal do Azure](create-host-pools-azure-marketplace.md).

## <a name="enable-languages-in-windows-settings-app"></a>Habilitar idiomas no aplicativo de configurações do Windows

Por fim, você precisará adicionar o idioma à lista de idiomas de cada usuário para que eles possam selecionar seu idioma preferencial no menu configurações.

Para garantir que os usuários possam selecionar os idiomas que você instalou, entre como o usuário, execute o seguinte cmdlet do PowerShell para adicionar os pacotes de idiomas instalados ao menu idiomas. Você também pode configurar esse script como uma tarefa automatizada que é ativada quando o usuário entra na sessão.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Depois que um usuário altera as configurações de idioma, ele precisará sair de sua sessão de área de trabalho virtual do Windows e entrar novamente para que as alterações entrem em vigor. 

## <a name="next-steps"></a>Próximas etapas

Se você estiver curioso sobre problemas conhecidos para pacotes de idiomas, consulte [adicionando pacotes de idiomas no Windows 10, versão 1803 e versões posteriores: problemas conhecidos](/windows-hardware/manufacture/desktop/language-packs-known-issue).

Se você tiver outras dúvidas sobre o Windows 10 Enterprise Multi-Session, confira nossas [perguntas frequentes](windows-10-multisession-faq.md).
