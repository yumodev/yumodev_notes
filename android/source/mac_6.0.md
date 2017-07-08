### 参考

[Establishing a Build Environment](https://source.android.com/source/initializing.html#setting-up-a-mac-os-x-build-environment)
[Can not find SDK 10.6 at /Developer/SDKs/MacOSX10.6.sdk](http://stackoverflow.com/questions/31589866/running-aosp-build-on-mac-yosemite-and-later)

### 分区

* 创建分区

   sudo hdiutil create -type SPARSE -fs 'Case-sensitive Journaled HFS+' -size 100g ~/android.dmg

* 挂载分区

  sudo hdiutil attach ~/android.dmg.sparseimage -mountpoint /Volumes/android


### 下载


### 编译

source build/envsetup.sh
lunch
make -j4
 1. aosp_arm-eng
     2. aosp_arm64-eng
     3. aosp_mips-eng
     4. aosp_mips64-eng
     5. aosp_x86-eng
     6. aosp_x86_64-eng
     7. aosp_deb-userdebug
     8. aosp_flo-userdebug
     9. full_fugu-userdebug
     10. aosp_fugu-userdebug
     11. mini_emulator_arm64-userdebug
     12. m_e_arm-userdebug
     13. mini_emulator_mips-userdebug
     14. mini_emulator_x86-userdebug
     15. mini_emulator_x86_64-userdebug
     16. aosp_flounder-userdebug
     17. aosp_angler-userdebug
     18. aosp_bullhead-userdebug
     19. aosp_hammerhead-userdebug
     20. aosp_hammerhead_fp-userdebug
     21. aosp_shamu-userdebug


build/core/combo/mac_version.mk:38: *****************************************************
build/core/combo/mac_version.mk:39: * Can not find SDK 10.6 at /Developer/SDKs/MacOSX10.6.sdk
build/core/combo/mac_version.mk:40: *****************************************************
build/core/combo/mac_version.mk:41: *** Stop..  Stop.

cd /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs
build/core/combo/mac_version.mk,  mac_sdk_versions_supported := 10.11
****
### 启动

emulator

### 导入Android Studio

* 进入源码根目录执行

  source build/envsetup.sh
  mmm development/tools/idegen/
  
  out/host/linux-x86/frameworks/目录下生成了idegen.jar文件
  
  development/tools/idegen/idegen.sh
  


