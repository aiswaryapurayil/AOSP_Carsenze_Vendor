## Repo status 

project device/google/cuttlefish/               (*** NO BRANCH ***)
 -m     shared/device.mk
 --     shared/device_framework_matrix.xml
 --     shared/hardware.mk
 --     shared/sepolicy/vendor/carsenze.te
 -m     shared/sepolicy/vendor/file_contexts
 --     shared/sepolicy/vendor/hal_carsenze_default.te
 -m     shared/sepolicy/vendor/seapp_contexts
 -m     shared/sepolicy/vendor/service.te
 -m     shared/sepolicy/vendor/service_contexts

## Repo diff for reference

project device/google/cuttlefish/
diff --git a/shared/device.mk b/shared/device.mk
index fb60c3a95..8fd69c1a9 100644
--- a/shared/device.mk
+++ b/shared/device.mk
@@ -26,6 +26,9 @@ $(call inherit-product, $(SRC_TARGET_DIR)/product/userspace_reboot.mk)
 # Enforce generic ramdisk allow list
 $(call inherit-product, $(SRC_TARGET_DIR)/product/generic_ramdisk.mk)
 
+# Include carsenze hardware packages
+$(call inherit-product, device/google/cuttlefish/shared/hardware.mk)
+
 # Set Vendor SPL to match platform
 VENDOR_SECURITY_PATCH = $(PLATFORM_SECURITY_PATCH)
 
@@ -576,3 +579,4 @@ PRODUCT_CHECK_VENDOR_SEAPP_VIOLATIONS := true
 PRODUCT_CHECK_DEV_TYPE_VIOLATIONS := true
 
 TARGET_BOARD_FASTBOOT_INFO_FILE = device/google/cuttlefish/shared/fastboot-info.txt
+
diff --git a/shared/sepolicy/vendor/file_contexts b/shared/sepolicy/vendor/file_contexts
index f5a5cbec0..7f777d00b 100644
--- a/shared/sepolicy/vendor/file_contexts
+++ b/shared/sepolicy/vendor/file_contexts
@@ -118,6 +118,9 @@
 /vendor/bin/snapshot_hook_post_resume u:object_r:snapshot_hook_sh:s0
 /vendor/bin/snapshot_hook_pre_suspend u:object_r:snapshot_hook_sh:s0
 
+# carsenze hardware files
+/vendor/bin/hw/vendor\.hardware\.carsenze-service u:object_r:hal_carsenze_default_exec:s0
+
 /vendor/lib(64)?/hw/android\.hardware\.health@2\.0-impl-2\.1-cuttlefish\.so  u:object_r:same_process_hal_file:s0
 /vendor/lib(64)?/libcuttlefish_fs.so  u:object_r:same_process_hal_file:s0
 /vendor/lib(64)?/vsoc_lib.so  u:object_r:same_process_hal_file:s0
diff --git a/shared/sepolicy/vendor/seapp_contexts b/shared/sepolicy/vendor/seapp_contexts
index f2a116f0d..04146f219 100644
--- a/shared/sepolicy/vendor/seapp_contexts
+++ b/shared/sepolicy/vendor/seapp_contexts
@@ -1,2 +1,5 @@
 # GceService app
 user=_app isPrivApp=true seinfo=default name=com.android.google.gce.gceservice domain=gceservice type=app_data_file levelFrom=all
+
+# Carsenze
+user=_app seinfo=platform name=com.hardware domain=carsenze type=app_data_file levelFrom=all
diff --git a/shared/sepolicy/vendor/service.te b/shared/sepolicy/vendor/service.te
index 19f43f14e..01632e56b 100644
--- a/shared/sepolicy/vendor/service.te
+++ b/shared/sepolicy/vendor/service.te
@@ -1,6 +1,8 @@
 # Binder service types
 type gce_service,               service_manager_type;
 
+type carsenze_service,               service_manager_type, hal_service_type;
+
 # WLC
 type hal_wireless_charger_service, hal_service_type, protected_service, service_manager_type;
 
diff --git a/shared/sepolicy/vendor/service_contexts b/shared/sepolicy/vendor/service_contexts
index 03940b166..ef0a3558e 100644
--- a/shared/sepolicy/vendor/service_contexts
+++ b/shared/sepolicy/vendor/service_contexts
@@ -5,6 +5,7 @@ android.hardware.neuralnetworks.IDevice/nnapi-sample_sl_shim  u:object_r:hal_neu
 
 # Binder service mappings
 gce                                       u:object_r:gce_service:s0
+vendor.hardware.carsenze.ICarsenze/default          u:object_r:carsenze_service:s0
 
 # WLC
 vendor.google.wireless_charger.IWirelessCharger/default                      u:object_r:hal_wireless_charger_service:s0
