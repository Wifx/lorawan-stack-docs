---
title: "Migrate Active Device Session"
description: ""
---

This section explains how to migrate end devices from {{% tts %}} Community Edition to {{% tts %}} Cloud while persisting sessions that were already established between those devices and {{% tts %}} Community Edition.

<!--more-->

{{< note >}} Keep in mind that [migrating devices without persisting active sessions]({{< ref "/getting-started/migrating/migrating-between-tts-distributions/establish-new-session" >}}) is highly recommended. {{</ note >}}

Read the instructions below to migrate your OTAA or ABP devices.

### Note on Gateway Migration

Even though they are both connected to [Packet Broker]({{< ref "/getting-started/packet-broker" >}}), {{% tts %}} Community Edition and {{% tts %}} Cloud use different DevAddr blocks.

When an active device session is migrated from {{% tts %}} Community Edition to {{% tts %}} Cloud, the DevAddr that the device was assigned with (OTAA) or programmed with (ABP) when it joined {{% tts %}} Community Edition will be preserved. Since Packet Broker routes traffic according to the DevAddr blocks, in case of migrating an active session to {{% tts %}} Cloud it won't be able to route your device's traffic properly, because your device still has its Community Edition-related DevAddr.

Hence, to successfully migrate an active device session from {{% tts %}} Community Edition to {{% tts %}} Cloud, you also need to migrate your gateway to {{% tts %}} Cloud. See instructions for [Migrating Gateways]({{< ref "/getting-started/migrating/gateway-migration" >}}). The ideal scenario would be to migrate your gateway and your device simultaneously, in order to not loose any traffic.

## Migrate OTAA and ABP Devices

Since migrating an active session implies migrating a large number of parameters that cannot be configured manually, it is possible to do it only using the [migration tool]({{< ref "/getting-started/migrating/migration-tool">}}) or the [CLI]({{< ref "/getting-started/cli" >}}).

{{< tabs/container "Migration tool" "CLI">}}

{{< tabs/tab "Migration tool" >}}

For detailed instructions on how to configure the migration tool before exporting your device and how to adjust the following command for migrating multiple devices or whole applications, head over to [Export Devices from {{% tts %}}]({{< ref "/getting-started/migrating/migration-tool/export-from-tts" >}}).

To export device using the [migration tool]({{< ref "/getting-started/migrating/migration-tool" >}}) with its active session, use the following command:

```bash
ttn-lw-migrate device --source ttnv3 'my-device' > devices.json
```

The migration tool retains session of extracted devices by default.

Before your device's session is imported in {{% tts %}} Cloud, you need to completely delete your device from {{% tts %}} Community Edition to prevent conflicts. To do it, use the following command:

```bash
ttn-lw-migrate device --source ttnv3 'my-device' \
    --ttnv3.delete-source-device
```

Next, you need to import the `devices.json` file in your {{% tts %}} Cloud application. See instructions on how to [Import End Devices in {{% tts %}}]({{< ref "/getting-started/migrating/import-devices" >}}). Keep in mind that if you are using the CLI to import devices, you first have to configure it to connect to {{% tts %}} Cloud. See [Configuring the CLI]({{< ref "/getting-started/cli/configuring-cli" >}}) guide for instructions.

{{</ tabs/tab >}}

{{< tabs/tab "CLI" >}}

Migrating an active OTAA or ABP device session means the existing session that was established between the device and {{% tts %}} Community Edition will just be transferred to {{% tts %}} Cloud. To migrate active device sessions from {{% tts %}} Community Edition to {{% tts %}} Cloud using the CLI, follow the steps described below.

First, configure your CLI to connect to {{% tts %}} Community Edition. See [Configuring the CLI]({{< ref "/getting-started/cli/configuring-cli" >}}) guide for instructions. Make sure you also perform a [Login with the CLI]({{< ref "/getting-started/cli/login" >}}) to {{% tts %}} Community Edition.

{{< note >}} We recommend to use the latest version of the CLI. Instructions for upgrading the CLI if you already have it installed are available in the [Installing the CLI]({{< ref "/getting-started/cli/installing-cli" >}}) guide. {{</ note >}}

To export your OTAA device's session from {{% tts %}} Community Edition:

```bash
ttn-lw-cli end-devices get --application-id <app-id> --device-id <device-id> \
    --name \
    --description \
    --lorawan-version \
    --lorawan-phy-version \
    --frequency-plan-id \
    --supports-join \
    --root-keys \
    --mac-settings \
    --mac-state \
    --session > device-session.json
```

To export your ABP device's session from {{% tts %}} Community Edition is just slightly different:

```bash
ttn-lw-cli end-devices get --application-id <app-id> --device-id <device-id> \
    --name \
    --description \
    --lorawan-version \
    --lorawan-phy-version \
    --frequency-plan-id \
    --supports-join \
    --mac-settings \
    --mac-state \
    --session > device-session.json
```

The command above will export your device's session to the `device-session.json` file in the current folder. Open the file with a text editor and remove the following fields: `join_server_address`, `network_server_address` and `application_server_address`.

Before your device's session is imported in {{% tts %}} Cloud, you need to completely delete your device from {{% tts %}} Community Edition to prevent conflicts. 

Next, you need to import the `devices.json` file in your {{% tts %}} Cloud application. See instructions on how to [Import End Devices in {{% tts %}}]({{< ref "/getting-started/migrating/import-devices" >}}). Keep in mind that if you are using the CLI to import devices, you first have to configure it to connect to {{% tts %}} Cloud. Again, see [Configuring the CLI]({{< ref "/getting-started/cli/configuring-cli" >}}) guide for instructions.

{{</ tabs/tab >}}

{{</ tabs/container >}}

When your device's session is finally imported in {{% tts %}} Cloud, assuming that your gateway was also migrated to {{% tts %}} Cloud, you will see uplinks arriving from your device.
