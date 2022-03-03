# Troubleshoot Tanzu Application Platform GUI

This topic describes troubleshooting information for problems with installing Tanzu Application Platform GUI.

## <a id='catalog-not-found'></a> Catalog not found
### Symptom

When you pull up the Tanzu Application Platform UI, you get the error `Catalog Not Found`.

### Cause

This issue is caused because the catalog plug-in can't read the Git location of your catalog definition files.

### Solution

There are a number of potential causes:

1. Make sure you've either built your own [Backstage Compatible](http://backstage.io) catalog,
or that you've downloaded one of the Tanzu Application Platform GUI catalogs from the Tanzu Network
(the same place you download Tanzu Application Platform from).
Make sure you define the catalog in the values file that you input as part of installation.
If you need to update this location, you can change the definition file.
Change either the Tanzu Application Platform profile file if you used the profile method to install,
or change the standalone Tanzu Application Platform GUI values file if you're only installing that package on its own.

  ```
      namespace: tap-gui
      service_type: <SERVICE-TYPE>
      app_config:
        catalog:
          locations:
            - type: url
              target: https://<GIT-CATALOG-URL>/catalog-info.yaml
  ```

2. You need to make sure that you provide the proper integration information for the Git location you specified above.

  ```
      namespace: tap-gui
      service_type: <SERVICE-TYPE>
      app_config:
        app:
          baseUrl: https://<EXTERNAL-IP>:<PORT>
        integrations:
          gitlab: # Other integrations available
            - host: <GITLAB-HOST>
              apiBaseUrl: https://<GITLAB-URL>/api/v4
              token: <GITLAB-TOKEN>
  ```

You can substitute for other integrations as defined in the [Backstage documentation](https://backstage.io/docs/integrations/)

## <a id='updating-tap-gui-values'></a> Issues updating the values file
### Symptom

When you need to update the configuration of Tanzu Application Platform GUI (either by using the profiles method or as a standalone package install), how can you tell whether the configuration has reloaded?

### Suggestions

1. Check the logs of the Pods and verify whether the configuration reloaded by running `kubectl get pods -n tap-gui`. For example:

    ```
    $ kubectl get pods -n tap-gui
    NAME                      READY   STATUS    RESTARTS   AGE
    server-6b9ff657bd-hllq9   1/1     Running   0          13m

    $ kubectl logs server-6b9ff657bd-hllq9 -n tap-gui

    Find this line:
    2021-10-29T15:08:49.725Z backstage info Reloaded config from app-config.yaml, app-config.yaml
    ```

2. Try deleting and re-instantiating the Pod by running:

    ```
    kubectl delete pod -l app=backstage -n tap-gui
    ```

>**Note:** `tap-gui` Pods aren't stateful. `config` is held in ConfigMaps, Git catalog, or Secrets.

## <a id='tap-gui-logs'></a> Pull logs from Tanzu Application Platform GUI

### Symptom

You have problems with Tanzu Application Platform GUI, such as `Catalog: Not Found`,
and you need to learn more about the problem in order to diagnose it.

### Solution

Get timestamped logs from the running pod and review the logs:

1. Pull the logs using the pod label:

    ```
    kubectl logs -l app=backstage -n tap-gui
    ```

2. Review the logs.
