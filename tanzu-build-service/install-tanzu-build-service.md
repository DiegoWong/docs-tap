# Install Tanzu Build Service

This document describes how to install Tanzu Build Service
from the Tanzu Application Platform package repository by using the Tanzu CLI.

>**Note:** Use the instructions on this page if you do not want to use a profile to install packages.
Both the full and light profiles include Tanzu Build Service.
For more information about profiles, see [Installing the Tanzu Application Platform Package and Profiles](../install.md).

>**Note:** The following procedure might not include some configurations required for your specific environment.
>For more advanced details on installing Tanzu Build Service, see
>[Installing Tanzu Build Service](https://docs.vmware.com/en/VMware-Tanzu-Build-Service/index.html).

## <a id='tbs-prereqs'></a> Prerequisites

Before installing Tanzu Build Service:

- Complete all prerequisites to install Tanzu Application Platform. For more information, see [Installing the Tanzu CLI](../install-general.md).
- You must have access to a Docker registry that Tanzu Build Service can use to create builder images.
Approximately 10&nbsp;GB of registry space is required when using the full descriptor.
- Your Docker registry must be accessible with username and password credentials.

## <a id='tbs-tcli-install'></a> Install Tanzu Build Service by using the Tanzu CLI

To install Tanzu Build Service by using the Tanzu CLI:

1. List version information for the package by running:

    ```
    tanzu package available list buildservice.tanzu.vmware.com --namespace tap-install
    ```

    For example:

    ```
    $ tanzu package available list buildservice.tanzu.vmware.com --namespace tap-install
    - Retrieving package versions for buildservice.tanzu.vmware.com...
      NAME                           VERSION  RELEASED-AT
      buildservice.tanzu.vmware.com  1.4.2    2021-12-17T00:00:00Z
    ```

1. (Optional) To make changes to the default installation settings, run:

    ```
    tanzu package available get buildservice.tanzu.vmware.com/VERSION-NUMBER --values-schema --namespace tap-install
    ```

    Where `VERSION-NUMBER` is the version of the package listed in step 1 above.

    For example:

    ```
    $ tanzu package available get buildservice.tanzu.vmware.com/1.4.2 --values-schema --namespace tap-install
    ```




1. Gather the values schema by running:

    ```
    tanzu package available get buildservice.tanzu.vmware.com/1.4.2 --values-schema --namespace tap-install
    ```

    For example:

    ```
    $ tanzu package available get buildservice.tanzu.vmware.com/1.4.2 --values-schema --namespace tap-install
    | Retrieving package details for buildservice.tanzu.vmware.com/1.4.2...
      KEY                                  DEFAULT  TYPE    DESCRIPTION
      kp_default_repository                <nil>    string  Docker repository used for builder images and dependencies
      kp_default_repository_password       <nil>    string  Username for kp_default_repository
      kp_default_repository_username       <nil>    string  Password for kp_default_repository
      tanzunet_username                    <nil>    string  Optional: Tanzunet registry username required for dependency import at install.
      tanzunet_password                    <nil>    string  Optional: Tanzunet registry password required for dependency import at install.
      descriptor_name                      <nil>    string  Name of descriptor to import (required for dependency updater feature)
      descriptor_version                   <nil>    string  Optional: Version of descriptor to use during install. This will override the version installed by default.
      enable_automatic_dependency_updates  <nil>    bool    Optional: Allow automatic import of new dependency updates from Tanzunet
      ca_cert_data                         <nil>    string  Optional: TBS registry ca certificate
      http_proxy                           <nil>    string  Optional: the HTTP proxy to use for network traffic.
      https_proxy                          <nil>    string  Optional: the HTTPS proxy to use for network traffic.
      no_proxy                             <nil>    string  Optional: A comma-separated list of hostnames, IP addresses, or IP ranges in CIDR format that should not use a proxy.
    ```

1. Create a `tbs-values.yaml` file.

    ```
    ---
    kp_default_repository: REPOSITORY
    kp_default_repository_username: REGISTRY-USERNAME
    kp_default_repository_password: REGISTRY-PASSWORD
    tanzunet_username: TANZUNET-USERNAME
    tanzunet_password: TANZUNET-PASSWORD
    descriptor_name: DESCRIPTOR-NAME
    enable_automatic_dependency_updates: true
    ```
    Where:

    - `REPOSITORY` is the fully qualified path to the TBS repository.
    This path must be writable. For example:

        * Docker Hub: `my-dockerhub-account/build-service`
        * Google Container Registry: `gcr.io/my-project/build-service`
        * Artifactory: `artifactory.com/my-project/build-service`
        * Harbor: `harbor.io/my-project/build-service`

    - `REGISTRY-USERNAME` and `REGISTRY-PASSWORD` are the user name and password for the registry. The install requires a `kp_default_repository_username` and `kp_default_repository_password` to write to the repository location.
    - `TANZUNET-USERNAME` and `TANZUNET-PASSWORD` are the email address and password that you use to log in to Tanzu Network. The Tanzu Network credentials allow for configuration of the Dependencies Updater. This resource accesses and installs the build dependencies (buildpacks and stacks) Tanzu Build Service needs on your cluster. It also keeps these dependencies up to date as new versions are released on Tanzu Network.
    - `DESCRIPTOR-NAME` is the name of the descriptor to import automatically. Current available options at time of release:
        - `tap-1.0.0-full` contains all dependencies and is for production use.
        - `tap-1.0.0-lite` smaller footprint used for speeding up installs. Requires Internet access on the cluster.

    >**Note:** Using the `tbs-values.yaml` configuration,
    >`enable_automatic_dependency_updates: false` can be used to pause the automatic update of
    >Build Service dependencies.

1. Install the package by running:

    ```
    tanzu package install tbs -p buildservice.tanzu.vmware.com -v 1.4.2 -n tap-install -f tbs-values.yaml --poll-timeout 30m
    ```

    For example:

    ```
    $ tanzu package install tbs -p buildservice.tanzu.vmware.com -v 1.4.2 -n tap-install -f tbs-values.yaml --poll-timeout 30m
    | Installing package 'buildservice.tanzu.vmware.com'
    | Getting namespace 'tap-install'
    | Getting package metadata for 'buildservice.tanzu.vmware.com'
    | Creating service account 'tbs-tap-install-sa'
    | Creating cluster admin role 'tbs-tap-install-cluster-role'
    | Creating cluster role binding 'tbs-tap-install-cluster-rolebinding'
    | Creating secret 'tbs-tap-install-values'
    - Creating package resource
    - Package install status: Reconciling

     Added installed package 'tbs' in namespace 'tap-install'
    ```

    >**Note:** Installing the `buildservice.tanzu.vmware.com` package with Tanzu Network credentials
    >automatically relocates buildpack dependencies to your cluster. This install process can take
    >some time and the `--poll-timeout` flag increases the timeout duration.
    >Using the `lite` descriptor speeds this up significantly.
    >If the command times out, periodically run the installation verification step provided in the
    >following optional step. Image relocation continues in the background.

1. (Optional) Verify the clusterbuilders created by the Tanzu Build Service install by running:

    ```
    tanzu package installed get tbs -n tap-install
    ```