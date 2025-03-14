---
title: Configure Autopilot profiles
description: Learn how to configure device profiles for Windows Autopilot deployment.
ms.technology: itpro-deploy
ms.prod: windows-client
ms.localizationpriority: medium
author: frankroj
ms.author: frankroj
ms.reviewer: jubaptis
manager: aaroncz
ms.date: 09/13/2023
ms.collection: 
  - M365-modern-desktop
  - highpri
  - tier1
ms.topic: how-to
appliesto:
  - ✅ <a href="https://learn.microsoft.com/windows/release-health/supported-versions-windows-client" target="_blank">Windows 11</a>
  - ✅ <a href="https://learn.microsoft.com/windows/release-health/supported-versions-windows-client" target="_blank">Windows 10</a>
  - ✅ <a href="https://learn.microsoft.com/hololens/hololens-release-notes" target="_blank">Windows Holographic</a>  
---

# Configure Autopilot profiles

After you have [created a device group](enrollment-autopilot.md), you can apply a Windows Autopilot deployment profile to each device in the group.  Deployment profiles determine the deployment mode, and customize the OOBE for your end users.

You can create Autopilot profiles through:

1. [Microsoft Admin Center](https://admin.microsoft.com/)
1. [Intune admin center](https://go.microsoft.com/fwlink/?linkid=2109431)
1. [Intune graph](/graph/api/resources/intune-graph-overview)

For Intune managed devices, pre-provisioning, self-deploying, and co-management profiles can only be created and assigned in Intune.

## Create an Autopilot deployment profile

Autopilot deployment profiles are used to configure the Autopilot devices. You can create up to 350 profiles per tenant.

1. In the [Microsoft Intune admin center](https://go.microsoft.com/fwlink/?linkid=2109431), choose **Devices** > **Windows** > **Windows enrollment** > **Deployment Profiles** > **Create Profile** > **Windows PC** or **HoloLens**. This article explains how to set up Autopilot for Windows PC. For more information about Autopilot and HoloLens, see [Windows Autopilot for HoloLens 2](/hololens/hololens2-autopilot).

1. On the **Basics** page, type a **Name** and optional **Description**.

    :::image type="content" source="images/create-profile-basics.png" alt-text="Screenshot of Basics page.":::

1. If you want all devices in the assigned groups to automatically register to Autopilot, set **Convert all targeted devices to Autopilot** to **Yes**. All corporate owned, non-Autopilot devices in assigned groups register with the Autopilot deployment service. Personally owned devices aren't registered to Autopilot. Allow 48 hours for the registration to be processed. When the device is unenrolled and reset, Autopilot enrolls it again. After a device is registered in this way, disabling this setting or removing the profile assignment won't remove the device from the Autopilot deployment service. You must instead [remove the device directly](add-devices.md#delete-autopilot-devices).

    > [!NOTE]
    >
    > Using the setting **Converting all targeted devices to Autopilot** doesn't automatically convert existing hybrid Azure AD device in the assigned group(s) into an Azure AD device. The setting only registers the devices in the assigned group(s) for the Autopilot service.

1. Select **Next**.

1. On the **Out-of-box experience (OOBE)** page, for **Deployment mode**, choose one of these two options:
    - **User-driven**: Devices with this profile are associated with the user enrolling the device. User credentials are required to enroll the device.
    - **Self-deploying (preview)**: Devices with this profile aren't associated with the user enrolling the device. User credentials aren't required to enroll the device. When a device has no user associated with it, user-based compliance policies don't apply to it. When self-deploying mode is used, only compliance policies targeting the device are applied.

    :::image type="content" source="images/create-profile-out-of-box.png" alt-text="Screenshot of OOBE page.":::

    > [!NOTE]
    >
    > Options that are dimmed or shaded in the selected deployment mode aren't currently supported.

1. In the **Join to Azure AD as** box, choose **Azure AD joined**.

1. Configure the following options:
    - **Microsoft Software License Terms**: Choose if you want to show the EULA to users.

    - **Privacy settings**: Choose if you want to show privacy settings to users.

      > [!IMPORTANT]
      >
      > The default value for the Diagnostic Data setting is set to Full during the out-of-box experience. For more information, see [Windows Diagnostics Data](/windows/privacy/windows-diagnostic-data)

    - **Hide change account options**: Choose **Hide** to prevent change account options from displaying on the company sign-in and domain error pages. This option requires [company branding to be configured in Azure Active Directory](/azure/active-directory/fundamentals/customize-branding).

    - **User account type**: Choose the user's account type (**Administrator** or **Standard** user). We allow the user joining the device to be a local Administrator by adding them to the local Admin group. We don't enable the user as the default administrator on the device.

    - **Allow pre-provisioned deployment** ([Prerequisites](pre-provision.md#prerequisites)): Choose **Yes** to allow pre-provisioning support.

      > [!NOTE]
      >
      > The **pre-provision** feature use to be known as **white glove**. References to **White Glove OOBE** in Intune refer to the Autopilot [pre-provisioning](pre-provision.md) process.
      >
      > When setting **Allow pre-provisioned deployment** to **No**, it's still possible to press the Windows key five times during OOBE to invoke pre-provisioning and progress down that path. However, Intune enforces this setting and a pre-provisioning failure with error code 0x80180005 occurs.

    - **Language (Region)**: Choose the language to use for the device. This option is available in all Deployment modes.
    
    - **Automatically configure keyboard**: If a **Language (Region)** is selected, choose **Yes** to skip the keyboard selection page. This option is available in all Deployment modes.

        > [!NOTE]
        >
        > Language and keyboard settings requires ethernet connectivity. Wi-fi connectivity isn't supported because of the requirement to choose a language, locale, and keyboard to make that Wi-fi connection.

    - **Apply device name template** (requires Azure AD join type): Choose **Yes** to create a template to use when naming a device during enrollment. Names must be 15 characters or less, and can have letters, numbers, and hyphens. Names can't be all numbers. Use the [%SERIAL% macro](/windows/client-management/mdm/accounts-csp) to add a hardware-specific serial number. Or, use the [%RAND:x% macro](/windows/client-management/mdm/accounts-csp) to add a random string of numbers, where x equals the number of digits to add. You can only provide a prefix for hybrid devices in a [domain join profile](./windows-autopilot-hybrid.md#create-and-assign-a-domain-join-profile).
  
1. Select **Next**.

1. On the **Assignments** page, choose **Selected groups** for **Assign to**.

    :::image type="content" source="images/create-profile-assignments.png" alt-text="Screenshot of Assignments page.":::

1. Choose **Select groups to include**, and choose the groups you want to include in this profile.

1. If you want to exclude any groups, choose **Select groups to exclude**, and choose the groups you want to exclude.

   > [!NOTE]
   >
   > When the assignment **All Devices** is used, exclusions aren't supported. Attempting to exclude groups while targeting to all devices may cause assignment problems and may require uploading device hashes again.

1. Select **Next**.

1. On the **Review + Create** page, choose **Create** to create the profile.

    :::image type="content" source="images/create-profile-review.png" alt-text="Screenshot of Review page.":::

> [!NOTE]
>
> Intune will periodically check for new devices in the assigned groups, and then begin the process of assigning profiles to those devices. Due to several different factors involved in the process of Autopilot profile assignment, an estimated time for the assignment can vary from scenario to scenario. These factors can include AAD groups, membership rules, hash of a device, Intune and Autopilot service, and internet connection. The assignment time will vary depending on all the factors and variables involved in a specific scenario.
>
> Before deploying a device, ensure that this process has completed. You can check under **Devices** > **Windows** > **Windows enrollment** > **Devices** (under **Windows Autopilot Deployment Program**) where you should see the profile status change from **Unassigned** to **Assigning** and finally to **Assigned**. Once the device is showing **Assigned**, open the properties of the device by selecting it, and then ensuring that **Date assigned** is populated. If **Date assigned** isn't yet populated, wait until it's populated before deploying the device.

## Edit an Autopilot deployment profile

After you've created an Autopilot deployment profile, you can edit certain parts of the deployment profile.

1. In the [Microsoft Intune admin center](https://go.microsoft.com/fwlink/?linkid=2109431), choose **Devices** > **Windows** > **Windows enrollment** > **Deployment profiles**.

1. Select the profile you would like to edit.

1. Select **Properties** on the left to change the name or description of the deployment profile. Select **Save** after you make changes.

1. Select **Settings** to make changes to the OOBE settings. Select **Save** after you make changes.

    > [!NOTE]
    >
    > Changes to the profile are applied to devices assigned to that profile. However, the updated profile won't be applied to a device that has already enrolled in Intune until after the device is reset and enrolled again.

If a device is registered in Autopilot and a profile isn't assigned, it receives the default Autopilot profile. If you don't want a device to go through Autopilot, you must remove the Autopilot registration.

## Alerts for Windows Autopilot unassigned devices
<!-- 163236 -->

Alerts show how many Autopilot program devices don't have Autopilot deployment profiles. Use the information in the alert to create profiles and assign them to the unassigned devices. When you select the alert, you see a full list of Windows Autopilot devices and detailed information about them.

To see alerts for unassigned devices, in the [Microsoft Intune admin center](https://go.microsoft.com/fwlink/?linkid=2109431), choose **Devices** > **Overview** > **Enrollment alerts** > **Unassigned devices**.

## Autopilot deployments report

You can see details on each device deployed through Windows Autopilot.
To see the report, go to the [Microsoft Intune admin center](https://go.microsoft.com/fwlink/?linkid=2109431), choose **Devices** > **Monitor** > **Autopilot deployments**.
The data is available for 30 days after deployment.

This report is in preview. Device deployment records are currently triggered only by new Intune enrollment events. Deployments that don't trigger a new Intune enrollment don't appear this report. This case includes any kind of reset that maintains enrollment and the user portion of Autopilot pre-provisioning.

## Autopilot profile tutorials

For a detailed tutorial on configuring and assigning a Windows Autopilot deployment profile for each of the Windows Autopilot scenarios using Intune, see the following articles:

- [User-driven Azure AD join: Create and assign user-driven Azure AD join Autopilot profile](tutorial/user-driven/azure-ad-join-autopilot-profile.md)
- [User-driven hybrid Azure AD join: Create and assign user-driven hybrid Azure AD join Autopilot profile](tutorial/user-driven/hybrid-azure-ad-join-autopilot-profile.md)
- [Pre-provision Azure AD join: Create and assign a pre-provisioned Azure AD join Autopilot profile](tutorial/pre-provisioning/azure-ad-join-autopilot-profile.md)
- [Pre-provision hybrid Azure AD join: Create and assign a pre-provisioned hybrid Azure AD join Autopilot profile](tutorial/pre-provisioning/hybrid-azure-ad-join-autopilot-profile.md)
- [Self-deploying mode: Create and assign self-deploying Autopilot profile](tutorial/self-deploying/self-deploying-autopilot-profile.md)

## Related articles

[Profile download](troubleshooting.md#profile-download)

[Registering devices](add-devices.md)
