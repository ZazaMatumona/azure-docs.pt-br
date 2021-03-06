---
title: Configurar operações de vRealize para a solução do Azure VMware
description: Saiba como configurar operações de vRealize para sua nuvem privada da solução Azure VMware.
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 729ee5c64776d7d04f702af62451175f7c53421b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750391"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Configurar operações de vRealize para a solução do Azure VMware


o vRealize Operations Manager é uma plataforma de gerenciamento de operações que permite que os administradores de infraestrutura do VMware monitorem os recursos do sistema. Esses recursos do sistema podem ser objetos de nível de aplicativo ou de infraestrutura (físico e virtual). Historicamente, a maioria dos administradores de VMware usou operações de vRealize para monitorar e gerenciar os componentes de nuvem privada VMware – vCenter, ESXi, NSX-T, vSAN e HCX (extensão de nuvem híbrida). Cada nuvem privada da solução Azure VMware é provisionada com uma implantação do vCenter, NSX-T, vSAN e HCX dedicada. 

Revisão completa [antes de começar](#before-you-begin) e [pré-requisitos](#prerequisites) primeiro. Em seguida, vamos orientá-lo pelas duas topologias típicas de implantação para vRealize Operations Manager com a solução Azure VMware:

> [!div class="checklist"]
> * [Operações de vRealize locais Gerenciando a implantação de solução do Azure VMware](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Operações de vRealize em execução na implantação da solução Azure VMware](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Antes de começar
* Examine a [documentação do produto vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para saber mais sobre a implantação de operações do vRealize. 
* Examine a [série de tutoriais](tutorial-network-checklist.md)básicos do programa de datacenter (SDDC) da solução VMware do Azure.
* Opcionalmente, examine a documentação do produto do [controlador remoto do VRealize Operations](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) para as operações vRealize locais Gerenciando a opção de implantação de solução do Azure VMware. 



## <a name="prerequisites"></a>Pré-requisitos
* A VPN ou o Azure ExpressRoute deve ser configurado entre o local e a solução SDDC do Azure VMware.
* Uma nuvem privada da solução Azure VMware foi implantada no Azure.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Operações de vRealize locais Gerenciando a implantação de solução do Azure VMware
A maioria dos clientes tem uma implantação local existente de operações vRealize usadas para gerenciar um ou mais domínios vCenters locais. Quando os clientes provisionam uma nova nuvem privada da solução Azure VMware no Azure, normalmente conectam seu ambiente local com a solução do Azure VMware usando um Azure ExpressRoute ou usando uma solução de VPN de camada 3, como mostrado abaixo.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Operações de vRealize locais Gerenciando a implantação de solução do Azure VMware"  border="false":::

Para estender os recursos de operações do vRealize para a nuvem privada da solução Azure VMware, crie uma [instância de adaptador para os recursos de nuvem privada da solução Azure VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) -para coletar dados da nuvem privada da solução Azure VMware e colocá-los em operações vRealize locais. A instância de Operations Manager do vRealize local pode se conectar diretamente ao vCenter e ao NSX-T Manager na solução VMware do Azure ou você pode, opcionalmente, implantar um coletor remoto de operações do vRealize na nuvem privada da solução Azure VMware. Um coletor remoto de operações do vRealize compacta e criptografa os dados coletados da nuvem privada da solução Azure VMware antes que eles sejam enviados pela rede de ExpressRoute ou VPN para o vRealize Operations Manager em execução no local. 

> [!TIP]
> Consulte a [documentação do VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para obter um guia passo a passo para a instalação do vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Operações de vRealize em execução na implantação da solução Azure VMware

Outra opção de implantação é implantar uma instância do vRealize Operations Manager em um dos clusters vSphere na nuvem privada da solução Azure VMware – conforme mostrado abaixo. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Operações de vRealize em execução na solução VMware do Azure" border="false":::

Depois de implantar a instância da solução do Azure VMware de operações do vRealize, você pode configurar as operações do vRealize para coletar dados do vCenter, ESXi, NSX-T, vSAN e HCX. 

> [!TIP]
> Consulte a [documentação do VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para obter um guia passo a passo para a instalação do vRealize Operations Manager.




<!-- LINKS - external -->


<!-- LINKS - internal -->




