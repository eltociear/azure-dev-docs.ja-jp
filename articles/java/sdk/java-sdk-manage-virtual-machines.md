---
title: Azure 仮想マシンを Java で管理する | Microsoft Docs
description: Azure SDK for Java を使って Azure 仮想マシンを管理するためのサンプル コード
author: rloutlaw
ms.assetid: 88629aee-6279-433e-a08b-4f8e290446d0
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.openlocfilehash: 90bf21232ed5ac8b4f98122951b6cfe4aed2eb4c
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82104983"
---
# <a name="manage-azure-virtual-machines-from-your-java-applications"></a>Azure 仮想マシンを Java アプリケーションから管理する

[このサンプル](https://github.com/Azure-Samples/compute-java-manage-vm/)では、[Azure Management Libraries for Java](https://github.com/Azure/azure-sdk-for-java) を使って Azure 仮想マシンの作成と操作を行います。

## <a name="run-the-sample"></a>サンプルを実行する

[認証ファイル](https://docs.microsoft.com/azure/java/java-sdk-azure-authenticate#mgmt-file)を作成し、そのファイルのコンピューター上における完全なパスを保持する環境変数 `AZURE_AUTH_LOCATION` を設定します。 次に、次のコマンドを実行します。

```
git clone https://github.com/Azure-Samples/compute-java-manage-vm.git
cd compute-java-manage-vm
mvn clean compile exec:java
```

[完全なコード サンプルについては GitHub](https://github.com/Azure-Samples/compute-java-manage-vm/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/ManageVirtualMachine.java) を参照してください。

## <a name="authenticate-with-azure"></a>Azure での認証

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-windows-virtual-machine"></a>Windows 仮想マシンの作成

```java
// Prepare a data disk for VM
Disk dataDisk = azure.disks().define(SdkContext.randomResourceName("dsk", 30))
            .withRegion(region)
            .withNewResourceGroup(rgName)
            .withData()
            .withSizeInGB(50)
            .create();

// create the windows virtual machine with the data disk            
VirtualMachine windowsVM = azure.virtualMachines().define(windowsVmName)
            .withRegion(region)
            .withNewResourceGroup(rgName)
            .withNewPrimaryNetwork("10.0.0.0/28")
            .withPrimaryPrivateIpAddressDynamic()
            .withoutPrimaryPublicIpAddress()
            .withPopularWindowsImage(KnownWindowsVirtualMachineImage.WINDOWS_SERVER_2012_R2_DATACENTER)
            .withAdminUsername(userName)
            .withAdminPassword(password)
            .withNewDataDisk(10)
            .withNewDataDisk(dataDiskCreatable)
            .withExistingDataDisk(dataDisk)
            .withSize(VirtualMachineSizeTypes.STANDARD_D3_V2)
            .create();
```

このコードによって以下が行われます。   

0. 仮想マシンで使用する、50 GB のサイズの `Disk` Creatable をランダムな名前で定義します。
0. `azure.virtualMachines().define()..create()` チェーンを使って、Windows Server 2012 仮想マシンを作成します。 この API によって、前の手順で定義した `Disk` が仮想マシンと同時に作成されます。 仮想マシンには、`withNewDataDisk(10)` を通じて 10 GB のデータ ディスクがアタッチされます。

[Creatable<T> オブジェクト](java-sdk-azure-concepts.md#Creatables)を使うと、リソースのローカルな表現を定義しておいて、実際に他の Azure リソースから必要とされたタイミングで作成することができます。詳細については、リンク先のページを参照してください。

## <a name="stop-start-and-restart-a-virtual-machine"></a>仮想マシンの停止、開始、再起動

```java
// look up a virtual machine by its ID and then restart, stop, and start it
azureVM = azure.getVirtualMachine.getById(windowsVM.id());

azureVM.restart();
azureVM.powerOff();
azureVM.start();
```

`powerOff()` は、仮想マシン オペレーティング システムを停止するだけで、そのリソースの割り当ては解除しません。

## <a name="add-a-virtual-machine-to-an-existing-network"></a>既存のネットワークへの仮想マシンの追加

```java
// Get the virtual network the current virtual machine is using
Network network = windowsVM.getPrimaryNetworkInterface().primaryIPConfiguration().getNetwork();

// Create a Linux VM in the same subnet
VirtualMachine linuxVM = azure.virtualMachines().define(linuxVmName)
           .withRegion(region)
           .withExistingResourceGroup(rgName)
           .withExistingPrimaryNetwork(network)
           .withSubnet("subnet1") // default subnet name when no name specified at creation
           .withPrimaryPrivateIPAddressDynamic()
           .withoutPrimaryPublicIPAddress()
           .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_16_04_LTS)
           .withRootUsername(userName)
           .withRootPassword(password)
           .withSize(VirtualMachineSizeTypes.STANDARD_D3_V2)
           .create();
```

Windows VM ではなく Linux VM を定義するには、`withPopularLinuxImage` を使用します。


## <a name="list-virtual-machines"></a>仮想マシンの列挙

```java
// get a list of VMs in the same resource group as an existing VM
String resourceGroupName = windowsVM.resourceGroupName();
PagedList<VirtualMachine> resourceGroupVMs = azure.virtualMachines()
        .listByResourceGroup(resourceGroupName); 

// for each vitual machine in the resource group, log their name and plan
for (VirtualMachine virtualMachine : azure.virtualMachines().listByResourceGroup(resourceGroupName)) {
    System.out.println("VM " + virtualMachine.computerName() + 
        " has plan " + virtualMachine.plan());
}
```

`azure.virtualMachines().list()` を使ってサブスクリプションのすべての仮想マシンを列挙し、リソース グループごとに、`tags()` から返された Map を反復処理しながら、タグ付けされた仮想マシンのコレクションを管理します。

## <a name="update-a-virtual-machine"></a>仮想マシンの更新

```java
// add a 10GB data disk to the virtual machine
windowsVM.update()
     .withNewDataDisk(10)
     .apply();
```

`update()...apply()` を使用して仮想マシンの構成を更新します。`define()...create()` で仮想マシンを構成するときに使用したものと同じメソッドを使用しています。

## <a name="delete-a-virtual-machine"></a>仮想マシンの削除

```java
// delete by ID if you already are working with the VM object
azure.virtualMachines().deleteById(windowsVM.id());

// delete by resource group and name
azure.virtualMachines().deleteByResourceGroup(rgName,windowsVmName);
```

## <a name="sample-explanation"></a>サンプルの説明

[サンプル コード](https://github.com/Azure-Samples/compute-java-manage-vm/blob/master/src/main/java/com/microsoft/azure/management/compute/samples/ManageVirtualMachine.java)では、50 GB のデータ ディスクを使って Windows 仮想マシンを作成します。 その後、10 GB のデータ ディスクを別途作成し、それをこの Windows 仮想マシンにアタッチします。
そのうえで、この Windows 仮想マシンと同じ仮想ネットワークに Linux 仮想マシンを作成します。

サンプルでは、この 2 つの仮想マシンについての情報をログに記録します。どちらのログも完了前に削除されます。

| サンプルで使われているクラス | メモ
|-------|-------|
| [VirtualMachine](/java/api/com.microsoft.azure.management.compute.virtualmachine) | 仮想マシンのプロパティを照会したり、仮想マシンの状態を管理したりすることができます。 `azure.virtualMachines().list()` を使ってリスト形式で取得するか、`azure.virtualMachines().getByResourceGroup()` で名前または ID を使って取得します。
| [VirtualMachineSizeTypes](/java/api/com.microsoft.azure.management.compute.virtualmachinesizetypes) | [仮想マシン サイズのオプション](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)に対応した静的な値を保持するクラスです。VM に割り当てるリソースを `withSize()`メソッドで定義する際に使用します。
| [Disk](/java/api/com.microsoft.azure.management.compute.disk) | ディスクを定義する際、データを格納するためのディスクは `withData()` で作成し、オペレーティング システム イメージを格納するためのディスクは、`withLinux` または `withWindows` のどちらか適切なメソッドで作成します。 仮想マシンへのディスクのアタッチは、作成時に (`using withNewDataDisk` または `withExistingDataDisk`) 行うか、作成後に VirtualMachine オブジェクトの `update()..apply()` で行います。
| [DiskSkuTypes](/java/api/com.microsoft.azure.management.compute.diskskutypes) | Standard または [Premium](/azure/storage/storage-premium-storage) のストレージ プランでディスクを定義するための静的な値を保持するクラスです。
| [KnownLinuxVirtualMachineImage](/java/api/com.microsoft.azure.management.compute.knownlinuxvirtualmachineimage) | 仮想マシンを定義するときに `withPopularLinuxImage()` メソッドで使用する一連の Linux 仮想マシン オプションを保持するクラスです。
| [KnownWindowsVirtualMachineImage](/java/api/com.microsoft.azure.management.compute.knownwindowsvirtualmachineimage) | 仮想マシンを定義するときに `withPopularWindowsImage()` メソッドで使用する一連の Windows 仮想マシン イメージ オプションを保持するクラスです。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [next-steps](includes/java-next-steps.md)]