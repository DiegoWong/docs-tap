# Accepting Tanzu Application Platform EULAs, installing Cluster Essentials and the Tanzu CLI

This topic describes how to:

  * [Accept Tanzu Application Platform EULAs](#accept-eulas)
  * [Set the Kubernetes cluster context](#cluster-context)
  * [Install Cluster Essentials for Tanzu](#tanzu-cluster-essentials)
  * [Install or update the Tanzu CLI and plug-ins](#cli-and-plugin)


## <a id='accept-eulas'></a> Accept the End User License Agreements  

Before downloading and installing Tanzu Application Platform packages, you must accept the
End User License Agreements (EULAs) as follows:

1. Sign in to [VMware Tanzu Network](https://network.tanzu.vmware.com).

1. Accept or confirm that you have accepted the EULAs for each of the following:

    - [Cluster Essentials for VMware Tanzu](https://network.tanzu.vmware.com/products/tanzu-cluster-essentials/#/releases/1011100)
    - [Tanzu Application Platform](https://network.tanzu.vmware.com/products/tanzu-application-platform/)
    - Tanzu Build Service associated components:
        - [Tanzu Build Service Dependencies](https://network.tanzu.vmware.com/products/tbs-dependencies/)
        - [Buildpacks for VMware Tanzu](https://network.tanzu.vmware.com/products/tanzu-buildpacks-suite)
        - [Stacks for VMware Tanzu](https://network.tanzu.vmware.com/products/tanzu-stacks-suite)


### <a id='accept-tap-eula'></a> Example of accepting the Tanzu Application Platform EULA

To accept the Tanzu Application Platform EULA:

1. Go to [Tanzu Application Platform](https://network.tanzu.vmware.com/products/tanzu-application-platform/).

1. Select the ***Click here to sign the EULA*** link in the yellow warning box under the release drop-down menu. If the yellow warning box is not visible, the EULA has already been accepted.

    ![Screenshot of a VMware Tanzu Network download page.](images/install-tanzu-cli-eulas1.png)

1. Select ***Agree*** in the bottom-right of the dialog box as seen in the following screenshot.

    ![Screenshot of a dialog box inviting the reader to accept the EULA. The AGREE button is framed.](images/install-tanzu-cli-eulas2.png)

This example shows that you have now accepted the EULAs for Tanzu Application Platform. In addition, you must accept the EULAs for Cluster Essentials for VMware Tanzu and for Tanzu Build Services and its associated components as stated above.

## <a id='install-tanzu-cli'></a> Installing the Tanzu CLI

This document describes how to [Set Kubernetes cluster context](#cluster-context),
[Install Cluster Essentials for Tanzu](#tanzu-cluster-essentials),
and [Install or Update the Tanzu CLI and plug-ins](#cli-and-plugin) for Tanzu Application Platform:

## <a id='cluster-context'></a> Set the Kubernetes cluster context

To set the Kubernetes cluster context:

1. List the existing contexts by running:

    ```console
    kubectl config get-contexts
    ```

    For example:

    ```console
    $ kubectl config get-contexts
    CURRENT   NAME                                CLUSTER           AUTHINFO                                NAMESPACE
            aks-repo-trial                      aks-repo-trial    clusterUser_aks-rg-01_aks-repo-trial
    *       aks-tap-cluster                     aks-tap-cluster   clusterUser_aks-rg-01_aks-tap-cluster
    ```

1. Set the context to the cluster that you want to use for the Tanzu Application Platform packages installation by running:

    ```console
    kubectl config use-context CONTEXT
    ```

    Where `CONTEXT` is the cluster that you want to use. For example, `aks-tap-cluster`.

    For example:

    ```console
    $ kubectl config use-context aks-tap-cluster
    Switched to context "aks-tap-cluster".
    ```


## <a id='tanzu-cluster-essentials'></a> Install Cluster Essentials for Tanzu

[Cluster Essentials for VMware Tanzu](https://docs.vmware.com/en/Cluster-Essentials-for-VMware-Tanzu/index.html)
simplifies the process of installing the open-source [Carvel](https://carvel.dev) tools on your cluster.
It includes a script to download and install supported versions of kapp-controller and
secretgen-controller on the target cluster.
It also installs the kapp, imgpkg, ytt, and kbld CLIs on your local machine.
Currently, Cluster Essentials only supports macOS and Linux.

If you are using a VMware Tanzu Kubernetes Grid cluster, you don't need to install Cluster Essentials
because the contents of Cluster Essentials are already installed on your cluster.

To install Cluster Essentials, see [Deploying Cluster Essentials](https://docs.vmware.com/en/Cluster-Essentials-for-VMware-Tanzu/1.1/cluster-essentials/GUID-deploy.html).


## <a id='cli-and-plugin'></a> Install or update the Tanzu CLI and plug-ins

You use the Tanzu CLI and plug-ins to install and use the Tanzu Application Platform functions
and features.

To install the Tanzu CLI and plug-ins:

1. Sign in to [VMware Tanzu Network](https://network.tanzu.vmware.com).
1. Go to the [TAP Release v1.1.0 > tanzu-cli-v0.11.2 download tile page](https://network.pivotal.io/products/tanzu-application-platform/#/releases/1078790/file_groups/7867).
1. Download the Tanzu framework bundle for your operating system.
1. (Optional) If an earlier upgrade attempt failed, it might be best to uninstall the previous version of the Tanzu CLI and associated plug-ins and files. To do so, see [Remove Tanzu CLI, plug-ins, and associated files](uninstall.html#remove-tanzu-cli).

For Windows installation instructions, see [Install Tanzu CLI: Windows](#windows-tanzu-cli).

### <a id='linux-mac-tanzu-cli'></a> Install Tanzu CLI: Linux or macOS

1. Create a `$HOME/tanzu` directory on your machine.
1. Unpack the downloaded TAR file into the `$HOME/tanzu` directory by running:  

   * **For Linux:**

     ```console
     tar -xvf tanzu-framework-linux-amd64.tar -C $HOME/tanzu
     ```

   * **For macOS:**

     ```console
     tar -xvf tanzu-framework-darwin-amd64.tar -C $HOME/tanzu
     ```

1. Set the environment variable `TANZU_CLI_NO_INIT` to `true` to ensure the local downloaded versions of
the CLI core and plug-ins are installed by running:

    ```console
    export TANZU_CLI_NO_INIT=true
    ```

1. Install or update the CLI core by running:

   * **For Linux:**

     ```console
     cd $HOME/tanzu
     VERSION=v0.11.2 sudo install cli/core/$VERSION/tanzu-core-linux_amd64 /usr/local/bin/tanzu
     ```

   * **For macOS:**

     ```console
     cd $HOME/tanzu
     VERSION=v0.11.2 install cli/core/$VERSION/tanzu-core-darwin_amd64 /usr/local/bin/tanzu
     ```

1. Confirm the installation by running:

    ```console
    tanzu version
    ```

    Expected outcome:

    ```console
    version: v0.11.2
    ...
    ```

1. Proceed to [Install/Update Tanzu CLI plug-ins](#cli-plugin-install)

### <a id='windows-tanzu-cli'></a> Install Tanzu CLI: Windows

1. Open the Windows file browser.

1. Create a `Program Files\tanzu` directory on your machine.

1. From the `Downloads` directory, right-click the `tanzu-framework-windows.amd64.zip` file, select the **Extract All...** menu option, enter `C:\Program files\tanzu` in the **Files will be extracted to this folder:** text box, and click the **Extract**.

1. From the `Program Files\tanzu` directory, move and rename the executable file from

    ```console
    Program Files\tanzu\cli\core\v0.11.2\tanzu-core-windows_amd64.exe
    ```

    to

    ```console
    tar -xvf tanzu-framework-darwin-amd64.tar -C $HOME/tanzu
    ```

1. From the `Program Files` directory, right-click the `tanzu` directory and select **Properties > Security**.

1. Ensure that your user account has the **Full Control** permission.

1. Use Windows Search to search for `env`, select **Edit the system environment variables**, click **Environment Variables** on the bottom right of the dialogue box.

1. Find and select the **Path** row under **System variables**, click **Edit**.

1. Click **New**, enter the path value, click **OK**.

    >**Note:** The path value must not include **tanzu.exe**. For example, `C:\Program Files\tanzu`.

1. Click **New** below the **System Variables** section, add a new environmental variable named `TANZU_CLI_NO_INIT` with a variable value `true`, click **OK**.

1. Use Windows Search to search for `cmd`, select **Command Prompt** to open the command line terminal.

1. Verify the Tanzu CLI installation by running:

    ```console
    tanzu version
    ```

    Expected outcome:

    ```console
    version: v0.11.2
    ...
    ```

1. Proceed to [Install/Update Tanzu CLI plug-ins](#cli-plugin-install)


## <a id='cli-plugin-install'></a> Install/Update Tanzu CLI plug-ins

To install or update Tanzu CLI plug-ins from your terminal, follow these steps:

1. Install plug-ins from the `$HOME/tanzu` directory (if on Linux or macOS) or `Program Files\tanzu` directory (if on Windows) by running:

    ```console
    tanzu plugin install --local cli all
    ```

1. Verify that you installed the plug-ins by running:

    ```console
    tanzu plugin list
    ```

    Expected outcome:

    ```console
    NAME                DESCRIPTION                                                                   SCOPE       DISCOVERY             VERSION      STATUS
    login               Login to the platform                                                         Standalone  default               v0.11.1      not installed
    management-cluster  Kubernetes management-cluster operations                                      Standalone  default               v0.11.1      not installed
    package             Tanzu package management                                                      Standalone  default               v0.11.1      installed
    pinniped-auth       Pinniped authentication operations (usually not directly invoked)             Standalone  default               v0.11.1      not installed
    secret              Tanzu secret management                                                       Standalone  default               v0.11.1      installed
    services            Discover Service Types, Service Instances and manage Resource Claims (ALPHA)  Standalone                        v0.2.0-rc.1  installed
    accelerator         Manage accelerators in a Kubernetes cluster                                   Standalone                        v1.1.0       installed
    apps                Applications on Kubernetes                                                    Standalone                        v0.5.0       installed
    insight             post & query image, package, source, and vulnerability data                   Standalone                        v1.1.0       installed
    ```

> **Note:** Currently, `insight` plug-in only supports macOS and Linux.

You can now proceed with [installing the Tanzu Application Platform Package and Profiles](install.html).
